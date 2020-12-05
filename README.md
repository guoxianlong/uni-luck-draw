
## 官方文档 & Demo演示

> **中文**：[https://100px.net/document/uni-app.html](https://100px.net/document/uni-app.html)  

<br />

## 在 uni-app 中使用

### 1. 安装插件

- **你可以选择通过 `HBuilderX` 导入插件：** [https://ext.dcloud.net.cn/plugin?id=3499](https://ext.dcloud.net.cn/plugin?id=3499)

- **也可以选择通过 `npm / yarn` 安装**

```shell
# npm 安装：
npm install uni-luck-draw

# yarn 安装：
yarn add uni-luck-draw
```

### 2. 引入并使用

```html
<view>
  <!-- 大转盘抽奖 -->
  <LuckyWheel
    width="600rpx"
    height="600rpx"
    ...你的配置
  />
  <!-- 九宫格抽奖 -->
  <LuckyGrid
    width="600rpx"
    height="600rpx"
    ...你的配置
  />
</view>
```

```js
// npm 下载会默认到 node_modules 里面，直接引入包名即可
import { LuckyWheel, LuckyGrid } from 'uni-luck-draw'

// 如果你是通过 HBuilderX 导入插件，那你需要指定一下路径
// import { LuckyWheel, LuckyGrid } from './components/uni-luck-draw'

export default {
  // 注册组件
  components: { LuckyWheel, LuckyGrid },
}
```

### 3. 我提供了一个最基本的 demo 供你用于尝试

由于 uni-app 渲染 md 的时候会出问题，所以我把 demo 代码放到了文档里

- [https://100px.net/document/uni-app.html](https://100px.net/document/uni-app.html)
