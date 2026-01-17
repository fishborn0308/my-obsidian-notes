<%*
// 昨日の日付と明日の日付を計算（ナビゲーション用）
const yesterday = tp.date.now("YYYY-MM-DD", -1);
const tomorrow = tp.date.now("YYYY-MM-DD", 1);
-%>
---
type: daily
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
- Daily
---

[[<% yesterday %>|◀ 昨日]] | [[<% tomorrow %>|明日 ▶]]

## [Daily] <% tp.date.now("YYYY-MM-DD (ddd)") %>

### 📅 今日の目標

- [ ] 

### 📝 作業・学習ログ (Time Log)

> [!TIP] `Cmd + Shift + I`（自作ショートカット等）で現在時刻を入れてから書き始めると、後でタイムラインとして振り返りやすくなります。

-  **[Pentest]**
-  **[SEC]**
-  **[NET]**

### 🔗 今日作成・更新したノート

> [!INFO] その日に触ったプロジェクトノートが自動でリストアップされます（Dataview）。

```dataview
LIST FROM "" WHERE (file.cday = this.file.day OR file.mday = this.file.day) AND type != "daily"
````

### 🛠 明日への申し送り

- [ ]