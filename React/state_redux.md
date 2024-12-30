
---

# TypeScript React Redux 模板

## 1. 定义 Action (`src/store/actions/counterActions.ts`)

```typescript
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';

export interface IncrementAction {
  type: typeof INCREMENT;
}

export interface DecrementAction {
  type: typeof DECREMENT;
}

export type CounterActionTypes = IncrementAction | DecrementAction;

export const increment = (): IncrementAction => ({
  type: INCREMENT,
});

export const decrement = (): DecrementAction => ({
  type: DECREMENT,
});
```

---

## 2. 定义 Reducer (`src/store/reducers/counterReducer.ts`)

```typescript
import { CounterActionTypes, INCREMENT, DECREMENT } from '../actions/counterActions';

interface CounterState {
  count: number;
}

const initialState: CounterState = {
  count: 0,
};

const counterReducer = (state = initialState, action: CounterActionTypes): CounterState => {
  switch (action.type) {
    case INCREMENT:
      return {
        ...state,
        count: state.count + 1,
      };
    case DECREMENT:
      return {
        ...state,
        count: state.count - 1,
      };
    default:
      return state;
  }
};

export default counterReducer;
```

---

## 3. 配置 Store (`src/store/store.ts`)

```typescript
import { createStore } from 'redux';
import counterReducer from './reducers/counterReducer';

const store = createStore(counterReducer);

export default store;
```

---

## 4. 主应用入口 (`src/App.tsx`)

```typescript
import React from 'react';
import { Provider } from 'react-redux';
import store from './store/store';
import Counter from './components/Counter';

const App: React.FC = () => (
  <Provider store={store}>
    <div>
      <h1>Redux Counter App</h1>
      <Counter />
    </div>
  </Provider>
);

export default App;
```

---

## 5. 计数器组件 (`src/components/Counter.tsx`)

```typescript
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from '../store/actions/counterActions';
import { CounterState } from '../store/reducers/counterReducer';

const Counter: React.FC = () => {
  const count = useSelector((state: CounterState) => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
};

export default Counter;
```

---

## 6. 运行应用

在项目根目录下运行以下命令启动应用：

```bash
npm start
```

---

## 7. 最佳实践总结

### 7.1 文件结构
- 将 Redux 相关代码（`actions`、`reducers`、`store`）放在单独的目录中，便于维护。

### 7.2 Action 和 Reducer
- 使用常量定义 action 类型，避免拼写错误。
- 在 reducer 中始终返回新的状态对象，而不是直接修改原状态。

### 7.3 Store 配置
- 使用 `createStore` 创建 store，并通过 `Provider` 将 store 注入到 React 应用中。

### 7.4 React 组件
- 使用 `useSelector` 获取状态，使用 `useDispatch` 分发 action。
- 将组件与 Redux 逻辑分离，保持组件的纯净性。

### 7.5 扩展性
- 如果需要处理异步操作，可以使用 `Redux Thunk` 或 `Redux Saga`。
- 如果需要管理多个 reducer，可以使用 `combineReducers`。

---

## 8. 完整代码

### `src/store/actions/counterActions.ts`
```typescript
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';

export interface IncrementAction {
  type: typeof INCREMENT;
}

export interface DecrementAction {
  type: typeof DECREMENT;
}

export type CounterActionTypes = IncrementAction | DecrementAction;

export const increment = (): IncrementAction => ({
  type: INCREMENT,
});

export const decrement = (): DecrementAction => ({
  type: DECREMENT,
});
```

### `src/store/reducers/counterReducer.ts`
```typescript
import { CounterActionTypes, INCREMENT, DECREMENT } from '../actions/counterActions';

interface CounterState {
  count: number;
}

const initialState: CounterState = {
  count: 0,
};

const counterReducer = (state = initialState, action: CounterActionTypes): CounterState => {
  switch (action.type) {
    case INCREMENT:
      return {
        ...state,
        count: state.count + 1,
      };
    case DECREMENT:
      return {
        ...state,
        count: state.count - 1,
      };
    default:
      return state;
  }
};

export default counterReducer;
```

### `src/store/store.ts`
```typescript
import { createStore } from 'redux';
import counterReducer from './reducers/counterReducer';

const store = createStore(counterReducer);

export default store;
```

### `src/App.tsx`
```typescript
import React from 'react';
import { Provider } from 'react-redux';
import store from './store/store';
import Counter from './components/Counter';

const App: React.FC = () => (
  <Provider store={store}>
    <div>
      <h1>Redux Counter App</h1>
      <Counter />
    </div>
  </Provider>
);

export default App;
```

### `src/components/Counter.tsx`
```typescript
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from '../store/actions/counterActions';
import { CounterState } from '../store/reducers/counterReducer';

const Counter: React.FC = () => {
  const count = useSelector((state: CounterState) => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
};

export default Counter;
```

---

## 9. 总结

通过以上模板，你可以快速搭建一个基于 TypeScript 的 React Redux 应用。Redux 的核心思想是将状态集中管理，并通过 action 和 reducer 来更新状态。结合 React-Redux，可以轻松地将 Redux 集成到 React 应用中。