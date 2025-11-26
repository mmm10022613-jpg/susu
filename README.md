協發行泡菜 | 線上即時同步訂購單 (Firebase + Tailwind CSS)

這是一個單頁面 (Single HTML file) 應用程式，用於實現一個多人協作、即時同步的線上訂購單。所有訂單資料都會即時寫入 Google Firebase Firestore 資料庫，並在所有使用者的畫面上同步更新。

🛠️ 技術棧

前端: HTML5, Tailwind CSS (CDN), Vanilla JavaScript (ES Modules)

資料庫: Google Firebase Firestore

認證: Firebase Authentication (匿名登入)

部署: 可直接透過 GitHub Pages 部署

🚀 專案啟動與設定

步驟 1: 建立 Firebase 專案

前往 Firebase Console 建立一個新專案。

在專案中啟用 Firestore Database。

在專案中啟用 Authentication，並開啟 匿名 (Anonymous) 登入方法。

步驟 2: 取得 Firebase 設定 (🚨 關鍵步驟：請替換金鑰！)

在 Firebase 專案設定中，找到您的 Web App 設定，複製以下配置資訊。您必須將這些資訊填入 index.html 檔案中 DEFAULT_FIREBASE_CONFIG 的位置。

const DEFAULT_FIREBASE_CONFIG = {
    apiKey: "YOUR_API_KEY", // <-- 替換成您的金鑰
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID", // <-- 替換成您的專案 ID
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID",
    measurementId: "YOUR_MEASUREMENT_ID"
};


步驟 3: 設定 Firestore 安全規則 (最重要！)

由於應用程式涉及讀寫多人協作的資料，您必須在 Firebase Console 中設定適當的 Firestore 安全規則 (Security Rules)，以允許已登入（即匿名登入）的使用者進行操作。

請前往 Firestore > 規則 (Rules)，並使用以下規則範本：

rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // 專案的共同 ID，程式碼中設定為 "shared-kimchi-order"
    match /artifacts/{appId} {

      // 1. 公共彙總路徑：所有已認證的使用者都可以讀寫 (用於彙總清單)
      match /public/data/all_orders/{orderId} {
        allow read, write: if request.auth != null;
      }

      // 2. 私人訂單路徑：使用者只能讀寫自己的訂單
      match /users/{userId}/orders/{orderId} {
        // request.auth != null 確保使用者已登入 (即使是匿名登入)
        // request.auth.uid == userId 確保使用者只能操作自己的資料夾
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
    }
  }
}


部署到 GitHub Pages

在您的 GitHub 建立一個新的儲存庫。

將 index.html 和 README.md 檔案上傳到儲存庫的根目錄。

進入 Settings (設定) > Pages (頁面)，將來源設定為 main 分支的根目錄。

等待幾分鐘，您的訂購單就會透過 GitHub Pages 提供的網址上線了！
