<%*

// 1. カテゴリを選択させる（変数に格納して実行を安定させる）

const categories = ['Git', 'Docker', 'Linux', 'Windows', 'Pentest'];

const category = await tp.system.suggester(categories, categories);

const title = tp.file.title;

-%>

---
type: cheatsheet
category: <% category %>
tags:
	- Knowledge
date: <% tp.date.now("YYYY-MM-DD") %>
---

## [<% tp.frontmatter["category"] %>] <% tp.file.title %>

### 概要

- 

### コマンド / 設定例

```bash

```

### 逆引き・逆引き（➔ 逆引き・解決策 に修正）

- **やりたいこと:** （例：特定のプロセスを強制終了したい）
- **解決策:** `kill -9 <PID>`
- **補足:** ゾンビプロセスには効かない場合がある

### このナレッジを使用した検証ログ

[!INFO] このツール/知識を実際に使用した 20_Projects のログが自動で表示されます。

```dataview
TABLE target_ip as "ターゲット", status as "状況", date as "実施日"
FROM "20_Projects"
WHERE contains(tools, this.file.name) OR contains(tools, [[<% title %>]])
SORT date DESC
```


### 関連リンク

- [[_Index_<% tp.frontmatter["category"] %>]]