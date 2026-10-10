# sangeki-scripts

**ネタバレ注意**: 脚本の中身（役職・事件の犯人）と、脚本家の指針を含みます。

[sangeki-engine](../sangeki-engine) で使う、設計・生成した脚本、脚本家の指針、次の一手問題、調整した重みです。
原作は **惨劇RoopeR**（BakaFire Party）。BakaFire Party とは関係のない、ファンによる非公式の制作物です
（二次創作のガイドライン http://bakafire.main.jp/rooper/sr_dl_04_sozai.htm に従っています）。

| 場所 | 中身 |
|---|---|
| `scripts/designed/` | 設計した脚本（d01〜） |
| `scripts/generated/` | 自動生成した脚本 |
| `scripts/guides/` | 脚本ごとの脚本家の指針（人間向けの .md と、自動の脚本家が読む .json） |
| `positions/` | 次の一手問題（`positions/games/` は相手の手を凍結した記録） |
| `tuned/` | `selfplay/tune.py` で調整した重み |
| `replays/` | 盤面で見返せるリプレイの見本（最後の戦いまで進んだ試合） |

各ファイルの形式（脚本・ID の一覧・指針・問題・決定の記録・重み）は [`FORMAT.md`](FORMAT.md) にあります。

## 使い方

sangeki-engine の隣に置くと、エンジンが自動で読み込みます（`../sangeki-scripts`）。

```bash
# 次の一手問題（このリポジトリの根で）
PYTHONPATH=../sangeki-engine python -m selfplay.positions positions/*.json --mm calcG --pc tuned-pc_r2 --samples 3
# 設計した脚本で対戦（sangeki-engine の根で）
python ai_gm.py --script designed/d16 --mm calcG
```

リプレイの見本 `replays/sample_d16_calcG.json` は、d16「お嬢様の身代わり」を脚本家 calcG・自動の主人公 readlook（seed 1）で最後の戦いまで進めた試合です。
盤面の最初のダイアログの『リプレイを見る…』で開くか、ブラウザ版に URL を渡して開けます:
https://cha2maru.github.io/sangeki-engine/?replay=https://raw.githubusercontent.com/cha2maru/sangeki-scripts/main/replays/sample_d16_calcG.json

問題集は、作ったときのエンジンの版と組で動きます（凍結した記録がその版のコードに依存します）。

ライセンス: MIT（`LICENSE`）。
