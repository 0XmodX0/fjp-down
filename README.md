# 小飞机网盘直链解析

Cloudflare Worker，将小飞机网盘分享链接 302 重定向到下载直链，无需登录、无需打开网页。

## 部署

1. Cloudflare Dashboard → Workers & Pages → 创建 Worker
2. 粘贴 `worker.js` 全部代码
3. 绑定自定义域名（可选）

## 用法

```
https://your-domain/?url=https://share.feijipan.com/s/XXXXX
```

| 参数 | 必填 | 说明 |
|---|---|---|
| `url` | ✅ | 小飞机网盘分享链接 |
| `idx` | ❌ | 文件序号，1=第一个（默认1） |
| `pwd` | ❌ | 提取码 |

### 示例

```bash
# 默认第一个文件
curl -L "https://fjp.xiaow.qzz.io/?url=https://share.feijipan.com/s/0EdkoxGQ"

# 指定第二个文件
curl -L "https://fjp.xiaow.qzz.io/?url=https://share.feijipan.com/s/0EdkoxGQ&idx=2"

# 带提取码
curl -L "https://fjp.xiaow.qzz.io/?url=https://share.feijipan.com/s/XXX&pwd=1234"
```

## 工作原理

1. 从分享链接提取 `shareId`
2. 调用小飞机 API `/ws/recommend/list` 获取文件列表
3. AES-128-ECB 加密 `fileId|userId` 生成 `downloadId`
4. 请求 `/ws/file/redirect` 获取 302 跳转地址
5. 返回 302 重定向到真实下载直链

AES 密钥通过 [netdisk-fast-download](https://github.com/qaiu/netdisk-fast-download) 逆向获取。

## License

MIT
