# ManXia 图源扩展

## 介绍

本目录包含ManXia漫画阅读器的图源配置文件。这些配置文件定义了如何从各个漫画网站获取数据。

## 已支持的图源

### 1. 拷贝漫画 (CopyManga)
- **API版本**: `copymanga_api.json`
- **WebView版本**: `copymanga_webview.json`
- **特性**: 域名切换、简繁转换、UA轮换

### 2. 哔咔漫画 (PicaComic) ⭐ 新增
- **文件**: `picacomic_api.json`
- **特性**: JWT认证、HMAC-SHA256签名、分流线路
- **要求**: 需要登录账号
- **元数据**: `com.manxia.extension.zh.picacomic.json`

### 3. 其他图源
- 咚漫漫画 (dongmanmanhua.json)
- 禁漫天堂 (jinmantiantang_webview.json)
- Komiic (komiic.json, komiic_api.json, komiic_webview.json)
- 再漫画 (zaimanhua_api.json)

## 哔咔漫画移植说明

哔咔漫画图源是从 [keiyoushi-extensions-source](../keiyoushi-extensions-source/src/zh/picacomic) 的Kotlin实现移植而来。

### 原始Kotlin源位置
```
keiyoushi-extensions-source/src/zh/picacomic/src/eu/kanade/tachiyomi/extension/zh/picacomic/
├── Picacomic.kt          # 主要实现
├── HmacSHA256.kt         # HMAC加密
├── ChannelDns.kt         # DNS解析
└── PicaApiSchemas.kt     # API数据结构
```

### 新增通用能力

为支持哔咔漫画，在框架中新增了以下**通用工具类**：

1. **CryptoUtils.ets** (`entry/src/main/ets/Utils/CryptoUtils.ets`)
   - HMAC-SHA256加密
   - 随机字符串生成
   - Base64编解码
   - MD5哈希

2. **JWTUtils.ets** (`entry/src/main/ets/Utils/JWTUtils.ets`)
   - JWT令牌解析
   - 过期时间验证
   - 用户信息提取

3. **SignatureHandler.ets** (`entry/src/main/ets/Framework/Source/SignatureHandler.ets`)
   - API请求签名生成
   - 支持多种签名算法
   - 自动时间戳和nonce

4. **AuthenticationHandler.ets** (`entry/src/main/ets/Framework/Source/AuthenticationHandler.ets`)
   - JWT令牌管理
   - 自动令牌验证
   - 认证头构建

### 兼容性保证

✅ **所有新增工具类都是可选的**
- 不影响现有图源运行
- 通过配置项启用
- 向后兼容

### 详细文档

完整的实现文档请查看: [PICACOMIC_IMPLEMENTATION.md](../sources/PICACOMIC_IMPLEMENTATION.md)

## 图源配置格式

所有图源配置文件都使用JSON格式，包含以下主要部分：

```json
{
  "metadata": {
    "id": "source_id",
    "name": "图源名称",
    "version": "1.0.0",
    "language": "zh-CN",
    "baseUrl": "https://example.com"
  },
  "capabilities": {
    "urlResolver": true,
    "pagination": true,
    "loginSupport": false
  },
  "network": {
    "userAgent": "...",
    "headers": {},
    "timeout": 30000
  },
  "workflows": {
    "popular": { /* 热门漫画 */ },
    "latest": { /* 最新更新 */ },
    "search": { /* 搜索 */ },
    "getMangaDetail": { /* 漫画详情 */ },
    "getChapterList": { /* 章节列表 */ },
    "getPageList": { /* 页面列表 */ }
  }
}
```

## 添加新图源

### 方法1: 从Kotlin源移植

1. 分析Kotlin源的核心功能
2. 识别需要的新能力（加密、认证等）
3. 在框架中添加通用工具类
4. 创建JSON配置文件
5. 测试验证

### 方法2: 直接创建JSON配置

1. 分析目标网站的API
2. 创建JSON配置文件
3. 定义workflows
4. 配置认证和签名（如需要）
5. 测试验证

## 注意事项

⚠️ **重要原则**

1. **不能影响现有图源**: 新增功能必须是可选的
2. **通用性优先**: 新增的工具类应该是通用的，可被其他图源复用
3. **配置驱动**: 尽量通过JSON配置实现功能，减少代码修改
4. **向后兼容**: 保持与现有图源的兼容性

## 参与贡献

1. Fork 本仓库
2. 创建新分支 (`git checkout -b feature/new-source`)
3. 添加新图源配置
4. 提交更改 (`git commit -am 'Add new source'`)
5. 推送到分支 (`git push origin feature/new-source`)
6. 创建 Pull Request

## 许可证

请遵守各个漫画网站的使用条款和版权规定。
