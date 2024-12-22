# Magic Link API 文档
> 🌏 [English translation of this document](API-documentation.md)

> ❗ 请确保您所部署的 Magic Link 的版本在 [Commit-cac2f3f4ba45b987cf8952ea405d9764c66b4c39](https://github.com/lilac-milena/Magic-Link/commit/cac2f3f4ba45b987cf8952ea405d9764c66b4c39) 后，只有该版本后的代码才适配了该文档所描述的新版鉴权方式。

> 👀 Magic Link 为开发者提供了 增、查、删 三个 API 接口以帮助开发者快速地将 Magic Link 接入其他应用程序  
> Magic Link 的所有 API 请求均通过 “GET” 方式，该文档将指导您根据您拼接出 API URI。  

## 【必看】鉴权部分
Magic Link 同大部分应用程序一样，API 的鉴权参数在请求头（Header）中进行传递：
|  参数名   | 解释  |
|  ----  | ----  |
| type  | 鉴权方式，对于一般用户而言，应该填写 "session" |
| session  | 鉴权密钥（Session） |

> Magic-Link 之所以引入 "type" 参数是为了方便开发者后续自行接入第三方认证系统

示例请求头:
```
type: session
session: YourSession
```

## 新建（增）
### 简介
Host：/admin/api/create  
方式：GET  
请求头：请附带鉴权请求头  
GET参数：
|  参数名   | 解释 | 备注  |
|  ----  | ----  | ----  |
| to  | 长链接（要转换的链接） | 1. 须以 http:// https:// mailto:// 或 ftp:// 开头，2. **需转换为 Base64 编码** |
| path  | 短链接（转换后的链接） 路径 | **可空**，需以正斜杠(/)开头，留空则随机生成（会保证不与已存在的 path 相冲突），如（不留空）指定的 path 已经存在，则不会继续新建 |

### 示例 cURL：
``` bash
curl --location 'https://example.com/admin/api/create?to=aHR0cHM6Ly9nb29nbGUuY29tLw%3D%3D' \
--header 'type: session' \
--header 'session: YourSession'
```

## 删除（删）
### 简介
Host: /admin/api/delete  
方式： GET  
请求头：请携带鉴权请求头
GET 参数：
|  参数名   | 解释 | 备注  |
|  ----  | ----  | ----  |
| path | 要删除的链接 | **以正斜杠(/)开头** |

### 示例cURL：
``` bash
curl --location 'https://example.com/admin/api/delete?path=%2Fpath' \
--header 'type: session' \
--header 'session: YourSession'
``` 

## 查询（查）
### 简介
Host：/admin/api/getLinkList  
方式： GET  
请求头：请携带鉴权请求头
GET 参数：
|  参数名   | 解释 | 备注  |
|  ----  | ----  | ----  |
| page | 查询页数 | **"0" 为初始页数** |
| pageSize | 单页数量 |  |
| other | 查询限制参数 | **可空**，使用基本 *Mongodb query syntax* 语法，使用详见下文 |

### 示例cURL：
``` bash
curl --location 'https://example.com/admin/api/getLinkList?page=0&pageSize=20' \
--header 'type: session' \
--header 'session: YourSession'
```

### 如何使用查询限制参数（下称查参）：  
查参为一个 JSON 字段，用于在 Mongodb 数据库查询时限定查询内容：，以下为几个示例：  
``` JSON
// 查询指向 https://google.com/ 的链接
{
    "to":"https://google.com/"
}
```
``` JSON
// 查询路径为 /google 的链接
{
    "path":"/google"
}
```
``` JSON
// 查询创建者为 admin 的链接
// 本查询示例中的 creater 字段拼写错误是历史遗留问题，因考虑到数据库结构跨版本兼容性，故没有修改。
{
    "creater":"admin"
}
```