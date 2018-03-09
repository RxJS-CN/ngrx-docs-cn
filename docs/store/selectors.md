# 选择器（Selectors）

选择器是用来得到状态中某一部分切片的方法，`@ngrx/store` 提供了一些帮助函数（helper function）来优化选取。

当使用 `createSelector` 和 `createFeatureSelector` 函数时，`@ngrx/store` 会记录上一次选择器函数调用的参数，因为选择器是[纯函数](https://en.wikipedia.org/wiki/Pure_function)，所以当参数相同时，可以直接返回上一次的结果，而不需要重新调用你的选择器函数，这样可以在性能方面带来好处，尤其是当选择器函数需要执行大量的计算时，这种做法被称为[memoization](https://en.wikipedia.org/wiki/Memoization)。

## createSelector

`createSelector` 方法返回一个用来选取状态切片的回调函数。

### 示例

```ts
// reducers.ts
import { createSelector } from '@ngrx/store';

export interface FeatureState {
  counter: number;
}

export interface AppState {
  feature: FeatureState
}

export const selectFeature = (state: AppState) => state.feature;
export const selectFeatureCount = createSelector(selectFeature, (state: FeatureState) => state.counter);
```

### 为多个状态切片使用选择器

`createSelector` 可以用来根据同一状态的几个切片从状态中选取一些数据。

`createSelector` 函数最多可以使用 8 个选择器函数来获得更完整的状态选择。

例如，想象一下在你的状态中，有一个 `selectedUser` 对象，还有一个包含 `book` 对象的 `allBooks` 数组。


你希望能够展示当前用户下的所有书籍。

你可以使用 `createSelector` 来实现这一点。只要有被选择的用户，可见的书籍将始终处于最新状态，甚至当你在 `allBooks` 中更新了它们。如果没有用户被选择则显示所有的书籍。

这个结果将只是被状态中另一部分所过滤的状态中的一部分，而且会始终保持最新状态。

```ts
// reducers.ts
import { createSelector } from '@ngrx/store';

export interface User {
  id: number;
  name: string;
}

export interface Book {
  id: number;
  userId: number;
  name: string;
}

export interface AppState {
  selectedUser: User;
  allBooks: Book[];
}

export const selectUser = (state: AppState) => state.selectedUser;
export const selectAllBooks = (state: AppState) => state.allBooks;

export const selectVisibleBooks = createSelector(selectUser, selectAllBooks, (selectedUser: User, allBooks: Books[]) => {
  if (selectedUser && allBooks) {
    return allBooks.filter((book: Book) => book.userId === selectedUser.id);
  } else {
    return allBooks;
  }
});
```

## createFeatureSelector

`createFeatureSelector` 是一种返回顶级功能模块状态的便捷方法，它为状态的功能模块切片（feature slice of state）返回一个类型化的选择器函数。

### 示例

```ts
// reducers.ts
import { createSelector, createFeatureSelector } from '@ngrx/store';

export interface FeatureState {
  counter: number;
}

export interface AppState {
  feature: FeatureState
}

export const selectFeature = createFeatureSelector<FeatureState>('feature');
export const selectFeatureCount = createSelector(selectFeature, (state: FeatureState) => state.counter);

```

## 重置带缓存值的选择器（Reset Memoized Selector）

通过调用 `createSelector` 或者 `createFeatureSelector` 返回的选择器函数最初有一个为 `null` 的缓存值（memoized value），当一个选择器第一次被调用后它的缓存值（memoized value）会被存储在内存中，如果这个选择器随后被通过相同的参数调用就会直接返回这个缓存值（memoized value），如果这个选择器被通过不同的参数调用就会重新计算一次，并更新它的缓存值（memoized value），请看下面：

```ts
import { createSelector } from '@ngrx/store';

export interface State {
  counter1: number;
  counter2: number;
}

export const selectCounter1 = (state: State) => state.counter1;
export const selectCounter2 = (state: State) => state.counter2;
export const selectTotal = createSelector(
  selectCounter1,
  selectCounter2,
  (counter1, counter2) => counter1 + counter2
); // selectTotal 有一个为 null 的缓存值（memoized value），因为它还没有被调用。

let state = { counter1: 3, counter2: 4 };

selectTotal(state); // 计算 3，4 的和，返回 7。selectTotal 现在有了一个为 7 的缓存值（memoized value）。
selectTotal(state); // 不再计算 3,4 的和，selectTotal 直接返回缓存值（memoized value） 7

state = { ...state, counter2: 5 };

selectTotal(state); // 计算 3，5 的和，返回 8。selectTotal 现在有了一个为 8 的缓存值（memoized value）。

```

选择器中的缓存值（memoized value ） 会无限期地保留在内存中，如果缓存值是不再需要的大数据集，则可以通过将缓存值（memoized value）重置为 null，以便可以将大数据集从内存中移除，这个可以通过调用选择器上的 `release` 方法来完成。

```ts
selectTotal(state); // 返回缓存值 8
selectTotal.release() // selectTotal 的缓存值现在为 null
```

释放一个选择器会同时释放它的所有祖先选择器。请看下面：

```ts
export interface State {
  evenNums: number[];
  oddNums: number[];
}

export const selectSumEvenNums = createSelector(
  (state: State) => state.evenNums,
  (evenNums) => evenNums.reduce((prev, curr) => prev + curr)
);
export const selectSumOddNums = createSelector(
  (state: State) => state.oddNums,
  (oddNums) => oddNums.reduce((prev, curr) => prev + curr)
);
export const selectTotal = createSelector(
  selectSumEvenNums,
  selectSumOddNums,
  (evenSum, oddSum) => evenSum + oddSum
);

selectTotal({
  evenNums: [2, 4],
  oddNums: [1, 3]
});

/**
 * 调用 selectTotal.release() 之前的缓存值
 *   selectSumEvenNums  6
 *   selectSumOddNums   4
 *   selectTotal        10
 */

selectTotal.release();

/**
 * 调用 selectTotal.release() 之后的缓存值
 *   selectSumEvenNums  null
 *   selectSumOddNums   null
 *   selectTotal        null
 */
```

## 通过 Store 使用选择器
 
 `createSelector` 和 `createFeatureSelector` 方法返回的函数作为检索状态中相应部分的字符串语法的替代方法。
 
### 示例

```ts
// app.component.ts
import { Component } from '@angular/core';
import { Observable } from 'rxjs/Observable';
import { Store, select } from '@ngrx/store';

import * as fromRoot from './reducers';

@Component({
  selector: 'my-app',
  template: `
    <div>Current Count: {{ counter | async }}</div>
  `
})
class MyAppComponent {
  counter: Observable<number>;

  constructor(private store: Store<fromRoot.AppState>){
    this.counter = store.pipe(select(fromRoot.selectFeatureCount));
  }
}
```
