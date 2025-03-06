# tencentcloud-cdn-cert-manager

## 项目简介

`tencentcloud-cdn-cert-manager` 是一个基于 Node.js 的工具，旨在简化腾讯云 SSL 证书和 CDN 域名配置的管理。其主要功能包括：

- **上传本地证书**：将本地生成的 SSL 证书上传至腾讯云进行托管。
- **更新 CDN 配置**：根据 SSL 证书的域名信息自定将托管的证书应用到相关的 CDN 域名上。
- **删除陈旧证书**：自动识别并删除与当前证书域名重复的旧证书。

## 功能特点

- 支持从本地加载证书和密钥文件（`fullchain.pem` 和 `privkey.pem`）。
- 自动解析证书信息，包括指纹、域名。
- 与腾讯云 SSL 和 CDN 服务无缝集成。
- 提供域名匹配逻辑，支持通配符域名和多域名证书。
- 删除重复或过期的旧证书，保持证书列表清洁。

## 安装

1. **克隆仓库**：

   ```bash
   git clone https://github.com/inkss/tencentcloud-cdn-cert-manager.git
   cd tencentcloud-cdn-cert-manager
   ```

2. **安装依赖**：

   确保已安装 Node.js（版本 >= 14.0.0），然后运行：

   ```bash
   npm install
   ```

## 使用方法

### 准备工作

1. **证书文件**：
   - 准备好证书文件（`fullchain.pem`）和密钥文件（`privkey.pem`）。
2. **腾讯云密钥**：
   - 获取您的腾讯云 `SecretId` 和 `SecretKey`，可在腾讯云控制台的 [API 密钥管理](https://console.cloud.tencent.com/cam/capi) 页面生成。
   - 需要 SSL 和 CDN 相关的权限授权。

### 配置环境变量

配置环境变量，或者在项目同级目录下创建 `.env` 文件并配置。

```sh .env
# 腾讯云配置
TENCENT_SECRET_ID=YOUR_SECRET_ID
TENCENT_SECRET_KEY=YOUR_SECRET_KEY
```

### 运行脚本

1. **基本用法**：

   ```sh
   node ./main.js ./cert
   ```

   - `./certs` 是包含 `fullchain.pem` 和 `privkey.pem` 的目录。
   - 如果不提供路径，默认使用 **工作目录**。

2. **示例输出**：

   ```text
   证书解析成功：{SAN: example.com,*.example.com, FP: XXXXXXXXXXXXXXXXXXXXXXXXXXXX}
   上传新证书，ID: XXXXXXX1
   域名 [example.com] 证书更新成功
   域名 [www.example.com] 证书更新成功
   删除证书: XXXXXXX2，域名: example.com
   ```

3. **与 1Panel 配置**：

   - 勾选【推送证书到本地目录】

   - 勾选【申请证书之后执行脚本】

   - 脚本内容：示例

     ```text
     export PATH=$PATH:/root/.nvm/versions/node/v23.9.0/bin
     node /opt/apps/tencentcloud-cdn-cert-manager/main.js
     ```
