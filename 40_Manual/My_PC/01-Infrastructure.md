
## 1. ハードウェア・アセット・インベントリ

| カテゴリ            | デバイス名 / モデル番号                                 | スペック・詳細備考                              |
| :-------------- | :-------------------------------------------- | :------------------------------------- |
| **PC (Win)**    | Minisforum M1 Pro-285H                        | Core Ultra 9 285H / 64GB RAM / 2TB SSD |
| **PC (Mac)**    | Mac mini 2020 M1                              | 16GB RAM / 512GB SSD                   |
| **Monitor 1**   | innocn WR40-PRO                               | 40インチ ウルトラワイド (中央メイン)                  |
| **Monitor 2/3** | TERRA 2448WPV V3.1                            | 24インチ ×2 (右上横置き / 右下縦置き)               |
| **Network**     | TP-LINK BE85 / TL-SG505I/A                    | Wi-Fi 7 ルーター / 5ポートスイッチハブ              |
| **Key/Mouse1**  | MX Keys S / MX Master 4                       | **Logi Bolt規格**へ統合 Mac・Winでドングル使用      |
| **Key/Mouse2**  | Magic Keyboard / Magic Trackpad / Magic Mouse | Mac側で使用 (BlueToouth)                   |
| **Power**       | サンワ 10個口 / 個別SW付タップ                           | 一括スイッチ型と個別スイッチ型の2系統                    |
| **Light**       | BenQ ScreenBar Halo                           | 24インチ上段に設置 / ワイヤレスリモコン                 |

---

## 2. 電源系統図 (Power Distribution)

### 【系統A：大元】

**用途：長期不在時・災害時の完全遮断用。** 通常時は常にON。

* **物理配置：** デスク奥など、日常的に手が届かない場所に配置。
* **直結デバイス：** 昇降デスク、ルーター（BE85）
* **下位接続：** 系統B、系統C
	* **FlexiSpot E7 Pro:** 昇降デスク本体（常に動かせるようにするため）
	* **LENTIONハブ:** セルフパワー給電用（外付けHDDの安定稼働）
	* **TP-LINK BE85:** Wi-Fi 7 ルーター
	* **系統B**
	* **系統C**

### 【系統B：常時通電 / インフラ用】

**用途：Macのスリープ維持とネットワークの安定。** * **スイッチ特性：** 個別スイッチなし、または常にONを物理的にガード。

* **構成：** Mac mini, スイッチハブ, 40インチモニター, ScreenBar
	* **TP-LINK TL-SG505I/A:** スイッチハブ
	* **Mac mini:** 本体電源（スリープ運用）
	* **innocon WR40-PRO:** モニター電源
	* **BenQ ScreenBar Halo:** ライト電源

### 【系統C：作業時一括起動用】

**用途：作業時のみON。待機電力のカット。**

* **物理配置：** デスク上の手の届きやすい位置にスイッチを配置。
* **構成：** Minisforum, 24インチモニター×2, 各種充電器
	* **Minisforum:** 本体電源
	* **TERRA 2448WPV ×2:** モニター電源
	* **携帯スタンド:** 携帯とiPodsの充電用
	* **充電器:** USB-Cとライトニング  

> [!IMPORTANT]

> **注意点:** 系統Cをオフにしても、系統Aがオンであればルーター（Wi-Fi）やMac mini（スリープ）は生きているため、外出先からのTailscale経由のアクセスなども維持されます。この「二段構え」が明確であれば、以前指摘した矛盾は解消されます。



---

## 3. 詳細配線マップ (Port-by-Port Mapping)

### Mac mini 2020 M1 背面

1. **HDMIポート:** 24インチ (右下・縦置き用) ※OS側で90度回転設定
2. **USB-C (1):** 40インチモニター (DisplayPort出力)
3. **USB-C (2):** LENTIONハブ ➔ 外付けHDD×2 / Logi Bolt ドングル
4. **USB-A (1):** j5create JAC-365EJ (USB-A変換プラグ使用) ➔ 24インチ (右上・横置き用)
5. **USB-A (2):** EMEET S600 (Webカメラ)

### Windows (Minisforum)

1. **映像出力:** 40インチモニターへ直接接続
2. **USB-A (前面):** Logi Bolt ドングル
3. **USB-A (前面/延長):** YubiKey 5 NFC (天板右前端に固定)
4. **ネットワーク:** 有線LAN (スイッチハブ経由)

---
