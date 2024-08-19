## 快速开始

开始前需要准备好开发环境，你需要先安装Git、Node.js 和Pnpm。作者使用的版本信息如下：

> 注：从v0.0.7开启，项目的脚手架从webpack迁移到vite

- Git版本：`2.37.2`
- Node版本：`v18.16.1`
- Pnpm版本：`8.6.6`

## 克隆项目

项目在 **_[Github](https://github.com/xiaopujun/light-chaser)_** 和 *
*_[Gitee](https://gitee.com/xiaopujun/light-chaser)_**
上都有托管，你可以选择其中一个进行克隆。进度是同步的。如果你想贡献代码，请fork项目后clone

```bash
# github
git clone https://github.com/xiaopujun/light-chaser.git

# gitee
git clone https://gitee.com/xiaopujun/light-chaser
```

## 安装依赖

进入项目目录，安装依赖

```bash
pnpm install
```

## 启动项目

```bash
pnpm dev
```

启动完毕后访问你本地的`http://localhost:5173`即可看到项目的首页。

## 打包

如果你需要打包项目，可以执行以下命令，执行后会在项目根目录下生成一个`dist`目录，里面是打包后的文件。之后你可以在自己的服务器上进行部署。

```bash
pnpm build
```

