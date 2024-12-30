
---

# TypeScript React 路由管理模板

## 1. 路由配置文件 (`routes.tsx`)

```typescript
import React from 'react';
import { RouteProps } from 'react-router-dom';
import Home from './Home';
import Users from './Users';
import UserDetail from './UserDetail';

interface RouteConfig extends RouteProps {
  path: string;
  component: React.ComponentType<any>;
  routes?: RouteConfig[];
}

const routes: RouteConfig[] = [
  {
    path: '/',
    exact: true,
    component: Home,
  },
  {
    path: '/users',
    component: Users,
    routes: [
      {
        path: '/users/:id',
        component: UserDetail,
      },
    ],
  },
];

export default routes;
```

---

## 2. 递归渲染嵌套路由的组件 (`RouteWithSubRoutes.tsx`)

```typescript
import React from 'react';
import { Route, Switch, RouteProps } from 'react-router-dom';

interface RouteWithSubRoutesProps extends RouteProps {
  component: React.ComponentType<any>;
  routes?: RouteWithSubRoutesProps[];
}

export const RouteWithSubRoutes: React.FC<RouteWithSubRoutesProps> = (route) => {
  return (
    <Route
      path={route.path}
      exact={route.exact}
      render={(props) => (
        <route.component {...props} routes={route.routes} />
      )}
    />
  );
};
```

---

## 3. 主应用组件 (`App.tsx`)

```typescript
import React from 'react';
import { BrowserRouter as Router, Switch } from 'react-router-dom';
import routes from './routes';
import { RouteWithSubRoutes } from './RouteWithSubRoutes';

const App: React.FC = () => {
  return (
    <Router>
      <Switch>
        {routes.map((route, index) => (
          <RouteWithSubRoutes key={index} {...route} />
        ))}
      </Switch>
    </Router>
  );
};

export default App;
```

---

## 4. 嵌套路由组件示例 (`Users.tsx`)

```typescript
import React from 'react';
import { RouteWithSubRoutes } from './RouteWithSubRoutes';
import { RouteConfig } from './routes';

interface UsersProps {
  routes?: RouteConfig[];
}

const Users: React.FC<UsersProps> = ({ routes }) => {
  return (
    <div>
      <h2>Users</h2>
      <Switch>
        {routes?.map((route, index) => (
          <RouteWithSubRoutes key={index} {...route} />
        ))}
      </Switch>
    </div>
  );
};

export default Users;
```

---

## 5. 其他页面组件

### `Home.tsx`

```typescript
import React from 'react';

const Home: React.FC = () => {
  return <h1>Home</h1>;
};

export default Home;
```

### `UserDetail.tsx`

```typescript
import React from 'react';
import { useParams } from 'react-router-dom';

const UserDetail: React.FC = () => {
  const { id } = useParams<{ id: string }>();
  return <h1>User Detail: {id}</h1>;
};

export default UserDetail;
```

---

## 6. 总结

- **一级路由**：`/` 渲染 `Home` 组件。
- **二级嵌套路由**：`/users` 渲染 `Users` 组件，`/users/:id` 渲染 `UserDetail` 组件。
- **递归渲染**：通过 `RouteWithSubRoutes` 组件实现嵌套路由的递归渲染。