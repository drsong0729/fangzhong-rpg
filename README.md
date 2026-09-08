# 房仲菜鳥大冒險

8-bit 風格的房仲養成 2D RPG，單一 HTML 檔、無外部相依（字型走 Google Fonts CDN，離線時自動退回系統字型）。

**玩法**：跑街找掛「售」的房子 → 調閱電傳看二類謄本 → 導航到屋主戶籍地 → 說服屋主簽委託。
一天 100 行動點數（調閱 20、敲門對話 40），30 天內拿到 6 件委託即通關，天數會登上排行榜。

- 主角名字可自訂，進度可存讀檔（localStorage）
- 城市、門牌、坪數、屋主性格與劇本皆程序生成，每一局都不一樣
- 難纏的屋主會直接關門，要跑好幾趟才願意談；每跑一趟他的戒心就低一點
- **所有對話都要自己打字**，沒有選項。系統／AI 會評分、回話，並在下方用小字給評語與建議
- BGM 與音效全部由 WebAudio 即時合成
- 在 Claude Artifact 上開啟時，屋主由 Claude 即時扮演；一般網頁版用內建的房仲話術評分引擎

---

## 共享排行榜（Firebase）

預設是**本機排行榜**（只記錄這台裝置）。要變成跨裝置共享，做兩件事：

### 1. 填入設定

打開 `index.html`，搜尋 `const FIREBASE=`，填入專案資訊：

```js
const FIREBASE={ apiKey:'AIza...', projectId:'你的專案ID', collection:'leaderboard' };
```

（建議**另開一個 Firebase 專案**專門放排行榜，不要用 estate-tracker，
因為那個專案有 App Check 與白名單規則，會擋掉未登入的玩家。）

### 2. 設定 Firestore 安全規則

在 Firebase Console → Firestore → 規則，貼上：

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /leaderboard/{doc} {
      allow read: if true;
      allow create: if request.resource.data.keys().hasOnly(['name','days','signed','ts'])
        && request.resource.data.name is string
        && request.resource.data.name.size() > 0
        && request.resource.data.name.size() <= 12
        && request.resource.data.days is int
        && request.resource.data.days >= 1 && request.resource.data.days <= 30
        && request.resource.data.signed is int
        && request.resource.data.signed >= 6 && request.resource.data.signed <= 60
        && request.resource.data.ts is int;
      allow update, delete: if false;
    }
    match /{document=**} { allow read, write: if false; }
  }
}
```

只允許新增、不允許改寫或刪除，而且欄位與數值範圍都會被驗證，
所以 API key 直接寫在前端沒有問題（Firebase 的 web API key 本來就是公開的）。

排行榜連不上時會自動退回本機榜，不會讓遊戲壞掉。

---

企劃・製作：日勝國際不動產　Ryan 吳松儒
遊戲內所有地址、街名、人名、建號皆為虛構，與現實世界之地址無關。
