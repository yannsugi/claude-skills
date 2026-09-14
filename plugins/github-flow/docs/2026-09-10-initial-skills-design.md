# github-flow 初期実装(試走の装備)設計

DESIGN.md の「初期実装」節に従い、次の3点のみを作る。他フェーズは素の Claude Code + DESIGN.md への都度指示で回す。

## 決定事項

- 配置: `skills/github-flow/` をローカルプラグイン(`.claude-plugin/plugin.json`, name=github-flow)にする。持ち出し先では marketplace 登録か `skills/` 配下の `.claude/skills/` へのコピーで動く。SKILL.md 内の参照はプラグイン相対
- 入出力の正: GitHub Issue / PR(`gh` CLI)。DESIGN.md の「GitHub=具体」に従う
- モデル割当: clarify-expectation=Fable 5 / verify-traceability=Sonnet 5(図解の割当表どおり)

## 成果物

| path | 内容 |
|---|---|
| `templates/ISSUE_TEMPLATE/expectation.md` | 期待(人間承認)/コンテキスト(承認不要)の2部構成。トリアージ・Asis/Tobe・影響画面・制約・非機能定型欄・⚠(最大3)・変更履歴。親Issue用に統合期待・依存グラフを任意欄で持つ |
| `skills/clarify-expectation/SKILL.md` | 入力契約 → トリアージ → 現状調査 → 質問ルーター → 期待詳細化 → E2E語彙プリフライト → 変更履歴。`gh issue edit` で本文更新、差分サマリをコメント |
| `skills/verify-traceability/SKILL.md` | 入力契約(実現案L2とコンテキスト節は読まない)→ 期待各行→テスト・エビデンス対応表 → 未カバー一覧 → 迷う差分はエスカレーション。PR コメントとして投稿 |

## 設計からの意図的な縮小

verify-traceability の「実装図解と実現案図の機械突合」「システムマップ diff 生成」は対象外。実現案スキル(propose-with-diagram)が未実装のため。試走で痛みを観測してから追加する。

## 試走で観測すること(最初の eval 素材)

解明の往復回数 / 各ゲートの承認時間と読み味 / 前提崩壊の有無 / QA NG の有無 / フェーズ別コンテキスト消費。

## 追記 2026-09-14: 解明フェーズまでのスキル追加

初期実装3点に `triage-issue`(Fable 5)と `survey-codebase`(Opus 5)を追加し、トリアージ→網羅的調査(L のみ)→解明の経路をスキルで通した。

- トリアージは clarify-expectation から分離した。図解上も独立工程で、L のときだけ survey が間に挟まるため
- システムマップの置き場は対象 repo の `docs/system-map.md`(仮説。鮮度閾値と同様に試走で検証)
- baseline で観測した素の欠陥: triage は根拠が長文化し仮説・次アクションまで書く / survey はマトリクス無しで原因推理に流れる。スキルはこの2点を封じる形で書いた

## 追記 2026-09-14: propose-with-diagram を追加

実現案フェーズ(Fable 5)。設計の提示仕様(期待の語彙・三層・⚠予算3・変更タイプ別の図形式固定)をそのまま契約にした。

- 図は Mermaid。GitHub のコメントが直接レンダリングするので、設計の「レンダリングはスクリプトへ」を別スクリプト無しで満たす
- baseline で観測した素の欠陥: L1 の図が無く本文全読みが必要 / 方針の節に設定キー・フック名などの実装語彙が混ざる / リスクが「残課題」に散って⚠の名指しにならない / 人間への質問を並べる
- 途中までしか満たせない期待行の書き方(範囲を明記)、⚠は実現案固有のみで期待側は番号参照、を試走の指摘から追加
