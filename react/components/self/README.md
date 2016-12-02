# 自己封装的组件



## 请求组件

依赖: 开源fetch组件

组件地址: /src/components/Lib/fetch/index.js


```js

// 具体的拦截用法请查看/src/components/Lib/fetch/index.js


// 普通请求数据方法请查看/src/views/userAnalysis/baseTarget/ActiveUser.jsx 
import FetchData from '../../../components/Lib/fetch/index';

FetchData(url, data, configs).then(function(res) {
  //请求成功回调
},function(err) {
  //请求失败回调
})


```


## 请求进度条组件

组件地址: /src/components/ReactProcessLoadingBar/index.js



```js

// 具体用法请参考/src/components/Lib/fetch/index.js
import Progress from "../../ReactProcessLoadingBar";

Progress.show(); // 显示进度条
Progress.hide();    // 隐藏进度条
Progress.hideAll(); // 隐藏所有
Progress.isVisible(); // 返回进度条是否可见

```

## MD5组件
组件地址: /src/components/lib/md5/index.js


```js

// 具体的用法请参考/src/views/login/login.jsx
import MD5 from './md5';

MD5(str);


```


## 下拉单选框
组件地址: /src/views/userAnalysis/baseTarget/dropSelect/DropRadio.js

```js

// 具体用法请参考/src/views/userAnalysis/baseTarget/ActiveUser.jsx


```


## 下拉多选框
组件地址: /src/views/userAnalysis/baseTarget/dropSelect/DropSelect.js

```js

// 具体用法请参考/src/views/userAnalysis/useReport/WechatBuy.jsx

```