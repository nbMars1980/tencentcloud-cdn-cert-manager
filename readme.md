# tencentcloud-cdn-cert-manager

## 项目简介

本项目是一个基于 Node.js  的工具，旨在简化腾讯云 SSL 证书和 CDN 域名配置的管理。

- **上传本地证书**：将本地 SSL 证书上传至腾讯云进行托管（禁用过期证书上传）。
- **更新 CDN 配置**：根据证书主题和可选名称信息，自动将托管证书应用到对应域名。
- **支持泛域名证书识别**：自动识别并更新所有符合层级的 CDN 域名。
- **清理旧证书**：自动识别并删除与当前证书主题相同的重复或过期旧证书。

## 处理流程

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
TENCENT_SECRET_ID=您的 SecretId
TENCENT_SECRET_KEY=您的 SecretKey
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
     node /opt/apps/tencentcloud-cdn-cert-manager/main.js
     ```
   - 1Panel 示例：  
     ![配置示例](img/1panel.png)
3. **示例输出**：
   
   ```text
   证书解析成功：{
    主题：example.com
    可选名称: example.com, *.example.com
    指纹: B60B45C0D9759F184CAF64DD2A68EDFB2CBD12A2
    过期时间：May 16 08:13:44 2025 GMT
    剩余日期：66
   }
   上传新证书，ID: XXXXXXX1
   域名 [example.com] 更新成功
   域名 [web.example.com] 更新成功
   删除旧证书: XXXXXXX2 (主题: example.com)
   ```

## 注意事项

- 若证书解析并上传成功，程序将依据证书中允许的域名信息，自动更新对应 CDN 域名的 HTTPS 证书配置。
- 对于泛域名证书，程序会自动调整所有符合匹配规则的 CDN 域名，即便这些域名当前使用的证书有效期更长。
- 请再次注意，在更新 CDN 配置时，程序不会检查原证书文件与新证书文件的时间差，而是直接进行覆盖。
- 唯一值得欣慰的是（😀），在删除旧证书时，程序绝不会移除有效期晚于新证书的证书。
- 此外，程序仅处理来源为 *上传证书* 的证书，确保操作范围可控。
