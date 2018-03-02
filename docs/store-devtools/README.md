# @ngrx/store-devtools

[@ngrx/store](../store/README.md) 的开发者工具

### 安装
从 npm 中安装  @ngrx/store-devtools-builds

`npm install @ngrx/store-devtools --save`  或 `yarn add @ngrx/store-devtools`


### 每日构建版本
`npm install github:ngrx/store-devtools-builds` 或 `yarn add github:ngrx/store-devtools-builds`

## 仪表盘


### 使用火狐、谷歌浏览器的扩展查看路由的变更状态
1. 下载 [Redux Devtools Extension](http://zalmoxisus.github.io/redux-devtools-extension/)
2. 在你的 `AppModule` 中通过在元数据 metaData 中导入 `StoreDevtoolsModule.instrument` 添加仪表盘


```ts
import { StoreDevtoolsModule } from '@ngrx/store-devtools';
import { environment } from '../environments/environment'; // Angular CLI environemnt

@NgModule({
  imports: [
    StoreModule.forRoot(reducers),
    // 注意： 必须马上在引入 `StoreModule` 紧接着引入你的 `StoreDevtoolsModule`
    StoreDevtoolsModule.instrument({
      maxAge: 25 //  保留最新的 25 个 state
      logOnly: environment.production // 根据应用运行环境限制扩展的行为

    })
  ]
})
export class AppModule { }
```


### 可选的设置项
当你调用仪表盘时， 可以给它配置一些可选的参数：

#### `maxAge`
`number` 型 ( 必须>1 ) 或者 false - 配置允许存储在记录中的 `actions` 的最大值。达到最大值时，最早的 `action` 会被清除。 这个参数很影响性能。 默认值是 `false` ( 表示无限大 )。

#### `logOnly`
boolean - connect to the Devtools Extension in log-only mode. Default is `false` which enables all extension [features](https://github.com/zalmoxisus/redux-devtools-extension/blob/master/docs/API/Arguments.md#features).

#### `name`
`string` 型 展示在仪表 监控面板上的实例名字。 默认是 _NgRx Store DevTools_ 。

#### `monitor`:
`function` 型 - 配置你想劫持的监控函数

#### `actionSanitizer`
`function` 型  入参 `action` 和 `Id` ， 返回 指定的 `action` 对象。 

#### `stateSanitizer`
`function` 型  传入 `state` 对象 和 索引，返回 `state` 对象。

#### `serialize` 
false | configuration 对象 - 控制你序列化 state 的方案，[这里查看更多细节](https://github.com/zalmoxisus/redux-devtools-extension/blob/master/docs/API/Arguments.md#serialize)。

