# OpenSSL — 加密与安全通信工具箱

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | OpenSSL Project（开源社区） |
| 首次发布 | 1998 年 |
| 用途 | 加密/解密、证书管理、哈希计算、SSL/TLS 通信调试，网络安全的基础设施 |

## 🎯 用来做什么？

- **生成密钥和证书** — 为 HTTPS 服务创建自签名证书、生成 RSA/EC 密钥对
- **加密解密文件** — 用对称加密算法（AES、ChaCha20）保护文件
- **计算哈希和指纹** — 计算文件的 SHA256/MD5，验证下载文件完整性
- **调试 TLS/SSL 连接** — 检查远程服务器的证书链、协议版本、加密套件

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `openssl version` | 查看 OpenSSL 版本和编译选项 |
| `openssl rand -hex 16` | 生成 16 字节（32 hex 字符）随机数 |
| `openssl genrsa 2048` | 生成 2048 位 RSA 私钥 |
| `openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes` | 创建自签名证书（有效期 365 天） |
| `openssl dgst -sha256 file.txt` | 计算文件的 SHA-256 哈希值 |
| `openssl enc -aes-256-cbc -pbkdf2 -in plain.txt -out encrypted.enc` | 用 AES-256-CBC 加密文件（需输入密码） |
| `openssl base64 -in file.bin` | 对二进制数据进行 Base64 编码 |
| `openssl s_client -connect example.com:443` | 测试远程服务器的 TLS 连接详情 |
| `openssl x509 -in cert.pem -text -noout` | 以人类可读格式输出证书内容 |

## 💡 日常使用示例

### 1. 生成自签名 TLS 证书（开发环境 HTTPS）

```bash
# 一步生成私钥 + 自签名证书，无需交互
openssl req -x509 -newkey rsa:2048 \
  -keyout server.key -out server.crt \
  -days 365 -nodes \
  -subj '/CN=localhost'

# 查看证书详情
openssl x509 -in server.crt -text -noout | head -20
```

输出示例：
```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: ...
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = localhost
        Validity
            Not Before: Jun  2 17:01:28 2026 GMT
            Not After : Jun  2 17:01:28 2027 GMT
        Subject: CN = localhost
```

### 2. 文件加密与解密

```bash
# 加密文件（需要输入密码）
echo "这是机密的配置文件内容" > secret.txt
openssl enc -aes-256-cbc -pbkdf2 -salt \
  -in secret.txt -out secret.txt.enc
# 会提示 Enter password:
# 注意：-pbkdf2 使用基于密码的密钥派生（推荐，比 -md md5 更安全）

# 解密文件
openssl enc -aes-256-cbc -pbkdf2 -d \
  -in secret.txt.enc -out secret_decrypted.txt
cat secret_decrypted.txt
# 输出：这是机密的配置文件内容

# 用 base64 编码输出（方便文本传输）
openssl enc -aes-256-cbc -pbkdf2 -a \
  -in secret.txt -out secret.txt.b64
cat secret.txt.b64
# 输出类似：U2FsdGVkX1/...
```

### 3. 验证下载文件的完整性

```bash
# 很多官方网站提供 SHA256 校验值，用 OpenSSL 验证
# 假设下载了一个文件 file.iso，官方给了 sha256.txt

# 计算文件 SHA256
openssl dgst -sha256 file.iso
# 输出：SHA2-256(file.iso)= abcdef1234567890...

# 对比官方提供的值是否一致
echo "abcdef1234567890...  file.iso" | openssl dgst -sha256 -c
# 如果匹配，输出类似：(stdin)= abcdef1234567890...

# 更常见的做法：使用 shasum 等工具
# 但 openssl 同样能胜任
```

## 🚨 常见坑点

| 问题 | 原因 | 解决方法 |
|------|------|----------|
| `unable to write 'random state'` | 随机状态文件权限问题 | 不用管，不影响实际功能；或设置 `RANDFILE` 环境变量 |
| 解密报错 `bad decrypt` | 密码错误，或加密/解密参数不匹配（比如开了 -pbkdf2 解密忘记加） | 检查密码和解密参数是否与加密时完全一致 |
| `verify error:num=19:self-signed certificate in chain` | 自签名证书不被系统信任 | 开发环境加 `-noverify` 或导入到信任列表；生产环境用 CA 签发证书 |
| 证书过期 | 忘了续期 | `openssl x509 -in cert.pem -dates -noout` 查看有效期 |
| macOS 上 `openssl` 是 LibreSSL | macOS 自带的是 LibreSSL（命令兼容但功能少一些） | `brew install openssl` 安装完整版，然后用 `$(brew --prefix openssl)/bin/openssl` |

## 🔗 参考链接

- [OpenSSL 官方文档](https://www.openssl.org/docs/)
- [OpenSSL 命令参考](https://www.openssl.org/docs/man3.0/man1/)
- [SSL Labs 证书检查](https://www.ssllabs.com/ssltest/)