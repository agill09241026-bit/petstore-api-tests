# Swagger Petstore Postman 接口测试项目

> 基于 Swagger Petstore 公开 API，使用 Postman 完成用户与宠物模块核心接口的功能测试，包含完整的业务链路调用与多层级断言验证。

## 📌 项目简介
本项目针对 Swagger Petstore 开放 API 进行接口功能测试。采用 Postman 作为测试工具，不仅验证单接口的正常与异常场景，还通过**环境变量**串联起完整的业务链路：**创建用户 → 查询用户 → 用户登录 → 新增宠物 → 按ID查询 → 按状态查询 → 更新宠物 → 删除宠物**。

## 🧪 测试环境
- **被测系统**：Swagger Petstore API (`https://petstore.swagger.io/v2`)
- **测试工具**：Postman
- **环境变量文件**：`environments/petstore_env.json`
- **测试数据**：通过 Postman 脚本动态生成唯一测试数据（时间戳/随机数），避免数据冲突

## 📂 文件目录说明
```text
petstore-api-tests/
├── collections/
│   └── swagger_petstore_collection.json  # Postman 接口集合（含测试脚本）
├── environments/
│   └── petstore_env.json                 # Postman 环境变量文件
├── screenshots/                          # 测试执行结果截图
│   ├── 01_collection_overview.png
│   ├── 02_post_addPet.png
│   ├── ...
│   └── runner_summary.png
├── test-report.md                        # 独立测试报告
└── README.md                             # 项目说明文档
```

## 🔗 接口执行顺序与依赖说明
测试按照真实业务流执行，后续接口依赖前序接口返回的数据：
1. `POST /user` - 创建用户（生成唯一用户名）
2. `GET /user/{username}` - 根据用户名查询用户（校验数据一致性）
3. `GET /user/login` - 用户登录（获取 Session 信息）
4. `POST /pet` - 新增宠物（**提取 `petId` 存入环境变量 `{{savedPetId}}`**）
5. `GET /pet/{petId}` - 根据 ID 查询宠物（依赖步骤4的 petId）
6. `GET /pet/findByStatus` - 按状态查询宠物列表
7. `PUT /pet` - 更新宠物信息（依赖步骤4的 petId）
8. `DELETE /pet/{petId}` - 删除宠物（依赖步骤4的 petId）

## ✅ 断言设计与测试策略
每个接口的 `Tests` 脚本采用**四层校验**策略，确保接口功能与数据结构的正确性：
- **第一层：HTTP 状态码校验** —— 校验 `200` 或 `404`（异常场景）。
- **第二层：响应头校验** —— 校验 `Content-Type` 是否为 `application/json`。
- **第三层：响应体结构校验** —— 校验关键字段存在性及数据类型（如 `id` 为 number，`name` 为 string）。
- **第四层：业务数据一致性校验** —— 比对返回的 `id`、`name`、`status` 与请求参数是否一致。

**异常场景覆盖**：额外测试了查询不存在的宠物 ID（`999999999`），验证系统正确返回 404 状态码。

## 📊 运行结果
- **执行接口数**：8 个
- **测试断言数**：25+ 条
- **执行结果**：13 个测试请求全部通过，错误数 `0`
- **平均响应时间**：约 522ms
- **异常场景验证**：查询不存在 ID 正确返回 404

### 断言执行结果示例
![新增宠物断言](screenshots/02_post_addPet.png)
![查询宠物断言](screenshots/03_get_queryPetById.png)
![异常场景404](screenshots/12-error-404.png)

### Collection Runner 批量运行总览
![runner_summary](screenshots/runner_summary.png)

## 🚀 复现步骤
1. 导入 `collections/` 下的 Postman 集合文件。
2. 导入 `environments/` 下的环境文件，并在 Postman 右上角选中 `petstore-env` 环境。
3. 打开 Collection Runner，选中上述 8 个接口，迭代次数设置为 `1`。
4. 点击 `Start run` 开始执行，查看运行报告与断言结果。

## 💡 项目亮点与收获
- **接口联调与传参**：通过 `pm.environment.set()` 实现跨接口参数传递，模拟真实的端到端业务流转。
- **动态测试数据**：利用 Pre-request Script 生成时间戳与随机 ID，保证每次运行新增接口的数据唯一性。
- **异常与边界测试**：不仅测试正常流，还覆盖了不存在的 ID 等边界场景，并验证 HTTP 状态码的正确性。
```
