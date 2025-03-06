# tencentcloud-cdn-cert-manager

## 项目简介

`tencentcloud-cdn-cert-manager` 是一个基于 Node.js 的工具，旨在简化腾讯云 SSL 证书和 CDN 域名配置的管理。其主要功能包括：

- **上传本地证书**：将本地生成的 SSL 证书上传至腾讯云进行托管。
- **更新 CDN 配置**：将托管的证书应用到相关的 CDN 域名。
- **删除陈旧证书**：自动识别并删除与当前证书域名重复的旧证书。

这个工具适用于需要自动化管理腾讯云 CDN 证书的开发者或运维人员，帮助减少手动操作，提高效率。

## 功能特点

- 支持从本地加载证书和密钥文件（`fullchain.pem` 和 `privkey.pem`）。
- 自动解析证书信息，包括指纹、过期时间和域名。
- 与腾讯云 SSL 和 CDN 服务无缝集成。
- 提供域名匹配逻辑，支持通配符域名（例如 `*.example.com`）。
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
   - 准备好您的证书文件（`fullchain.pem`）和密钥文件（`privkey.pem`）。
   - 将它们放入一个目录（例如 `./certs`）。

2. **腾讯云密钥**：
   - 获取您的腾讯云 `SecretId` 和 `SecretKey`，可在腾讯云控制台的 [API 密钥管理](https://console.cloud.tencent.com/cam/capi) 页面生成。

### 配置环境变量

根据您的操作系统，设置以下环境变量：

#### Windows
- **通过命令行临时设置**：
  ```cmd
  set TENCENT_SECRET_ID=您的SecretId
  set TENCENT_SECRET_KEY=您的SecretKey
  ```
- **永久设置**（推荐）：
  1. 右键“此电脑” -> “属性” -> “高级系统设置” -> “环境变量”。
  2. 在“系统变量”或“用户变量”中点击“新建”：
     - 变量名：`TENCENT_SECRET_ID`，变量值：`您的SecretId`。
     - 变量名：`TENCENT_SECRET_KEY`，变量值：`您的SecretKey`。
  3. 保存后重启命令行窗口。

#### Linux
- **通过命令行临时设置**：
  ```bash
  export TENCENT_SECRET_ID=您的SecretId
  export TENCENT_SECRET_KEY=您的SecretKey
  ```
- **永久设置**（推荐）：
  1. 编辑用户环境变量文件，例如 `~/.bashrc` 或 `~/.bash_profile`：
     ```bash
     nano ~/.bashrc
     ```
  2. 在文件末尾添加：
     ```bash
     export TENCENT_SECRET_ID=您的SecretId
     export TENCENT_SECRET_KEY=您的SecretKey
     ```
  3. 保存并应用：
     ```bash
     source ~/.bashrc
     ```

### 运行脚本

1. **基本用法**：
   将证书路径作为参数传递给脚本：
   ```bash
   npm start -- ./certs
   ```
   - `./certs` 是包含 `fullchain.pem` 和 `privkey.pem` 的目录。
   - 如果不提供路径，默认使用脚本所在目录。

2. **示例输出**：
   ```
   证书加载成功。
   证书解析成功：域名: example.com，SAN: example.com,www.example.com
   找到匹配证书，ID: cert-abc123
   域名 [example.com] 证书更新成功
   域名 [www.example.com] 证书更新成功
   删除证书: cert-old456，域名: example.com
   ```

## 代码结构

- **`index.js`**：主脚本，包含 `CertUpdater` 类和入口函数。
- **`CertUpdater` 类**：
  - `loadCertificateAndKeyFiles`：加载本地证书和密钥。
  - `parseCertificateInfo`：解析证书信息。
  - `getOrUploadCertificate`：查找或上传证书。
  - `updateDomainCerts`：更新 CDN 域名配置。
  - `deleteOldCertificates`：删除陈旧证书。

## 注意事项

- 确保证书文件格式正确（PEM 格式），否则可能抛出解析错误。
- 检查腾讯云 API 的频率限制（默认每秒 10 次请求），避免短时间内频繁调用。
- 如果证书关联了其他云资源，删除操作可能会失败，需手动处理。

## 许可证

本项目采用 [MIT 许可证](LICENSE)。详情请查看 `LICENSE` 文件。
