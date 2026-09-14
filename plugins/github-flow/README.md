# github-flow

Claude Code 向けの **開発フロー設計** と、そこから起こすスキル群の置き場。

## 目的

「GitHub Issue を AI と一緒に解く」ときの工程・人間ゲート・資産化ループを設計し、
その設計に沿った SKILL.md / テンプレートを実装していく。

設計の方針は次の2点:

- 開発者の労力を通時的に最小化し、品質を最大化する。承認は労力ではなく **理解** を生産する投資
- 人間が理解するのは **期待の正しさ / 判断の方向 / 翻訳の一致** の3点だけ。実装の正しさは E2E の観測と指標(前提崩壊率など)が担保する

## 中身

Claude Code のプラグイン(`.claude-plugin/plugin.json`)。`yannsugi/claude-skills` マーケットプレイスの `plugins/github-flow/` に置かれている。

| path | 内容 |
|------|------|
| `github_issue_flow_v3.html` | GitHub Issue 解決フロー v3(凍結版・北極星)。トリアージ→解明→実現案→実装→品質担保→マージの定常フロー、3つの人間ゲート、例外フロー(NG調査)、資産(eval セット / システムマップ)、コンテキスト最小化規律、世代交代ループ、スキルとモデル割当(Fable / Opus / Sonnet)を1枚にまとめた図解 |
| `DESIGN.md` | 上記図解の文章版(思想文書)。スキルはここを参照する。核・3ゲート・各フェーズの規律・通底原則・初期実装3点と仮説リスト |
| `templates/ISSUE_TEMPLATE/expectation.md` | 期待テンプレート。「期待」(人間承認)/「コンテキスト」(承認不要)の2部構成。持ち出し先の `.github/ISSUE_TEMPLATE/` に複製する |
| `skills/triage-issue/` | トリアージ(Fable 5)。S/M/L 判定と根拠一行のみ。L なら survey-codebase へ |
| `skills/survey-codebase/` | 網羅的調査(Opus 5・L のみ)。領域×観点マトリクスの全セル消込、影響マップ、未確認セルの⚠候補化、`docs/system-map.md` の更新 |
| `skills/clarify-expectation/` | 解明フェーズ(Fable 5)。トリアージ・質問ルーター・E2E 語彙プリフライト・変更履歴。`gh issue edit` / `gh issue comment` で出力 |
| `skills/verify-traceability/` | 品質担保の照合役(Sonnet 5)。期待→テスト対応表・未カバー・エスカレーション。`gh pr comment` で出力 |
| `docs/` | 実装ごとの設計メモ |

`github_issue_flow_v3.html` はブラウザで直接開いて読む(ビルド不要)。

```bash
open github_issue_flow_v3.html
```

## 使い方

```bash
claude plugin marketplace add yannsugi/claude-skills
```

```bash
claude plugin install github-flow@claude-skills
```

SKILL.md 内の `${CLAUDE_PLUGIN_ROOT}` はこのディレクトリを指す。期待テンプレートは対象 repo の `.github/ISSUE_TEMPLATE/` に複製して使う。

Issue 番号ではなくローカル Markdown を対象にした試走もできる(各 SKILL.md 末尾の試走モード参照)。

GitHub への書き込み(`gh issue comment` / `gh issue edit` / `gh pr comment` / ラベル付け)は、どのスキルも利用者が明示的に指示した場合にのみ行う。指示が無ければ文面を提示して止まる。読み取り(`gh issue view` 等)は自由。

## 現状と次の一手

**設計は凍結済み。初期実装3点に triage-issue / survey-codebase を加えた5点が試走可能な状態。** 実現案以降(propose-with-diagram・実装)は未実装で、DESIGN.md への都度指示で回す。 以後の改善は本設計への追加ではなく、
試走で検証する **仮説** として扱う(仮説リストは図解末尾)。

試走の初期実装は次の3点のみ(実装済み):

1. 期待テンプレート(`.github/ISSUE_TEMPLATE` — スキルではない。全工程の正解の定義)
2. `clarify-expectation`(詳細化・質問ルーター・変更履歴・E2E可能語彙のプリフライト)
3. `verify-traceability`(図解突合・期待→テスト対応表・エビデンス・マップ diff 生成)

他フェーズは素の Claude Code + 設計文書への都度指示で一周し、**痛みが観測されてから**スキル化する。

試走の観測項目 = 最初の eval 素材: 解明の往復回数 / 各ゲートの承認時間と読み味 /
前提崩壊の有無 / QA NG の有無 / フェーズ別コンテキスト消費。
