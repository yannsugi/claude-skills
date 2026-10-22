# github-flow

Claude Code 向けの **開発フロー設計** と、そこから起こすスキル群。

## 目的

「GitHub Issue を AI と一緒に解く」ときの工程・人間ゲート・資産化ループを設計し、その設計に沿った SKILL.md / 書式を実装する。

- 開発者の労力を通時的に最小化し、品質を最大化する。承認は労力ではなく **理解** を生産する投資
- 人間が理解するのは **要件の正しさ / 判断の方向 / 翻訳の一致** の3点だけ。実装の正しさは E2E の観測と指標(前提崩壊率など)が担保する
- 人間は散文を読まない。承認の対象は表と格子で出す

## 実行順とスキル

スキル名の先頭はレイヤ(req=要件 / design=設計 / verify=検証 / improve=資産化)とレイヤ内の順序。辞書順は実行順と一致しないので、順序はこの表で見る。

| 順 | スキル | 内容 | 産物 |
|---|---|---|---|
| 1 | `req-1-triage-issue` | S/M/L 判定・根拠一行・完了基準 C1〜Cn の採番 | トリアージブロック |
| 2 | `req-2-survey-codebase`(L のみ) | 入口・経路の前提確認。領域×観点マトリクスの全セル消込。C ごとの「既存で満たせるか/新規の仕組み/repo 外依存」の表、前例、ドメイン語の横断探索(Issue 未記載の関わり)、影響マップ、system-map(データの流れの行) | 網羅的調査 |
| 3 | `req-3-clarify-expectation` | 業務背景・完了基準(子 Issue 列)・状態×操作の格子・受け入れ基準の表(T)。文末に懸念・コンテキスト・変更履歴。要件確認は別コメント(投稿は別指示)。本文の書式は `assets/expectation.md`、要件確認は `assets/questions.md` | 実装前の要件・受け入れ基準の整理 → 承認 |
| 4 | `design-1-propose-with-diagram` | T ごとの対応可否の目次表・変更がある機能単位の節・図(任意)・設計判断の根拠と⚠懸念点の 2 段・却下案と実装メモ(AI 用)は折りたたみ。その Issue で完結させる | 実現案 → 承認 |
| — | (実装) | 自作スキルなし。実装メモに沿って実装し、前提が崩れたら申告して止まる | PR |
| 5 | `verify-1-traceability` | T→テスト対応表・エビデンス・未カバー・エスカレーション | 照合結果 → 承認 |
| 6 | `improve-1-distill-feedback`(試走のあと) | 試走の指摘を台帳の行(プロダクト固有語なし)と改善 Issue draft に変換。マージ後に台帳の状態を更新。書式は `assets/ledger.md` と `assets/issue-draft.md` | 台帳・改善 Issue draft |

C と T の ID が工程を跨ぐ唯一の参照で、途中で振り直さない。

| path | 内容 |
|---|---|
| `DESIGN.md` | 設計思想。スキルはここを参照する。今のフローを良くする形に常に更新する |
| `github_issue_flow.html` | DESIGN.md の図解版。[ブラウザで開く](https://yannsugi.github.io/claude-skills/plugins/github-flow/github_issue_flow.html) |
| `references/principles.md` | 全スキル共通の原則(出力先と投稿の単位・書式・`#番号` と固有語の扱い・技術事実の断定・C/T・分割の条件) |
| `skills/<name>/SKILL.md` | 各スキル。`model:` で実行モデルを指定(全て Opus 5.5。判定役は Fable 5.1) |

## 使い方

```bash
claude plugin marketplace add yannsugi/claude-skills
```

```bash
claude plugin install github-flow@claude-skills
```

SKILL.md 内の `${CLAUDE_PLUGIN_ROOT}` はこのディレクトリを指す。対象は既存の GitHub Issue で、新規起票用のテンプレートは持たない。

Issue 番号ではなくローカル Markdown を対象にした試走もできる(各 SKILL.md 末尾の試走モード参照)。

成果物の出力先は作業用のメモ置き場。repo への書き込み(system-map・コミット)と GitHub への書き込み(`gh issue comment` / `gh issue edit` / `gh pr comment` / `gh issue create` / ラベル付け)は、どのスキルも利用者が明示的に指示した場合にのみ行う。投稿は単位ごとに指示を取る(本文の投稿指示は要件確認コメントに及ばない)。読み取り(`gh issue view` 等)は自由。

## 更新のしかた

試走で見つかった改善点は `improve-1-distill-feedback` で台帳に落とし、改善 Issue の draft にまとめる。PR でスキル本体・assets・principles・DESIGN.md・図解を同時に直し、マージ後に台帳の状態を「反映済(版)」にする。経緯の記録は git に任せ、文書には今の状態だけを書く。

未実装: NG 調査・skill-gardener・図解突合・map diff。痛みを観測してから作る。
