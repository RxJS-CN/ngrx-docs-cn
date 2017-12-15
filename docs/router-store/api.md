# API
## Navigation actions

Navigation actions are not provided as part of the router package. You provide your own
custom navigation actions that use the `Router` within effects to navigate.

Navigation actions 并没有作为 `router` 包的一部分提供给你。 你应该使用 `Router` 和 `Effects` 自己实现定制化的 navigation actions 提供导航。

```ts
import { Action } from '@ngrx/store';
import { NavigationExtras } from '@angular/router';

export const GO = '[Router] Go';
export const BACK = '[Router] Back';
export const FORWARD = '[Router] Forward';

export class Go implements Action {
  readonly type = GO;

  constructor(public payload: {
    path: any[];
    query?: object;
    extras?: NavigationExtras;
  }) {}
}

export class Back implements Action {
  readonly type = BACK;
}

export class Forward implements Action {
  readonly type = FORWARD;
}

export type Actions
  = Go
  | Back
  | Forward;
```

```ts
import * as RouterActions from './actions/router';

store.dispatch(new RouterActions.Go({
  path: ['/path', { routeParam: 1 }],
  query: { page: 1 },
  extras: { replaceUrl: false }
});

store.dispatch(new RouterActions.Back());

store.dispatch(new RouterActions.Forward());
```
## Effects

```ts
import 'rxjs/add/operator/do';
import 'rxjs/add/operator/map';
import { Injectable } from '@angular/core';
import { Router } from '@angular/router';
import { Location } from '@angular/common';
import { Effect, Actions } from '@ngrx/effects';
import * as RouterActions from './actions/router';

@Injectable()
export class RouterEffects {
  @Effect({ dispatch: false })
  navigate$ = this.actions$.ofType(RouterActions.GO)
    .map((action: RouterActions.Go) => action.payload)
    .do(({ path, query: queryParams, extras})
      => this.router.navigate(path, { queryParams, ...extras }));

  @Effect({ dispatch: false })
  navigateBack$ = this.actions$.ofType(RouterActions.BACK)
    .do(() => this.location.back());

  @Effect({ dispatch: false })
  navigateForward$ = this.actions$.ofType(RouterActions.FORWARD)
    .do(() => this.location.forward());    

  constructor(
    private actions$: Actions,
    private router: Router,
    private location: Location
  ) {}
}
```
## Custom Router State Serializer
## 订制你的 Router State 序列化方式

During each navigation cycle, a `RouterNavigationAction` is dispatched with a snapshot of the state in its payload, the `RouterStateSnapshot`. The `RouterStateSnapshot` is a large complex structure, containing many pieces of information about the current state and what's rendered by the router. This can cause performance
issues when used with the Store Devtools. In most cases, you may only need a piece of information from the `RouterStateSnapshot`. In order to pare down the `RouterStateSnapshot` provided during navigation, you provide a custom serializer for the snapshot to only return what you need to be added to the payload and store.

在每个导航的生命周期中，  `RouterStateSnapshot` 是由提供了一个有效载荷 ( payload ) 的 `state` 快照分发的`RouterNavigationAction`。 `RouterStateSnapshot` 是一个又大又复杂的结构，包含了当前状态的很多信息和路由都渲染了哪些内容。 当使用了 Store Devtools 的话可能会引起性能问题。 大多数的使用场景下，你可能仅需要的是 `RouterStateSnapshot` 提供的部分内容。那么想减少 `RouterStateSnapshot` 提供的信息或者只提供你想要的信息，你需要自己完成序列化的程序部分。

Additionally, the router state snapshot is a mutable object, which can cause issues when developing with [store freeze](https://github.com/brandonroberts/ngrx-store-freeze) to prevent direct state mutations. This can be avoided by using a custom serializer.

另外，路由的状态快照是一个可修改对象，这可能会造成一些问题，你可以使用 [store freeze](https://github.com/brandonroberts/ngrx-store-freeze) 来阻止状态的直接变化。

To use the time-traveling debugging in the Devtools, you must return an object containing the `url` when using the `routerReducer`.

想要在开发工具中使用时光穿梭功能的话，你必须在使用 `routerReducer` 时返回一个包含 `url` 的对象。

```ts
import { StoreModule, ActionReducerMap } from '@ngrx/store';
import { Params, RouterStateSnapshot } from '@angular/router';
import {
  StoreRouterConnectingModule,
  routerReducer,
  RouterReducerState,
  RouterStateSerializer
} from '@ngrx/router-store';

export interface RouterStateUrl {
  url: string;
  params: Params;
  queryParams: Params;
}

export interface State {
  routerReducer: RouterReducerState<RouterStateUrl>;
}

export class CustomSerializer implements RouterStateSerializer<RouterStateUrl> {
  serialize(routerState: RouterStateSnapshot): RouterStateUrl {
    let route = routerState.root;
    while (route.firstChild) {
      route = route.firstChild;
    }

    const { url } = routerState;
    const queryParams = routerState.root.queryParams;
    const params = route.params;

    // Only return an object including the URL, params and query params
    // instead of the entire snapshot
    // 只返回 包含 URL 、 `params` 和 `query params` 而不是整个路由快照
    return { url, params, queryParams };
  }
}

export const reducers: ActionReducerMap<State> = {
  routerReducer: routerReducer
};

@NgModule({
  imports: [
    StoreModule.forRoot(reducers),
    RouterModule.forRoot([
      // routes
    ]),
    StoreRouterConnectingModule
  ],
  providers: [
    { provide: RouterStateSerializer, useClass: CustomSerializer }
  ]
})
export class AppModule { }
```
