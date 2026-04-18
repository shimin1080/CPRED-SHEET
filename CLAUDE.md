# CPRED-SHEET プロジェクト概要

Cyberpunk RED TTRPG のキャラクターシート。**単一HTMLファイル**（index.html）で完結するWebアプリ。GitHub Pages でホスティング。

- **本番URL**: https://shimin1080.github.io/CPRED-SHEET/
- **リポジトリ**: https://github.com/shimin1080/CPRED-SHEET
- **ブランチ**: main（直接push）

---

## ファイル構成

```
index.html          ← アプリ本体（HTML + CSS + JS、現在約5174行）
CLAUDE.md           ← このファイル
.claude/
  launch.json       ← プレビューサーバー設定（port 8080）
  worktrees/elated-bose/index.html  ← プレビュー用コピー（要手動sync）
```

**プレビュー時は必ず同期が必要：**
```bash
cp /Users/yamamotoshou/Desktop/CPRED-SHEET/index.html \
   /Users/yamamotoshou/Desktop/CPRED-SHEET/.claude/worktrees/elated-bose/index.html
```

---

## index.html の構造（行番号は目安）

| 行範囲 | 内容 |
|--------|------|
| 1〜40 | `<head>`、CSS変数・リセット |
| 42〜1490 | **CSS** 全体（TOPBAR / LAYOUT / SIDEBAR / 武器DB / サイバーウェア / ライフパス / スキル / プリント） |
| 1491〜1550 | レスポンシブ・プリントCSS |
| 1552〜1570 | キャラクター選択画面 HTML |
| 1571〜2130 | キャラクターシート HTML（サイドバー・タブ・ページ群） |
| 2134〜2530 | JS: キャラクター管理（保存・読込・複数キャラ） |
| 2532〜2800 | JS: `WEAPON_DB` + 武器モーダル関数 |
| 2803〜2884 | JS: `SKILL_TIPS`（ツールチップ用テキスト） |
| 2885〜3082 | JS: 能力値（stats）/ HP / 人間性 / 運命ポイント |
| 3082〜3462 | JS: ロール別ライフパス（buildRoleLp 等） |
| 3464〜3776 | JS: `SKILL_SECTIONS` + スキル描画・ステッパー |
| 3779〜3940 | JS: エクスポート/インポート・武器行・装備行 |
| 3947〜4641 | JS: **サイバーウェアシステム全体** |
| 4651〜5110 | JS: `LP_TABLES` + ライフパスロール関数 |
| 5112〜5168 | JS: lpCollect/Restore（セーブ連携） |
| 5169〜5174 | JS: beforeunload・INIT |

---

## 主要データ構造

### サイバーウェア

```js
// 基礎サイバーウェア（インストール可能な部位）
CYBER_FOUNDATION_DB = {
  cyberEye: { name, slots, price, humanity, desc, options: [...] },
  cyberArm: { ... },
  cyberLeg: { ... },
  cyberAudio: { ... },
  cyberNeural: { ... },
}

// オプション（各部位に追加できるアイテム）
CYBER_OPTION_DB = {
  cyberEye:      [...],  // 12件
  cyberArm:      [...],  // 22件（プラスチック・カヴァーリング4種含む）
  cyberLeg:      [...],  // 10件（プラスチック・カヴァーリング4種含む）
  cyberAudio:    [...],  // 11件
  cyberNeural:   [...],  // 11件
  cyberInternal: [...],  // 13件
  cyberExternal: [...],  // 4件
  cyberFashion:  [...],  // 7件
  cyberBorg:     [...],  // 5件
}
```

**各オプション行のフィールド：**
```js
{
  name: '名称',
  slots: 1,           // スロット消費数（0=スロット不要）
  price: 100,
  humanity: '1d6',
  desc: 'データベースポップアップ用の詳細説明',
  sessionDesc: 'キャラクターシートに表示する短い説明',  // ← 必須
  noSlot: false,      // trueならスロット消費なし
  needsChipware: false, // true=チップウェアソケット必須
  isSocket: false,    // trueはソケット自体
  pairInstall: false, // trueは両側に同時インストール
  bareInstall: false, // trueは素のアーム/レッグ（スロット非表示）
}
```

### キャラクター保存

- `localStorage` に JSON 形式で保存
- キー: `cpred_characters`（複数キャラクター管理）
- `collectSheet()` → シートからデータ収集
- `restoreSheet(d)` → データをシートに反映

### 武器DB

```js
WEAPON_DB = {
  melee: [...],    // 近接武器
  ranged: [...],   // 遠隔武器
  special: [...],  // 特殊武器
}
```

---

## 主要関数

| 関数 | 役割 |
|------|------|
| `cyberRenderFoundation(id)` | サイバーウェア欄を再描画 |
| `cyberInstallFoundation(type)` | 基礎サイバーウェアをインストール |
| `cyberInstallBareArm/Leg(idx)` | 素のアーム/レッグをインストール |
| `cyberOpenOptionDB(partId)` | オプション選択モーダルを開く |
| `cyberSelectOption(idx)` | オプションをインストール |
| `buildSkills()` | スキルページを描画 |
| `collectSheet()` | シート全データをオブジェクトに収集 |
| `restoreSheet(d)` | オブジェクトからシートを復元 |
| `lpRollAll()` | ライフパスを全てロール |
| `showPage(name, btn)` | タブ切替 |

---

## デプロイ手順

```bash
git add index.html
git commit -m "変更内容"
git push origin main
# GitHub Pages が自動で反映（数分かかる場合あり）
```

---

## 削除済み機能（復活させない）

- イニシアチブトラッカー（~1130行削除済み）
- セッションシート
- ダイスロールパネル（能力値・技能クリックで判定、~545行削除済み）

---

## よくある作業パターン

**サイバーウェアのデータ修正**
→ `CYBER_OPTION_DB` または `CYBER_FOUNDATION_DB` を直接編集（~3992行付近）

**新しいスタイルを追加**
→ 該当セクションのCSS末尾に追記（セクション名でgrepして特定）

**プレビュー確認**
→ cp でworktreeに同期してからプレビューサーバーを起動
