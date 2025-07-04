# 寵物協尋 App - 產品需求文件 (PRD)

## 高層次摘要

### 電梯簡報 (Elevator Pitch)
「一站式寵物協尋與守護平台」- 在寵物走失的黃金72小時內，為心急如焚的飼主與充滿愛心的拾獲者，提供最高效、最溫暖的資訊中心。除了核心協尋功能，整合動物醫院列表和收養小百科，建立完整的寵物守護生態系。

### 問題陳述 (Problem Statement)
解決寵物走失時的「資訊破碎化」問題，讓飼主與拾獲者能在最短時間內建立有效連結，提高寵物回家的成功率。

### 目標受眾 (Target Audience)
- **主要使用者**：寵物飼主、熱心民眾/拾獲者
- **次要使用者**：動物救援組織、獸醫診所

### 獨特賣點 (Unique Selling Point)
- 專注於台灣在地化需求
- 整合協尋、醫療、收養三大功能
- 溫暖、簡潔、可信賴的使用體驗
- 完全免費的核心功能

### 建構平台 (Platforms)
第一階段：Web 網站（快速驗證市場）
未來規劃：iOS 與 Android App

### 地理範圍
台灣全境

### 貨幣化策略 (Monetization)
MVP 版本完全免費，未來考慮：
1. B2B 合作（動物醫院認證標章）
2. 增值服務（強力曝光服務）
3. 社群贊助
4. 周邊商品

---

## 系統架構考量

### 技術堆疊
- **前端**：React
- **後端**：Node.js + Express
- **資料庫**：MongoDB (MongoDB Atlas)
- **雲端服務**：AWS 或 Google Cloud Platform
- **CDN**：CloudFlare 或 AWS CloudFront

### 第三方整合
- **地圖服務**：Google Maps API
- **社群分享**：Facebook SDK、Line Share API
- **通知服務**：SendGrid (Email)、Twilio (SMS)
- **圖片儲存**：AWS S3 或 Google Cloud Storage

### 系統架構圖
```
[使用者] → [React 前端] → [Express API] → [MongoDB]
                ↓
        [第三方服務整合]
        - Google Maps API
        - 社群分享 API
        - 通知服務
        - 圖片儲存服務
```

---

## 功能詳述

### 1. 拾獲通報功能
**技術需求**：
- 多圖片上傳（最多5張，自動壓縮至1MB以下）
- GPS 定位整合
- 表單驗證與資料清理
- 即時預覽功能

**實作細節**：
- 使用 React Hook Form 處理表單狀態
- 整合 Google Maps API 進行地點選擇
- 使用 Multer 處理檔案上傳
- 圖片壓縮使用 Sharp 套件

**資料模型**：
```javascript
{
  _id: ObjectId,
  type: "found", // found | lost
  petInfo: {
    species: String, // 狗、貓、其他
    breed: String,
    color: String,
    size: String, // 小型、中型、大型
    gender: String,
    age: String,
    specialFeatures: String
  },
  location: {
    address: String,
    coordinates: [Number, Number], // [lng, lat]
    district: String
  },
  images: [String], // S3 URLs
  description: String,
  contactInfo: {
    name: String,
    phone: String,
    email: String,
    preferredContact: String,
    isPublic: Boolean
  },
  status: String, // active | resolved | expired
  createdAt: Date,
  updatedAt: Date,
  expiresAt: Date // 30天後自動過期
}
```

### 2. 走失尋找功能
**技術需求**：
- 多條件篩選系統
- 地圖模式瀏覽
- 關鍵字搜尋
- 分頁載入

**實作細節**：
- 使用 MongoDB 的地理空間查詢
- 實作 Elasticsearch 進行全文搜尋
- 使用 React Virtualized 優化大量資料渲染

### 3. 留言板/線索區
**技術需求**：
- 即時留言系統
- 留言審核機制
- 推播通知

**實作細節**：
- 使用 Socket.io 實現即時通訊
- 實作留言過濾機制防止垃圾訊息
- 整合 FCM 推播通知

### 4. 動物醫院列表
**技術需求**：
- 地理位置搜尋
- 營業時間顯示
- 一鍵導航
- 評分系統

**資料模型**：
```javascript
{
  _id: ObjectId,
  name: String,
  address: String,
  coordinates: [Number, Number],
  phone: String,
  website: String,
  businessHours: {
    monday: { open: String, close: String },
    // ... 其他天
  },
  services: [String], // 急診、手術、美容等
  rating: Number,
  reviews: [{
    userId: ObjectId,
    rating: Number,
    comment: String,
    createdAt: Date
  }]
}
```

### 5. 收養小百科
**技術需求**：
- CMS 內容管理系統
- 文章分類與標籤
- 搜尋功能
- SEO 優化

---

## API 端點設計

### 協尋案件相關
```
POST /api/cases - 建立新案件
GET /api/cases - 取得案件列表（支援篩選）
GET /api/cases/:id - 取得特定案件詳情
PUT /api/cases/:id - 更新案件狀態
DELETE /api/cases/:id - 刪除案件

POST /api/cases/:id/comments - 新增留言
GET /api/cases/:id/comments - 取得案件留言
```

### 動物醫院相關
```
GET /api/hospitals - 取得醫院列表
GET /api/hospitals/:id - 取得醫院詳情
POST /api/hospitals/:id/reviews - 新增評論
```

### 使用者相關
```
POST /api/auth/register - 使用者註冊
POST /api/auth/login - 使用者登入
GET /api/users/profile - 取得使用者資料
PUT /api/users/profile - 更新使用者資料
```

---

## 設計系統

### 色彩系統
- **主色調**：溫暖橘色 (#FF8A65) - 代表希望與溫暖
- **輔助色**：柔和藍色 (#64B5F6) - 代表信任與安全
- **中性色**：
  - 深灰 (#424242) - 主要文字
  - 中灰 (#757575) - 次要文字
  - 淺灰 (#F5F5F5) - 背景色
- **狀態色**：
  - 成功：#4CAF50
  - 警告：#FF9800
  - 錯誤：#F44336

### 字體系統
- **主要字體**：Noto Sans TC（Google Fonts）
- **字體大小階層**：
  - H1: 32px (頁面標題)
  - H2: 24px (區塊標題)
  - H3: 20px (小標題)
  - Body: 16px (內文)
  - Caption: 14px (說明文字)

### 間距系統
- 基礎單位：8px
- 間距階層：8px, 16px, 24px, 32px, 48px, 64px

### 組件設計
- **按鈕**：圓角 8px，最小高度 44px
- **輸入框**：圓角 4px，邊框 1px
- **卡片**：圓角 12px，陰影 0 2px 8px rgba(0,0,0,0.1)

---

## 使用者體驗設計

### 關鍵使用者旅程

#### 1. 拾獲寵物通報流程
1. **進入頁面** → 顯示簡潔的引導說明
2. **選擇動物類型** → 大圖示選擇（狗/貓/其他）
3. **上傳照片** → 拖拽上傳，即時預覽
4. **填寫基本資訊** → 智能表單，自動建議
5. **標記地點** → 地圖點選，GPS 定位
6. **聯絡方式** → 隱私設定選項
7. **確認發布** → 預覽頁面，一鍵分享

#### 2. 走失寵物發布流程
（與拾獲流程類似，但增加「最後出現地點」和「走失時間」）

#### 3. 搜尋與篩選體驗
1. **首頁搜尋** → 大搜尋框，熱門標籤
2. **篩選條件** → 側邊欄篩選，即時結果
3. **結果展示** → 卡片式佈局，關鍵資訊突出
4. **地圖模式** → 切換按鈕，標記點群集

### 狀態設計

#### 拾獲通報頁面狀態
- **初始狀態**：顯示引導文字和大型上傳區域
- **上傳中狀態**：進度條和載入動畫
- **驗證錯誤狀態**：紅色邊框和錯誤提示
- **成功狀態**：綠色確認圖示和成功訊息

#### 搜尋結果頁面狀態
- **載入狀態**：骨架屏動畫
- **空結果狀態**：友善的插圖和建議文字
- **錯誤狀態**：重試按鈕和錯誤說明

---

## 安全與隱私考量

### 資料保護
- 使用者可選擇聯絡方式的公開程度
- 敏感資料加密儲存
- 定期資料備份和災難恢復計畫

### 使用者安全
- 相約見面安全提醒
- 舉報和封鎖功能
- 內容審核機制

### 技術安全
- HTTPS 加密傳輸
- JWT Token 身份驗證
- API 速率限制
- SQL 注入防護
- XSS 攻擊防護

---

## 基礎設施與部署策略

### 開發環境
- **版本控制**：Git + GitHub
- **CI/CD**：GitHub Actions
- **測試**：Jest + React Testing Library
- **程式碼品質**：ESLint + Prettier

### 生產環境
- **主機**：AWS EC2 或 Google Cloud Compute Engine
- **資料庫**：MongoDB Atlas
- **CDN**：CloudFlare
- **監控**：New Relic 或 DataDog
- **錯誤追蹤**：Sentry

### 部署流程
1. 開發分支合併至 main
2. 自動化測試執行
3. 建置 Docker 映像
4. 部署至 staging 環境
5. 手動驗證後部署至生產環境
6. 健康檢查和監控

---

## 效能考量

### 前端優化
- 程式碼分割和懶載入
- 圖片壓縮和 WebP 格式
- Service Worker 快取策略
- 關鍵渲染路徑優化

### 後端優化
- 資料庫索引優化
- Redis 快取層
- API 回應壓縮
- 連線池管理

### 預期效能指標
- 首頁載入時間 < 3 秒
- API 回應時間 < 500ms
- 圖片載入時間 < 2 秒
- 搜尋結果回應 < 1 秒

---

## 測試策略

### 單元測試
- React 組件測試
- API 端點測試
- 工具函數測試
- 目標覆蓋率：80%

### 整合測試
- 使用者流程測試
- API 整合測試
- 第三方服務整合測試

### E2E 測試
- 關鍵使用者旅程
- 跨瀏覽器相容性
- 行動裝置響應式測試

---

## 上線計畫

### Phase 1: MVP 發布（月1-3）
- 核心協尋功能
- 基礎搜尋和篩選
- 動物醫院列表
- 使用者註冊登入

### Phase 2: 功能增強（月4-6）
- 即時通訊系統
- 推播通知
- 社群分享功能
- 收養小百科

### Phase 3: 平台優化（月7-12）
- 行動 App 開發
- 進階搜尋功能
- 數據分析儀表板
- 付費增值服務

---

## 成功指標 (KPIs)

### 使用者指標
- 月活躍使用者數 (MAU)
- 使用者留存率
- 平均使用時長

### 業務指標
- 每日新增案件數
- 案件解決率
- 平台分享次數

### 技術指標
- 系統可用性 (99.9%)
- 平均回應時間
- 錯誤率 (<1%)

---

## 風險評估與應對策略

### 技術風險
- **風險**：第三方 API 服務中斷
- **應對**：實作備用方案和優雅降級

### 業務風險
- **風險**：使用者採用率低
- **應對**：積極的行銷推廣和使用者回饋收集

### 法律風險
- **風險**：個資法規遵循
- **應對**：諮詢法律專家，建立完善的隱私政策

---

*此 PRD 文件將隨著專案進展持續更新和優化*