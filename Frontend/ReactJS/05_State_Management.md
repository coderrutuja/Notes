### **State Management in React: Local State vs Global State**  

State management is a critical aspect of React applications, as it determines how data is handled and shared between components.  

---

## **1. Local State**  
### **Definition**  
Local state is a state that is managed within a specific component. It is used for component-specific logic and does not affect other parts of the application.  

### **When to Use Local State**  
- Managing UI-related data (e.g., modal visibility, dropdown state).  
- Handling form inputs within a component.  
- Managing temporary data that does not need to be shared with other components.  

### **Example of Local State Using useState Hook**  
```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

export default Counter;
```
**Key Points**:  
✅ Simple and efficient for component-specific state.  
✅ Easy to manage and update.  
❌ Cannot be accessed by other components unless passed via props.  

---

## **2. Global State**  
### **Definition**  
Global state is shared across multiple components. It allows data to be accessed from anywhere in the application without passing props manually.  

### **When to Use Global State**  
- When multiple components need access to the same data (e.g., user authentication, theme settings).  
- Managing complex states like cart items in an e-commerce app.  
- When state needs to persist across different pages/routes.  

### **Example of Global State Using Context API**  
```jsx
import { createContext, useContext, useState } from 'react';

// Create a context
const ThemeContext = createContext();

// Provide global state
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Custom hook to access context
function useTheme() {
  return useContext(ThemeContext);
}

// Consuming global state in a component
function ThemeSwitcher() {
  const { theme, setTheme } = useTheme();

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Switch to {theme === 'light' ? 'Dark' : 'Light'} Mode
    </button>
  );
}

// Usage in App component
function App() {
  return (
    <ThemeProvider>
      <ThemeSwitcher />
    </ThemeProvider>
  );
}

export default App;
```
**Key Points**:  
✅ Eliminates prop drilling (passing props manually).  
✅ Useful for app-wide settings like authentication, theme, and user preferences.  
❌ Can make debugging harder if overused.  

---

## **Comparison Table: Local vs Global State**
| Feature        | Local State (`useState`) | Global State (`Context API`, `Redux`) |
|--------------|-----------------|------------------------|
| **Scope**      | Component-level | Application-wide |
| **Accessibility** | Available only inside the component | Accessible by multiple components |
| **Complexity** | Simple | More complex |
| **Use Case** | UI state, form input, small data changes | Authentication, theme, cart, notifications |
| **Performance Impact** | Minimal | Can affect performance if overused |

---

### **Other Global State Management Solutions**
Besides the Context API, there are more advanced solutions for managing global state:  

1. **Redux** – Best for large-scale applications with complex state logic.  
2. **Zustand** – A lightweight state management library with minimal boilerplate.  
3. **Recoil** – Provides an atom-based state management approach.  

🚀

# **Context API for State Management in React**  

The **Context API** is a built-in React feature that allows global state management without prop drilling. It is an alternative to external state management libraries like Redux or Zustand.  

---

## **1. Why Use Context API?**  
- Eliminates **prop drilling** (passing props manually through multiple components).  
- Provides **global state** without third-party libraries.  
- Works well for **theme, authentication, and user preferences**.  

---

## **2. How Context API Works**  
Context API consists of three main components:  
1. **Create Context** – Defines the global state.  
2. **Provider** – Wraps components to provide access to the global state.  
3. **Consumer (useContext Hook)** – Allows components to access the global state.  

---

## **3. Example: Theme Management Using Context API**  

### **Step 1: Create a Context and Provider**
Create a new file `ThemeContext.js`:  
```jsx
import { createContext, useState } from "react";

// Create the context
const ThemeContext = createContext();

// Create the provider component
export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  // Function to toggle theme
  const toggleTheme = () => {
    setTheme(theme === "light" ? "dark" : "light");
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export default ThemeContext;
```

---

### **Step 2: Wrap the App with the Provider**  
In `App.js`, wrap your app with `ThemeProvider`:  
```jsx
import React from "react";
import { ThemeProvider } from "./ThemeContext";
import ThemeSwitcher from "./ThemeSwitcher";

function App() {
  return (
    <ThemeProvider>
      <ThemeSwitcher />
    </ThemeProvider>
  );
}

export default App;
```

---

### **Step 3: Consume Context in a Component**  
Create `ThemeSwitcher.js` to access and update the theme:  
```jsx
import { useContext } from "react";
import ThemeContext from "./ThemeContext";

function ThemeSwitcher() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <div style={{ background: theme === "light" ? "#fff" : "#333", color: theme === "light" ? "#000" : "#fff", padding: "20px" }}>
      <p>Current Theme: {theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}

export default ThemeSwitcher;
```

---

## **4. When to Use Context API?**
✅ **Great for:**
- Authentication (`UserContext`)  
- Theme management (`ThemeContext`)  
- Language settings (`LanguageContext`)  

❌ **Not ideal for:**
- Large applications with frequently changing state (Redux or Zustand is better).  
- Performance-heavy apps where excessive re-renders might be an issue.  

---

## **5. Optimizing Context API**
- **Use Multiple Contexts** – Split different concerns (e.g., separate `AuthContext`, `ThemeContext`).  
- **Use Context Selectors** – Avoid unnecessary re-renders by selecting only required values.  

🚀

# **Redux Basics: Store, Actions, and Reducers**  

Redux is a **state management library** for JavaScript applications, commonly used with React. It helps manage global state in a predictable way.  

---

## **1. Core Concepts of Redux**  

1️⃣ **Store** – The central place that holds the application's state.  
2️⃣ **Actions** – Plain JavaScript objects describing what happened.  
3️⃣ **Reducers** – Functions that specify how the state changes based on actions.  

---

## **2. Installing Redux and React-Redux**  
To use Redux in a React project, install the required packages:  
```bash
npm install redux react-redux
```

---

## **3. Setting Up Redux Step by Step**  

### **Step 1: Create a Redux Store**  
The store is the **centralized state container**.  

📌 Create `store.js`:  
```jsx
import { createStore } from "redux";
import counterReducer from "./counterReducer"; // Import reducer

// Create Redux store
const store = createStore(counterReducer);

export default store;
```

---

### **Step 2: Create Actions**  
Actions are **plain objects** that describe what should change in the state.  

📌 Create `actions.js`:  
```jsx
// Action types
export const INCREMENT = "INCREMENT";
export const DECREMENT = "DECREMENT";

// Action creators
export const increment = () => ({ type: INCREMENT });
export const decrement = () => ({ type: DECREMENT });
```

---

### **Step 3: Create Reducer**  
Reducers specify **how the state changes** based on the action.  

📌 Create `counterReducer.js`:  
```jsx
import { INCREMENT, DECREMENT } from "./actions";

// Initial state
const initialState = { count: 0 };

// Reducer function
function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { count: state.count + 1 };
    case DECREMENT:
      return { count: state.count - 1 };
    default:
      return state;
  }
}

export default counterReducer;
```

---

### **Step 4: Provide Redux Store to React App**  
Wrap the React app with the `Provider` component from `react-redux`.  

📌 Update `index.js`:  
```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import store from "./store";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

---

### **Step 5: Use Redux State in Components**  
Components can access and update the Redux store using `useSelector` and `useDispatch`.  

📌 Create `Counter.js`:  
```jsx
import { useSelector, useDispatch } from "react-redux";
import { increment, decrement } from "./actions";

function Counter() {
  const count = useSelector((state) => state.count); // Access state
  const dispatch = useDispatch(); // Dispatch actions

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  );
}

export default Counter;
```

---

### **Step 6: Add Counter Component to App.js**  
📌 Update `App.js`:  
```jsx
import Counter from "./Counter";

function App() {
  return (
    <div>
      <h1>Redux Counter</h1>
      <Counter />
    </div>
  );
}

export default App;
```

---

## **4. Summary**
| Concept | Description |
|---------|------------|
| **Store** | Holds the global state |
| **Actions** | Plain JavaScript objects describing state changes |
| **Reducers** | Functions that modify state based on actions |
| **Provider** | Makes the store accessible in React components |
| **useSelector** | Retrieves data from the Redux store |
| **useDispatch** | Dispatches actions to update the state |

---

## **5. When to Use Redux?**
✅ Best for large applications with **complex state management**.  
✅ When state needs to be **shared across multiple components**.  
❌ Not ideal for small projects—**use Context API instead**.  

🚀

# **Redux Middleware: Thunk vs Saga**  

Middleware in Redux allows handling **asynchronous actions**, like API calls, before they reach the reducer. The two most common middleware solutions are **Redux Thunk** and **Redux Saga**.  

---

## **1. Why Use Middleware?**  
🔹 **Redux alone only handles synchronous actions.**  
🔹 Middleware allows us to handle **async logic** (API requests, delays, logging).  
🔹 Middleware sits between the **dispatch** and **reducer**, modifying or delaying actions.  

---

# **Redux Thunk**
### **1. What is Redux Thunk?**  
Redux Thunk allows you to write action creators that return **functions** instead of objects. This enables **async API calls** inside actions before dispatching the final action.  

### **2. Install Redux Thunk**  
```bash
npm install redux-thunk
```

### **3. Setup Redux Thunk in Store**  
📌 **Modify `store.js`**  
```jsx
import { createStore, applyMiddleware } from "redux";
import thunk from "redux-thunk"; // Import Redux Thunk
import rootReducer from "./rootReducer"; // Your reducer

const store = createStore(rootReducer, applyMiddleware(thunk)); // Apply middleware

export default store;
```

### **4. Create an Async Action with Thunk**  
📌 **Modify `actions.js`**  
```jsx
export const FETCH_USERS_REQUEST = "FETCH_USERS_REQUEST";
export const FETCH_USERS_SUCCESS = "FETCH_USERS_SUCCESS";
export const FETCH_USERS_FAILURE = "FETCH_USERS_FAILURE";

// Action creators
const fetchUsersRequest = () => ({ type: FETCH_USERS_REQUEST });
const fetchUsersSuccess = (users) => ({ type: FETCH_USERS_SUCCESS, payload: users });
const fetchUsersFailure = (error) => ({ type: FETCH_USERS_FAILURE, payload: error });

// Async action using Thunk
export const fetchUsers = () => {
  return async (dispatch) => {
    dispatch(fetchUsersRequest()); // Start loading
    try {
      const response = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await response.json();
      dispatch(fetchUsersSuccess(data)); // Dispatch success action
    } catch (error) {
      dispatch(fetchUsersFailure(error.message)); // Dispatch failure action
    }
  };
};
```

### **5. Create a Reducer for Handling API Data**  
📌 **Modify `rootReducer.js`**  
```jsx
import { FETCH_USERS_REQUEST, FETCH_USERS_SUCCESS, FETCH_USERS_FAILURE } from "./actions";

const initialState = {
  loading: false,
  users: [],
  error: null,
};

function rootReducer(state = initialState, action) {
  switch (action.type) {
    case FETCH_USERS_REQUEST:
      return { ...state, loading: true };
    case FETCH_USERS_SUCCESS:
      return { loading: false, users: action.payload, error: null };
    case FETCH_USERS_FAILURE:
      return { loading: false, users: [], error: action.payload };
    default:
      return state;
  }
}

export default rootReducer;
```

### **6. Use Thunk in React Component**  
📌 **Modify `UsersList.js`**  
```jsx
import { useEffect } from "react";
import { useSelector, useDispatch } from "react-redux";
import { fetchUsers } from "./actions";

function UsersList() {
  const { loading, users, error } = useSelector((state) => state);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchUsers()); // Fetch users on mount
  }, [dispatch]);

  return (
    <div>
      {loading && <p>Loading...</p>}
      {error && <p>Error: {error}</p>}
      <ul>
        {users.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default UsersList;
```

---

## **Redux Saga**
### **1. What is Redux Saga?**  
Redux Saga is another middleware that **uses generator functions (`function*`)** to handle asynchronous actions. Unlike Thunk, it **manages side effects (API calls, delays, etc.) outside of components**.  

### **2. Install Redux Saga**  
```bash
npm install redux-saga
```

### **3. Setup Redux Saga in Store**  
📌 **Modify `store.js`**  
```jsx
import { createStore, applyMiddleware } from "redux";
import createSagaMiddleware from "redux-saga";
import rootReducer from "./rootReducer";
import rootSaga from "./sagas"; // Import sagas

const sagaMiddleware = createSagaMiddleware(); // Create saga middleware
const store = createStore(rootReducer, applyMiddleware(sagaMiddleware));

sagaMiddleware.run(rootSaga); // Run sagas

export default store;
```

### **4. Create an Async Saga**  
📌 **Create `sagas.js`**  
```jsx
import { takeLatest, call, put } from "redux-saga/effects";
import { FETCH_USERS_REQUEST, fetchUsersSuccess, fetchUsersFailure } from "./actions";

// Fetch data from API
function* fetchUsersSaga() {
  try {
    const response = yield call(fetch, "https://jsonplaceholder.typicode.com/users");
    const data = yield response.json();
    yield put(fetchUsersSuccess(data)); // Dispatch success action
  } catch (error) {
    yield put(fetchUsersFailure(error.message)); // Dispatch failure action
  }
}

// Watcher Saga
function* rootSaga() {
  yield takeLatest(FETCH_USERS_REQUEST, fetchUsersSaga); // Listen for fetch action
}

export default rootSaga;
```

### **5. Dispatch Action from React Component**  
📌 **Modify `UsersList.js`**  
```jsx
import { useEffect } from "react";
import { useSelector, useDispatch } from "react-redux";
import { FETCH_USERS_REQUEST } from "./actions";

function UsersList() {
  const { loading, users, error } = useSelector((state) => state);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch({ type: FETCH_USERS_REQUEST }); // Dispatch fetch action
  }, [dispatch]);

  return (
    <div>
      {loading && <p>Loading...</p>}
      {error && <p>Error: {error}</p>}
      <ul>
        {users.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default UsersList;
```

---

## **Thunk vs Saga: Key Differences**
| Feature | Redux Thunk | Redux Saga |
|---------|------------|------------|
| **Syntax** | Uses functions (`() => {}`) | Uses generators (`function* () {}`) |
| **API Calls** | Inside action creators | Handled externally in sagas |
| **Complexity** | Simple | More advanced |
| **Performance** | Suitable for small apps | Better for large apps |
| **Side Effects** | Handled directly in actions | Managed using saga effects (`call`, `put`) |

---

## **When to Use Thunk vs Saga?**
✅ **Use Redux Thunk if:**
- You have a simple app with **few async operations**.
- You prefer **easier debugging** and fewer concepts.

✅ **Use Redux Saga if:**
- You have a **large-scale app** with **complex async flows**.
- You need to **handle side effects efficiently** (API calls, background tasks).

🚀

# **Redux Saga Authentication Example (Login Flow)**  

This example demonstrates how to handle **user authentication (login/logout)** using **Redux Saga**.  

---

## **1. Install Dependencies**  
```bash
npm install redux redux-saga react-redux
```

---

## **2. Create Redux Store with Saga Middleware**  
📌 **Create `store.js`**  
```jsx
import { createStore, applyMiddleware } from "redux";
import createSagaMiddleware from "redux-saga";
import rootReducer from "./reducers";
import rootSaga from "./sagas"; // Import rootSaga

const sagaMiddleware = createSagaMiddleware();
const store = createStore(rootReducer, applyMiddleware(sagaMiddleware));

sagaMiddleware.run(rootSaga); // Run Sagas

export default store;
```

---

## **3. Define Authentication Actions**  
📌 **Create `actions.js`**  
```jsx
export const LOGIN_REQUEST = "LOGIN_REQUEST";
export const LOGIN_SUCCESS = "LOGIN_SUCCESS";
export const LOGIN_FAILURE = "LOGIN_FAILURE";
export const LOGOUT = "LOGOUT";

// Action creators
export const loginRequest = (credentials) => ({ type: LOGIN_REQUEST, payload: credentials });
export const loginSuccess = (user) => ({ type: LOGIN_SUCCESS, payload: user });
export const loginFailure = (error) => ({ type: LOGIN_FAILURE, payload: error });
export const logout = () => ({ type: LOGOUT });
```

---

## **4. Create Authentication Reducer**  
📌 **Create `authReducer.js`**  
```jsx
import { LOGIN_REQUEST, LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT } from "./actions";

const initialState = {
  user: null,
  loading: false,
  error: null,
};

function authReducer(state = initialState, action) {
  switch (action.type) {
    case LOGIN_REQUEST:
      return { ...state, loading: true };
    case LOGIN_SUCCESS:
      return { user: action.payload, loading: false, error: null };
    case LOGIN_FAILURE:
      return { user: null, loading: false, error: action.payload };
    case LOGOUT:
      return { user: null, loading: false, error: null };
    default:
      return state;
  }
}

export default authReducer;
```

---

## **5. Setup Redux Saga for Authentication**  
📌 **Create `sagas.js`**  
```jsx
import { takeLatest, call, put } from "redux-saga/effects";
import { LOGIN_REQUEST, loginSuccess, loginFailure } from "./actions";

// Mock API function for login
const fakeLoginApi = async (credentials) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (credentials.username === "admin" && credentials.password === "password") {
        resolve({ username: "admin", token: "fake-jwt-token" });
      } else {
        reject(new Error("Invalid username or password"));
      }
    }, 1000);
  });
};

// Worker Saga
function* loginSaga(action) {
  try {
    const user = yield call(fakeLoginApi, action.payload);
    yield put(loginSuccess(user)); // Dispatch success action
  } catch (error) {
    yield put(loginFailure(error.message)); // Dispatch failure action
  }
}

// Watcher Saga
function* rootSaga() {
  yield takeLatest(LOGIN_REQUEST, loginSaga);
}

export default rootSaga;
```

---

## **6. Provide Store to React Application**  
📌 **Modify `index.js`**  
```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import store from "./store";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

---

## **7. Create Login Component**  
📌 **Create `Login.js`**  
```jsx
import { useState } from "react";
import { useDispatch, useSelector } from "react-redux";
import { loginRequest, logout } from "./actions";

function Login() {
  const [credentials, setCredentials] = useState({ username: "", password: "" });
  const dispatch = useDispatch();
  const { user, loading, error } = useSelector((state) => state);

  const handleLogin = () => {
    dispatch(loginRequest(credentials)); // Dispatch login request
  };

  return (
    <div>
      {user ? (
        <>
          <h2>Welcome, {user.username}!</h2>
          <button onClick={() => dispatch(logout())}>Logout</button>
        </>
      ) : (
        <>
          <input
            type="text"
            placeholder="Username"
            value={credentials.username}
            onChange={(e) => setCredentials({ ...credentials, username: e.target.value })}
          />
          <input
            type="password"
            placeholder="Password"
            value={credentials.password}
            onChange={(e) => setCredentials({ ...credentials, password: e.target.value })}
          />
          <button onClick={handleLogin} disabled={loading}>
            {loading ? "Logging in..." : "Login"}
          </button>
          {error && <p style={{ color: "red" }}>{error}</p>}
        </>
      )}
    </div>
  );
}

export default Login;
```

---

## **8. Add Login Component to App.js**  
📌 **Modify `App.js`**  
```jsx
import Login from "./Login";

function App() {
  return (
    <div>
      <h1>Redux Saga Authentication</h1>
      <Login />
    </div>
  );
}

export default App;
```

---

# **How It Works (Flow)**  
1️⃣ **User enters credentials and clicks "Login"**  
2️⃣ `dispatch(loginRequest(credentials))` is triggered  
3️⃣ **Redux Saga listens for `LOGIN_REQUEST` and runs `loginSaga()`**  
4️⃣ **Fake API is called (`fakeLoginApi`)**
   - ✅ If successful → Dispatch `LOGIN_SUCCESS`
   - ❌ If failed → Dispatch `LOGIN_FAILURE`  
5️⃣ **React UI updates based on the state**  

---

# **Redux Thunk vs Redux Saga for Authentication**
| Feature | Redux Thunk | Redux Saga |
|---------|------------|------------|
| **Syntax** | Uses functions (`() => {}`) | Uses generator functions (`function*`) |
| **Handling Side Effects** | Inside actions | In separate saga files |
| **Complexity** | Simple | More powerful |
| **Best For** | Small projects | Large apps with complex flows |

---

# **Final Thoughts**
✅ **Redux Thunk** is great for **simple apps** where async logic is straightforward.  
✅ **Redux Saga** is better for **large apps** where side effects like authentication, API calls, and background tasks need **better control**.  

🚀

# **Redux Saga Authentication Example (Login Flow)**  

This example demonstrates how to handle **user authentication (login/logout)** using **Redux Saga**.  

---

## **1. Install Dependencies**  
```bash
npm install redux redux-saga react-redux
```

---

## **2. Create Redux Store with Saga Middleware**  
📌 **Create `store.js`**  
```jsx
import { createStore, applyMiddleware } from "redux";
import createSagaMiddleware from "redux-saga";
import rootReducer from "./reducers";
import rootSaga from "./sagas"; // Import rootSaga

const sagaMiddleware = createSagaMiddleware();
const store = createStore(rootReducer, applyMiddleware(sagaMiddleware));

sagaMiddleware.run(rootSaga); // Run Sagas

export default store;
```

---

## **3. Define Authentication Actions**  
📌 **Create `actions.js`**  
```jsx
export const LOGIN_REQUEST = "LOGIN_REQUEST";
export const LOGIN_SUCCESS = "LOGIN_SUCCESS";
export const LOGIN_FAILURE = "LOGIN_FAILURE";
export const LOGOUT = "LOGOUT";

// Action creators
export const loginRequest = (credentials) => ({ type: LOGIN_REQUEST, payload: credentials });
export const loginSuccess = (user) => ({ type: LOGIN_SUCCESS, payload: user });
export const loginFailure = (error) => ({ type: LOGIN_FAILURE, payload: error });
export const logout = () => ({ type: LOGOUT });
```

---

## **4. Create Authentication Reducer**  
📌 **Create `authReducer.js`**  
```jsx
import { LOGIN_REQUEST, LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT } from "./actions";

const initialState = {
  user: null,
  loading: false,
  error: null,
};

function authReducer(state = initialState, action) {
  switch (action.type) {
    case LOGIN_REQUEST:
      return { ...state, loading: true };
    case LOGIN_SUCCESS:
      return { user: action.payload, loading: false, error: null };
    case LOGIN_FAILURE:
      return { user: null, loading: false, error: action.payload };
    case LOGOUT:
      return { user: null, loading: false, error: null };
    default:
      return state;
  }
}

export default authReducer;
```

---

## **5. Setup Redux Saga for Authentication**  
📌 **Create `sagas.js`**  
```jsx
import { takeLatest, call, put } from "redux-saga/effects";
import { LOGIN_REQUEST, loginSuccess, loginFailure } from "./actions";

// Mock API function for login
const fakeLoginApi = async (credentials) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (credentials.username === "admin" && credentials.password === "password") {
        resolve({ username: "admin", token: "fake-jwt-token" });
      } else {
        reject(new Error("Invalid username or password"));
      }
    }, 1000);
  });
};

// Worker Saga
function* loginSaga(action) {
  try {
    const user = yield call(fakeLoginApi, action.payload);
    yield put(loginSuccess(user)); // Dispatch success action
  } catch (error) {
    yield put(loginFailure(error.message)); // Dispatch failure action
  }
}

// Watcher Saga
function* rootSaga() {
  yield takeLatest(LOGIN_REQUEST, loginSaga);
}

export default rootSaga;
```

---

## **6. Provide Store to React Application**  
📌 **Modify `index.js`**  
```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import store from "./store";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

---

## **7. Create Login Component**  
📌 **Create `Login.js`**  
```jsx
import { useState } from "react";
import { useDispatch, useSelector } from "react-redux";
import { loginRequest, logout } from "./actions";

function Login() {
  const [credentials, setCredentials] = useState({ username: "", password: "" });
  const dispatch = useDispatch();
  const { user, loading, error } = useSelector((state) => state);

  const handleLogin = () => {
    dispatch(loginRequest(credentials)); // Dispatch login request
  };

  return (
    <div>
      {user ? (
        <>
          <h2>Welcome, {user.username}!</h2>
          <button onClick={() => dispatch(logout())}>Logout</button>
        </>
      ) : (
        <>
          <input
            type="text"
            placeholder="Username"
            value={credentials.username}
            onChange={(e) => setCredentials({ ...credentials, username: e.target.value })}
          />
          <input
            type="password"
            placeholder="Password"
            value={credentials.password}
            onChange={(e) => setCredentials({ ...credentials, password: e.target.value })}
          />
          <button onClick={handleLogin} disabled={loading}>
            {loading ? "Logging in..." : "Login"}
          </button>
          {error && <p style={{ color: "red" }}>{error}</p>}
        </>
      )}
    </div>
  );
}

export default Login;
```

---

## **8. Add Login Component to App.js**  
📌 **Modify `App.js`**  
```jsx
import Login from "./Login";

function App() {
  return (
    <div>
      <h1>Redux Saga Authentication</h1>
      <Login />
    </div>
  );
}

export default App;
```

---

# **How It Works (Flow)**  
1️⃣ **User enters credentials and clicks "Login"**  
2️⃣ `dispatch(loginRequest(credentials))` is triggered  
3️⃣ **Redux Saga listens for `LOGIN_REQUEST` and runs `loginSaga()`**  
4️⃣ **Fake API is called (`fakeLoginApi`)**
   - ✅ If successful → Dispatch `LOGIN_SUCCESS`
   - ❌ If failed → Dispatch `LOGIN_FAILURE`  
5️⃣ **React UI updates based on the state**  

---

# **Redux Thunk vs Redux Saga for Authentication**
| Feature | Redux Thunk | Redux Saga |
|---------|------------|------------|
| **Syntax** | Uses functions (`() => {}`) | Uses generator functions (`function*`) |
| **Handling Side Effects** | Inside actions | In separate saga files |
| **Complexity** | Simple | More powerful |
| **Best For** | Small projects | Large apps with complex flows |

---

# **Final Thoughts**
✅ **Redux Thunk** is great for **simple apps** where async logic is straightforward.  
✅ **Redux Saga** is better for **large apps** where side effects like authentication, API calls, and background tasks need **better control**.  

 🚀

 # **Redux Saga Authentication with JWT (Real API Example)**  

This guide extends the previous Redux Saga example by **integrating JWT authentication** using a real API. We'll implement:  
✅ **Login** (Send credentials, receive JWT token)  
✅ **Store JWT in localStorage**  
✅ **Auto-login on page refresh**  
✅ **Logout** (Clear JWT from storage)  
✅ **Protect Routes** (Redirect unauthorized users)  

---

## **1. Install Dependencies**  
```bash
npm install redux redux-saga react-redux axios react-router-dom
```

---

## **2. Create Redux Store with Saga Middleware**  
📌 **Modify `store.js`**  
```jsx
import { createStore, applyMiddleware } from "redux";
import createSagaMiddleware from "redux-saga";
import rootReducer from "./reducers";
import rootSaga from "./sagas";

const sagaMiddleware = createSagaMiddleware();
const store = createStore(rootReducer, applyMiddleware(sagaMiddleware));

sagaMiddleware.run(rootSaga);

export default store;
```

---

## **3. Define Authentication Actions**  
📌 **Modify `actions.js`**  
```jsx
export const LOGIN_REQUEST = "LOGIN_REQUEST";
export const LOGIN_SUCCESS = "LOGIN_SUCCESS";
export const LOGIN_FAILURE = "LOGIN_FAILURE";
export const LOGOUT = "LOGOUT";
export const CHECK_AUTH = "CHECK_AUTH";

// Action creators
export const loginRequest = (credentials) => ({ type: LOGIN_REQUEST, payload: credentials });
export const loginSuccess = (user) => ({ type: LOGIN_SUCCESS, payload: user });
export const loginFailure = (error) => ({ type: LOGIN_FAILURE, payload: error });
export const logout = () => ({ type: LOGOUT });
export const checkAuth = () => ({ type: CHECK_AUTH });
```

---

## **4. Create Authentication Reducer**  
📌 **Modify `authReducer.js`**  
```jsx
import { LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT } from "./actions";

const initialState = {
  user: JSON.parse(localStorage.getItem("user")) || null, // Persist user session
  error: null,
};

function authReducer(state = initialState, action) {
  switch (action.type) {
    case LOGIN_SUCCESS:
      return { user: action.payload, error: null };
    case LOGIN_FAILURE:
      return { user: null, error: action.payload };
    case LOGOUT:
      return { user: null, error: null };
    default:
      return state;
  }
}

export default authReducer;
```

---

## **5. Setup Redux Saga for Authentication with JWT API**  
📌 **Modify `sagas.js`**  
```jsx
import { takeLatest, call, put } from "redux-saga/effects";
import axios from "axios";
import { LOGIN_REQUEST, loginSuccess, loginFailure, LOGOUT } from "./actions";

// API call for login
const loginApi = async (credentials) => {
  const response = await axios.post("https://your-api.com/login", credentials);
  return response.data;
};

// Worker Saga: Handle login
function* loginSaga(action) {
  try {
    const data = yield call(loginApi, action.payload);
    localStorage.setItem("user", JSON.stringify(data)); // Store user session
    yield put(loginSuccess(data)); // Dispatch success action
  } catch (error) {
    yield put(loginFailure(error.response?.data?.message || "Login failed"));
  }
}

// Worker Saga: Handle logout
function* logoutSaga() {
  localStorage.removeItem("user");
}

// Watcher Saga
function* rootSaga() {
  yield takeLatest(LOGIN_REQUEST, loginSaga);
  yield takeLatest(LOGOUT, logoutSaga);
}

export default rootSaga;
```

---

## **6. Provide Store to React Application**  
📌 **Modify `index.js`**  
```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import { BrowserRouter } from "react-router-dom";
import store from "./store";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <Provider store={store}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </Provider>
);
```

---

## **7. Create Login Component with JWT Handling**  
📌 **Modify `Login.js`**  
```jsx
import { useState } from "react";
import { useDispatch, useSelector } from "react-redux";
import { loginRequest, logout } from "./actions";

function Login() {
  const [credentials, setCredentials] = useState({ email: "", password: "" });
  const dispatch = useDispatch();
  const { user, error } = useSelector((state) => state);

  const handleLogin = () => {
    dispatch(loginRequest(credentials)); // Dispatch login request
  };

  return (
    <div>
      {user ? (
        <>
          <h2>Welcome, {user.name}!</h2>
          <button onClick={() => dispatch(logout())}>Logout</button>
        </>
      ) : (
        <>
          <input
            type="email"
            placeholder="Email"
            value={credentials.email}
            onChange={(e) => setCredentials({ ...credentials, email: e.target.value })}
          />
          <input
            type="password"
            placeholder="Password"
            value={credentials.password}
            onChange={(e) => setCredentials({ ...credentials, password: e.target.value })}
          />
          <button onClick={handleLogin}>Login</button>
          {error && <p style={{ color: "red" }}>{error}</p>}
        </>
      )}
    </div>
  );
}

export default Login;
```

---

## **8. Create Protected Route (Redirect Unauthorized Users)**  
📌 **Create `ProtectedRoute.js`**  
```jsx
import { Navigate } from "react-router-dom";
import { useSelector } from "react-redux";

function ProtectedRoute({ children }) {
  const { user } = useSelector((state) => state);
  return user ? children : <Navigate to="/login" />;
}

export default ProtectedRoute;
```

---

## **9. Define Routes and Protect Dashboard**  
📌 **Modify `App.js`**  
```jsx
import { Routes, Route } from "react-router-dom";
import Login from "./Login";
import Dashboard from "./Dashboard";
import ProtectedRoute from "./ProtectedRoute";

function App() {
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/dashboard" element={<ProtectedRoute><Dashboard /></ProtectedRoute>} />
    </Routes>
  );
}

export default App;
```

---

## **10. Create Dashboard Component**  
📌 **Create `Dashboard.js`**  
```jsx
function Dashboard() {
  return <h2>Welcome to the Dashboard! You are logged in.</h2>;
}

export default Dashboard;
```

---

# **How It Works (Flow)**  
1️⃣ **User enters email & password → Clicks Login**  
2️⃣ `dispatch(loginRequest(credentials))` is triggered  
3️⃣ **Redux Saga calls the API (`loginApi`)**
   - ✅ **If successful:** Stores user & token in `localStorage`, dispatches `LOGIN_SUCCESS`
   - ❌ **If failed:** Dispatches `LOGIN_FAILURE`  
4️⃣ **User is redirected to the Dashboard** (`ProtectedRoute`)  
5️⃣ **On logout, Redux Saga clears localStorage and state**  

---

# **Bonus: Auto-Login on Refresh**
To persist the session, modify `App.js` to check `localStorage` on mount:  
```jsx
import { useEffect } from "react";
import { useDispatch } from "react-redux";
import { checkAuth } from "./actions";

function App() {
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(checkAuth());
  }, [dispatch]);

  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/dashboard" element={<ProtectedRoute><Dashboard /></ProtectedRoute>} />
    </Routes>
  );
}

export default App;
```

---

# **Final Thoughts**
✅ This setup ensures **secure authentication** with JWT.  
✅ Users stay logged in after refresh.  
✅ Unauthorized users are redirected from protected routes.  

🚀

# **Redux Saga Authentication with JWT Refresh & Role-Based Access Control**  

This guide enhances our JWT authentication setup by adding:  
✅ **Refresh Token Mechanism** (Auto-renew expired tokens)  
✅ **Role-Based Access Control (RBAC)** (Restrict routes based on user roles)  

---

## **1. Update Dependencies**  
Ensure you have `axios` and `react-router-dom`:  
```bash
npm install axios react-router-dom redux redux-saga react-redux
```

---

## **2. Modify Authentication Actions**  
📌 **Modify `actions.js`**  
```jsx
export const LOGIN_REQUEST = "LOGIN_REQUEST";
export const LOGIN_SUCCESS = "LOGIN_SUCCESS";
export const LOGIN_FAILURE = "LOGIN_FAILURE";
export const LOGOUT = "LOGOUT";
export const REFRESH_TOKEN = "REFRESH_TOKEN";
export const CHECK_AUTH = "CHECK_AUTH";

// Action Creators
export const loginRequest = (credentials) => ({ type: LOGIN_REQUEST, payload: credentials });
export const loginSuccess = (user) => ({ type: LOGIN_SUCCESS, payload: user });
export const loginFailure = (error) => ({ type: LOGIN_FAILURE, payload: error });
export const logout = () => ({ type: LOGOUT });
export const refreshToken = () => ({ type: REFRESH_TOKEN });
export const checkAuth = () => ({ type: CHECK_AUTH });
```

---

## **3. Modify Authentication Reducer**  
📌 **Modify `authReducer.js`**  
```jsx
import { LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT } from "./actions";

const initialState = {
  user: JSON.parse(localStorage.getItem("user")) || null,
  error: null,
};

function authReducer(state = initialState, action) {
  switch (action.type) {
    case LOGIN_SUCCESS:
      return { user: action.payload, error: null };
    case LOGIN_FAILURE:
      return { user: null, error: action.payload };
    case LOGOUT:
      return { user: null, error: null };
    default:
      return state;
  }
}

export default authReducer;
```

---

## **4. Setup JWT Refresh & API Handling in Redux Saga**  
📌 **Modify `sagas.js`**  
```jsx
import { takeLatest, call, put } from "redux-saga/effects";
import axios from "axios";
import { LOGIN_REQUEST, LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT, REFRESH_TOKEN } from "./actions";

// Axios instance with interceptor for token refresh
const api = axios.create({
  baseURL: "https://your-api.com",
});

api.interceptors.request.use((config) => {
  const user = JSON.parse(localStorage.getItem("user"));
  if (user?.accessToken) {
    config.headers.Authorization = `Bearer ${user.accessToken}`;
  }
  return config;
});

// Refresh token function
const refreshApi = async () => {
  const user = JSON.parse(localStorage.getItem("user"));
  const response = await api.post("/refresh", { refreshToken: user.refreshToken });
  return response.data;
};

// Login function
const loginApi = async (credentials) => {
  const response = await api.post("/login", credentials);
  return response.data;
};

// Worker Saga: Handle login
function* loginSaga(action) {
  try {
    const data = yield call(loginApi, action.payload);
    localStorage.setItem("user", JSON.stringify(data));
    yield put({ type: LOGIN_SUCCESS, payload: data });
  } catch (error) {
    yield put({ type: LOGIN_FAILURE, payload: error.response?.data?.message || "Login failed" });
  }
}

// Worker Saga: Handle refresh token
function* refreshSaga() {
  try {
    const newData = yield call(refreshApi);
    localStorage.setItem("user", JSON.stringify(newData));
    yield put({ type: LOGIN_SUCCESS, payload: newData });
  } catch (error) {
    yield put({ type: LOGOUT }); // Logout on refresh failure
  }
}

// Worker Saga: Handle logout
function* logoutSaga() {
  localStorage.removeItem("user");
}

// Watcher Saga
function* rootSaga() {
  yield takeLatest(LOGIN_REQUEST, loginSaga);
  yield takeLatest(REFRESH_TOKEN, refreshSaga);
  yield takeLatest(LOGOUT, logoutSaga);
}

export default rootSaga;
```

---

## **5. Modify ProtectedRoute for Role-Based Access**  
📌 **Modify `ProtectedRoute.js`**  
```jsx
import { Navigate } from "react-router-dom";
import { useSelector } from "react-redux";

function ProtectedRoute({ children, requiredRole }) {
  const { user } = useSelector((state) => state);

  if (!user) {
    return <Navigate to="/login" />;
  }

  if (requiredRole && user.role !== requiredRole) {
    return <Navigate to="/unauthorized" />;
  }

  return children;
}

export default ProtectedRoute;
```

---

## **6. Define Routes with Role-Based Protection**  
📌 **Modify `App.js`**  
```jsx
import { Routes, Route } from "react-router-dom";
import Login from "./Login";
import Dashboard from "./Dashboard";
import AdminPanel from "./AdminPanel";
import Unauthorized from "./Unauthorized";
import ProtectedRoute from "./ProtectedRoute";

function App() {
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/unauthorized" element={<Unauthorized />} />
      <Route path="/dashboard" element={<ProtectedRoute><Dashboard /></ProtectedRoute>} />
      <Route path="/admin" element={<ProtectedRoute requiredRole="admin"><AdminPanel /></ProtectedRoute>} />
    </Routes>
  );
}

export default App;
```

---

## **7. Create Unauthorized Page**  
📌 **Create `Unauthorized.js`**  
```jsx
function Unauthorized() {
  return <h2>Access Denied: You do not have permission to view this page.</h2>;
}

export default Unauthorized;
```

---

## **8. Create Admin Panel Component**  
📌 **Create `AdminPanel.js`**  
```jsx
function AdminPanel() {
  return <h2>Welcome Admin! You have special access.</h2>;
}

export default AdminPanel;
```

---

# **How It Works (Flow)**  
1️⃣ **User logs in → Redux Saga stores JWT & refresh token**  
2️⃣ **API requests use JWT** (via Axios interceptor)  
3️⃣ **If JWT expires** → Saga calls refresh API to get a new token  
4️⃣ **Protected routes check roles**  
   - ✅ **If user has access** → Show page  
   - ❌ **If not** → Redirect to Unauthorized page  
5️⃣ **Logout clears user session**  

---

# **Final Thoughts**  
✅ **Automatic token refresh** prevents forced logouts.  
✅ **Role-based access control (RBAC)** secures pages.  
✅ **Users can't access admin pages unless authorized.**  

🚀

# **Google OAuth Authentication with Redux Saga & JWT**  

This guide adds **Google OAuth authentication** to our Redux Saga setup. We'll:  
✅ Integrate **Google Sign-In**  
✅ Send **Google token to backend** for **JWT generation**  
✅ Store **JWT & refresh token**  
✅ Auto-refresh JWT when expired  

---

## **1. Install Dependencies**  
```bash
npm install @react-oauth/google axios redux react-redux redux-saga react-router-dom
```

---

## **2. Setup Google OAuth Provider**  
📌 **Modify `index.js`**  
```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import { GoogleOAuthProvider } from "@react-oauth/google";
import { BrowserRouter } from "react-router-dom";
import store from "./store";
import App from "./App";

const CLIENT_ID = "YOUR_GOOGLE_CLIENT_ID"; // Replace with your Google Client ID

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <Provider store={store}>
    <GoogleOAuthProvider clientId={CLIENT_ID}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </GoogleOAuthProvider>
  </Provider>
);
```

---

## **3. Modify Authentication Actions**  
📌 **Modify `actions.js`**  
```jsx
export const GOOGLE_LOGIN_REQUEST = "GOOGLE_LOGIN_REQUEST";
export const LOGIN_SUCCESS = "LOGIN_SUCCESS";
export const LOGIN_FAILURE = "LOGIN_FAILURE";
export const LOGOUT = "LOGOUT";
export const REFRESH_TOKEN = "REFRESH_TOKEN";

export const googleLoginRequest = (googleToken) => ({ type: GOOGLE_LOGIN_REQUEST, payload: googleToken });
export const loginSuccess = (user) => ({ type: LOGIN_SUCCESS, payload: user });
export const loginFailure = (error) => ({ type: LOGIN_FAILURE, payload: error });
export const logout = () => ({ type: LOGOUT });
export const refreshToken = () => ({ type: REFRESH_TOKEN });
```

---

## **4. Modify Authentication Reducer**  
📌 **Modify `authReducer.js`**  
```jsx
import { LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT } from "./actions";

const initialState = {
  user: JSON.parse(localStorage.getItem("user")) || null,
  error: null,
};

function authReducer(state = initialState, action) {
  switch (action.type) {
    case LOGIN_SUCCESS:
      return { user: action.payload, error: null };
    case LOGIN_FAILURE:
      return { user: null, error: action.payload };
    case LOGOUT:
      return { user: null, error: null };
    default:
      return state;
  }
}

export default authReducer;
```

---

## **5. Setup Google OAuth Login in Redux Saga**  
📌 **Modify `sagas.js`**  
```jsx
import { takeLatest, call, put } from "redux-saga/effects";
import axios from "axios";
import { GOOGLE_LOGIN_REQUEST, LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT, REFRESH_TOKEN } from "./actions";

// Axios instance with interceptor for JWT refresh
const api = axios.create({
  baseURL: "https://your-api.com",
});

api.interceptors.request.use((config) => {
  const user = JSON.parse(localStorage.getItem("user"));
  if (user?.accessToken) {
    config.headers.Authorization = `Bearer ${user.accessToken}`;
  }
  return config;
});

// Google Login API request
const googleLoginApi = async (googleToken) => {
  const response = await api.post("/auth/google", { token: googleToken });
  return response.data;
};

// Refresh token API request
const refreshApi = async () => {
  const user = JSON.parse(localStorage.getItem("user"));
  const response = await api.post("/refresh", { refreshToken: user.refreshToken });
  return response.data;
};

// Worker Saga: Handle Google login
function* googleLoginSaga(action) {
  try {
    const data = yield call(googleLoginApi, action.payload);
    localStorage.setItem("user", JSON.stringify(data)); // Store JWT & refresh token
    yield put({ type: LOGIN_SUCCESS, payload: data });
  } catch (error) {
    yield put({ type: LOGIN_FAILURE, payload: error.response?.data?.message || "Google login failed" });
  }
}

// Worker Saga: Handle refresh token
function* refreshSaga() {
  try {
    const newData = yield call(refreshApi);
    localStorage.setItem("user", JSON.stringify(newData));
    yield put({ type: LOGIN_SUCCESS, payload: newData });
  } catch (error) {
    yield put({ type: LOGOUT }); // Logout on refresh failure
  }
}

// Worker Saga: Handle logout
function* logoutSaga() {
  localStorage.removeItem("user");
}

// Watcher Saga
function* rootSaga() {
  yield takeLatest(GOOGLE_LOGIN_REQUEST, googleLoginSaga);
  yield takeLatest(REFRESH_TOKEN, refreshSaga);
  yield takeLatest(LOGOUT, logoutSaga);
}

export default rootSaga;
```

---

## **6. Add Google Login Button**  
📌 **Modify `Login.js`**  
```jsx
import { useDispatch, useSelector } from "react-redux";
import { googleLoginRequest, logout } from "./actions";
import { GoogleLogin } from "@react-oauth/google";

function Login() {
  const dispatch = useDispatch();
  const { user, error } = useSelector((state) => state);

  const handleGoogleSuccess = (response) => {
    dispatch(googleLoginRequest(response.credential)); // Send Google token to Redux Saga
  };

  return (
    <div>
      {user ? (
        <>
          <h2>Welcome, {user.name}!</h2>
          <button onClick={() => dispatch(logout())}>Logout</button>
        </>
      ) : (
        <>
          <GoogleLogin onSuccess={handleGoogleSuccess} onError={() => console.log("Login failed")} />
          {error && <p style={{ color: "red" }}>{error}</p>}
        </>
      )}
    </div>
  );
}

export default Login;
```

---

## **7. Protect Routes with JWT & Role-Based Access**  
📌 **Modify `ProtectedRoute.js`**  
```jsx
import { Navigate } from "react-router-dom";
import { useSelector } from "react-redux";

function ProtectedRoute({ children, requiredRole }) {
  const { user } = useSelector((state) => state);

  if (!user) {
    return <Navigate to="/login" />;
  }

  if (requiredRole && user.role !== requiredRole) {
    return <Navigate to="/unauthorized" />;
  }

  return children;
}

export default ProtectedRoute;
```

---

## **8. Define Routes with Google Auth & Role Protection**  
📌 **Modify `App.js`**  
```jsx
import { Routes, Route } from "react-router-dom";
import Login from "./Login";
import Dashboard from "./Dashboard";
import AdminPanel from "./AdminPanel";
import Unauthorized from "./Unauthorized";
import ProtectedRoute from "./ProtectedRoute";

function App() {
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/unauthorized" element={<Unauthorized />} />
      <Route path="/dashboard" element={<ProtectedRoute><Dashboard /></ProtectedRoute>} />
      <Route path="/admin" element={<ProtectedRoute requiredRole="admin"><AdminPanel /></ProtectedRoute>} />
    </Routes>
  );
}

export default App;
```

---

# **How It Works (Flow)**  
1️⃣ **User clicks Google Login → Google returns a token**  
2️⃣ `dispatch(googleLoginRequest(googleToken))` is triggered  
3️⃣ **Redux Saga sends token to backend** (`/auth/google`)  
   - ✅ **If successful:** Backend returns JWT & refresh token  
   - ❌ **If failed:** Shows error  
4️⃣ **JWT is stored in localStorage & used for API requests**  
5️⃣ **If JWT expires**, Redux Saga calls `/refresh` for a new token  
6️⃣ **Role-Based Access Control (RBAC)** restricts unauthorized users  

---

# **Final Thoughts**  
✅ **Google OAuth integration with JWT & Redux Saga**  
✅ **Auto-refresh JWT to prevent logout**  
✅ **Role-based route protection**  

🚀

# **Social Login with Google, Facebook & GitHub (Redux Saga + JWT)**  

This guide extends **Google OAuth authentication** to support **Facebook & GitHub login** using Redux Saga. We'll:  
✅ **Integrate Facebook & GitHub Login**  
✅ **Send tokens to backend for JWT authentication**  
✅ **Store JWT & refresh token**  
✅ **Auto-refresh JWT when expired**  

---

## **1. Install Dependencies**  
```bash
npm install @react-oauth/google react-facebook-login react-github-login axios redux react-redux redux-saga react-router-dom
```

---

## **2. Setup OAuth Providers**  
📌 **Modify `index.js`**  
```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import { GoogleOAuthProvider } from "@react-oauth/google";
import { BrowserRouter } from "react-router-dom";
import store from "./store";
import App from "./App";

const GOOGLE_CLIENT_ID = "YOUR_GOOGLE_CLIENT_ID";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <Provider store={store}>
    <GoogleOAuthProvider clientId={GOOGLE_CLIENT_ID}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </GoogleOAuthProvider>
  </Provider>
);
```

---

## **3. Modify Authentication Actions**  
📌 **Modify `actions.js`**  
```jsx
export const GOOGLE_LOGIN_REQUEST = "GOOGLE_LOGIN_REQUEST";
export const FACEBOOK_LOGIN_REQUEST = "FACEBOOK_LOGIN_REQUEST";
export const GITHUB_LOGIN_REQUEST = "GITHUB_LOGIN_REQUEST";
export const LOGIN_SUCCESS = "LOGIN_SUCCESS";
export const LOGIN_FAILURE = "LOGIN_FAILURE";
export const LOGOUT = "LOGOUT";
export const REFRESH_TOKEN = "REFRESH_TOKEN";

// Action Creators
export const googleLoginRequest = (googleToken) => ({ type: GOOGLE_LOGIN_REQUEST, payload: googleToken });
export const facebookLoginRequest = (fbToken) => ({ type: FACEBOOK_LOGIN_REQUEST, payload: fbToken });
export const githubLoginRequest = (githubCode) => ({ type: GITHUB_LOGIN_REQUEST, payload: githubCode });
export const loginSuccess = (user) => ({ type: LOGIN_SUCCESS, payload: user });
export const loginFailure = (error) => ({ type: LOGIN_FAILURE, payload: error });
export const logout = () => ({ type: LOGOUT });
export const refreshToken = () => ({ type: REFRESH_TOKEN });
```

---

## **4. Modify Authentication Reducer**  
📌 **Modify `authReducer.js`**  
```jsx
import { LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT } from "./actions";

const initialState = {
  user: JSON.parse(localStorage.getItem("user")) || null,
  error: null,
};

function authReducer(state = initialState, action) {
  switch (action.type) {
    case LOGIN_SUCCESS:
      return { user: action.payload, error: null };
    case LOGIN_FAILURE:
      return { user: null, error: action.payload };
    case LOGOUT:
      return { user: null, error: null };
    default:
      return state;
  }
}

export default authReducer;
```

---

## **5. Setup Social Login in Redux Saga**  
📌 **Modify `sagas.js`**  
```jsx
import { takeLatest, call, put } from "redux-saga/effects";
import axios from "axios";
import { GOOGLE_LOGIN_REQUEST, FACEBOOK_LOGIN_REQUEST, GITHUB_LOGIN_REQUEST, LOGIN_SUCCESS, LOGIN_FAILURE, LOGOUT, REFRESH_TOKEN } from "./actions";

// Axios instance with interceptor for JWT refresh
const api = axios.create({
  baseURL: "https://your-api.com",
});

api.interceptors.request.use((config) => {
  const user = JSON.parse(localStorage.getItem("user"));
  if (user?.accessToken) {
    config.headers.Authorization = `Bearer ${user.accessToken}`;
  }
  return config;
});

// API Requests
const googleLoginApi = async (googleToken) => {
  const response = await api.post("/auth/google", { token: googleToken });
  return response.data;
};

const facebookLoginApi = async (fbToken) => {
  const response = await api.post("/auth/facebook", { token: fbToken });
  return response.data;
};

const githubLoginApi = async (githubCode) => {
  const response = await api.post("/auth/github", { code: githubCode });
  return response.data;
};

const refreshApi = async () => {
  const user = JSON.parse(localStorage.getItem("user"));
  const response = await api.post("/refresh", { refreshToken: user.refreshToken });
  return response.data;
};

// Worker Sagas
function* googleLoginSaga(action) {
  try {
    const data = yield call(googleLoginApi, action.payload);
    localStorage.setItem("user", JSON.stringify(data));
    yield put({ type: LOGIN_SUCCESS, payload: data });
  } catch (error) {
    yield put({ type: LOGIN_FAILURE, payload: "Google login failed" });
  }
}

function* facebookLoginSaga(action) {
  try {
    const data = yield call(facebookLoginApi, action.payload);
    localStorage.setItem("user", JSON.stringify(data));
    yield put({ type: LOGIN_SUCCESS, payload: data });
  } catch (error) {
    yield put({ type: LOGIN_FAILURE, payload: "Facebook login failed" });
  }
}

function* githubLoginSaga(action) {
  try {
    const data = yield call(githubLoginApi, action.payload);
    localStorage.setItem("user", JSON.stringify(data));
    yield put({ type: LOGIN_SUCCESS, payload: data });
  } catch (error) {
    yield put({ type: LOGIN_FAILURE, payload: "GitHub login failed" });
  }
}

function* refreshSaga() {
  try {
    const newData = yield call(refreshApi);
    localStorage.setItem("user", JSON.stringify(newData));
    yield put({ type: LOGIN_SUCCESS, payload: newData });
  } catch (error) {
    yield put({ type: LOGOUT });
  }
}

function* logoutSaga() {
  localStorage.removeItem("user");
}

// Watcher Saga
function* rootSaga() {
  yield takeLatest(GOOGLE_LOGIN_REQUEST, googleLoginSaga);
  yield takeLatest(FACEBOOK_LOGIN_REQUEST, facebookLoginSaga);
  yield takeLatest(GITHUB_LOGIN_REQUEST, githubLoginSaga);
  yield takeLatest(REFRESH_TOKEN, refreshSaga);
  yield takeLatest(LOGOUT, logoutSaga);
}

export default rootSaga;
```

---

## **6. Add Google, Facebook & GitHub Login Buttons**  
📌 **Modify `Login.js`**  
```jsx
import { useDispatch, useSelector } from "react-redux";
import { googleLoginRequest, facebookLoginRequest, githubLoginRequest, logout } from "./actions";
import { GoogleLogin } from "@react-oauth/google";
import FacebookLogin from "react-facebook-login";
import GitHubLogin from "react-github-login";

function Login() {
  const dispatch = useDispatch();
  const { user, error } = useSelector((state) => state);

  const handleGoogleSuccess = (response) => {
    dispatch(googleLoginRequest(response.credential));
  };

  const handleFacebookSuccess = (response) => {
    dispatch(facebookLoginRequest(response.accessToken));
  };

  const handleGitHubSuccess = (response) => {
    dispatch(githubLoginRequest(response.code));
  };

  return (
    <div>
      {user ? (
        <>
          <h2>Welcome, {user.name}!</h2>
          <button onClick={() => dispatch(logout())}>Logout</button>
        </>
      ) : (
        <>
          <GoogleLogin onSuccess={handleGoogleSuccess} onError={() => console.log("Google login failed")} />
          <FacebookLogin
            appId="YOUR_FACEBOOK_APP_ID"
            fields="name,email,picture"
            callback={handleFacebookSuccess}
          />
          <GitHubLogin
            clientId="YOUR_GITHUB_CLIENT_ID"
            onSuccess={handleGitHubSuccess}
            onFailure={() => console.log("GitHub login failed")}
          />
          {error && <p style={{ color: "red" }}>{error}</p>}
        </>
      )}
    </div>
  );
}

export default Login;
```

---

# **Final Thoughts**  
✅ **Google, Facebook & GitHub login using Redux Saga & JWT**  
✅ **Auto-refresh JWT to prevent forced logouts**  
✅ **Seamless social authentication**  

🚀

I'll add **Multi-Factor Authentication (MFA)** to your social login system.  
We'll implement:  
✅ **Time-based One-Time Password (TOTP)** (Google Authenticator)  
✅ **Email/SMS-based OTP**  
✅ **Secure Token Verification**  

Give me a moment to set this up. 🚀

# **Multi-Factor Authentication (MFA) with Redux Saga & JWT**  

This guide adds **MFA (Multi-Factor Authentication)** to your social login system:  
✅ **Google Authenticator (TOTP-based MFA)**  
✅ **Email/SMS OTP Verification**  
✅ **Secure token handling with Redux Saga**  

---

## **1. Install Dependencies**  
```bash
npm install speakeasy qrcode axios redux react-redux redux-saga react-router-dom
```

---

## **2. Backend API (Node.js Example)**  
Your backend needs to support MFA. Here’s an example:

📌 **Generate MFA Secret (for Google Authenticator)**
```javascript
const speakeasy = require("speakeasy");

app.post("/mfa/setup", (req, res) => {
  const secret = speakeasy.generateSecret({ length: 20 });
  res.json({ secret: secret.base32 });
});
```

📌 **Verify OTP Code**
```javascript
app.post("/mfa/verify", (req, res) => {
  const { token, secret } = req.body;
  const verified = speakeasy.totp.verify({
    secret,
    encoding: "base32",
    token,
  });

  if (verified) {
    res.json({ success: true });
  } else {
    res.status(401).json({ success: false, message: "Invalid OTP" });
  }
});
```

---

## **3. Update Redux Actions for MFA**  
📌 **Modify `actions.js`**
```jsx
export const MFA_SETUP_REQUEST = "MFA_SETUP_REQUEST";
export const MFA_SETUP_SUCCESS = "MFA_SETUP_SUCCESS";
export const MFA_VERIFY_REQUEST = "MFA_VERIFY_REQUEST";
export const MFA_VERIFY_SUCCESS = "MFA_VERIFY_SUCCESS";
export const MFA_FAILURE = "MFA_FAILURE";

// Action Creators
export const mfaSetupRequest = () => ({ type: MFA_SETUP_REQUEST });
export const mfaSetupSuccess = (secret) => ({ type: MFA_SETUP_SUCCESS, payload: secret });
export const mfaVerifyRequest = (token, secret) => ({ type: MFA_VERIFY_REQUEST, payload: { token, secret } });
export const mfaVerifySuccess = () => ({ type: MFA_VERIFY_SUCCESS });
export const mfaFailure = (error) => ({ type: MFA_FAILURE, payload: error });
```

---

## **4. Modify Authentication Reducer**  
📌 **Modify `authReducer.js`**
```jsx
import { MFA_SETUP_SUCCESS, MFA_VERIFY_SUCCESS, MFA_FAILURE } from "./actions";

const initialState = {
  user: JSON.parse(localStorage.getItem("user")) || null,
  mfaSecret: null,
  mfaVerified: false,
  error: null,
};

function authReducer(state = initialState, action) {
  switch (action.type) {
    case MFA_SETUP_SUCCESS:
      return { ...state, mfaSecret: action.payload, error: null };
    case MFA_VERIFY_SUCCESS:
      return { ...state, mfaVerified: true, error: null };
    case MFA_FAILURE:
      return { ...state, error: action.payload };
    default:
      return state;
  }
}

export default authReducer;
```

---

## **5. Handle MFA in Redux Saga**  
📌 **Modify `sagas.js`**
```jsx
import { takeLatest, call, put } from "redux-saga/effects";
import axios from "axios";
import { MFA_SETUP_REQUEST, MFA_SETUP_SUCCESS, MFA_VERIFY_REQUEST, MFA_VERIFY_SUCCESS, MFA_FAILURE } from "./actions";

const api = axios.create({ baseURL: "https://your-api.com" });

const setupMfaApi = async () => {
  const response = await api.post("/mfa/setup");
  return response.data;
};

const verifyMfaApi = async (token, secret) => {
  const response = await api.post("/mfa/verify", { token, secret });
  return response.data;
};

// Worker Sagas
function* setupMfaSaga() {
  try {
    const data = yield call(setupMfaApi);
    yield put({ type: MFA_SETUP_SUCCESS, payload: data.secret });
  } catch (error) {
    yield put({ type: MFA_FAILURE, payload: "MFA setup failed" });
  }
}

function* verifyMfaSaga(action) {
  try {
    const { token, secret } = action.payload;
    yield call(verifyMfaApi, token, secret);
    yield put({ type: MFA_VERIFY_SUCCESS });
  } catch (error) {
    yield put({ type: MFA_FAILURE, payload: "Invalid OTP" });
  }
}

// Watcher Saga
function* rootSaga() {
  yield takeLatest(MFA_SETUP_REQUEST, setupMfaSaga);
  yield takeLatest(MFA_VERIFY_REQUEST, verifyMfaSaga);
}

export default rootSaga;
```

---

## **6. Add MFA to Login Flow**  
📌 **Modify `MfaSetup.js`**
```jsx
import { useDispatch, useSelector } from "react-redux";
import { mfaSetupRequest, mfaVerifyRequest } from "./actions";
import { useState } from "react";
import QRCode from "qrcode.react";

function MfaSetup() {
  const dispatch = useDispatch();
  const { mfaSecret, error } = useSelector((state) => state);
  const [token, setToken] = useState("");

  const handleSetup = () => dispatch(mfaSetupRequest());

  const handleVerify = () => dispatch(mfaVerifyRequest(token, mfaSecret));

  return (
    <div>
      <h2>Setup MFA</h2>
      <button onClick={handleSetup}>Generate MFA Secret</button>
      {mfaSecret && (
        <>
          <p>Scan this QR code in Google Authenticator:</p>
          <QRCode value={mfaSecret} />
          <input type="text" value={token} onChange={(e) => setToken(e.target.value)} placeholder="Enter OTP" />
          <button onClick={handleVerify}>Verify OTP</button>
        </>
      )}
      {error && <p style={{ color: "red" }}>{error}</p>}
    </div>
  );
}

export default MfaSetup;
```

---

## **7. Add MFA Check in Login Component**  
📌 **Modify `Login.js`**
```jsx
import { useDispatch, useSelector } from "react-redux";
import { googleLoginRequest, facebookLoginRequest, githubLoginRequest, logout } from "./actions";
import { useNavigate } from "react-router-dom";
import MfaSetup from "./MfaSetup";
import { useEffect } from "react";

function Login() {
  const dispatch = useDispatch();
  const navigate = useNavigate();
  const { user, mfaVerified } = useSelector((state) => state);

  useEffect(() => {
    if (user && !mfaVerified) navigate("/mfa-setup");
  }, [user, mfaVerified, navigate]);

  return (
    <div>
      {user ? (
        <>
          <h2>Welcome, {user.name}!</h2>
          <button onClick={() => dispatch(logout())}>Logout</button>
        </>
      ) : (
        <>
          <button onClick={() => dispatch(googleLoginRequest())}>Login with Google</button>
          <button onClick={() => dispatch(facebookLoginRequest())}>Login with Facebook</button>
          <button onClick={() => dispatch(githubLoginRequest())}>Login with GitHub</button>
        </>
      )}
    </div>
  );
}

export default Login;
```

---

## **8. Add MFA Setup Route**  
📌 **Modify `App.js`**
```jsx
import { Routes, Route } from "react-router-dom";
import Login from "./Login";
import MfaSetup from "./MfaSetup";

function App() {
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/mfa-setup" element={<MfaSetup />} />
    </Routes>
  );
}

export default App;
```

---

# **Final Thoughts**  
✅ **Google Authenticator (TOTP) for extra security**  
✅ **Backend API integration for MFA setup & verification**  
✅ **Redux Saga handles async authentication flow**  
✅ **Users must verify OTP before accessing their account**  

🚀

Let’s integrate **Biometric Authentication (Face ID / Fingerprint)** into your React app using **WebAuthn** (the modern web standard for secure biometric login). Here's what we’ll do:

✅ Use **WebAuthn API** (via browser) for biometric auth  
✅ Register user biometrics securely  
✅ Authenticate with biometric prompt  
✅ Integrate with JWT + Redux Saga  

---

## 🔐 **1. What You Need**
To use WebAuthn:
- Your site must be **served over HTTPS**
- Browser must support **WebAuthn** (Chrome, Safari, Firefox ✅)
- Backend needs to support **FIDO2** credential creation & verification  
  (Node.js libs: `@simplewebauthn/server`, `fido2-lib`)

---

## 🧠 **2. Flow Summary**
### Registration:
1. User logs in (via social login)
2. You ask for biometric registration
3. Browser shows biometric prompt
4. Credential is securely saved (not a password)

### Login:
1. User triggers biometric login
2. Browser verifies Face ID / Fingerprint
3. Sends signed assertion to backend
4. Backend verifies → issue JWT

---

## ⚙️ **3. Frontend Setup (React)**  
We’ll use `navigator.credentials.create()` and `navigator.credentials.get()` for WebAuthn.

### 📦 No extra npm packages needed!

---

## 📁 **4. WebAuthn Registration Component**
```jsx
// BiometricRegister.js
import axios from "axios";

function BiometricRegister() {
  const handleRegister = async () => {
    const { data: options } = await axios.post("/webauthn/register-request");

    const credential = await navigator.credentials.create({
      publicKey: options,
    });

    const credentialData = {
      id: credential.id,
      rawId: btoa(String.fromCharCode(...new Uint8Array(credential.rawId))),
      response: {
        attestationObject: btoa(
          String.fromCharCode(...new Uint8Array(credential.response.attestationObject))
        ),
        clientDataJSON: btoa(
          String.fromCharCode(...new Uint8Array(credential.response.clientDataJSON))
        ),
      },
      type: credential.type,
    };

    await axios.post("/webauthn/register-response", credentialData);
    alert("Biometric registered!");
  };

  return <button onClick={handleRegister}>Register Biometric</button>;
}

export default BiometricRegister;
```

---

## 🔐 **5. Biometric Login Component**
```jsx
// BiometricLogin.js
import axios from "axios";
import { useDispatch } from "react-redux";
import { loginSuccess } from "./actions";

function BiometricLogin() {
  const dispatch = useDispatch();

  const handleLogin = async () => {
    const { data: options } = await axios.post("/webauthn/login-request");

    const assertion = await navigator.credentials.get({ publicKey: options });

    const loginData = {
      id: assertion.id,
      rawId: btoa(String.fromCharCode(...new Uint8Array(assertion.rawId))),
      response: {
        authenticatorData: btoa(
          String.fromCharCode(...new Uint8Array(assertion.response.authenticatorData))
        ),
        clientDataJSON: btoa(
          String.fromCharCode(...new Uint8Array(assertion.response.clientDataJSON))
        ),
        signature: btoa(
          String.fromCharCode(...new Uint8Array(assertion.response.signature))
        ),
      },
      type: assertion.type,
    };

    const res = await axios.post("/webauthn/login-response", loginData);
    localStorage.setItem("user", JSON.stringify(res.data));
    dispatch(loginSuccess(res.data));
    alert("Biometric login successful!");
  };

  return <button onClick={handleLogin}>Login with Face ID / Fingerprint</button>;
}

export default BiometricLogin;
```

---

## 🧠 **6. Backend Routes (Outline Only)**
Example (Node.js + `@simplewebauthn/server`):
```js
app.post("/webauthn/register-request", (req, res) => {
  // Generate registration options
});

app.post("/webauthn/register-response", (req, res) => {
  // Verify credential & save it
});

app.post("/webauthn/login-request", (req, res) => {
  // Return options for credential assertion
});

app.post("/webauthn/login-response", (req, res) => {
  // Verify assertion and issue JWT
});
```

---

## 🔐 Pro Tips
- Store user’s biometric publicKey on backend
- Use HTTPS only for WebAuthn
- Combine with JWT for secure sessions
- Use fallback (MFA) if biometric fails

---

