# 前端项目目录结构


**【强制】** 所有资源类型必须按以下目录放置；

## 工作流默认目录

```html

_client/
├── /dist/               # 生产环境。打包输出的部署文件夹，上线部署时使用此文件
├── /scripts/            # 自动化脚本目录，比如生成国际化配置文件脚本
├── /node_modules/       # 第三方类库和工具
├── /src/                # 开发环境。 源码目录
├ ├── /assets/           # 静态资源文件
│ ├── /components/       # 公共组件目录，自己封装的公共组件
│ ├── /entries/          # 应用入口文件
│ ├── /locale/           # 国际化配置文件,会自动生成
│ ├── /route/            # 路由配置目录
│ ├── /store/            # redux数据管理目录
│ ├── /styles/           # 全局样式目录
│ ├── /temp/             # 临时文件，会自动生成
│ └── /views/            # 子系统模块目录
|   |———— /子系统1页面目录 # 子系统1的目录
|        |———— folder    # 
|        |———— page1.jsx # 
|        |———— page1.less# 
|        |———— img1.png
|        |———— ...
|   |———— /子系统2页面目录 # 子系统2的目录
|   |———— /子系统3页面目录 # 子系统3的目录
├── .babelrc             # 配置babel，编译ES6等
├── webpack.config.js    # 扩展开发调试 webpack 配置
├── webpack.production.config.js    # 扩展打包构建 webpack 配置
└── package.json         # 配置入口文件、依赖和 scripts
```
