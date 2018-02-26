# API

## 初始状态

在提供 `Store` 的时候配置初始状态。`initialState` 既可以是一个实际的状态对象，也可以是一个返回初始状态的函数。

```ts
import { StoreModule } from '@ngrx/store';
import { reducers } from './reducers';

@NgModule({
  imports: [
    StoreModule.forRoot(reducers, {
      initialState: {
        counter: 3
      }
    })
  ]
})
export class AppModule {}
```

### 初始状态和预编译（AOT）

Angular 的预编译（AoT）模式需要所有在其类型构造中引用的符号（比如 `@NgModule`，`@Component`, `@Injectable` 等等）都被静态定义。所以，当使用预编译（AOT）时我们不能在运行时动态的注入 `state`，除非我们提供的 `initialState` 是一个函数。因此，下面 `NgModule` 的定义将做一些微小的改动：

Angular 的预编译（AOT）模式需要所有装饰器元数据中引用的符号都能被静态分析。所以，当使用预编译（AOT）时我们不能在运行时动态的注入 `state`，除非我们提供的 `initialState` 是一个函数。因此，下面的 `NgModule` 的定义将做一些微小的改动：

```ts
// 假装这是在运行时动态注入的
const initialStateFromSomewhere = { counter: 3 };

// 静态的状态
const initialState = { counter: 2 };

// 在这个函数中将动态的状态切片，如果状态存在，将会在运行时覆盖掉静态的状态
export function getInitialState() {
  return { ...initialState, ...initialStateFromSomewhere };
}

@NgModule({
  imports: [
    StoreModule.forRoot(reducers, { initialState: getInitialState })
  ]
})
```

## Meta Reducers

@ngrx/store 会将一组 `reducers` 的映射变为一个单一的 `reducer`。使用 `metaReducers` 配置项来提供一组会按照从右到左的顺序进行处理的 `meta-reducers`

```ts
import { StoreModule, ActionReducer, MetaReducer } from '@ngrx/store';
import { reducers } from './reducers';

// 打印出所有的 actions
export function debug(reducer: ActionReducer<any>): ActionReducer<any> {
  return function(state, action) {
    console.log('state', state);
    console.log('action', action);

    return reducer(state, action);
  }
}

export const metaReducers: MetaReducer<any>[] = [debug];

@NgModule({
  imports: [
    StoreModule.forRoot(reducers, { metaReducers })
  ]
})
export class AppModule {}
```

## 功能模块状态（feature state）的组成

Store 使用分形的状态管理，通过功能模块（feature module）来提供状态的组成，支持预加载或者懒加载。通过 `StoreModule.forFeature` 方法来提供功能模块状态（feature state），这个方法定义了这个功能模块状态（feature state）的名字，以及用来生成这个功能模块状态（feature state）的 `reducers`，同时 `initalState` 和 `metaReducers` 的配置项依然可以使用。

```ts
// feature.module.ts
import { StoreModule, ActionReducerMap } from '@ngrx/store';

export const reducers: ActionReducerMap<any> = {
  subFeatureA: featureAReducer,
  subFeatureB: featureBReducer,
};

@NgModule({
  imports: [
    StoreModule.forFeature('featureName', reducers)
  ]
})
export class FeatureModule {}
```

功能模块状态（feature state）一旦加载完成后就会被加入到全局的应用状态中，然后就可以很方便的使用 [createFeatureSelector](./selectors.md#createFeatureSelector) 方法来选取它。

## 注入 Reducers

将 `根reducers` 注入到你的应用中，使用 `InjectionToken` 和 `Provider` 通过依赖注入来注册 `reducers`。

```ts
import { NgModule, InjectionToken } from '@angular/core';
import { StoreModule, ActionReducerMap } from '@ngrx/store';

import { SomeService } from './some.service';
import * as fromRoot from './reducers';

export const REDUCER_TOKEN = new InjectionToken<ActionReducerMap<fromRoot.State>>('Registered Reducers');

export function getReducers(someService: SomeService) {
  return someService.getReducers();
}

@NgModule({
  imports: [
    StoreModule.forRoot(REDUCER_TOKEN),
  ],
  providers: [
    {
      provide: REDUCER_TOKEN,
      deps: [SomeService],
      useFactory: getReducers
    }
  ]
})
export class AppModule { }
```

`Reducers` 也可以在通过功能模块（feature module）构造 `state` 时注入

```ts
import { NgModule, InjectionToken } from '@angular/core';
import { StoreModule, ActionReducerMap } from '@ngrx/store';

import * as fromFeature from './reducers';

export const FEATURE_REDUCER_TOKEN = new InjectionToken<ActionReducerMap<fromFeature.State>>('Feature Reducers');

export function getReducers(): ActionReducerMap<fromFeature.State> {
  // map of reducers
  return {

  };
}

@NgModule({
  imports: [
    StoreModule.forFeature('feature', FEATURE_REDUCER_TOKEN),
  ],
  providers: [
    {
      provide: FEATURE_REDUCER_TOKEN,
      useFactory: getReducers
    }
  ]
})
export class FeatureModule { }
```

## 注入 Meta-Reducers

注入 `meta reducers`，使用 `Store API` 中导出的 `META_REDUCERS` 注入令牌和 `Provider` 来通过依赖注入注册 `meta reducers`。

```ts
import { MetaReducer, META_REDUCERS } from '@ngrx/store';
import { SomeService } from './some.service';

export function getMetaReducers(some: SomeService): MetaReducer[] {
  // 返回包含 meta reducer 的数组
}

@NgModule({
  providers: [
    {
      provide: META_REDUCERS,
      deps: [SomeService],
      useFactory: getMetaReducers
    }
  ]
})
export class AppModule {}
```
