# @ngrx/store-devtools


Devtools for [@ngrx/store](../store/README.md).


[@ngrx/store](../store/README.md) 的开发者工具

### Installation
Install @ngrx/store-devtools from npm:

`npm install @ngrx/store-devtools --save` OR `yarn add @ngrx/store-devtools`


### 安装
从 npm 中安装  @ngrx/store-devtools-builds

`npm install @ngrx/store-devtools --save`  或 `yarn add @ngrx/store-devtools`


### Nightly builds

`npm install github:ngrx/store-devtools-builds` OR `yarn add github:ngrx/store-devtools-builds`


### 每日构建版本

`npm install github:ngrx/store-devtools-builds` 或 `yarn add github:ngrx/store-devtools-builds`


## Instrumentation


## 仪表盘

### Instrumentation with the Chrome / Firefox Extension

### 使用火狐、谷歌浏览器的插件查看监测

1. Download the [Redux Devtools Extension](http://zalmoxisus.github.io/redux-devtools-extension/)

1. 下载 [Redux Devtools Extension](http://zalmoxisus.github.io/redux-devtools-extension/)

2. In your `AppModule` add instrumentation to the module imports using `StoreDevtoolsModule.instrument`:

2. 在你的 `AppModule` 中通过在元数据中导入 `StoreDevtoolsModule.instrument` 添加仪表盘

```ts
import { StoreDevtoolsModule } from '@ngrx/store-devtools';

@NgModule({
  imports: [
    StoreModule.forRoot(reducers),
    // Note that you must instrument after importing StoreModule (config is optional)
    StoreDevtoolsModule.instrument({
      maxAge: 25 //  Retains last 25 states
    })
  ]
})
export class AppModule { }
```

### Available options

### 可选的设置项

When you call the instrumentation, you can give an optional configuration object:

当你调用仪表盘时， 可以给它配置一些可选的参数：

#### `maxAge`
number (>1) | false - maximum allowed actions to be stored in the history tree. The oldest actions are removed once maxAge is reached. It's critical for performance. Default is `false` (infinite).

`number` 型参数 或者 false - 配置允许存储在记录中的 `actions` 的最大值。达到最大值时，最早的 `action` 会被清除。 这个参数很影响性能。 默认值是 `false` ( 无限大)。

#### `name`
string - the instance name to be showed on the monitor page. Default value is _NgRx Store DevTools_.

`string` 型参数 展示在仪表 监控面板上的实例名字。 默认是 _NgRx Store DevTools_ 。

#### `monitor`:
function - the monitor function configuration that you what to hook.

`function` 型参数 - 配置你想劫持的监控函数

#### `actionSanitizer`
function which takes `action` object and id number as arguments, and should return `action` object back.
 
`function` 型参数  入参 `action` 和 `Id` ， 返回 指定的 `action` 对象。 

#### `stateSanitizer`
function which takes `state` object and index as arguments, and should return `state` object back.

`function` 型参数  传入 `state` 对象 和 索引，返回 `state` 对象。

#### `serialize` 
false | configuration object - Handle the way you want to serialize your state, [more information here](https://github.com/zalmoxisus/redux-devtools-extension/blob/master/docs/API/Arguments.md#serialize). 

false | configuration 对象 - 控制你序列化 state 的方案，[这里查看更多细节](https://github.com/zalmoxisus/redux-devtools-extension/blob/master/docs/API/Arguments.md#serialize)。
