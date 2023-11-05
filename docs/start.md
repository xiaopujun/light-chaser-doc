# 快速开始

开始前需要准备好开发环境，你需要先安装git、node.js 和yarn（可选）。作者使用的版本信息如下：

- git：`2.37.2`
- node版本为：`v16.10.0`
- yarn版本为：`1.22.4`

# 克隆项目

项目在github和gitee上都有托管，你可以选择其中一个进行克隆。进度是同步的。如果你想贡献代码，请fork项目后clone

```bash
# github
git clone https://github.com/xiaopujun/light-chaser.git

# gitee
git clone https://gitee.com/xiaopujun/light-chaser
```

# 安装依赖

进入项目目录，安装依赖

```bash
yarn install
```

# 启动项目

```bash
yarn start
```

启动完毕后访问你本地的`http://localhost:3000`即可看到项目的首页。

# 打包

如果你需要打包项目，可以执行以下命令，执行后会在项目根目录下生成一个`build`目录，里面是打包后的文件。可以在你自己的服务器上部署。

```bash
yarn build
```

