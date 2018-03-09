# 测试

### 为测试提供 Store
在测试组件或服务时通过在 `TestBed` 配置中使用 `StoreModule.forRoot` 来注入 `Store`。

* 归并状态的操作是同步的，所以模拟 `Store` 并不是必需的。
* 通过使用 `combineReducers` 方法和功能模块的 `reducers` 映射来组成测试使用的 `State`。
* 通过 `dispatch(action)` 往 `Store` 中加载数据。

my-component.ts
```ts
import { Component, OnInit } from '@angular/core';
import { Store, select } from '@ngrx/store';
import * as fromFeature from '../reducers';
import * as Data from '../actions/data';

@Component({
  selector: 'my-component',
  template: `
    <div *ngFor="let item of items$ | async">{{ item }}</div>

    <button (click)="onRefresh()">Refresh Items</button>
  `,
})
export class MyComponent implements OnInit {
  items$ = this.store.pipe(select(fromFeature.selectFeatureItems));

  constructor(private store: Store<fromFeature.State>) {}

  ngOnInit() {
    this.store.dispatch(new Data.LoadData());
  }

  onRefresh() {
    this.store.dispatch(new Data.RefreshItems());
  }
}
```

my-component.spec.ts
```ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { StoreModule, Store, combineReducers } from '@ngrx/store';
import { MyComponent } from './my.component';
import * as fromRoot from '../reducers';
import * as fromFeature from './reducers';
import * as Data from '../actions/data';

describe('My Component', () => {
  let component: MyComponent;
  let fixture: ComponentFixture<MyComponent>;
  let store: Store<fromFeature.State>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [
        StoreModule.forRoot({
          ...fromRoot.reducers,
          'feature': combineReducers(fromFeature.reducers)
        }),
        // other imports
      ],
      declarations: [
        MyComponent,
        // other declarations
      ],
      providers: [
        // other providers
      ]
    });

    store = TestBed.get(Store);

    spyOn(store, 'dispatch').and.callThrough();

    fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should be created', () => {
    expect(component).toBeTruthy();
  });

  it('should dispatch an action to load data when created', () => {
    const action = new Data.LoadData();

    expect(store.dispatch).toHaveBeenCalledWith(action);
  });

  it('should dispatch an action to refreshing data', () => {
    const action = new Data.RefreshData();

    component.onRefresh();

    expect(store.dispatch).toHaveBeenCalledWith(action);
  });

  it('should display a list of items after the data is loaded', () => {
    const items = [1, 2, 3];
    const action = new Data.LoadDataSuccess({ items });

    store.dispatch(action);

    component.items$.subscribe(data => {
      expect(data.length).toBe(items.length);
    });
  });
});
```
