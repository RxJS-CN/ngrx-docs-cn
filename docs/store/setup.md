# @ngrx/store

### 准备步骤
为你的应用中的每种数据类型创建一个 `reducer` 函数，这些 `reducers` 组合起来之后就构成了你的应用的状态。

```ts
// counter.ts
import { Action } from '@ngrx/store';

export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';
export const RESET = 'RESET';

export function counter(state: number = 0, action: Action) {
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

为了在你的应用中注册全局的状态容器，导入那些 `reducers` 然后在 `AppModule` 中的 `imports` 数组中使用 `StoreModule.forRoot` 函数。

```ts
import { NgModule } from '@angular/core'
import { StoreModule } from '@ngrx/store';
import { counter } from './counter';

@NgModule({
  imports: [
    BrowserModule,
    StoreModule.forRoot({ counter: counter })
  ]
})
export class AppModule {}
```

你可以在你的组件或者服务中注入 `Store` 服务，使用 `store.select` 来挑选出状态的某一部分。

```ts
import { Store, select } from '@ngrx/store';
import { Observable } from 'rxjs/Observable';
import { INCREMENT, DECREMENT, RESET } from './counter';

interface AppState {
  counter: number;
}

@Component({
  selector: 'my-app',
  template: `
    <button (click)="increment()">Increment</button>
    <div>Current Count: {{ counter | async }}</div>
    <button (click)="decrement()">Decrement</button>

    <button (click)="reset()">Reset Counter</button>
  `
})
class MyAppComponent {
  counter: Observable<number>;

  constructor(private store: Store<AppState>){
    this.counter = store.pipe(select('counter'));
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
