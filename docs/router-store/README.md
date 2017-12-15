# @ngrx/router-store

Bindings to connect the Angular Router with @ngrx/store

用 `@ngrx/store` 绑定 ng 的 路由

### Installation

### 安装

Install @ngrx/router-store from npm:

`npm install @ngrx/router-store --save` OR `yarn add @ngrx/router-store`


从 npm 安装 `Install @ngrx/router-store` :
`npm install @ngrx/router-store --save` 或 `yarn add @ngrx/router-store`

### Nightly builds
### 每日构建版

`npm install github:ngrx/router-store-builds` OR `yarn add github:ngrx/router-store-builds`

`npm install github:ngrx/router-store-builds` 或 `yarn add github:ngrx/router-store-builds`

## Usage
## 用法

During the navigation, before any guards or resolvers run, the router will dispatch a `ROUTER_NAVIGATION` action, which has the signature `RouterNavigationAction`:

在路由导航跳转期间， 在任何的路由守卫和路由解析器执行之前，路由会触发一个有 `RouterNavigationAction` 签名的 `ROUTER_NAVIGATION` action：

```ts
/**
 * Payload of ROUTER_NAVIGATION.
 * ROUTER_NAVIGATION 的有效载荷 （携带的信息）
 */
export declare type RouterNavigationPayload<T> = {
    routerState: T;
    event: RoutesRecognized;
};
/**
 * An action dispatched when the router navigates.
 * 路由导航开始时，action 将被派发出去 
 */
export declare type RouterNavigationAction<T = RouterStateSnapshot> = {
    type: typeof ROUTER_NAVIGATION;
    payload: RouterNavigationPayload<T>;
};
```

- Reducers receive this action. Throwing an error in the reducer cancels navigation.
- `Reducers` 会收到这个 `action`。 如果取消导航的话会抛出一个错误。

- Effects can listen for this action.
- `Effects` 能监听这个 `action`。

- The `ROUTER_CANCEL` action represents a guard canceling navigation.
- `ROUTER_CANCEL` action 表示路由守卫取消了当前的导航。

- A `ROUTER_ERROR` action represents a navigation error .
- `ROUTER_ERROR` action 表示一个导航错误。

- `ROUTER_CANCEL` and `ROUTER_ERROR` contain the store state before the navigation. Use the previous state to restore the consistency of the store.
- `ROUTER_CANCEL` 和 `ROUTER_ERROR` 包含了开始导航前的 `store` 状态。 使用上一个状态来恢复 `store` 的前后一致性。

## Setup
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
## API Documentation
## API 文档
- [Navigation actions](./api.md#navigation-actions)
- [Effects](./api.md#effects)
- [Custom Router State Serializer](./api.md#custom-router-state-serializer)
