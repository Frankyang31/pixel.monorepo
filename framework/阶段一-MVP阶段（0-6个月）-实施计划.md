# 阶段一：MVP 阶段实施计划（0-6 个月）

## 阶段目标

快速验证产品假设，用最小成本验证市场反应。此阶段的核心原则是：**快速上线、避免过度工程、为扩展预留接口**。

---

## 一、技术栈清单

| 模块 | 技术选型 | 选型理由 |
|------|---------|---------|
| 前端框架 | Vue 3 + Nuxt 3 + TypeScript | SSR/SSG 兼顾 SEO，Vue 生态成熟 |
| 后端框架 | FastAPI (Python) | AI 生态首选，性能优秀，异步支持好 |
| 数据库 | PostgreSQL + pgvector | 向量扩展支持 RAG，向后兼容性好 |
| 缓存 | Redis 单节点 | 够用即可，单机 Redis 支撑万级 QPS |
| 任务队列 | Redis + Celery | Python 生态统一，与 FastAPI 无缝集成，运维简单 |
| 对象存储 | Cloudflare R2 | 零出口流量费，成本低 |
| 部署 | Docker Compose | 单机部署足够简单 |

### 开发工具链

| 用途 | 工具 |
|------|------|
| 代码管理 | Git + GitHub |
| CI/CD | GitHub Actions |
| API 文档 | FastAPI 自动生成 Swagger |
| 前端组件库 | Naive UI / Element Plus |
| 状态管理 | Pinia |
| HTTP 客户端 | Axios / ofetch |
| 代码规范 | ESLint + Prettier |

---

## 二、项目结构

```
ai-platform/
├── frontend/                    # Vue 3 + Nuxt 3 前端
│   ├── nuxt.config.ts
│   ├── app.vue
│   ├── pages/                   # 页面
│   │   ├── index.vue            # 首页
│   │   ├── tools/               # 工具列表
│   │   │   ├── index.vue
│   │   │   └── [id].vue         # 工具详情页
│   │   ├── generate/            # 生成结果页
│   │   └── user/                # 用户中心
│   ├── components/              # 组件
│   ├── composables/             # 组合式函数
│   ├── layouts/                 # 布局
│   └── server/                  # Nuxt Server Routes（可选）
│
├── backend/                     # FastAPI 后端
│   ├── app/
│   │   ├── main.py              # 应用入口
│   │   ├── config.py            # 配置管理
│   │   ├── api/                 # API 路由
│   │   │   ├── v1/
│   │   │   │   ├── user.py
│   │   │   │   ├── points.py
│   │   │   │   ├── task.py
│   │   │   │   └── models.py
│   │   ├── core/                # 核心模块
│   │   │   ├── model_gateway/   # 模型网关
│   │   │   │   ├── base.py      # 适配器基类
│   │   │   │   ├── openai.py
│   │   │   │   ├── stable_diffusion.py
│   │   │   │   └── factory.py   # 工厂方法
│   │   │   ├── task_queue/      # 任务队列
│   │   │   └── points/          # 积分模块
│   │   ├── models/              # 数据模型（SQLAlchemy/Pydantic）
│   │   ├── schemas/             # API 请求/响应模型
│   │   └── db/                  # 数据库连接
│   ├── worker/                  # 任务 Worker
│   │   └── main.py
│   ├── tests/
│   └── requirements.txt
│
├── docker-compose.yml           # 容器编排
├── .env.example                 # 环境变量模板
└── README.md
```

---

## 三、前端实施任务

### 3.1 项目初始化

```bash
# 创建 Nuxt 3 项目
npx nuxi@latest init frontend
cd frontend

# 安装依赖
npm install

# 安装常用依赖
npm install @vueuse/core pinia @pinia/nuxt naive-ui

# 安装 SEO 相关
npm install @nuxtjs/sitemap
```

### 3.2 核心页面开发

**优先级 P0（必须上线）**

1. **首页**（SSG + ISR）
   - 平台核心价值主张
   - 热门工具入口
   - 定价/积分说明
   - SEO 优化：标题、描述、Open Graph

2. **工具列表页**（SSG + ISR）
   - 场景分类导航
   - 工具卡片展示
   - SEO 优化：每种场景类型独立页面

3. **工具详情页**（SSR）
   - 工具介绍与功能说明
   - 参数配置表单
   - 历史生成案例展示
   - SEO 优化：结构化数据 Schema

4. **生成结果页**（CSR）
   - 任务进度展示（SSE）
   - 结果预览与下载
   - 分享功能

5. **用户中心**（CSR）
   - 注册/登录
   - 积分余额与流水
   - 会员等级
   - 历史任务记录

6. **定价页面**（SSG）
   - 会员套餐介绍
   - 积分购买选项

### 3.3 SEO 实施清单

```
[ ] nuxt.config.ts 配置
    - site URL 配置
    - sitemap 生成
    - robots.txt
    - OG/Twitter Card 默认配置

[ ] 每个页面 SEO 元数据
    - useSeoMeta() 设置独特标题和描述
    - 结构化数据 JSON-LD

[ ] 技术 SEO
    - 语义化 HTML（header/nav/main/article/section/footer）
    - 图片 alt 属性
    - 链接 title 属性
    - 面包屑导航
```

---

## 四、后端实施任务

### 4.1 项目初始化

```bash
# 创建后端项目
mkdir -p backend && cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 安装依赖
pip install fastapi uvicorn sqlalchemy asyncpg pydantic pydantic-settings
pip install redis celery python-jose passlib
pip install python-multipart aiohttp
pip install httpx  # 用于模型调用
```

### 4.2 核心模块开发

**优先级 P0（必须上线）**

#### 4.2.1 用户与认证模块

- 用户注册/登录 API（支持邮箱、手机）
- JWT Token 认证
- 会员等级管理

#### 4.2.2 积分系统 MVP 版本

```
核心表结构：

users
├── id (PK)
├── email / phone
├── password_hash
├── membership_level (0-3)
├── membership_expires_at
└── created_at

points_accounts
├── id (PK)
├── user_id (FK)
├── balance (DECIMAL)
├── frozen_balance (DECIMAL)
└── version (乐观锁)

points_transactions
├── id (PK)
├── user_id (FK)
├── transaction_id (唯一索引，幂等键)
├── type (EARN/SPEND/REFUND/FREEZE/UNFREEZE)
├── amount (DECIMAL)
├── balance_before
├── balance_after
├── source (MEMBERSHIP/SIGN/ACTIVITY/API_CALL)
├── reference_id (关联业务ID)
└── created_at
```

**简化版幂等性实现**：

```python
# 只实现事务号幂等（其他两重可在增长阶段补充）
# 关键：检查幂等键 + 余额扣减 + 记录流水 必须在同一事务内，防止中途崩溃导致重复扣费
async def deduct_points(user_id: str, amount: int, transaction_id: str):
    async with db.transaction():  # ← 必须包裹事务，三步原子执行
        # 1. 检查事务号是否已使用（幂等键）
        existing = await db.fetch_one(
            "SELECT id FROM points_transactions WHERE transaction_id = :tid",
            {"tid": transaction_id}
        )
        if existing:
            return  # 幂等：重复调用直接返回
        
        # 2. 原子扣减余额（带余额检查，避免透支）
        result = await db.execute(
            """
            UPDATE points_accounts 
            SET balance = balance - :amount 
            WHERE user_id = :user_id AND balance >= :amount
            """,
            {"amount": amount, "user_id": user_id}
        )
        
        if result.rowcount == 0:
            raise InsufficientBalance("积分余额不足")
        
        # 3. 记录流水（与扣减在同一事务内）
        await db.execute(
            """
            INSERT INTO points_transactions 
            (user_id, transaction_id, type, amount, source)
            VALUES (:user_id, :tid, 'SPEND', :amount, 'API_CALL')
            """,
            {"user_id": user_id, "tid": transaction_id, "amount": amount}
        )
```

#### 4.2.3 模型网关 MVP 版本

**适配器接口定义**：

```python
# backend/app/core/model_gateway/base.py
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import AsyncIterator
from enum import Enum

class ModelProvider(Enum):
    OPENAI = "openai"
    STABLE_DIFFUSION = "stable_diffusion"
    TONGYI = "tongyi"

@dataclass
class GenerateRequest:
    task_id: str
    model: str
    prompt: str
    negative_prompt: str = None
    width: int = 1024
    height: int = 1024
    steps: int = 30
    seed: int = None

@dataclass
class GenerateResult:
    task_id: str
    status: str  # completed, failed
    url: str = None  # 生成物 URL
    error: str = None
    metadata: dict = None

class BaseModelAdapter(ABC):
    @abstractmethod
    async def generate(self, request: GenerateRequest) -> GenerateResult:
        """同步生成（适合快速任务）"""
        pass
    
    @abstractmethod
    async def generate_async(self, request: GenerateRequest) -> str:
        """异步生成，返回任务 ID（适合耗时任务）"""
        pass
    
    @abstractmethod
    async def get_result(self, async_task_id: str) -> GenerateResult:
        """查询异步任务结果"""
        pass
    
    @abstractmethod
    def get_model_info(self) -> dict:
        """返回模型基本信息（名称、价格、速率限制等）"""
        pass

# 工厂方法
def get_adapter(provider: ModelProvider) -> BaseModelAdapter:
    adapters = {
        ModelProvider.OPENAI: OpenAIImageAdapter(),
        ModelProvider.STABLE_DIFFUSION: StableDiffusionAdapter(),
        ModelProvider.TONGYI: TongyiImageAdapter(),
    }
    return adapters[provider]
```

**OpenAI 适配器实现示例**：

```python
# backend/app/core/model_gateway/openai.py
import httpx
from .base import BaseModelAdapter, GenerateRequest, GenerateResult, ModelProvider

class OpenAIImageAdapter(BaseModelAdapter):
    BASE_URL = "https://api.openai.com/v1"
    
    def __init__(self, api_key: str):
        self.api_key = api_key
    
    async def generate(self, request: GenerateRequest) -> GenerateResult:
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{self.BASE_URL}/images/generations",
                headers={
                    "Authorization": f"Bearer {self.api_key}",
                    "Content-Type": "application/json"
                },
                json={
                    "model": request.model or "dall-e-3",
                    "prompt": request.prompt,
                    "n": 1,
                    "size": f"{request.width}x{request.height}"
                },
                timeout=60.0
            )
            
            if response.status_code != 200:
                return GenerateResult(
                    task_id=request.task_id,
                    status="failed",
                    error=f"API Error: {response.status_code}"
                )
            
            data = response.json()
            return GenerateResult(
                task_id=request.task_id,
                status="completed",
                url=data["data"][0]["url"]
            )
    
    async def generate_async(self, request: GenerateRequest) -> str:
        # OpenAI DALL-E 不支持异步，返回同步结果
        result = await self.generate(request)
        return result.task_id
    
    async def get_result(self, async_task_id: str) -> GenerateResult:
        # OpenAI 同步返回，无需查询
        pass
    
    def get_model_info(self) -> dict:
        return {
            "name": "DALL-E 3",
            "provider": ModelProvider.OPENAI,
            "cost_per_call": 0.04,  # 美元
            "rate_limit": 50,  # 每分钟
            "supports_negative_prompt": False,
            "max_dimensions": [1024, 1024]
        }
```

#### 4.2.4 任务队列集成

```python
# backend/app/core/task_queue/celery_app.py
from celery import Celery
from redis import Redis

celery_app = Celery(
    "worker",
    broker="redis://localhost:6379/0",
    backend="redis://localhost:6379/1"
)

celery_app.conf.update(
    task_serializer="json",
    accept_content=["json"],
    result_serializer="json",
    timezone="Asia/Shanghai",
    enable_utc=True,
    task_routes={
        "tasks.image.*": {"queue": "image"},
        "tasks.video.*": {"queue": "video"},
        "tasks.llm.*": {"queue": "llm"},
    },
    task_default_queue="default",
    worker_prefetch_multiplier=1,  # AI 任务耗时长，prefetch 1
    task_acks_late=True,  # 任务完成后才 ACK，失败可重试
)
```

**Worker 任务示例**：

```python
# backend/worker/tasks/image_tasks.py
from celery_app import celery_app
from app.core.model_gateway.factory import get_adapter
from app.core.points.service import deduct_points
from app.db.database import get_db

@celery_app.task(name="tasks.image.generate", bind=True, max_retries=5)
def generate_image(self, user_id: str, task_id: str, model: str, params: dict):
    try:
        # 1. 获取适配器
        adapter = get_adapter(model)
        
        # 2. 预扣积分（简化版：直接扣，不预扣）
        deduct_points(user_id, 10, f"DEDUCT:{task_id}")
        
        # 3. 调用模型
        result = adapter.generate(GenerateRequest(
            task_id=task_id,
            **params
        ))
        
        # 4. 更新任务状态
        update_task_status(task_id, "completed", result.url)
        
        return {"status": "completed", "url": result.url}
    
    except RetryableError as e:
        # 指数退避重试
        raise self.retry(exc=e, countdown=2 ** self.request.retries)
    
    except Exception as e:
        # 非重试错误
        update_task_status(task_id, "failed", str(e))
        # 退还积分
        refund_points(user_id, 10, f"REFUND:{task_id}")
        return {"status": "failed", "error": str(e)}
```

---

## 五、部署架构

### Docker Compose 配置

```yaml
# docker-compose.yml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    environment:
      - NUXT_PUBLIC_API_BASE=http://backend:8000
    depends_on:
      - backend
    restart: unless-stopped

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@postgres:5432/aigen
      - REDIS_URL=redis://redis:6379
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    depends_on:
      - postgres
      - redis
    restart: unless-stopped

  worker:
    build: ./backend
    command: celery -A worker.tasks worker --loglevel=info
    environment:
      - DATABASE_URL=postgresql://user:pass@postgres:5432/aigen
      - REDIS_URL=redis://redis:6379
    depends_on:
      - postgres
      - redis
    restart: unless-stopped

  postgres:
    image: postgres:15-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=aigen

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

---

## 六、MVP 阶段检查清单

### 功能完成度

```
[ ] 用户注册/登录（邮箱）
[ ] JWT 认证
[ ] 积分余额查询
[ ] 积分消耗（API 调用）
[ ] 积分获取（签到、注册赠送）
[ ] 工具调用（至少 1 个模型）
[ ] 任务状态查询
[ ] 结果展示与下载
```

### SEO 完成度

```
[ ] 首页 SEO 元数据
[ ] 工具列表页 SEO
[ ] sitemap.xml 生成
[ ] robots.txt 配置
[ ] Google Search Console 接入
```

### 运维就绪度

```
[ ] Docker Compose 一键部署
[ ] .env 环境变量模板
[ ] GitHub Actions CI/CD
[ ] 日志配置（结构化日志）
[ ] 错误监控（可先用 Sentry）
```

---

## 七、MVP 阶段里程碑

| 周数 | 里程碑 |
|------|--------|
| 第 1-2 周 | 项目初始化，Docker Compose 部署跑通 |
| 第 3-4 周 | 用户认证 + 积分 MVP 上线 |
| 第 5-6 周 | 第一个工具（文生图）上线 |
| 第 7-8 周 | 任务队列集成，结果推送 |
| 第 9-10 周 | SEO 优化，页面完善 |
| 第 11-12 周 | 内部测试，Bug 修复 |
| 第 13-24 周 | 小范围公测，收集反馈，快速迭代 |

---

## 八、风险与应对

| 风险 | 影响 | 应对措施 |
|------|------|---------|
| 模型 API 不稳定 | 生成失败率高 | 接入多个模型，降级链路 |
| 并发扣费导致余额错误 | 资金损失 | MVP 先限制单用户并发 |
| SEO 效果不达预期 | 获客困难 | 提前规划内容策略 |
| 冷启动无流量 | 无法验证假设 | 社群冷启动 + SEO 长期投入 |
