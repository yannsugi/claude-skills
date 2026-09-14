# claude-skills

個人用の Claude Code プラグイン集(マーケットプレイス)。ローカルで使う前提で、この repo から取得する。

## 使い方

```bash
claude plugin marketplace add yannsugi/claude-skills
```

```bash
claude plugin install github-flow@claude-skills
```

更新を取り込むときは `claude plugin marketplace update claude-skills`。

## プラグイン

| plugin | 内容 |
|---|---|
| [`plugins/github-flow/`](plugins/github-flow/) | GitHub Issue を AI と解くフロー。設計思想(DESIGN.md)と、トリアージ→網羅的調査→解明→実現案→品質担保の照合までのスキル群。[図解を開く](https://yannsugi.github.io/claude-skills/plugins/github-flow/github_issue_flow_v3.html) |

## レイアウト

```
.claude-plugin/marketplace.json   # このカタログ。pluginRoot = ./plugins
plugins/<name>/                   # 1プラグイン1ディレクトリ
  .claude-plugin/plugin.json
  skills/<skill>/SKILL.md
  README.md
```

新しいプラグインは `plugins/` に追加し、marketplace.json の `plugins` に1行足す。
`claude plugin validate .` で検証できる。

## License

MIT
