---
created: 2026-01-24 11:41
modified: 2026-01-24 11:42
tags:
  - BTT
  - config
---
# Mac - BTT

## Trackpad (BTT) ジェスチャー

- **3本指タップ:** 中央メイン領域（40インチ右寄り65%）へ移動
	- Action - Move/Resize:Top left Corner,top Teft corner of the screen
		- X:Relative to screen wid :35
		- Y:Relative to screen wid :0
		- wid:Relative to screen :65
		- height:Relative to screen :100
- **3本指スワイプ左:** サブ領域（40インチ左端30%）へ移動
	- Action - Move/Resize:Top left Corner,top Teft corner of the screen
		- X:Relative to screen wid :7
		- Y:Relative to screen wid :0
		- wid:Relative to screen :28
		- height:Relative to screen :100
- **3本指スワイプ右:** ピボット画面（24インチ縦）へ移動 ＋ 最大化
	- Action - Move Window to Next Monitor
	- Action - ctrl + opt + Enter
- **3本指スワイプ上:** ウィンドウを最大化
	- Action - ctrl + opt + Enter
- **3本指スワイプ下:** ウィンドウを３分の１で中央
	- Action - ctrl + opt + c