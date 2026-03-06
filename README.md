MM出版のクエバンのpdfからAnkiに取り込むコードについて作成

"QB_pdf_ver_2.ipynb"

アプリ等から落としたpdfしか対応できてません。
現状解説はうまく載せられません

#ライブラリのインストール　及び　#グーグルドライブと連携は各自のGoogle Colabの環境で行ってください。多分貼ってあるコード通りで行けるはず。


![image](https://github.com/user-attachments/assets/547db840-942f-4ea1-bace-d119287aa91e)



pdfの読み込み→csvファイルの出力は、左上のファイルのアップロードから、Anki化したいクエバンのpdfファイルをアップロードした後に、右クリックでpassをコピーした後

上部の　
pdf_path = "/content/1A.pdf"と、
下部の
output_file = os.path.join(folder_path, "1A.csv")

に貼り付け及び書き出したい名前で書き直す。（1A.pdf,1A.csv　は例）

このコードを行うと、指定したディレクトリにpdfから抽出されたcsvファイル[問題、問題ID、解答、画像]と、問題に画像が存在する場合、その画像ファイルが同時に入っているはず。画像はzipファイル。


それぞれダウンロードして、画像ファイルはAnki→ファイル→プロファイル→バックアップを開くで出てくるディレクトリの一つ上位のユーザー１の直下のcollection.mediaに貼り付ける。アクセスが面倒なのでショートカットを作っておくと良いと思う。

ダウンロードしたcsvファイルは、Anki→ファイル→インポートでファイル選択。その後、”フィールド内でHTMLを使う”にチェックをいれるのを忘れずに！画像表示の都合上！
また同時にフィールドの割り当てに関しても確認しておく。
インポートボタンを押すとインポート完了。


"QB_csv_Ver_1.ipynb"
これは前述のファイルで出力したcsvファイルの問題文から、選択肢a~eを別で抽出するコード（4Aの多答選択肢は使えません）

# QB to Anki — QB Scraper JSON → Anki デッキ変換

**QB_Scrape_Ver.5.js** で出力された JSON + 画像フォルダを読み込み、
**Anki にインポート可能な `.apkg` ファイル** を生成する Python スクリプトです。

通常版・1周目データの両方に対応しており、同一問題番号でもデッキごとに独立したカードとして管理されます。

---

## ✨ 特徴

| 機能 | 説明 |
|------|------|
| **ワンコマンド変換** | フォルダ名を指定するだけで JSON → `.apkg` を生成 |
| **19 フィールド対応** | 問題文・選択肢・正解・正答率・解法の要点・選択肢解説・画像診断・診断・主要所見・KEYWORD・ガイドライン・基本事項・医ンプットなど |
| **画像自動同梱** | 問題画像・解説画像・基本事項画像を `.apkg` に埋め込み |
| **通常版 / 1周目 両対応** | デッキ名ベースの GUID で同一問題番号でも別カードとして管理 |
| **pdfmake オブジェクト対応** | 選択肢がプレーン文字列でも pdfmake レイアウトオブジェクトでも正しく抽出 |
| **連問（シリアル問題）** | 共通問題文 + 枝問をまとめて 1 カードに表示 |
| **CSS スタイル付き** | 青ヘッダー・赤正解・セクション分けの見やすいカードデザイン |
| **安定 GUID** | 同じデータから再生成しても Anki 上でカードが重複しない |

---

## 📋 前提条件

- **Python 3.10 以上**
- **genanki**（Anki パッケージ生成ライブラリ）
- **QB_Scrape_Ver.5.js の出力フォルダ**（JSON + 画像）

---

## 🚀 セットアップ

### 1. Python 環境を準備

```bash
pip install genanki
```

### 2. スクリプトを配置

`qb_to_anki.py` を QB Scraper の出力フォルダと同じ階層に置きます。

```
Desktop/
├── qb_to_anki.py          ← 本スクリプト
├── C 循環器/
│   ├── C 循環器.json
│   └── C 循環器_images/
│       ├── 111B18_問題_1.png
│       ├── 111B18_解説_1.png
│       └── ...
├── C 循環器 1周目/
│   ├── C 循環器 1周目.json
│   └── C 循環器 1周目_images/
│       └── ...
└── ...
```

---

## 💻 使い方

### 基本コマンド

```powershell
# 通常版
python .\qb_to_anki.py "C 循環器"

# 1周目データ
python .\qb_to_anki.py "C 循環器 1周目"
```

### オプション

```powershell
# デッキ名を指定
python .\qb_to_anki.py "C 循環器" --deck "国試::QB::C 循環器"

# 出力ファイル名を指定
python .\qb_to_anki.py "C 循環器" --output "循環器.apkg"

# タグを追加
python .\qb_to_anki.py "C 循環器" --tags QB 循環器 内科

# JSON / 画像フォルダを個別指定
python .\qb_to_anki.py "C 循環器" --json "path/to/data.json" --images "path/to/imgs"
```

### 全科目一括変換（PowerShell）

```powershell
# 通常版 全科目
Get-ChildItem -Directory | Where-Object { $_.Name -match '^[A-Z] ' -and $_.Name -notmatch '1周目|1週目' } | ForEach-Object {
    python .\qb_to_anki.py $_.Name
}

# 1周目 全科目
Get-ChildItem -Directory | Where-Object { $_.Name -match '1周目|1週目' } | ForEach-Object {
    python .\qb_to_anki.py $_.Name
}
```

---

## 🎴 カードデザイン

### 表面（問題）

```
┌─────────────────────────────────────┐
│ 111B18                    QB2026 C-2│
│─────────────────────────────────────│
│                                     │
│ 68歳の男性．胸痛を主訴に来院した．  │
│ ...問題文...                        │
│                                     │
│ 🖼 [問題画像]                       │
│                                     │
│ ┌─────────────────────────────────┐ │
│ │ ａ　心筋梗塞                    │ │
│ │ ｂ　狭心症                      │ │
│ │ ｃ　大動脈解離                  │ │
│ │ ｄ　心筋炎                      │ │
│ │ ｅ　肺血栓塞栓症                │ │
│ └─────────────────────────────────┘ │
└─────────────────────────────────────┘
```

### 裏面（解答・解説）

```
┌─────────────────────────────────────┐
│ ...問題面と同じ内容...              │
│━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━│
│          正解: a                    │
│        正答率: 91.1%                │
│                                     │
│ ┌ 主要所見 ───────────────────────┐ │
│ │ ST上昇, トロポニン陽性...       │ │
│ ├ KEYWORD ────────────────────────┤ │
│ │ 急性心筋梗塞                    │ │
│ ├ 解法の要点 ─────────────────────┤ │
│ │ 胸痛＋ST上昇から鑑別を...      │ │
│ ├ 選択肢解説 ─────────────────────┤ │
│ │ ○ａ 心筋梗塞 — ...             │ │
│ │ ×ｂ 狭心症 — ...               │ │
│ ├ 基本事項 ───────────────────────┤ │
│ │ 🖼 [基本事項画像]               │ │
│ └─────────────────────────────────┘ │
└─────────────────────────────────────┘
```

---

## 📊 フィールド一覧（19 フィールド）

| # | フィールド名 | JSON ソース | 説明 |
|---|-------------|------------|------|
| 1 | 問題番号 | `problem.problemNumber` | 例: `117C11` |
| 2 | 掲載頁 | `problem.reference` | 例: `QB2026 A-2` |
| 3 | 問題文 | `problem.questionText` | 問題本文 |
| 4 | 問題画像 | `_images/` フォルダ | `〇〇_問題_N.png` |
| 5 | 選択肢 | `problem.choices[]` | ａ〜ｅ の選択肢 |
| 6 | 正解 | `result.correctAnswer` | `a`, `b,d` など |
| 7 | 正答率 | `result.accuracyRate` | 例: `91.1%` |
| 8 | 解法の要点 | `explanation.explanationPoints` | 解法のポイント |
| 9 | 選択肢解説 | `explanation.optionAnalysis` | 各選択肢の詳細解説 |
| 10 | 画像診断 | `explanation.imageDiagnosisText` | 画像診断の解説 |
| 11 | 解説画像 | `_images/` フォルダ | `〇〇_解説_N.png` |
| 12 | 診断 | `explanation.diagnosis` | 最終診断名 |
| 13 | 主要所見 | `explanation.findings` | 主要な臨床所見 |
| 14 | KEYWORD | `explanation.keyword` | キーワード |
| 15 | ガイドライン | `explanation.guideline` | 関連ガイドライン |
| 16 | 基本事項 | `basic.textContent` | 基本事項テキスト |
| 17 | 基本事項画像 | `_images/` フォルダ | `〇〇_基本事項_N.png` |
| 18 | 医ンプット | `medicalInput.text` | 医ンプットテキスト |
| 19 | 連問情報 | `isSerial` + `subQuestions[]` | 連問の枝問 |

---

## ⚙️ CLI オプション

```
usage: qb_to_anki.py [-h] [--json JSON] [--images IMAGES]
                      [--output OUTPUT] [--deck DECK] [--tags [TAGS ...]]
                      input_dir

positional arguments:
  input_dir             QB Scraper の出力フォルダ（例: 'C 循環器'）

optional arguments:
  --json                JSON ファイルパス（省略時: <input_dir>/<input_dir>.json）
  --images              画像フォルダパス（省略時: <input_dir>/<input_dir>_images）
  --output, -o          出力 .apkg ファイルパス（省略時: <input_dir>.apkg）
  --deck, -d            Anki デッキ名（省略時: QB::<フォルダ名>）
  --tags, -t            カードに付けるタグ（スペース区切り、省略時: QB）
```

---

## 🔧 技術的な詳細

### GUID（重複防止）

```python
guid = genanki.guid_for(f"{deck_name}_{problem_number}")
```

デッキ名 + 問題番号から GUID を生成するため：
- **同じフォルダから再生成** → 既存カードが更新される（重複しない）
- **通常版と1周目** → デッキ名が違うので別カードとして登録される
- 例: `QB::C 循環器_111B18` ≠ `QB::C 循環器 1周目_111B18`

### 選択肢の自動変換

QB Scraper の JSON では `choices` が 2 種類の形式で保存される場合があります：

```json
// パターン1: プレーン文字列（1周目データなど）
"choices": ["ａ　食道", "ｂ　胃", "ｃ　十二指腸"]

// パターン2: pdfmake レイアウトオブジェクト（通常版で発生）
"choices": [{"text": "ａ　食道", "_margin": null, ...}, ...]
```

`format_choices()` が両方を自動判別して文字列に変換します。

### モデル ID

```python
MODEL_ID = 1607392319  # 固定値
```

すべてのデッキで同一モデルを使用するため、Anki 上でカードテンプレートやスタイルを一括変更できます。

---

## ⚠️ 注意事項

- **Python 3.10 以上が必須**です（`list[str] | None` 構文を使用）
- surya-env などの仮想環境を使う場合は、先に `Activate.ps1` を実行してください
- `MGLTools` 等の古い Python が PATH に入っている場合、そちらが優先されて SyntaxError になることがあります
- Anki へのインポートは **「ファイル → インポート」** から `.apkg` を選択してください

---

## 📝 ライセンス

MIT License
