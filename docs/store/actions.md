# Actions

## Action reducers

通过给你的 reducer 映射提供 `ActionReducerMap<T>` 来添加类型检查。

```ts
import { ActionReducerMap } from '@ngrx/store';
import * as fromAuth from './auth';

export interface State {
  auth: fromAuth.State;
}

export const reducers: ActionReducerMap<State> = {
  auth: fromAuth.reducer
};
```

## 类型化的 Actions

使用强类型的 `actions` 来发挥 `TypeScript` 的编译时检查的优势。

```ts
// counter.actions.ts
import { Action } from '@ngrx/store';

export enum CounterActionTypes {
  INCREMENT = '[Counter] Increment',
  DECREMENT = '[Counter] Decrement',
  RESET = '[Counter] Reset'
}

export class Increment implements Action {
  readonly type = CounterActionTypes.INCREMENT;
}

export class Decrement implements Action {
  readonly type = CounterActionTypes.DECREMENT;
}

export class Reset implements Action {
  readonly type = CounterActionTypes.RESET;

  constructor(public payload: number) {}
}

export type CounterActions
  = Increment
  | Decrement
  | Reset;
```

这个给你的 `reducer` 函数提供了类型化的 `actions`

```ts
// counter.reducer.ts
import { CounterActionTypes, CounterActions } from './counter.actions';

export function reducer(state: number = 0, action: CounterActions): State {
  switch(action.type) {
    case CounterActionTypes.INCREMENT: {
      return state + 1;
    }

    case CounterActionTypes.DECREMENT: {
      return state - 1;
    }

    case CounterActionTypes.RESET: {
      return action.payload; // number 类型
    }

    default: {
      return state;
    }
  }
}
```

实例化 `actions` 并使用 `store.dispatch()` 来 `dispatch` 它们。

```ts
import { Store, select } from '@ngrx/store';
import { Observable } from 'rxjs/Observable';
import * as Counter from './counter.actions';

interface AppState {
  counter: number;
}

@Component({
  selector: 'my-app',
  template: `
    <button (click)="increment()">Increment</button>
    <button (click)="decrement()">Decrement</button>
    <button (click)="reset()">Reset Counter</button>
    
    <div>Current Count: {{ counter | async }}</div>
  `
})
export class MyAppComponent {
  counter: Observable<number>;

  constructor(private store: Store<AppState>) {
    this.counter = store.pipe(select('counter'));
  }

  increment(){
    this.store.dispatch(new Counter.Increment());
  }

  decrement(){
    this.store.dispatch(new Counter.Decrement());
  }

  reset(){
    this.store.dispatch(new Counter.Reset(3));
  }
}
```
