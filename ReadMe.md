数据库设计
基于功能需求设计数据库，初期使用关系型数据库（如MySQL或PostgreSQL）更合适，后期根据流量需求可以引入NoSQL（如MongoDB）用作缓存。

数据库结构设计
用户表（users）
存储用户基本信息和权限状态。
sql

Copy
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100),
    password_hash VARCHAR(255) NOT NULL,
    role ENUM('student', 'teacher', 'admin') DEFAULT 'student',
    subscription_status ENUM('free', 'premium') DEFAULT 'free',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
题目表（questions）
存储题目信息，包括原题和解析。
sql

Copy
CREATE TABLE questions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title TEXT NOT NULL,
    content TEXT NOT NULL,
    topic_tags VARCHAR(255), -- 题目分类标签
    difficulty ENUM('easy', 'medium', 'hard'),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
OCR记录表（ocr_records）
存储用户上传的图片OCR结果。
sql

Copy
CREATE TABLE ocr_records (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    image_url VARCHAR(255) NOT NULL, -- 上传图片的存储路径
    ocr_text TEXT NOT NULL, -- OCR识别的文本
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
AI解析记录表（ai_explanations）
存储用户请求AI解析的记录。
sql

Copy
CREATE TABLE ai_explanations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    question_id INT NOT NULL,
    explanation TEXT NOT NULL, -- AI生成的解析
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (question_id) REFERENCES questions(id)
);
支付记录表（payments）
用于记录用户的支付信息。
sql

Copy
CREATE TABLE payments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    payment_method ENUM('wechat', 'alipay'),
    status ENUM('pending', 'completed', 'failed'),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
错题本表（user_mistakes）
用于记录用户的错题。
sql

Copy
CREATE TABLE user_mistakes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    question_id INT NOT NULL,
    mistake_type ENUM('wrong', 'skipped'), -- 错题类型
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (question_id) REFERENCES questions(id)
);



初始页面设计
1. 首页
功能：展示产品核心功能，吸引用户注册。

功能模块：
拍照上传按钮（跳转到OCR页面）。
搜索框（支持直接输入题目文本）。
登录/注册按钮。
2. OCR题目上传页
功能：用户上传题目图片，显示OCR识别结果。

功能模块：
图片上传组件。
OCR识别结果文本框（允许用户手动修改）。
“提交并匹配”按钮。
3. 题目匹配与解析页
功能：展示匹配的题目和AI解析。

功能模块：
匹配的题目标题和内容。
AI生成的解析和答案。
“解锁高级解析”按钮（付费功能入口）。
4. 用户中心页
功能：展示用户的学习记录、错题本等。

功能模块：
学习进度统计。
错题本列表。
订阅状态查看与升级。


### 项目技术选型和工具推荐

根据你的需求和开发计划，以下是推荐的技术选型和工具配置。

---

### **技术选型**

#### **前端（基于Vue 3 + JavaScript）**
- **框架**：Vue 3（简单易用，支持响应式开发，生态完善）。
- **状态管理**：Pinia（Vue官方推荐，替代Vuex，轻量且现代）。
- **样式库**：Tailwind CSS（快速构建响应式UI）。
- **构建工具**：Vite（高效的前端开发工具，适合Vue项目）。
- **路由管理**：Vue Router（官方路由管理工具）。
- **组件库**：Element Plus（成熟的Vue 3组件库，提供表单、弹窗等常用组件）。

#### **后端**
- **语言选择**：
  - **Python**：推荐使用 **FastAPI**（轻量、性能优越，支持异步开发）。
  - **Node.js**：如果团队熟悉JavaScript，可以使用 **Express.js** 或 **Koa.js**。
  - **Go**：适合高性能需求，但开发效率比Python稍低。
  - **C++**：不推荐，适合底层服务和算法实现，但开发效率低。
- **推荐方案**：采用 **Python + FastAPI**，快速开发API服务，同时适合集成OCR和AI服务。

#### **数据库**
- **主数据库**：PostgreSQL（支持关系型数据，适合存储题目和用户信息）。
- **全文搜索引擎**：Elasticsearch（如果需要支持复杂题目搜索）。
- **缓存**：Redis（提升查询性能，优化高频数据访问）。

#### **AI服务**
- 使用第三方AI和OCR服务：
  - **OCR服务**：Mistral OCR（开源）或百度OCR API（商用）。
  - **AI服务**：OpenAI API（答案生成和讲解），可选Claude或Hugging Face Transformers。

#### **支付功能**
- 使用官方SDK：
  - 微信支付：官方开发文档提供SDK。
  - 支付宝支付：使用支付宝开放平台SDK。
  - 第三方聚合服务：如Ping++，简化支付集成。

---

### **工具与平台**

#### **开发工具**
- **代码编辑器**：VS Code（支持前后端开发，插件丰富）。
- **接口测试**：Postman（测试API接口，验证后端逻辑）。
- **UI设计**：Figma（设计初版UI，确保用户体验）。
- **版本控制**：Git + GitHub/GitLab（管理代码和协作）。

#### **部署工具**
- **前端部署**：
  - **Vercel** 或 **Netlify**：快速部署静态前端应用。
- **后端部署**：
  - **Render**：支持免费层，适合小型项目。
  - **Docker**：使用容器化部署，保证环境一致性。
- **云服务**：
  - 阿里云/腾讯云：提供OCR服务、数据库和支付集成。
  - **Firebase**：适合快速实现用户认证和实时数据库需求。

---

### **目录结构**

以下是推荐的目录结构，适用于你的项目需求：

```
project-root/
│
├── client/                     # 前端代码
│   ├── public/                 # 静态资源（图标、图片等）
│   ├── src/                    # 核心代码
│   │   ├── components/         # 公共组件
│   │   ├── pages/              # 页面组件
│   │   ├── services/           # API请求封装
│   │   ├── utils/              # 工具函数
│   │   ├── App.vue             # 应用主入口
│   │   └── main.js             # 前端入口文件
│   └── package.json            # 前端依赖
│
├── server/                     # 后端代码
│   ├── controllers/            # 业务逻辑控制器
│   ├── routes/                 # 路由定义
│   ├── services/               # 第三方服务集成（OCR、AI、支付）
│   ├── models/                 # 数据模型
│   ├── utils/                  # 工具函数
│   ├── app.py                  # 服务入口文件
│   └── requirements.txt        # 后端依赖
│
├── .gitignore                  # Git忽略规则
├── README.md                   # 项目说明
└── docker-compose.yml          # 容器编排文件
```

---

### **需要安装的工具和依赖**

#### **前端依赖**
- Vue 3 项目初始化：
  ```bash
  npm init vue@latest
  ```
- 项目所需依赖：
  ```bash
  npm install pinia vue-router tailwindcss axios
  npm install -D vite
  ```

#### **后端依赖**
- Python + FastAPI 环境：
  ```bash
  pip install fastapi uvicorn
  pip install pydantic[dotenv] # 配置管理
  pip install requests         # 调用OCR和AI API
  pip install sqlalchemy       # 数据库操作
  pip install psycopg2         # PostgreSQL驱动
  ```
- 微信和支付宝支付SDK：
  ```bash
  pip install wechatpy alipay-sdk-python
  ```

#### **数据库工具**
- PostgreSQL 安装：
  ```bash
  sudo apt update
  sudo apt install postgresql postgresql-contrib
  ```

#### **其他工具**
- **Docker**：用于开发和生产环境的服务容器化。

---

### **部署计划**

#### **前端部署**
- 使用 **Vercel** 或 **Netlify**：
  - 部署命令：
    ```bash
    npm run build
    # 上传 dist 文件夹到 Vercel/Netlify
    ```

#### **后端部署**
- 使用 **Render** 或 **Docker**：
  - 单独运行后端服务：
    ```bash
    uvicorn server.app:app --host 0.0.0.0 --port 8000
    ```
  - Docker 部署：
    创建 `Dockerfile`：
    ```dockerfile
    FROM python:3.9-slim
    WORKDIR /app
    COPY ./server /app
    RUN pip install -r requirements.txt
    CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
    ```
    构建和启动容器：
    ```bash
    docker build -t ai-helper-backend .
    docker run -p 8000:8000 ai-helper-backend
    ```

#### **数据库部署**
- 使用阿里云/腾讯云的 RDS 或本地 PostgreSQL。

---

### **开发阶段优先任务（两周MVP）**

1. **前端**：
   - 构建首页、OCR上传页面和题目解析页面。
   - 与后端集成OCR和AI解析功能。
   - 实现用户注册/登录功能。

2. **后端**：
   - 搭建基础API（OCR上传/题目匹配/AI解析）。
   - 集成 DeepSeek API 和 OpenAI API。
   - 实现支付功能（高级解析解锁）。

3. **数据库**：
   - 创建用户表、题目表、OCR记录表和支付记录表。
   - 测试数据读写和API接口。

4. **测试与部署**：
   - 使用Postman测试后端API。
   - 部署前端至Vercel，后端至Render。

---

### **总结**

- **技术选型**：前端推荐 **Vue 3 + Tailwind CSS**，后端推荐 **Python + FastAPI**，数据库使用 **PostgreSQL**。
- **开发工具**：VS Code、Postman、Figma、Docker。
- **部署平台**：Vercel/Netlify（前端），Render/Docker（后端）。
- **两周MVP目标**：实现OCR题目识别、题目匹配、AI解析和支付功能。

通过上述技术选型和工具配置，可以快速构建基础功能，同时为后续扩展打下良好的基础。