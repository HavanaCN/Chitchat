# Chitchat 轻聊论坛

**Chitchat** 是一个仿 Flarum 风格的现代化 PHP 论坛程序，轻量、快速、美观，支持 Web 安装向导、模板系统和插件系统。

---

## 🚀 快速开始

### 系统要求

| 组件 | 版本要求 |
|------|---------|
| PHP | 8.0 或更高（推荐 8.2） |
| MySQL | 5.7+ 或 MariaDB 10.3+ |
| Web 服务器 | Nginx / Apache（支持 PHP 即可，开启 URL 重写体验更佳） |
| PHP 扩展 | PDO、PDO_MySQL、JSON、GD、mbstring（`fileinfo` 可选） |

> Chitchat 现在同时支持两种访问方式：
> 1. **伪静态模式**：服务器已正确配置重写时，地址可保持简洁。
> 2. **兼容模式**：即使服务器没有开启 URL 重写，系统也会自动回退到 `index.php?route=...` 形式，内部链接仍可正常访问。

### 安装步骤

1. **上传文件**  
   将整个 `chitchat/` 目录上传到服务器。既可以放在站点根目录，也可以放在子目录（例如 `/chitchatbbs`）。

2. **配置 Web 服务器**
   - **根目录部署**：Web 根目录直接指向 `chitchat/`。
   - **子目录部署**：例如论坛最终访问地址是 `http://your-domain.com/chitchatbbs`，请确保 Web 服务器实际指向该目录，并在安装向导中把 `base_url` 填成 `/chitchatbbs`。
   - **Apache**：确保允许读取根目录 `.htaccess`。如果启用了 `mod_rewrite`，建议在 `.htaccess` 中按实际子目录补上 `RewriteBase /chitchatbbs/`（根目录部署无需添加）。
   - **Nginx**：如果论坛运行在子目录，`try_files` 的回退路径必须指向子目录入口，例如 `/chitchatbbs/index.php?$query_string`，不能写成站点根入口 `/index.php?$query_string`。

3. **设置目录权限**
   ```bash
   chmod 755 storage/ uploads/ config/
   chmod -R 755 themes/ plugins/
   ```

4. **访问安装向导**  
   - 根目录部署：`http://your-domain.com/install/`
   - 子目录部署：`http://your-domain.com/chitchatbbs/install/`

5. **跟随安装向导完成配置**
   - 环境检查
   - 数据库连接配置
   - 论坛名称和管理员账号设置
   - 如果论坛安装在子目录，请正确填写 `base_url`（例如 `/chitchatbbs`）
   - 一键安装

6. **开始使用！**  
   安装完成后自动跳转论坛首页 🎉

### 子目录部署与 404 排查

如果首页能打开，但点击任意讨论、用户、后台菜单后都变成 404，通常是下面两类原因：

1. **服务器重写规则没有生效**
   - Apache 没有开启 `mod_rewrite`
   - `AllowOverride` 被禁用，导致 `.htaccess` 没有生效
   - Nginx 的 `try_files` 没有把请求回退到当前子目录的 `index.php`

2. **安装时 `base_url` 填错或留空**
   - 根目录安装可留空
   - 子目录安装必须填写类似 `/chitchatbbs`

**Apache 子目录部署示例**（论坛位于 `/chitchatbbs`）：

```apache
RewriteEngine On
RewriteBase /chitchatbbs/

RewriteRule ^(app|config|database|storage|install/install\.lock) - [F,L]
RewriteRule \.(sql|lock|log|env|git|htaccess)$ - [F,L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.php [L]
```

**Nginx 子目录部署示例**（论坛目录位于 `/var/www/html/chitchatbbs`）：

```nginx
location /chitchatbbs/ {
    try_files $uri $uri/ /chitchatbbs/index.php?$query_string;
}

location /chitchatbbs/install/ {
    try_files $uri $uri/ /chitchatbbs/install/index.php?$query_string;
}
```

如果你的服务器暂时无法开启 URL 重写，也可以先安装并直接使用，Chitchat 会自动生成兼容模式链接，不会再因为内部跳转全部走伪静态而整站 404。

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
- ✅ 头像（自定义上传 / Gravatar 后备）
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
