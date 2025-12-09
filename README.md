# 基于SpringBoot的固定资产管理系统

## 项目简介

本项目是一个基于 **Spring Boot + Vue.js** 的固定资产管理系统，作为毕业设计项目开发。系统采用前后端分离架构，实现了企业/高校对固定资产的全生命周期管理。

### 技术栈

| 层次 | 技术 |
|------|------|
| **前端** | Vue 2.7 + View Design + Axios + Vue Router |
| **后端** | Spring Boot 2.7 + MyBatis Plus + Spring Security |
| **数据库** | MySQL 5.7+ |
| **缓存** | Redis |

## 功能模块

### 核心业务模块
- **资产品类管理** - 资产分类与品类维护
- **资产单位管理** - 计量单位管理
- **资产仓库管理** - 仓库信息维护
- **资产供应商管理** - 供应商档案管理
- **资产库存管理** - 库存查询与统计
- **资产采购管理** - 采购申请与订单管理
- **资产采购审核** - 采购流程审批
- **资产报修管理** - 报修申请与处理

### 系统基础模块
- 用户管理
- 角色权限管理
- 组织架构管理
- 数据字典管理
- 系统日志管理

## 系统角色

| 角色 | 职责 |
|------|------|
| 资产管理员 | 维护基础档案，监控资产情况 |
| 资产采购专员 | 发起采购订单，完成入库操作 |
| 资产审核专员 | 审核采购订单 |

## 快速启动

### 环境要求
- JDK 1.8+
- Node.js 14+
- MySQL 5.7+
- Redis

### 数据库配置
1. 创建数据库 `assets`
2. 导入 `assets.sql` 初始化数据

### 启动后端
```bash
cd back
# 配置 application.yml 中的数据库连接信息
mvn spring-boot:run
```

### 启动前端
```bash
cd front
npm install
npm run dev
```

### 访问系统
- 前端地址: http://localhost:8080
- 后端接口: http://localhost:8081
- 默认账号: `admin` / `123456`

## 项目结构

```
├── back/                   # 后端 Spring Boot 项目
│   ├── src/main/java/cn/zwz/
│   │   ├── assets/        # 资产管理模块
│   │   ├── basics/        # 基础框架
│   │   └── data/          # 数据管理模块
│   └── src/main/resources/
│       └── application.yml # 配置文件
├── front/                  # 前端 Vue 项目
│   ├── src/views/         # 页面组件
│   └── src/router/        # 路由配置
└── assets.sql             # 数据库初始化脚本
```

## 系统截图

### 登录页面
![登录页](./image/3.png)

### 系统首页
![首页](./image/5.png)

### 资产管理
![资产管理](./image/10.png)

## 作者

**灿**

---

*本项目为毕业设计作品，仅供学习参考使用。*
