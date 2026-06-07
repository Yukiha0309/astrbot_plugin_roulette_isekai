# AstrBot 异界战争轮盘

异界战争轮盘现在拆分为两部分：AstrBot 桥接插件和独立网页游戏端。

| 名称 | 文件 | 作用 |
|---|---|---|
| AstrBot 桥接插件 | `astrbot_plugin_isekai_web_bridge_v0.1.5.zip` | 负责群聊创建网页房间、生成一次性密码、发送房间链接、游戏开始/结束播报 |
| 网页游戏端 | `isekai-web-roulette-v0.1.6.zip` | 负责实际游戏逻辑、网页操作、战报显示、PHP API 和 SQLite 数据 |

> 只安装 AstrBot 桥接插件无法单独运行网页模式，必须同时部署网页游戏端。

## 当前版本

| 项目 | 版本 |
|---|---|
| AstrBot 桥接插件 | v0.1.5 |
| 网页游戏端 | v0.1.6 |

## 运行需求

网页游戏端需要：

- Caddy
- PHP
- PHP 启用 `pdo_sqlite`

SQLite 不需要单独安装数据库服务，数据会保存在网页端的 `data/game.db`。

## 部署网页端

下载 `isekai-web-roulette-v0.1.6.zip`，解压到你的网页服务目录。

示例目录：

```text
C:\isekai-web-roulette\
```

Caddy 需要指向网页端的 `public` 目录。

示例 Caddyfile：

```caddyfile
isekai.example.cn {
    root * C:\isekai-web-roulette\public
    php_fastcgi 127.0.0.1:114514
    file_server
}
```

Windows 下启动 PHP-CGI 建议使用：

```bat
set PHP_FCGI_MAX_REQUESTS=0
php-cgi.exe -b 127.0.0.1:114514
```

如果不设置 `PHP_FCGI_MAX_REQUESTS`，PHP-CGI 可能在处理一定数量请求后自动退出。

## 安装 AstrBot 桥接插件

下载 `astrbot_plugin_isekai_web_bridge_v0.1.5.zip`，上传到 AstrBot 插件管理。

桥接插件只负责群聊联动，不负责完整游戏逻辑。

## 配置说明

网页端从 `config.example.php` 复制一份 `config.php`，然后修改配置。

常用配置：

```php
"public_base_url" => "https://你的域名",
"bot_shared_secret" => "你自己设置的一段随机密码",
"token_digits" => 6,
"jiuhu_event_enabled" => true,
"jiuhu_event_chance" => 30,
```

AstrBot 桥接插件中的密钥需要和网页端的 `bot_shared_secret` 保持一致。

## 指令说明

桥接插件指令示例：

| 指令 | 说明 |
|---|---|
| `isk网页创房` | 在群聊创建网页房间 |
| `我要密码 房间号` | 私聊 Bot 获取一次性加入密码 |

实际可用指令以插件配置和当前版本为准。

## 当前功能

- 网页房间创建
- 一次性数字密码加入
- 一个 QQ 同时只能绑定一个未结束房间
- 网页端开自己、开目标、梭哈
- 网页端使用道具
- 网页战报显示
- 群聊简要播报创建、开始、结束
- 酒狐随机事件
- SQLite 保存房间数据

## v0.1.6 更新内容

这是异界战争轮盘网页端的测试整合版本。

### 新增

- 新增独立网页端玩法，游戏主要操作可在网页内完成
- 新增一次性数字密码加入房间
- 新增网页端开自己、开目标、梭哈、使用道具等操作
- 新增网页战报显示，群聊只保留创建、开始、结束等简要播报
- 新增酒狐随机事件，默认触发概率为 30%，可在配置中调整
- 新增 Bot 联动桥接插件，用于创建网页房间、生成加入密码和结束播报

### 调整

- 异界战争玩法已从普通轮盘插件中拆分为独立项目
- 战报改为最新内容显示在上方
- 每个弹仓轮结束后，网页只保留当前弹仓轮战报
- 玩家列表不再显示血量，当前行动玩家区域显示生命值
- 网页轮询频率优化，减少 PHP-CGI 压力

### 道具调整

- 隙间之手改为随机偷取目标 1 个普通道具，并立即发动
- 酒狐委托会向弹仓加入 1 发未知子弹，可能是真弹也可能是假弹

### 修复

- 修复游戏结束后 Bot 可能没有播报胜者的问题
- 修复网页端部分战报刷新体验问题
- 优化 PHP-CGI 长时间运行时的请求压力

## 许可

本项目使用 MIT License。

