# BG Nexus 网站部署包

这是当前确认版本的官网、登录／注册页和 API 文档前端。已包含新的透明底 logo、首页动效、产品能力切换、统一导航和登录注册页面。

**直接部署 `site/` 文件夹的全部内容即可。无需 npm install，也无需重新构建。**

## 直接上传部署

1. 解压本文件。
2. 将 `site/` 内的全部文件和文件夹上传到网站根目录，确保根目录直接包含 `index.html`。
3. 在域名根路径 `/` 访问，服务器需支持目录的 `index.html`。例如 `https://你的域名/login/`。
4. 如使用 Nginx，可参考 `deploy/nginx.conf`，把 `root` 改为实际的网站根目录。

静态托管服务的发布目录填写 `site`，构建命令留空。本包使用以 `/` 开头的站内路径，不支持直接放在 `/某个项目/` 子目录下；请使用独立域名或独立子域名。请通过 HTTP(S) 服务器访问，不要双击 HTML 以 `file://` 方式打开。配置域名、HTTPS 证书和反向代理由部署环境处理。

## Docker 部署

在包含 Dockerfile 的目录运行：

```sh
docker build -t bg-nexus .
docker run -d --name bg-nexus -p 8080:80 --restart unless-stopped bg-nexus
```

访问 `http://服务器地址:8080/`。正式域名可由现有反向代理转发到该端口。镜像构建只复制 `site/` 到网站目录，不公开 `source/`、`tools/` 或本说明。

## 本地预览

安装 Python 3 后，在本目录运行：

```sh
python3 -m http.server 8080 --bind 127.0.0.1 --directory site
```

打开 `http://127.0.0.1:8080/`。Windows 可将 `python3` 换成 `python` 或 `py -3`。

## 页面入口

| 页面 | 路径 |
| --- | --- |
| 首页 | `/` |
| 产品能力 | `/#capabilities` |
| 登录 | `/login/` |
| 注册 | `/register/` |
| API 文档 | `/docs/` |
| 外汇 API 说明 | `/docs/#fx-api` |
| 跨境支付 API | `/docs/#payments-api` |

旧控制台入口会回到站内登录页；旧产品入口回到产品能力区域。页面内导航使用同一域名。

## 文件与修改位置

- `site/index.html`：当前首页源码，包含首页样式和动效。
- `site/site-nav.js`、`site/site-nav.css`：全站共用顶部导航。
- `site/site-routes.js`：站内链接与旧入口映射。
- `site/product-capabilities.js`、`.css`：产品能力、视频占位、API 对应关系；视频可在产品的 `video.src` 中配置。
- `site/login/index.html`、`site/login.css`、`site/login.js`：登录／注册共用模板、样式和前端交互。
- `site/register/index.html`：由登录模板生成的注册页。
- `site/bg-nexus-logo.png`：用户提供的透明底 logo，原图未修改。
- `site/docs/`、`site/static/`：API 文档及其依赖、字体。
- `source/docs-product-pages.js`、`.css`：可编辑的产品 API 文档内容。
- `source/vendor/docs-app.original.js`：导入的文档原始编译资源，只用于重建，不对外部署。
- `tools/rebuild-docs.py`：文档适配过程，移除原站旧首页、登录和控制台路由，保留文档。

首页、导航、产品和账户页面是可直接修改的 HTML/CSS/JavaScript。文档主界面来自现有编译资源，原始 React/TypeScript 工程未包含在此前材料中；本包提供现有资源和可复现的适配脚本。未混入旧版 Vinext 示例工程。

修改登录模板或 `source/docs-product-pages.*` 后，运行：

```sh
python3 tools/rebuild.py
python3 tools/validate.py
```

其余 `site/` 文件可直接编辑并部署。登录与注册共同的模板内容应在 `site/login/index.html` 修改，以免下次重建覆盖注册页单独的修改。

## 当前功能范围

- 官网、动效、产品切换、站内导航、登录注册表单和 API 文档可以直接部署。
- **真实登录、注册、邮件验证码、商户控制台后端及金融业务接口尚未接入。** 表单校验后会明确提示账号服务未连接，不会创建账户、发送或保存密码，也不会跳转到演示后台。
- 接入认证服务时，需要实际接口协议、会话与权限处理，以及与服务端一致的密码规则。当前注册页面的密码检查是前端预览规则。
- 两个产品演示视频仍为占位，外汇专属接口文档待提供；跨境支付页面保留现有接口定义。
- 文档中的示例地址、账号和响应用于说明，不代表生产服务配置。请在实际业务接入时核对服务端提供的 API 地址。

## 验证

本包附有 `tools/validate.py`，可检查页面本地资源、站内路径、当前 logo 引用、导出源文件一致性及旧登录演示逻辑是否重新出现。导出时的检查结果见 `VALIDATION.md`。`SHA256SUMS` 用于核对发布文件。
