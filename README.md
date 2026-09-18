# GNAS — Git-Native Autonomous System

Gitリポジトリ内のMarkdownとGitプリミティブだけで、コーディングエージェントの長期作業を計画・実行・検証・復帰可能にするための運用プロトコルです。

**Current version: 1.1**

## GNAS 1.1 の主な機能

- Gitネイティブな計画・実行管理
- 人間による実行認可境界
- Phase単位の客観的検証
- 失敗時の安全なロールバック
- ビルド失敗時の原因分析と解決策提示
- `resume/` による途中停止・セッション切断・レート制限からの再開
- ユーザー所有のビルド設定Markdownを読み取り専用で参照
- 小規模作業向け `GNAS-lite`

## どちらを使うか

| Profile | ファイル | 用途 |
|---|---|---|
| Full | `Agents.md` | 長期開発、複数Workstream、複数Phase、厳格な履歴・認可管理 |
| Lite | `GNAS-lite.md` | 小規模修正、短いセッション、少ないファイル変更 |

迷った場合、長期化・複雑化する可能性がある作業ではFullを使用してください。

## Full profile の基本構成

```text
build-tools.md                # 任意。ユーザーが用意するビルド設定。原則読み取り専用
resume/
├── current.md                # 実行中の再開チェックポイント
└── history/                  # 任意のResume履歴
plan/
├── master.md                 # M書: 全体戦略
├── ledger.md                 # L書: 圧縮された現在状態
├── queue.md                  # Q書: 現サイクルの実行認可
├── history/                  # 完了Queue
├── insights/
│   └── index.md              # 知見・制約
└── wsXXX-*/
    ├── ws.md                 # W書: Workstream
    └── phaseYYY/
        └── phase.md          # P書: 実行手順と検証
```

## 1.1で追加されたResume

`plan/ledger.md` と `resume/current.md` は役割が異なります。

- `plan/ledger.md`: プロジェクト全体の「現在地」を圧縮して保持
- `resume/current.md`: 実行途中の「どこから再開するか」を保持

レート制限やツール停止が起きても、エージェントは `resume/current.md` とGitの状態を照合して作業を続けられます。

## ビルドツール設定Markdown

ユーザーはリポジトリ直下に `build-tools.md` を用意できます。別のパスを明示指定しても構いません。

想定する内容:

```markdown
# Build Tools

- OS: Windows 11
- Compiler: Visual Studio 2022
- CMake: 3.30+
- Node.js: 24.x
- Build command: npm run build
- Test command: npm test
```

このファイルは**ユーザー所有**です。エージェントはビルド診断の優先資料として読みますが、ユーザーが明示的に編集を依頼しない限り、作成・変更・整形・削除を行いません。

## ビルド失敗時の扱い

GNAS 1.1では、エージェントは「ビルド失敗」で止まるだけではなく、可能な範囲で次を行います。

1. エラーとツールチェーン情報を保存
2. 原因の分類
3. 非破壊的な確認と修復
4. 自動解決できない場合は具体的な解決策を提示
5. 必要な知見を記録
6. 壊れた変更を残さない

## 導入

### Full

リポジトリに `Agents.md` を置き、エージェントにその規則へ従うよう指定します。必要に応じて `plan/` と `resume/` を作成します。

### Lite

小規模なリポジトリでは `GNAS-lite.md` を使用します。M/W/P/Qの完全な計画階層を省略しつつ、検証、Resume、ビルド診断、読み取り専用ビルド設定の規則を維持します。

## ファイル

- `Agents.md` — GNAS 1.1 Full specification
- `GNAS-lite.md` — GNAS 1.1 simplified specification
- `resume/README.md` — Resumeディレクトリの運用説明
- `LICENSE` — License

## License

本リポジトリのライセンスは `LICENSE` を参照してください。
