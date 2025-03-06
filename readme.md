# tencentcloud-cdn-cert-manager

## 项目简介

本项目是一个基于 Node.js 的工具，旨在简化腾讯云 SSL 证书和 CDN 域名配置的管理。主要功能包括：

- **上传本地证书**：将本地 SSL 证书上传至腾讯云进行托管。
- **更新 CDN 配置**：根据证书域名信息，自动将托管证书应用到对应 CDN （泛）域名。
- **清理旧证书**：自动识别并删除与当前证书域名重复或过期的旧证书。

## 大致流程

![大致流程](img/export.svg)

## 安装步骤

1. **克隆仓库**：
   ```bash
   git clone https://github.com/inkss/tencentcloud-cdn-cert-manager.git
   cd tencentcloud-cdn-cert-manager
   ```

2. **安装依赖**：
   确保已安装 Node.js（版本 ≥ 14.0.0），然后执行：
   ```bash
   npm install
   ```

## 使用指南

### 前期准备

1. **证书文件**：
   - 准备好 `fullchain.pem`（证书文件）和 `privkey.pem`（密钥文件）。
2. **腾讯云密钥**：
   - 在腾讯云控制台的 [API 密钥管理](https://console.cloud.tencent.com/cam/capi) 页面获取 `SecretId` 和 `SecretKey`。
   - 确保账户具备 SSL 和 CDN 操作权限。

### 配置环境变量

可通过环境变量配置，或在项目根目录创建 `.env` 文件：

```sh .env
# 腾讯云配置
TENCENT_SECRET_ID=您的SecretId
TENCENT_SECRET_KEY=您的SecretKey
```

### 运行脚本

1. **基本用法**：
   ```sh
   node ./main.js ./cert
   ```
   - `./cert` 为包含 `fullchain.pem` 和 `privkey.pem` 的目录。
   - 未指定路径时，默认使用当前工作目录。


2. **配合 1Panel 使用**：
   - 启用【推送证书到本地目录】。
   - 启用【申请证书后执行脚本】，脚本示例：
     ```text
     export PATH=$PATH:/root/.nvm/versions/node/v23.9.0/bin
     node /opt/apps/tencentcloud-cdn-cert-manager/main.js
     ```
3. **示例输出**：
   ```text
   证书解析成功：{SAN: example.com, *.example.com, FP: XXXXXXXXXXXXXXXXXXXXXXXXXXXX}
   上传新证书，ID: XXXXXXX1
   域名 [example.com] 更新成功
   域名 [www.example.com] 更新成功
   删除旧证书: XXXXXXX2 (域名: example.com)
   ```

## 注意事项

- 若证书解析并上传成功，程序将依据证书中允许的域名信息，自动更新对应 CDN 域名的 HTTPS 证书配置。
- 对于泛域名证书，程序会自动调整所有符合匹配规则的 CDN 域名，即便这些域名当前使用的证书有效期更长。
- 请务必注意，程序不会校验证书是否已过期，因此您可能会不慎上传一张已过期的证书至腾讯云。
- 唯一值得欣慰的是（😀），在删除旧证书时，程序绝不会移除有效期晚于新证书的证书。
- 此外，程序仅处理来源为 *上传证书* 的证书，确保操作范围可控。
