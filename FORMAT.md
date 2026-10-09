# ファイルの形式

sangeki-scripts の各ファイルの形式です。どれも UTF-8 の JSON（記録は1行1つの JSON＝JSON Lines）です。

1. [脚本](#1-脚本-scripts)
2. [ID の一覧](#2-id-の一覧)
3. [脚本家の指針](#3-脚本家の指針-scriptsguides)
4. [次の一手問題](#4-次の一手問題-positions)
5. [決定の記録](#5-決定の記録-positionsgamesjsonl)
6. [調整した重み](#6-調整した重み-tuned)

---

## 1. 脚本（`scripts/`）

脚本家の書の「非公開シート」にあたる情報です。`scripts/designed/d16.json` は脚本 ID `designed/d16` で読み込めます
（`python ai_gm.py --script designed/d16`）。

```json
{
  "id": "d16",
  "title": "お嬢様の身代わり",
  "set": "BTX",
  "loops": 3,
  "days": 6,
  "rules": ["Y_BOMB", "X_CIRCLE", "X_FACTOR"],
  "characters": ["C03", "C05", "C12", "C15", "C20", "C23", "C28", "C32", "C33", "C34"],
  "roles": {"C15": "WITCH", "C03": "FACTOR", "C34": "FRIEND", "C20": "FRIEND", "C23": "MISLEADER", "C28": "MISLEADER"},
  "init": {"C34": "SCH"},
  "incidents": [
    {"day": 2, "id": "SUICIDE", "culprit": "C03"},
    {"day": 3, "id": "MURDER", "culprit": "C32"}
  ],
  "notes": {"roles": "…", "pp": "…", "cs": "…", "solution": "…", "wins": "…"},
  "source": "作成の経緯",
  "use": "train"
}
```

| 項目 | 必須 | 意味 |
|---|---|---|
| `id` | ○ | 脚本の ID（ファイル名と同じにする） |
| `title` | | 題名 |
| `set` | | 惨劇セット。今は `"BTX"`（Basic Tragedy Χ）だけ |
| `loops`・`days` | ○ | ループ数・1ループの日数 |
| `rules` | ○ | ルールY 1つ＋ルールX 2つ（[ID](#ルール)） |
| `characters` | ○ | 登場人物（[ID](#キャラクター)） |
| `roles` | ○ | 役職（パーソン以外）。`{人物: 役職}` |
| `incidents` | ○ | 事件 `{day, id, culprit}`。犯人は互いに異なる |
| `init` | | 初期エリア `{人物: エリア}`。省くとカードの初期エリア。手先・従者など、脚本家が決める人物は必須 |
| `appear` | | 途中から登場する人物。`{"C24": {"day": 2}}`（転校生）、`{"C13": {"loop": 2}}`（神格） |
| `special_rules` | | 特別ルールの文 |
| `notes` | | 作者の意図（脚本家の書の「タブー」「CS/PP」に沿って書く。エンジンは読まない） |
| `source`・`use` | | 作成の経緯・分類のラベル（`train`・`eval`・`generated` など。エンジンは読まない） |
| `stats` | | 自動生成した脚本の、ふるいにかけたときの成績（`gen_filter` が書く） |

エンジンは読み込むときに整合を確かめます（役職の員数・犯人の重複・キャラクターごとの制約など）:

```python
from engine import scripts
errors, warnings = scripts.check(scripts.load('scripts/designed/d16.json'))
```

## 2. ID の一覧

### エリア

| ID | エリア | ボードの ID |
|---|---|---|
| `HOS` | 病院 | `B:HOS` |
| `SHR` | 神社 | `B:SHR` |
| `CIT` | 都市 | `B:CIT` |
| `SCH` | 学校 | `B:SCH` |

### ルール

| ID | 名前 | | ID | 名前 |
|---|---|---|---|---|
| `Y_MURDER` | 殺人計画 | | `X_CIRCLE` | 友情サークル |
| `Y_SEAL` | 封印されしモノ | | `X_LOVE` | 恋愛風景 |
| `Y_CONTRACT` | 僕と契約しようよ！ | | `X_KILLER` | 潜む殺人鬼 |
| `Y_FUTURE` | 未来改変プラン | | `X_RUMOR` | 不穏な噂 |
| `Y_BOMB` | 巨大時限爆弾Xの存在 | | `X_VIRUS` | 妄想拡大ウイルス |
| | | | `X_THREAD` | 因果の糸 |
| | | | `X_FACTOR` | 不定因子χ |

### 役職

| ID | 名前 | ID | 名前 |
|---|---|---|---|
| `PERSON` | パーソン | `WITCH` | ウィッチ |
| `KEY` | キーパーソン | `FRIEND` | フレンド |
| `KILLER` | キラー | `MISLEADER` | ミスリーダー |
| `KUROMAKU` | クロマク | `LOVERS` | ラバーズ |
| `CULTIST` | カルティスト | `MAIN_LOVERS` | メインラバーズ |
| `TT` | タイムトラベラー | `SK` | シリアルキラー |
| `FACTOR` | ファクター | | |

### 事件

| ID | 名前 | ID | 名前 |
|---|---|---|---|
| `MURDER` | 殺人事件 | `REMOTE` | 遠隔殺人 |
| `SPREAD` | 不安拡大 | `MISSING` | 行方不明 |
| `CORRUPT` | 邪気の汚染 | `RUMOR_SPREAD` | 流布 |
| `SUICIDE` | 自殺 | `BUTTERFLY` | 蝶の羽ばたき |
| `HOSPITAL` | 病院の事件 | | |

### キャラクター

| ID | 名前 | ID | 名前 | ID | 名前 |
|---|---|---|---|---|---|
| `C01` | 男子学生 | `C13` | 神格 | `C25` | 軍人 |
| `C02` | 女子学生 | `C14` | アイドル | `C26` | 黒猫 |
| `C03` | お嬢様 | `C15` | マスコミ | `C27` | 女の子 |
| `C04` | 巫女 | `C16` | 大物 | `C28` | コピーキャット |
| `C05` | 刑事 | `C17` | ナース | `C29` | 教祖 |
| `C06` | サラリーマン | `C18` | 手先 | `C30` | ご神木 |
| `C07` | 情報屋 | `C19` | 学者 | `C31` | 妹 |
| `C08` | 医者 | `C20` | 幻想 | `C32` | アルバイト |
| `C09` | 入院患者 | `C21` | 鑑識官 | `C33` | アルバイト？ |
| `C10` | 委員長 | `C22` | A.I. | `C34` | 従者 |
| `C11` | イレギュラー | `C23` | 教師 | `C35` | 上位存在（特性・能力は未対応） |
| `C12` | 異世界人 | `C24` | 転校生 | | |

不安臨界・初期エリア・禁止エリア・属性は sangeki-engine の `engine/data/characters.json` にあります。

### 行動カード

| 脚本家 | 意味 | 主人公 | 意味 |
|---|---|---|---|
| `PAR+` | 不安+1（2枚） | `PAR+` | 不安+1 |
| `PAR-` | 不安−1 | `PAR-` | 不安−1（ループに1回） |
| `PARX` | 不安禁止 | `GW1` | 友好+1 |
| `GWX` | 友好禁止 | `GW2` | 友好+2（ループに1回） |
| `INT1` | 暗躍+1 | `INTX` | 暗躍禁止 |
| `INT2` | 暗躍+2（ループに1回） | `MVX` | 移動禁止（ループに1回） |
| `MV_V`・`MV_H` | 移動↑↓・移動←→ | `MV_V`・`MV_H` | 移動↑↓・移動←→ |
| `MV_D` | 移動斜め（ループに1回） | | |

## 3. 脚本家の指針（`scripts/guides/`）

脚本ごとの、脚本家の打ち方の指針です。

- `<脚本の id>.md`: 人間向け。負け筋、仕上げの日、おとり、同時に出す脅威、隠す役職など
- `<脚本の id>.json`: 機械向け。自動の脚本家 `searchguide` が読みます

JSON の書式は [`scripts/guides/README.md`](scripts/guides/README.md) にあります（`routes`・`cards`・`incidents`・`setup`・`lock_hide`・`hide`）。

## 4. 次の一手問題（`positions/`）

1ファイルに問題の配列です。

```json
{
  "id": "d16-l2d1-mm",
  "script": "designed/d16",
  "seed": 64,
  "mm": "positions/games/cvc_d16_s64_mm.jsonl",
  "pc": "positions/games/cvc_d16_s64_pc.jsonl",
  "at": {"loop": 2, "day": 1, "kind": "mm_cards"},
  "good": [[{"card": "INT2", "target": "C34"}, {"card": "INT1", "target": "B:CIT"}]],
  "bad": [[{"card": "INT2", "target": "B:CIT"}]],
  "note": "なぜそれが良い・悪いか",
  "split": "tune"
}
```

| 項目 | 意味 |
|---|---|
| `id` | 問題の ID |
| `script`・`seed` | 脚本と、記録したときの乱数の種 |
| `mm`・`pc` | 両陣営の[決定の記録](#5-決定の記録-positionsgamesjsonl) |
| `mm_opp`・`pc_opp` | 片側だけ人が遊んだ記録のとき、相手の自動のプレイヤーの型（記録したときの `--opp`）。この場合 `mm`・`pc` の片方は省く |
| `mm_rec`・`pc_rec` | 相手の手を凍結した記録（`positions/games/frozen_*.pkl`）。`--freeze` が書く |
| `at` | 局面。`kind` は `mm_cards`（脚本家の伏せ札）・`incident`（事件の選択）・`pc_cards`（主人公の札） |
| `good` | 札の組の一覧。どれか1組を全部含めば正解。省くと「`bad` でなければ正解」 |
| `bad` | 札の組の一覧。どれか1組を全部含めば不正解 |
| `note` | 解説 |
| `split` | `tune`（重みの調整に使う）・`hold`（確かめ用） |

組の中の札は、書いた項目（`card`・`target`・`by` など）だけを照合します。`{"card": "INT2"}` なら置き先を問いません。

使い方は sangeki-engine の [`docs/research.md`](../sangeki-engine/docs/research.md) の「次の一手問題」を見てください。

## 5. 決定の記録（`positions/games/*.jsonl`）

`play_cli` の決定ファイルと同じ形で、1行に1つの決定を、起きた順に並べます。

```json
{"kind": "mm_cards", "choice": [{"target": "C03", "card": "INT1"}, {"target": "B:CIT", "card": "INT2"}, {"target": "C12", "card": "PAR+"}]}
{"kind": "pc_cards", "choice": [{"by": "A", "target": "C03", "card": "INTX"}, {"by": "B", "target": "C12", "card": "PAR-"}, {"by": "C", "target": "C05", "card": "GW1"}]}
{"kind": "abilities", "choice": [["C05", 0, null], ["C10", 1, {"card": "PAR-"}]]}
{"kind": "mm_ability", "choice": "C03"}
{"kind": "incident", "choice": {"target": "C34"}}
{"kind": "refuse", "choice": true}
{"kind": "final", "choice": [{"char": "C03", "role": "FACTOR"}, {"char": "C34", "role": "FRIEND"}]}
```

| `kind` | `choice` |
|---|---|
| `loop_setup` | ループの準備（手先の初期エリア・学者の特性など）`{"init": {"C18": "HOS"}}` |
| `mm_cards` | 脚本家の伏せ札3枚 |
| `pc_cards` | 主人公の札（リーダーから順に3枚） |
| `abilities` | その日に宣言する友好能力 `[人物, 能力の番号, 引数]` の一覧（無ければ `[]`） |
| `mm_ability` | 脚本家能力の対象（人物・ボード、使わないなら `null`） |
| `incident` | 事件の効果の対象（`{"target": …}`、不安拡大は `{"par_target": …, "int_target": …}`、無ければ `null`） |
| `killer` | キラーなどの能力を使うか `[{"char": "C08", "ability": "KILLER_KEY"}]` |
| `refuse` | 友好能力を拒否するか |
| `rumor` | 不穏な噂のボード |
| `final` | 最後の戦いの指摘（宣言順） |

友好能力の引数の例は sangeki-engine の `selfplay/play_cli.py` の冒頭にあります。

`positions/games/frozen_*.pkl` は凍結した相手の手（Python の pickle）です。作ったときのエンジンの版と組で読み込みます。

## 6. 調整した重み（`tuned/`）

```json
{
  "base": "readlookQ2",
  "params": {"urgency": 1.0, "info_weight": 0.247, "reach_info": 1.8417},
  "tune": 0.667,
  "hold": 0.455,
  "source": "selfplay/runs/tune2_pc.jsonl"
}
```

| 項目 | 意味 |
|---|---|
| `base` | 基準の自動のプレイヤーの型 |
| `params` | その型の属性に差し込む重み（`selfplay/tune.py` の `SPACE` の名前） |
| `tune`・`hold` | 調整用・確かめ用の問題の正解率 |
| `source` | 調整の記録 |

`tuned/pc_r2.json` は `tuned-pc_r2` という名前で使えます。
