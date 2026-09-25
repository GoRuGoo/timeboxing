# Codex Timeboxing

通常のMarkdownタスクリストとGoogle Calendarを組み合わせて、その日の時間割をCodexに提案・登録してもらうための環境です。

Codexは、Calendarの既存予定と`Inbox.md`の未完了タスクから空き時間を計算します。最初は提案だけを行い、内容を確認して明示的に承認した後に限り、Google Calendarへ予定を登録します。

## 前提

- CodexでGoogle Calendarプラグインに接続済みであること
- `daily-timeboxing` Skillが`~/.codex/skills/daily-timeboxing`にインストールされていること
- タイムゾーンは既定で`Asia/Tokyo`

GoogleのパスワードやOAuthトークンは、このディレクトリには保存しません。Google Calendarの認証はCodex側の接続機能が管理します。

## ファイル構成

```text
~/timeboxing/
├── README.md
├── Inbox.md
├── AGENTS.md
└── Daily/
    └── YYYY-MM-DD.md
```

- [`Inbox.md`](./Inbox.md): 未完了タスクを管理します。
- [`AGENTS.md`](./AGENTS.md): 作業時間、休憩、通知、安全ルールなどの設定です。
- `Daily/YYYY-MM-DD.md`: 提案、確定した時間割、Calendar登録結果、任意の作業ログです。

## 基本的な使い方

### 1. Inboxへタスクを書く

`Inbox.md`の`## Inbox`以下へ、未完了タスクをチェックボックス形式で追加します。

```markdown
- [ ] ManabirubeのAPI実装 | priority: high | estimate: 120m | type: deep
- [ ] 大学の課題 | priority: medium | estimate: 90m | type: deep
- [ ] メール返信 | priority: low | estimate: 30m | type: admin | people: yes
```

タスク名だけでも利用できます。

```markdown
- [ ] Manabirubeの実装
- [ ] 大学の課題
```

主なメタデータは次のとおりです。

| 項目 | 意味 | 例 |
|---|---|---|
| `priority` | 優先度 | `high`, `medium`, `low` |
| `estimate` | 見積時間 | `30m`, `90m`, `120m` |
| `type` | 作業の種類 | `deep`, `implementation`, `admin` |
| `people` | 他の人が関わるか | `yes`, `no` |
| `time` | 開始・終了を固定 | `13:00-14:30` |
| `window` | 配置可能な時間帯 | `15:00-18:00` |
| `before` | この時刻までに終了 | `16:00` |
| `reminder` | 通知時間 | `15m`, `none` |

例：

```markdown
- [ ] 大学の課題 | time: 13:00-14:30 | priority: high
- [ ] Manabirubeの実装 | window: 15:00-18:00 | estimate: 90m
- [ ] メール返信 | before: 16:00 | estimate: 30m | reminder: 10m
```

### 2. Codexへ依頼する

Codexで次のように指示します。

```text
今日のタイムボックスを作って
```

Skillを明示的に指定する場合：

```text
$daily-timeboxing 今日のタイムボックスを作って
```

Codexは次の処理を行います。

1. `Inbox.md`の未完了タスクを読む
2. 今日のGoogle Calendar予定を読む
3. 既存予定と休憩を除いた空き時間を計算する
4. 優先度、見積時間、作業の種類を考慮して時間割案を提示する

この段階ではCalendarへ書き込みません。

### 3. 時間割を修正する

提案に対して、普通の文章で修正を依頼できます。

```text
大学の課題を午前にして
Manabirubeは午後に90分だけ
今日は10時から17時の間で組んで
16時以降には予定を入れないで
```

総見積が空き時間を超える場合、Codexは無理に詰め込まず、入らない時間と延期候補を提示します。

### 4. Calendar登録を依頼する

案がよければ、次のように伝えます。

```text
この案でGoogle Calendarに登録して
```

Codexは登録前に、次の内容を一覧表示します。

- 操作内容
- 登録先カレンダー
- タイトル
- 日付
- 開始・終了時刻
- タイムゾーン
- 通知設定

一覧を確認し、問題なければ次のように明示的に承認します。

```text
この一覧で実行してください
```

承認後、Codexは直前にCalendarを再確認し、重複や新しい競合がなければ登録します。登録後はCalendarを再度読み取り、結果を`Daily/YYYY-MM-DD.md`へ記録します。

## 通知

新しく作成するタイムボックスには、指定がなければ開始5分前のポップアップ通知が付きます。

別の通知時間を指定する例：

```text
すべて15分前に通知して
```

タスク単位で指定する例：

```markdown
- [ ] 大学の課題 | estimate: 90m | reminder: 15m
- [ ] 資料整理 | estimate: 30m | reminder: none
```

既存のCalendar予定の通知設定は、明示的な依頼と承認がない限り変更しません。

## タスクの完了

Calendarへ登録しただけでは、Inboxのタスクは完了になりません。作業が終わったら`Inbox.md`でチェックを付けます。

```markdown
- [x] ManabirubeのAPI実装 | priority: high | estimate: 120m
```

Codexへ更新を依頼することもできます。

```text
ManabirubeのAPI実装が完了したのでInboxを更新して
```

## 既定設定

現在の既定値は次のとおりです。変更する場合は`AGENTS.md`を編集します。

- 計画時間：09:00–18:00
- 昼休み：12:00–13:00
- 最小単位：30分
- 未割当バッファ：1日30分以上
- Calendarタイトルの接頭辞：`[TB]`
- 通知：開始5分前

当日だけ変更したい場合は、Codexへの依頼文で指定してください。指定内容は恒久設定にはなりません。

## 安全ルール

- Calendarの読み取りは、提案作成のために確認なしで実行できます。
- 作成・変更・削除は、必ず内容を一覧表示して承認を得てから実行します。
- 既存予定を勝手に移動・削除しません。
- 同名・同時間の予定は重複登録しません。
- Calendar登録だけではInboxのタスクを完了にしません。
- OAuthトークン、Cookie、APIキー、パスワードをMarkdownやGitへ保存しません。

## Gitで管理する場合

このディレクトリにはGoogle Calendarの認証情報は含まれません。ただし、`Inbox.md`と`Daily/`には個人的なタスクや予定が入るため、リポジトリは非公開を推奨します。

設定やテンプレートだけを公開する場合は、`.gitignore`へ次を追加してください。

```gitignore
Inbox.md
Daily/
```

別環境では、リポジトリを取得した後にGoogle Calendarプラグインを導入し、その環境でGoogleアカウントを改めて接続する必要があります。

## トラブルシューティング

### Skillが自動で選ばれない

明示的に呼び出してください。

```text
$daily-timeboxing 今日のタイムボックスを作って
```

### Calendarを読み取れない

Codexの設定からGoogle Calendarプラグインの接続状態を確認し、必要なら再接続します。

### タスクが提案に含まれない

`Inbox.md`で未完了タスクが`- [ ]`形式になっているか確認してください。`- [x]`は完了済みとして扱われます。
