# Classroom word cloud (Firebase + GitHub Pages)

檔案：`index.html`（學生）、`teacher.html`（老師）、`firebase-config.js`（你的 Firebase 設定）

## 1. 建立 Firebase
1. 到 https://console.firebase.google.com → Add project（不需要 Analytics）
2. Build → Firestore Database → Create database（Production mode，區域選離你近的）
3. Project settings → Your apps → 新增 Web app (`</>`) → 複製 `firebaseConfig` 貼到 `firebase-config.js`

## 2. Firestore 規則（Firestore → Rules → 貼上 → Publish）
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /rooms/{room}/words/{id} {
      allow read: if true;
      allow create: if room.size() <= 30
        && request.resource.data.keys().hasOnly(['word', 'createdAt'])
        && request.resource.data.word is string
        && request.resource.data.word.size() > 0
        && request.resource.data.word.size() <= 40
        && request.resource.data.createdAt == request.time;
      allow update, delete: if false;
    }
  }
}
```
學生只能新增，不能修改或刪除。老師端的「Hide」只在你自己的瀏覽器隱藏，不會動資料庫。

## 3. 部署到 GitHub Pages
把這個資料夾的檔案放進 repo → Settings → Pages → Deploy from a branch → main / root。
老師頁網址：`https://<帳號>.github.io/<repo>/teacher.html`

## 4. 上課使用
開啟 teacher.html → 輸入問題 → 按 Start → 按 Present full screen。學生掃 QR code 即可。
換下一題：按 New room（每個房間資料獨立）。
