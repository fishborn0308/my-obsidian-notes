# <% tp.file.title %>






---
## ログ・メモ
> 

## 振り返り
- **良かったこと**
	- 
- **改善点・明日やること**
	- 

---
## 🔗 関連リンク

```dataviewjs
// 1. デイリーノートが保存されているフォルダ名を指定（例: "DailyNotes"）
const folderName = "10_Daily";

// 2. 現在のファイル名を取得
const currentFile = dv.current().file;

// 3. フォルダ内の全ノートを取得して日付順にソート
const dailyNotes = dv.pages(`"${folderName}"`)
    .sort(p => p.file.name, 'asc');

// 4. 現在のノートがリストのどこにあるかインデックスを探す
const currentIndex = dailyNotes.indexOf(dailyNotes.find(p => p.file.name === currentFile.name));

// 5. 前後のノートへのリンクを生成
let prevLink = "";
let nextLink = "";

if (currentIndex > 0) {
    const prevFile = dailyNotes[currentIndex - 1].file;
    prevLink = `[[${prevFile.name}|← ${prevFile.name}]]`;
} else {
    prevLink = "<span style='color: gray;'>（最古）</span>";
}

if (currentIndex !== -1 && currentIndex < dailyNotes.length - 1) {
    const nextFile = dailyNotes[currentIndex + 1].file;
    nextLink = `[[${nextFile.name}|${nextFile.name} →]]`;
} else {
    nextLink = "<span style='color: gray;'>（最新）</span>";
}

// 6. 表示（中央揃えのバー形式）
dv.paragraph(`${prevLink} | ${nextLink}`);
```