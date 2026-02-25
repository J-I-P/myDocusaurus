---
sidebar_position: 2
---

# React Hooks 完整指南

深入了解 React Hooks，掌握現代 React 開發的核心概念和最佳實踐。

## 什麼是 Hooks？

Hooks 是 React 16.8 引入的新特性，允許你在函數組件中使用狀態和其他 React 特性。

### 為什麼使用 Hooks？

- **簡化組件邏輯** - 避免類組件的複雜性
- **更好的邏輯重用** - 自定義 Hook 可以跨組件共享
- **更容易測試** - 函數組件更容易進行單元測試
- **更小的打包體積** - 函數組件通常更輕量

## 基礎 Hooks

### useState - 狀態管理

最基本的 Hook，用於在函數組件中管理狀態。

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>你點擊了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>
        點擊我
      </button>
    </div>
  );
}
```

#### 複雜狀態管理

```jsx
function UserForm() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0
  });

  const updateUser = (field, value) => {
    setUser(prev => ({
      ...prev,
      [field]: value
    }));
  };

  return (
    <form>
      <input
        value={user.name}
        onChange={(e) => updateUser('name', e.target.value)}
        placeholder="姓名"
      />
      <input
        value={user.email}
        onChange={(e) => updateUser('email', e.target.value)}
        placeholder="電子郵件"
      />
    </form>
  );
}
```

### useEffect - 副作用處理

處理組件的副作用，如數據獲取、訂閱或手動 DOM 操作。

```jsx
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchUser() {
      try {
        setLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        const userData = await response.json();
        setUser(userData);
      } catch (error) {
        console.error('獲取用戶資料失敗:', error);
      } finally {
        setLoading(false);
      }
    }

    if (userId) {
      fetchUser();
    }
  }, [userId]); // 依賴數組

  if (loading) return <div>載入中...</div>;
  if (!user) return <div>找不到用戶</div>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}
```

#### useEffect 清理

```jsx
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const socket = io(`/rooms/${roomId}`);

    socket.on('message', (message) => {
      setMessages(prev => [...prev, message]);
    });

    // 清理函數
    return () => {
      socket.disconnect();
    };
  }, [roomId]);

  return (
    <div>
      {messages.map(msg => (
        <div key={msg.id}>{msg.text}</div>
      ))}
    </div>
  );
}
```

### useContext - 上下文消費

避免 props drilling，在組件樹中共享數據。

```jsx
import React, { createContext, useContext, useState } from 'react';

// 創建上下文
const ThemeContext = createContext();

// Provider 組件
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// 使用上下文的組件
function Header() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <header className={`header header--${theme}`}>
      <h1>我的應用</h1>
      <button onClick={toggleTheme}>
        切換到 {theme === 'light' ? '暗色' : '亮色'} 模式
      </button>
    </header>
  );
}

// App 組件
function App() {
  return (
    <ThemeProvider>
      <Header />
      {/* 其他組件也可以使用 ThemeContext */}
    </ThemeProvider>
  );
}
```

## 進階 Hooks

### useReducer - 複雜狀態邏輯

當狀態邏輯變得複雜時，useReducer 是比 useState 更好的選擇。

```jsx
import React, { useReducer } from 'react';

// 定義 action 類型
const actionTypes = {
  INCREMENT: 'INCREMENT',
  DECREMENT: 'DECREMENT',
  RESET: 'RESET',
  SET_VALUE: 'SET_VALUE'
};

// Reducer 函數
function counterReducer(state, action) {
  switch (action.type) {
    case actionTypes.INCREMENT:
      return { count: state.count + 1 };
    case actionTypes.DECREMENT:
      return { count: state.count - 1 };
    case actionTypes.RESET:
      return { count: 0 };
    case actionTypes.SET_VALUE:
      return { count: action.payload };
    default:
      throw new Error(`未知的 action 類型: ${action.type}`);
  }
}

function AdvancedCounter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <p>計數: {state.count}</p>
      <button onClick={() => dispatch({ type: actionTypes.INCREMENT })}>
        增加
      </button>
      <button onClick={() => dispatch({ type: actionTypes.DECREMENT })}>
        減少
      </button>
      <button onClick={() => dispatch({ type: actionTypes.RESET })}>
        重置
      </button>
      <button onClick={() => dispatch({
        type: actionTypes.SET_VALUE,
        payload: 10
      })}>
        設為 10
      </button>
    </div>
  );
}
```

### useMemo - 性能優化

記憶化昂貴的計算結果，避免不必要的重新計算。

```jsx
import React, { useState, useMemo } from 'react';

function ExpensiveComponent({ items }) {
  const [filter, setFilter] = useState('');

  // 昂貴的計算操作
  const filteredItems = useMemo(() => {
    console.log('執行過濾操作...');
    return items.filter(item =>
      item.name.toLowerCase().includes(filter.toLowerCase())
    );
  }, [items, filter]); // 只有當 items 或 filter 改變時才重新計算

  const expensiveValue = useMemo(() => {
    // 模擬昂貴的計算
    let result = 0;
    for (let i = 0; i < 1000000; i++) {
      result += Math.random();
    }
    return result;
  }, []); // 空依賴數組，只計算一次

  return (
    <div>
      <input
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="搜尋項目..."
      />
      <p>昂貴計算結果: {expensiveValue.toFixed(2)}</p>
      <ul>
        {filteredItems.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

### useCallback - 函數記憶化

記憶化函數，避免子組件不必要的重新渲染。

```jsx
import React, { useState, useCallback, memo } from 'react';

// 子組件使用 memo 優化
const ChildComponent = memo(function ChildComponent({ onClick, name }) {
  console.log(`${name} 組件重新渲染`);

  return (
    <button onClick={onClick}>
      點擊 {name}
    </button>
  );
});

function ParentComponent() {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);

  // 使用 useCallback 記憶化函數
  const handleClick1 = useCallback(() => {
    setCount1(prev => prev + 1);
  }, []); // 空依賴，函數永遠不會改變

  const handleClick2 = useCallback(() => {
    setCount2(prev => prev + 1);
  }, []); // 空依賴，函數永遠不會改變

  // 沒有使用 useCallback 的函數（每次都會重新創建）
  const handleClick3 = () => {
    console.log('未優化的函數');
  };

  return (
    <div>
      <p>計數1: {count1}</p>
      <p>計數2: {count2}</p>

      <ChildComponent onClick={handleClick1} name="按鈕1" />
      <ChildComponent onClick={handleClick2} name="按鈕2" />
      <ChildComponent onClick={handleClick3} name="按鈕3" />
    </div>
  );
}
```

## 自定義 Hooks

創建可重用的邏輯封裝。

### 數據獲取 Hook

```jsx
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);

        const response = await fetch(url);
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    if (url) {
      fetchData();
    }
  }, [url]);

  return { data, loading, error };
}

// 使用自定義 Hook
function UserList() {
  const { data: users, loading, error } = useFetch('/api/users');

  if (loading) return <div>載入中...</div>;
  if (error) return <div>錯誤: {error}</div>;

  return (
    <ul>
      {users?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### 本地儲存 Hook

```jsx
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  // 從 localStorage 獲取初始值
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(`從 localStorage 讀取 ${key} 時發生錯誤:`, error);
      return initialValue;
    }
  });

  // 封裝 setter 函數
  const setValue = (value) => {
    try {
      // 允許 value 是一個函數（如 useState）
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);

      // 保存到 localStorage
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(`保存 ${key} 到 localStorage 時發生錯誤:`, error);
    }
  };

  return [storedValue, setValue];
}

// 使用範例
function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  const [language, setLanguage] = useLocalStorage('language', 'zh-TW');

  return (
    <div>
      <select value={theme} onChange={(e) => setTheme(e.target.value)}>
        <option value="light">亮色</option>
        <option value="dark">暗色</option>
      </select>

      <select value={language} onChange={(e) => setLanguage(e.target.value)}>
        <option value="zh-TW">繁體中文</option>
        <option value="en">English</option>
      </select>
    </div>
  );
}
```

## 最佳實踐

### 1. 遵循 Hooks 規則

```jsx
// 正確：只在頂層調用 Hooks
function MyComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return <div>{count}</div>;
}

// 錯誤：在條件語句中調用 Hooks
function BadComponent({ shouldCount }) {
  if (shouldCount) {
    const [count, setCount] = useState(0); // 錯誤！
  }

  return <div>Bad component</div>;
}
```

### 2. 正確使用依賴數組

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  // 正確：包含所有依賴
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]);

  // 錯誤：遺漏依賴
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, []); // 遺漏了 userId 依賴

  return <div>{user?.name}</div>;
}
```

### 3. 避免不必要的重新渲染

```jsx
// 使用 useCallback 和 useMemo 優化性能
function OptimizedComponent({ items }) {
  const [filter, setFilter] = useState('');

  const filteredItems = useMemo(() => {
    return items.filter(item => item.name.includes(filter));
  }, [items, filter]);

  const handleFilterChange = useCallback((e) => {
    setFilter(e.target.value);
  }, []);

  return (
    <div>
      <input onChange={handleFilterChange} />
      {filteredItems.map(item => (
        <Item key={item.id} item={item} />
      ))}
    </div>
  );
}
```

## 常見陷阱

### 1. 閉包陷阱

```jsx
// 問題：舊的 count 值被閉包捕獲
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setCount(count + 1); // 總是使用初始的 count 值 (0)
    }, 1000);

    return () => clearInterval(timer);
  }, []); // 空依賴數組

  return <div>{count}</div>;
}

// 解決方案：使用函數式更新
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setCount(prevCount => prevCount + 1); // 使用函數式更新
    }, 1000);

    return () => clearInterval(timer);
  }, []); // 空依賴數組

  return <div>{count}</div>;
}
```

### 2. 無限重新渲染

```jsx
// 問題：每次渲染都創建新的對象
function UserComponent() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser({ id: 1, includeProfile: true }).then(setUser);
  }, [{ id: 1, includeProfile: true }]); // 每次都是新對象！

  return <div>{user?.name}</div>;
}

// 解決方案：提取到變量或使用 useMemo
function UserComponent() {
  const [user, setUser] = useState(null);

  const fetchParams = useMemo(() => ({
    id: 1,
    includeProfile: true
  }), []);

  useEffect(() => {
    fetchUser(fetchParams).then(setUser);
  }, [fetchParams]);

  return <div>{user?.name}</div>;
}
```

---

*React Hooks 是現代 React 開發的核心，掌握它們能讓你寫出更清潔、更可維護的代碼。*