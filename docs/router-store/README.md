# @ngrx/router-store

用 `@ngrx/store` 绑定 ng 的 路由

### 安装
从 npm 安装 `Install @ngrx/router-store` :
`npm install @ngrx/router-store --save` 或 `yarn add @ngrx/router-store`

### 每日构建版
`npm install github:ngrx/router-store-builds` 或 `yarn add github:ngrx/router-store-builds`

## 用法
在路由导航跳转期间， 在任何的路由守卫和路由解析器执行之前，路由会触发一个有 `RouterNavigationAction` 签名的 `ROUTER_NAVIGATION` action：

```ts
/**
 * ROUTER_NAVIGATION 的有效载荷 （携带的信息）
 */
export declare type RouterNavigationPayload<T> = {
    routerState: T;
    event: RoutesRecognized;
};
/**
 * 路由导航开始时，action 将被派发出去 
 */
export declare type RouterNavigationAction<T = RouterStateSnapshot> = {
    type: typeof ROUTER_NAVIGATION;
    payload: RouterNavigationPayload<T>;
};
```

- `Reducers` 会收到这个 `action`。 如果取消导航的话会抛出一个错误。
- `Effects` 能监听这个 `action`。
- `ROUTER_CANCEL` action 表示路由守卫取消了当前的导航。
- `ROUTER_ERROR` action 表示一个导航错误。
- `ROUTER_CANCEL` 和 `ROUTER_ERROR` 包含了开始导航前的 `store` 状态。 使用上一个状态来恢复 `store` 的前后一致性。

## 步骤

```ts
import { StoreRouterConnectingModule, routerReducer } from '@ngrx/router-store';
import { App } from './app.component';

@NgModule({
  imports: [
    BrowserModule,
    StoreModule.forRoot({ routerReducer: routerReducer }),
    RouterModule.forRoot([
      // routes
    ]),
    StoreRouterConnectingModule
  ],
  bootstrap: [App]
})
export class AppModule { }
```
## API 文档
- [Navigation actions](./api.md#navigation-actions)
- [Effects](./api.md#effects)
- [定制你的 state 序列化部分](./api.md#custom-router-state-serializer)
