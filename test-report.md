# Swagger Petstore 接口测试报告

## 1. 测试概述
- **被测系统**：Swagger Petstore API v2
- **测试工具**：Postman
- **测试人员**：agill
- **测试时间**：2026.09.28

## 2. 测试范围
用户模块（创建/查询/登录/更新/删除）、宠物模块（新增/查询/更新/删除）

## 3. 测试结果汇总

| 接口 | 方法 | 断言数 | 结果 |
|------|------|--------|------|
| Create user | POST | 2 | Pass |
| Get user by username | GET | 2 | Pass |
| Login | GET | 2 | Pass |
| Add pet | POST | 4 | Pass |
| Find pet by ID | GET | 4 | Pass |
| Find by status | GET | 3 | Pass |
| Update pet | PUT | 3 | Pass |
| Delete pet | DELETE | 2 | Pass |
| **合计** | | **22** | **100%** |

## 4. 断言设计说明
采用四层校验：HTTP状态码 → Content-Type → 响应体字段结构 → 业务数据一致性。

## 5. 异常场景验证
- 查询不存在的宠物ID（999999999），接口正确返回 404
- DELETE 不存在的宠物，接口正确返回 404

## 6. 接口问题发现
- 部分接口的错误响应未返回结构化业务错误码，仅依赖 HTTP 状态码
- 不同接口的错误响应体格式不一致

## 7. 测试结论
核心接口功能正常，断言全部通过。异常场景处理正确，错误响应规范性有优化空间。
```
