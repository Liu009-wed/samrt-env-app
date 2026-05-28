# 智慧环保 (Smart Environment) 项目 Code Wiki

## 1. 项目整体架构

本项目是一个基于 `uni-app` 框架开发的“智慧环保/诚信回收”应用，采用前后端分离的架构。项目整体分为两部分：
- **前端 (Frontend)**：基于 Vue.js 和 uni-app 框架开发的多端应用（主要面向小程序/H5/App平台）。前端负责 UI 展示、用户交互以及通过 HTTP 请求与后端通信。
- **后端 (Mock Backend)**：基于 `json-server` 搭建的轻量级本地 Mock 服务器，主要用于提供 RESTful API 数据接口，数据持久化保存在 `db.json` 中。

### 目录结构说明
```text
E:\软件\smart-env-app
├── db/case07/server/smartEpserver/  # 后端 Mock 服务目录
│   ├── public/                      # 静态资源目录（图片、头像等）
│   ├── db.json                      # 核心数据文件（模拟数据库）
│   ├── package.json                 # Node 项目配置文件及启动脚本
│   └── Mock.md                      # Mock 数据接口说明文档
└── smart-env-app/                   # 前端 uni-app 源码目录
    ├── pages/                       # 页面视图组件（各功能模块页面）
    ├── static/                      # 前端静态资源（Icon、默认图片等）
    ├── App.vue                      # 应用主组件（生命周期及全局样式）
    ├── main.js                      # 应用入口文件（Vue 实例挂载）
    ├── pages.json                   # 全局路由、导航栏和 TabBar 配置文件
    └── manifest.json                # 应用打包和跨端配置文件
```

---

## 2. 主要模块职责

### 2.1 前端核心模块 (`smart-env-app/pages/`)
前端包含了完整的业务流转，划分为以下主要模块：
- **首页模块 (`index`)**：展示轮播图（热点活动）、快捷功能入口（回收分类、附近回收、积分商城等）及预约上门回收入口。
- **用户认证模块 (`login` / `register`)**：提供普通用户和企业用户（回收员）的注册与登录功能，登录成功后在本地 `storage` 中保存状态。
- **分类查询模块 (`category` / `category-result`)**：展示所有的废品分类（如废纸、塑料、金属、纺织品、家电等），并支持查看子分类及对应回收单价。
- **预约下单模块 (`place-order`)**：核心业务模块。用户填写回收物品类型、预估重量、联系人信息、上门地址、时间并上传照片进行预约。
- **订单管理模块 (`order` / `order-detail`)**：展示用户的历史订单与当前订单状态（如待处理、已完成），提供详细的订单追踪信息。
- **企业/附近回收模块 (`company` / `company-detail` / `company-search`)**：供用户查找附近的回收企业，查看企业详情并可直接向特定企业发起预约。
- **个人中心模块 (`mine` / `setting` / `shop`)**：个人资料展示、系统设置及环保积分商城。

### 2.2 后端数据模块 (`db.json`)
数据结构被划分为以下几个“表”（顶级 JSON 键）：
- `types`：回收物品的分类数据（包括父类和子类、图标及单价）。
- `users`：用户数据（涵盖普通用户和企业用户，通过 `typeid` 或 `role` 区分）。
- `hotDots`：首页轮播图及热点信息数据。
- `actions`：活动信息或系统公告数据。
- `companys`：合作的回收企业列表及相关信息。
- `orders`：用户发起的回收订单信息，包含联系方式、重量、总价、订单状态等。

---

## 3. 关键类与函数说明

### 3.1 前端关键逻辑
- **`uni.request` 封装与调用**：项目中没有单独封装庞大的网络请求层，而是直接在各个 Vue 页面的 `methods` 或生命周期（如 `onLoad`）中调用 `uni.request`，向 `http://localhost:3004` 发起 RESTful 请求。
- **`login()` (位于 `pages/login/login.vue`)**：
  - **职责**：处理用户登录逻辑。
  - **流程**：请求后端的 `/users` 列表，在前端遍历匹配手机号 (`phoneNum` / `phone`)、密码 (`password`) 以及角色类型 (`typeid` / `role`)。匹配成功后将规范化后的 `userInfo` 存入 `uni.setStorageSync("user", ...)`，随后跳转首页。
- **`submitOrder()` (位于 `pages/place-order/place-order.vue`)**：
  - **职责**：处理预约上门回收的下单操作。
  - **流程**：校验表单必填项 -> 从本地分类数据计算出预估总收益 (`weight * unitPrice`) -> 组装订单对象（包含 `userId`、`state`、`createTime`、`coverUrl` 等） -> 通过 `POST` 方法请求 `http://localhost:3004/orders` -> 成功后跳转订单列表页。

### 3.2 后端关键机制
- **`json-server` 的 REST 路由**：
  - `GET /types`：获取所有分类。
  - `POST /orders`：创建新订单。
  - `GET /users`：获取用户列表以供登录验证。
  - `json-server` 默认将 `db.json` 中的顶层 key 映射为路由，自动处理增删改查逻辑。

---

## 4. 依赖关系

### 前端依赖
- **uni-app 框架**：跨端开发底层框架。
- **Vue.js**：前端组件化渲染（支持 Vue 2 及 Vue 3 SSR）。

### 后端依赖
- **Node.js**：运行环境。
- **json-server**：提供零代码、快速的完整 REST API 模拟服务。通过 `package.json` 中的 `devDependencies` 或全局环境引入。

---

## 5. 项目运行方式

### 5.1 启动后端 (Mock Server)
由于前端依赖于本地 `3004` 端口提供数据，因此**必须先启动后端服务**。
1. 确保系统已安装 **Node.js**。
2. 打开终端，进入后端目录：
   ```bash
   cd E:\软件\smart-env-app\db\case07\server\smartEpserver
   ```
3. （可选）如果尚未安装 `json-server`，请全局或本地安装：
   ```bash
   npm install -g json-server
   ```
4. 运行启动命令：
   ```bash
   npm run mock
   ```
   *此命令将执行 `json-server db.json --port 3004`，并在 `http://localhost:3004` 监听请求。*

### 5.2 启动前端 (uni-app)
推荐使用 **HBuilderX** 作为开发工具：
1. 打开 HBuilderX，选择 **文件 -> 导入 -> 从本地目录导入**，选中 `E:\软件\smart-env-app\smart-env-app` 文件夹。
2. 在项目管理器中选中该项目。
3. 点击顶部菜单栏的 **运行 -> 运行到浏览器**（例如 Chrome）或者 **运行到内置浏览器 / 小程序模拟器**。
4. 编译完成后，前端界面将自动打开。你可以通过预设账号或注册新账号进行登录并体验“预约上门回收”的完整流程。
