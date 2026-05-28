# 智慧环保 (Smart Environment)

[![Framework](https://img.shields.io/badge/Framework-uni--app-green.svg)](https://uniapp.dcloud.io/)
[![Language](https://img.shields.io/badge/Language-Vue.js-4fc08d.svg)](https://vuejs.org/)
[![Backend](https://img.shields.io/badge/Backend-json--server-blue.svg)](https://github.com/typicode/json-server)

## 📖 项目简介

“智慧环保”是一个基于 **uni-app** 框架开发的移动端应用，旨在通过数字化的方式简化废品回收流程。用户可以通过该应用查看回收分类、预约上门回收、查找附近回收企业并管理自己的回收订单。

项目采用前后端分离架构，前端使用 Vue.js 开发，后端使用 `json-server` 模拟 RESTful API 接口。

## ✨ 核心功能

- **智能分类**：详细的废品分类指南（废纸、塑料、金属等）及实时回收单价。
- **预约回收**：用户可在线填写回收物品信息、上传照片、选择上门时间及地址。
- **订单追踪**：实时查看订单状态（待处理、已完成），记录回收历史。
- **企业搜索**：查找并联系附近的专业回收公司。
- **积分商城**：通过环保回收获取积分，兑换环保礼品。
- **双端身份**：支持普通用户和企业用户（回收员）两种登录模式。

## 🛠️ 技术栈

- **前端**: [uni-app](https://uniapp.dcloud.io/) (Vue.js)
- **后端**: [json-server](https://github.com/typicode/json-server) (Mock API)
- **开发工具**: HBuilderX

## 🚀 快速开始

### 1. 环境准备
- 安装 [Node.js](https://nodejs.org/)
- 安装 [HBuilderX](https://www.dcloud.io/hbuilderx.html)

### 2. 启动后端服务 (Mock Server)
进入后端目录并启动服务：
```bash
cd db/case07/server/smartEpserver
npm install  # 如果是首次运行
npm run mock
```
后端服务将运行在 `http://localhost:3004`。

### 3. 启动前端应用
1. 使用 **HBuilderX** 打开 `smart-env-app` 文件夹。
2. 点击菜单栏 **运行 -> 运行到浏览器** (推荐 Chrome) 或 **运行到内置浏览器**。

## 📂 项目结构

```text
.
├── db/                          # 后端 Mock 服务
│   └── case07/server/smartEpserver/
│       ├── db.json              # 模拟数据库
│       └── public/              # 静态资源（图片、头像）
├── smart-env-app/               # 前端源码
│   ├── pages/                   # 页面组件
│   ├── static/                  # 本地静态资源
│   ├── App.vue                  # 全局配置
│   └── pages.json               # 路由配置
├── CODE_WIKI.md                 # 详细技术文档
└── README.md                    # 项目说明文档
```

## 📄 更多文档
关于项目的详细架构设计、关键函数说明及依赖关系，请参阅 [CODE_WIKI.md](./CODE_WIKI.md)。
