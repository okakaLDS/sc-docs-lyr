# 引き継ぎ資料（2026-06-21時点）

このファイルは、別PC・別Claudeセッションで本プロジェクトを継続するための引き継ぎ資料です。
`git clone` すれば一緒に手に入るよう、リポジトリ内に置いています。

## プロジェクトの目的

半導体顧客向けの**基本設計書サイト**を作る。現時点では中身（実際の設計内容）は
このPC・このリポジトリでは扱わず、**サイトの構造（土台）を作り切るところまで**が
今回のスコープ。

## リポジトリ情報

- ローカル: `C:\Git\sc-docs-lyr`
- GitHub: https://github.com/okakaLDS/sc-docs-lyr （Public）
- 公開先（GitHub Pages, GitHub Actions経由）: https://okakalds.github.io/sc-docs-lyr/
- ビルドツール: **Zensical**（mkdocs-materialチームによる新しい静的サイトジェネレーター。
  mkdocs.ymlやMarkdownはそのまま使える設計だが、まだ開発中でプラグイン互換に制限あり）

## ローカル開発の始め方

```
cd C:\Git\sc-docs-lyr
python -m venv .venv
.venv\Scripts\activate   (PowerShellなら .venv\Scripts\Activate.ps1)
pip install -r requirements.txt
python -m zensical build --strict   # ビルド確認
python -m zensical serve            # ローカルプレビュー（未実施・要確認）
```

`.venv/` は `.gitignore` 済みなので、別PCでは都度作り直すこと。

## デザイン・構成の経緯

本サイトは学習用に作った別のZensicalサイト（このリポジトリの外にある別プロジェクト）の
構成・デザインを土台に作成した。**必要な資産（CSS・画像・ワークフロー等）はすべて
このリポジトリ内にコピー済み**なので、元のプロジェクトを参照する必要はない。

- 配色: インディゴ(#4051B5系) × コーラル/アンバーのグラデーション
  (`docs/stylesheets/extra.css` の `--zen-gradient`)。zensical.org公式サイト準拠。
- admonitionアイコン・前後ナビ矢印: Zensical標準のLucideアイコンセット
- ロゴ/favicon: `docs/images/logo.png` / `favicon.png`（半導体・進捗管理イメージの
  二重正方形アイコン。Figmaの元データは別管理だが、サイト表示に必要なPNGはこのリポジトリ内にある）
- 各ページは「本文／改定履歴」タブ構成（`=== "本文"` / `=== "改定履歴"`）が記載ルール
- awesome-pagesプラグインの `.pages` ファイルでフォルダごとにタイトル・nav順を指定

## 章立て構成（全10章）

ユーザー指定の章立て構造を元に `docs/` 配下にスタブページを作成済み。構造自体は
以下の一覧と `docs/` フォルダの実体として、このリポジトリ内に完全に反映されているため、
元の指示ファイルを別途参照する必要はない。各ページの内容はまだ未着手（`status: "未着手"`）。

1. `01-cover` — 表紙
2. `02-system-requirements` — システム化の要件・基本方針
3. `03-business-design` — 業務設計書
4. `04-common-function-design` — 共通機能設計書
5. `05-system-design-overall` — システム設計書（全体）
6. `06-system-design-lists` — システム設計書（一覧、管理台帳）
   - `07-system-env-config-ledger/` はさらに1階層下（採番一覧／汎用コードマスター項目一覧）
7. `07-database-file-design` — データベース・ファイル設計書
8. `08-system-function-design` — システム機能設計書
   - `01-function-spec/` （機能定義書）は**機能ごとにフォルダを分ける構成**。
     `01-function-a/` をテンプレートとして用意済み（表紙・画面設計・チェック処理概要・
     ファイル出力仕様・更新条件表・検索条件表の6ページ）。
     機能を追加する場合は `01-function-a` フォルダを複製してリネームする。
9. `09-operation-design` — 運用設計
10. `10-infra-design` — インフラ設計

### システム機能一覧との紐付け

`06-system-design-lists/01-system-function-list.md`（システム機能一覧）と
`08-system-function-design/01-function-spec/` の機能フォルダは、**機能ID**（例: F001）で
1対1対応させる運用にした。

- システム機能一覧側: 機能ID・機能名・概要・機能定義書へのリンクの表
- 機能側（各機能フォルダの表紙ページ）: front matterに `function_id` を記載し、
  システム機能一覧へ戻るリンクも記載

機能を追加するときは、①機能定義書側にフォルダ追加、②システム機能一覧の表に1行追加、
の2点をセットで行う。

## ナビゲーション設定（mkdocs.yml の theme.features）

階層が深いため、当初の `navigation.expand`（常時全展開）は外し、
`navigation.sections` + `navigation.indexes` + `navigation.prune` に変更。
デフォルトは折りたたみ、クリックで展開する形にしている。

## 既知の不具合・未対応（Zensicalが開発中であるため）

これらはコード側の問題ではなく、**Zensical本体の未実装・バグ**として確認済み
（zensicalのコア処理はネイティブバイナリ `zensical.pyd` 内にあり、Python側からは
ソースを追えない）。新しいZensicalバージョンが出たら再確認すること。

1. **tagsプラグインのリンクが効かない**: front matterの `tags:` はバッジ表示されるが、
   クリックしてタグ一覧へ飛ぶリンク（`<a>`化）が未実装で `<span>` のまま。
2. **サイドバーの「親階層（折りたたみ見出し）」だけ英語表示になる**:
   例「02 system requirements」のように、フォルダ名（英語スラッグ）をそのまま
   見出しに使ってしまうバグ。`.pages` のtitle指定、親nav側のタイトルマッピング
   （`タイトル: フォルダ名`構文）、フォルダ内 `index.md` のfront matter title、
   いずれを試しても直らないことを確認済み（3パターン検証）。
   実際のページリンク自体（クリック後の遷移先）は正しい日本語タイトルになっている。
   → 本人の意向で、**今は英語表示のまま運用し、Zensicalの修正を待つ**方針。
   フォルダ名の日本語化やMkDocsへの巻き戻しは見送り。
3. **llms.txt未対応**（参考情報。mkdocs-llmstxtの開発者がZensical側に合流中でbacklog扱い）

## GitHub Pages公開について

リポジトリ・サイトともにPublicで運用する方針（実際の顧客固有情報はここでは扱わないため）。
GitHub Pagesは無料プランでも非公開リポジトリで使えるが、**公開URL自体は誰でも見られる**
（Enterpriseでない限り）という制約があるため、将来顧客固有の内容を載せる段階になったら
公開方法を再検討すること。

## 次にやること（未着手）

- 各章・各ページの実際の設計内容の作成（このリポジトリでは未対応、別途検討予定）
- 機能定義書の機能B・機能C…の追加（必要になった時点で `01-function-a` を複製）
- Zensicalのバージョンアップ時に「既知の不具合」セクションの再確認

## 開発方針の好み（参考）

別PCのClaudeセッションにはこのPCのメモは見えないため、関連する好みをここに直接記載する。

- テキストファイルはすべてMarkdown（.md）で作成する
- データ・プロジェクトファイルはできるだけフォルダ内で完結させたい（グローバルインストールを避ける）
- 技術文書サイトでは可読性優先。装飾的UIは避け、シンプルな配色とテーブル表示を好む
  （マイクロインタラクション程度の控えめな演出は好評）
- 専門用語には補足説明を付ける、「なぜそうするのか」の理由を添えるコミュニケーションを好む
