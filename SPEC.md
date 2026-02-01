# Best100 講師評鑑平台 - 系統規格

## 專案概述

**目標**：將現有的靜態講師推薦名單，升級為互動式評鑑平台

**核心價值**：
- 讓好講師被看見
- 讓企業 HR 有可靠的選擇依據
- 讓講師展現專業形象

---

## 使用者角色

### 1. 訪客（Guest）
- 瀏覽講師列表
- 搜尋/篩選講師
- 查看講師公開資料和評價

### 2. HR 用戶
- 所有訪客功能
- 評價講師（需驗證身份）
- 查看自己的評價歷史
- 收藏講師

### 3. 講師用戶
- 認領/建立個人頁面
- 更新個人資料、專長、經歷
- 查看收到的評價
- 回覆評價

### 4. 管理員
- 審核講師資料
- 驗證 HR 身份
- 管理評價內容
- 數據分析

---

## 功能模組

### Phase 1（MVP）

#### 1.1 講師列表
- 搜尋（姓名、課程主題）
- 篩選（分數區間、課程類型）
- 排序（推薦分數、最新評價）
- 分頁

#### 1.2 講師個人頁
- 基本資料（姓名、頭像、簡介）
- 專長主題
- 所有評價
- 推薦分數

#### 1.3 HR 評價功能
- 選擇課程主題
- 填寫評價內容
- 評分（1-10 分）
- 推薦指數

#### 1.4 用戶系統
- 註冊/登入
- 角色區分（HR/講師）
- 個人資料管理

### Phase 2

#### 2.1 進階篩選
- 課程類型細分
- 預算範圍
- 企業規模偏好
- 地區

#### 2.2 身份驗證
- HR 公司 email 驗證
- 講師身份認證
- 評價可信度標記

#### 2.3 互動功能
- 講師回覆評價
- HR 追蹤講師
- 通知系統

### Phase 3

#### 3.1 課程媒合
- 課程需求發布
- 講師報價
- 媒合配對

#### 3.2 數據分析
- 講師排行榜
- 熱門課程趨勢
- 企業使用報告

---

## 技術架構

### 前端
- **框架**：Next.js 14（React）
- **樣式**：Tailwind CSS
- **狀態管理**：Zustand
- **部署**：Zeabur / Vercel

### 後端
- **框架**：Node.js + Express 或 Next.js API Routes
- **資料庫**：PostgreSQL（Supabase）
- **認證**：NextAuth.js
- **檔案存儲**：Cloudflare R2

### 資料庫設計

```sql
-- 講師表
CREATE TABLE instructors (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255),
    avatar_url TEXT,
    bio TEXT,
    total_score INT DEFAULT 0,
    review_count INT DEFAULT 0,
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- 專長主題
CREATE TABLE topics (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    category VARCHAR(50)
);

-- 講師-主題關聯
CREATE TABLE instructor_topics (
    instructor_id INT REFERENCES instructors(id),
    topic_id INT REFERENCES topics(id),
    PRIMARY KEY (instructor_id, topic_id)
);

-- 評價
CREATE TABLE reviews (
    id SERIAL PRIMARY KEY,
    instructor_id INT REFERENCES instructors(id),
    reviewer_id INT REFERENCES users(id),
    topic_id INT REFERENCES topics(id),
    score INT CHECK (score >= 1 AND score <= 10),
    content TEXT NOT NULL,
    company VARCHAR(100),
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW()
);

-- 用戶
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(100),
    role VARCHAR(20) CHECK (role IN ('hr', 'instructor', 'admin')),
    company VARCHAR(100),
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW()
);
```

---

## 資料遷移計畫

### 現有資料
- 444 位講師
- 推薦分數
- 課程主題
- 評價內容

### 遷移步驟
1. 從現有網站擷取資料
2. 清洗和標準化
3. 匯入新資料庫
4. 建立主題分類

---

## 開發時程

| 階段 | 時間 | 交付物 |
|------|------|--------|
| Phase 1 MVP | 2-3 週 | 可用的基本平台 |
| Phase 2 | 2 週 | 進階功能 |
| Phase 3 | 3 週 | 完整平台 |

---

## 待確認事項

1. **域名**：使用 best100.com.tw 還是新域名？
2. **現有資料授權**：可否使用現有推薦資料？
3. **HR 驗證機制**：如何確認 HR 身份？
4. **商業模式**：免費？講師付費？企業訂閱？
5. **技術偏好**：有偏好的技術棧嗎？
