# Chitchat 轻聊论坛

**Chitchat** 是一个仿 Flarum 风格的现代化 PHP 论坛程序，轻量、快速、美观，支持 Web 安装向导、模板系统和插件系统。

---

## 🚀 快速开始

### 系统要求

| 组件 | 版本要求 |
|------|---------|
| PHP | 8.0 或更高（推荐 8.2） |
| MySQL | 5.7+ 或 MariaDB 10.3+ |
| Web 服务器 | Nginx / Apache（需支持 URL 重写） |
| PHP 扩展 | PDO、PDO_MySQL、JSON、GD、mbstring（`fileinfo` 可选） |

### 安装步骤

1. **上传文件**  
   将整个 `chitchat/` 目录上传到服务器的 Web 根目录（如 `/var/www/html/`）。

2. **配置 Web 服务器**
   - **Apache**: 确保根目录的 `.htaccess` 可用，开启 `mod_rewrite`，Web 根目录直接指向 `chitchat/`
   - **Nginx**: 参考 `nginx.conf.example` 配置，`root` 设为 `chitchat/` 目录

3. **设置目录权限**
   ```bash
   chmod 755 storage/ uploads/ config/
   chmod -R 755 themes/ plugins/
   ```

4. **访问安装向导**  
   在浏览器中打开：`http://your-domain.com/install/`

5. **跟随安装向导完成配置**
   - 环境检查
   - 数据库连接配置
   - 论坛名称和管理员账号设置
   - 一键安装

6. **开始使用！**  
   安装完成后自动跳转论坛首页 🎉

---

## 📁 目录结构

```
chitchat/
├── index.php            # 应用入口（Web 根目录即本目录）
├── .htaccess            # Apache URL 重写规则
├── assets/              # 公共静态资源
├── uploads/             # 用户上传文件
│   └── avatars/         # 用户头像
├── app/                 # 应用核心代码
│   ├── Core/            # 框架核心
│   │   ├── Application.php    # 应用引导
│   │   ├── Router.php         # URL 路由
│   │   ├── Request.php        # HTTP 请求
│   │   ├── Response.php       # HTTP 响应
│   │   ├── Database.php       # 数据库封装
│   │   ├── QueryBuilder.php   # 查询构建器
│   │   ├── Auth.php           # 认证系统
│   │   ├── Session.php        # 会话管理
│   │   ├── ViewEngine.php     # 模板引擎
│   │   ├── EventDispatcher.php # 事件系统
│   │   ├── PluginManager.php  # 插件管理器
│   │   ├── Container.php      # 依赖注入容器
│   │   ├── Config.php         # 配置管理
│   │   ├── BaseController.php # 基础控制器
│   │   └── BaseModel.php      # 基础模型
│   ├── Forum/           # 论坛业务逻辑
│   │   ├── Controllers/ # 前台控制器
│   │   └── Models/      # 数据模型
│   ├── Admin/           # 管理后台逻辑
│   ├── Helpers/         # 辅助函数
│   ├── autoload.php     # 类自动加载
│   └── routes.php       # 路由定义
├── themes/              # 主题模板目录
│   └── default/         # 默认 Flarum 风格主题
│       ├── layouts/     # 布局文件（header/footer）
│       ├── pages/       # 页面模板
│       ├── partials/    # 可复用片段
│       ├── assets/      # CSS/JS/图片
│       └── config.json  # 主题元信息
├── plugins/             # 插件目录
│   └── welcome-bot/     # 示例插件
├── storage/             # 缓存、日志等
├── config/              # 配置文件
│   └── config.php       # 安装后生成的配置
├── database/
│   └── schema.sql       # 数据库建表 SQL
├── install/             # 安装向导（安装后会被锁定）
└── nginx.conf.example   # Nginx 配置参考
```

---

## ✨ 核心功能

### 论坛功能
- ✅ 讨论列表（分页、多种排序）
- ✅ 讨论详情与帖子回复（时间线排列）
- ✅ Markdown 编辑器（实时预览、工具栏）
- ✅ 讨论 CRUD（创建/编辑/删除）
- ✅ 帖子 CRUD（回复/编辑/删除）
- ✅ 引用回复
- ✅ @用户名 提及
- ✅ 点赞系统
- ✅ 置顶 / 锁定讨论
- ✅ 标签分类系统（支持二级分类）
- ✅ 全文搜索

### 用户系统
- ✅ 注册 / 登录（支持 Remember Me）
- ✅ 头像（自定义上传 / Gravatar 后备，支持 1:1 裁剪）
- ✅ 个人主页（帖子统计、最近讨论）
- ✅ 账号设置（头像、个人简介、密码修改）
- ✅ 用户组与权限管理
- ✅ 用户封禁（临时 / 永久）

### 通知系统
- ✅ 站内通知（新回复、@提及）
- ✅ 实时未读数轮询
- ✅ 通知列表 + 一键已读

### 管理后台
- ✅ 数据统计仪表盘
- ✅ 用户管理（搜索、修改用户组、封禁、删除）
- ✅ 分类标签管理（增删改、颜色、层级）
- ✅ 插件管理（激活/停用/上传）
- ✅ 主题管理（切换）
- ✅ 论坛设置（名称、描述、邮件、自定义 CSS）

---

## 🧩 插件开发

在 `plugins/` 目录下创建插件目录，参考以下结构：

```
plugins/my-plugin/
├── plugin.json       # 插件元信息
└── bootstrap.php     # 插件入口
```

**plugin.json 示例：**
```json
{
  "name": "我的插件",
  "description": "插件描述",
  "version": "1.0.0",
  "author": "作者"
}
```

**bootstrap.php 示例：**
```php
<?php
namespace Plugins\MyPlugin;

class PluginBootstrap {
  public function __construct($container, $events) {
    $this->events = $events;
    $this->container = $container;
  }
  
  public function boot(): void {
    // 监听讨论创建事件
    $this->events->listen('discussion.created', function ($data) {
      // 自定义逻辑
    });
    
    // 添加过滤器
    $this->events->addFilter('forum_title_filter', function ($title) {
      return $title . ' - Powered by My Plugin';
    });
  }
}
```

**可用事件：**
- `discussion.created` - 讨论被创建
- `post.created` - 帖子被创建
- `user.registered` - 用户注册完成

---

## 🎨 主题开发

复制 `themes/default/` 目录到 `themes/my-theme/`，修改：

1. `config.json` - 更新主题名称和作者
2. `assets/css/app.css` - 修改样式
3. `pages/*.php` - 修改页面模板
4. `layouts/header.php` - 修改页面头部

然后在管理后台 → 主题模板 中切换到新主题。

---

## 🔒 安全说明

- 所有用户输入经过 `htmlspecialchars()` 转义，防止 XSS
- 密码使用 `password_hash()` + `PASSWORD_DEFAULT` 哈希
- 所有 POST 请求校验 CSRF Token
- SQL 查询使用 PDO 预处理语句，防止 SQL 注入
- 文件上传验证类型和大小

---

## 📄 许可协议

MIT License - 可自由使用、修改和分发。

---

*Chitchat - 轻量、快速、美观的现代化论坛程序*
