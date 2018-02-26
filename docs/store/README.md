# @ngrx/store

 RxJS 驱动的 Angular 应用状态管理工具，灵感来源于 `Redux`。

`@ngrx/store` 是一个包含受控状态的容器，设计的目的在于帮助我们在 `Angular` 之上写出高性能、稳定的应用。核心原理：
- `State` 是一个单一不可变的数据结构
- `Actions` 用于描述状态（state）的改变
- 被称为 `reducers` 的纯函数，通过获取之前的状态（state），和下一次的 action，来计算出新的状态（state）。
- 状态可以通过 `Store` 来进行访问，`Store` 包含一个针对 `state` 的可观察对象（observable）和一个对 `actions` 的观察者（observer）

通过这些核心原理，你可以在构建组件时使用 `OnPush` 变化检测(change detection)策略，让[智能且高效的变化检测（intelligent,performant change detection）](http://blog.thoughtram.io/angular/2016/02/22/angular-2-change-detection-explained.html#smarter-change-detection)贯穿在你的应用中。

### 安装
通过 `npm` 安装 `@ngrx/store`

`npm install @ngrx/store --save` 或者 `yarn add @ngrx/store`

### 每日构建版本

`npm install github:ngrx/store-builds` 或者 `yarn add github:ngrx/store-builds`

### 准备步骤
为你应用中的每种数据类型创建一个 `reducer` 函数，这些组合起来的 `reducers` 将会构成你的应用的状态。

```ts
// counter.ts
import { Action } from '@ngrx/store';

export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';
export const RESET = 'RESET';

export function counterReducer(state: number = 0, action: Action) {
	switch (action.type) {
		case INCREMENT:
			return state + 1;

		case DECREMENT:
			return state - 1;

		case RESET:
			return 0;

		default:
			return state;
	}
}
```

为了将这个状态容器注册到你的应用中，你需要在 `AppModule` 中导入这个 `reducers`，然后在 `@NgModule` 装饰器的 `imports` 数组中使用 `StoreModule.forRoot` 函数。

```ts
import { NgModule } from '@angular/core'
import { StoreModule } from '@ngrx/store';
import { counterReducer } from './counter';

@NgModule({
  imports: [
    BrowserModule,
    StoreModule.forRoot({ count: counterReducer })
  ]
})
export class AppModule {}
```

接下来你可以在你的组件或者服务中，注入 `Store` 服务，并通过 `select` 操作符来挑选出状态的某一部分。

```ts
import { Store, select } from '@ngrx/store';
import { Observable } from 'rxjs/Observable';
import { INCREMENT, DECREMENT, RESET } from './counter';

interface AppState {
  count: number;
}

@Component({
  selector: 'my-app',
  template: `
    <button (click)="increment()">Increment</button>
    <div>Current Count: {{ count$ | async }}</div>
    <button (click)="decrement()">Decrement</button>

    <button (click)="reset()">Reset Counter</button>
  `
})
export class MyAppComponent {
  count$: Observable<number>;

  constructor(private store: Store<AppState>) {
    this.count$ = store.pipe(select('count'));
  }

  increment(){
    this.store.dispatch({ type: INCREMENT });
  }

  decrement(){
    this.store.dispatch({ type: DECREMENT });
  }

  reset(){
    this.store.dispatch({ type: RESET });
  }
}
```

## API 文档
- [Action Reducers](./actions.md#action-reducers)
- [注入 reducers](./api.md#injecting-reducers)
- [Meta-Reducers/增强器](./api.md#meta-reducers)
- [注入 Meta-Reducers](./api.md#injecting-meta-reducers)
- [初始状态](./api.md#initial-state)
- [通过功能模块（feature module）合成 State](./api.md#feature-module-state-composition)
- [状态选择器](./selectors.md)
- [测试](./testing.md)
- [带类型的 Actions](./actions.md#typed-actions)


### 补充资料
- [通过 ngrx 从非响应式到响应式](https://www.youtube.com/watch?v=cyaAhXHhxgk)
- [使用 ngrx 的响应式 Angular2 （视频）](https://youtu.be/mhA7zZ23Odw)
- [@ngrx/store 的全面介绍](https://gist.github.com/btroncone/a6e4347326749f938510)
- [十分钟了解 @ngrx/store （视频）](https://egghead.io/lessons/angular-2-ngrx-store-in-10-minutes)
- [使用 Angular，Rxjs 和 @ngrx/store 来构建 Redux 风格的应用 （视频）](https://egghead.io/courses/building-a-time-machine-with-angular-2-and-rxjs)
