# Copilot Workspace: 概要

[Copilot Workspace](https://githubnext.com/projects/copilot-workspace/) は、_タスク中心の_ AIアシスタントです。開発者として、あなたは日々タスクから始まり、それを探索し、理解し、改善し、完了させる旅に出ます。この旅は、刺激的で、挑戦的で、魅力的で、やりがいのあるものです。Copilot Workspaceは、タスクから動作するコードへの旅を、あらゆる段階でサポートします。

Copilot Workspaceは、以下の原則に基づいて設計・開発されています：

* **Copilot Workspaceは_コンテキストを理解_します。**  
  GitHubと深く統合されており、リポジトリ、Issue、Pull Requestなど、タスクのコンテキストを把握します。

* **Copilot Workspaceは_アシスト_します。**  
  未知のタスクを進めるためのキャンバスを提供し、新しい形のAI支援によって開発スキルを向上させます。

* **Copilot Workspaceは_広範に適用可能_です。**  
  GitHub上の有効なすべてのリポジトリのIssueで利用でき、新しいコードを始める際にもテンプレートリポジトリで使用できます。

* **Copilot Workspaceは_反復的_です。**  
  AIが生成した出力を確認し、レビューし、修正し、繰り返すことを推奨します。最終的な決定権は開発者にあります。

* **Copilot Workspaceは_協力的_です。**  
  セッションをチームと共有し、IssueやPull Requestにリンクを追加できます。リポジトリ管理者向けに、AI支援開発の管理機能も提供します。

* **Copilot Workspaceは_設定可能_です。**  
  特定のタスクを指定することで、Copilot Workspaceをワークフローに統合できます。

このマニュアルでは、Copilot Workspaceの概念や機能について説明し、効果的に使用する方法を案内します。

<img src="images/overview/session.png" width="600" alt="A fully-implemented workspace session">

*A fully-implemented workspace session*

## 機能一覧

1. [タスク](#タスク)
1. [仕様作成](#仕様作成)
1. [計画](#計画)
1. [実装](#実装)
1. [ファイルの反復修正](#ファイルの反復修正)
1. [統合ターミナル](#統合ターミナル)
1. [セッション共有](#セッション共有)
1. [タスク完了](#タスク完了)
1. [セッションダッシュボード](#セッションダッシュボード)

## タスク

Copilot Workspaceのすべての作業は「タスク」から始まります。タスクは、自然言語で記述された意図の説明です。タスクには常にコンテキスト（GitHubリポジトリ）が関連付けられます。

この技術プレビュー版では、Copilot Workspaceは以下の4種類のタスクをサポートします：
- Issueの解決
- Pull Requestの改善
- [テンプレートからのリポジトリ作成](creating-repos.md)
- アドホックタスク

特に、最も一般的なエントリーポイントであるIssueの解決について説明します。

技術プレビューに登録すると、GitHubのすべてのIssueに **"Open in Workspace"** ボタンが表示されます。

<img src="images/general/open-in-workspace.png" width=400 alt="Button on issue page to open in Copilot Workspace">

*IssueをCopilot Workspaceで開く*

このボタンをクリックすると、対象のIssueのコンテキストに基づいてCopilot Workspaceが開かれます。Issueタスクの場合、タスクの内容はIssueのタイトルと本文、およびコメントスレッドに基づいて決まります。Copilot Workspaceはすぐに次のステップに進みます。

<img src="images/overview/issue-timeline-representation.png" width=600 alt="Issue task timeline representation">

*タスクは「Issue」としてラベル付けされ、分析が開始される*

## 仕様作成

複雑なタスク（例：長いコメントスレッドを持つIssue）の要点をまとめるために、Copilot Workspaceはまず「トピック」を生成します。これはコードベースに対して提起できる質問の形を取り、タスクの前後の成功基準を定義するために使用されます。

<img src="images/overview/topic-question.png" width=600 alt="Topic question">

*トピックが、Issueのタイトルだけでは明確でなかった部分を整理する様子*

トピックは、タスクの本質を明確にし、Copilot Workspaceが適切な方向に進んでいるかをすぐに確認するための手段です。もしトピックが間違っていれば、先に進む必要はありません。正しければ、タスクをより深く理解し、コードベースの最も重要な部分に集中できます。

この後、Copilot Workspaceはコードベースの現在の動作をリストアップします。これは、Copilot Workspaceが適切な方向に進んでいることを確認し、現在の状態を理解する手助けとなります。

<img src="images/overview/current-spec.png" width=600 alt="Current specification">

*現在の仕様が、トピックの質問に基づいて現在の状態を説明*

Copilot Workspaceが誤った情報を生成した場合、手動で編集・削除できるほか、新しい仕様を完全に再生成（「Try again」）することも可能です。

現在の仕様の後、Copilot Workspaceは「提案仕様」を生成します。これは、タスクを完了した後のコードベースの状態を示し、成功基準を定義します（[計画](#plan)とは異なり、実装の詳細は含みません）。

<img src="images/overview/proposed-spec.png" width=600 alt="Proposed specification">

*提案仕様は、タスクを解決するためにコードをどのように編集するかを示す*

## コンテンツ選択

現在と提案の仕様を生成するため、Copilot Workspaceはタスクの理解と完了に必要なファイルを識別します。これには、大規模言語モデル（LLM）技術と従来のコード検索が組み合わされます。最も関連性の高いファイルの内容が、ワークフローのほぼすべてのステップで使用されます。

ユーザーは、仕様パネルの **「View references」** ボタンを使用して、Copilot Workspaceが選択したファイルを確認できます。必要に応じて、タスクを編集し、関連するファイルを指定することも可能です。

<img src="images/overview/references.png" width=600 alt="Show references dialog">

*モデルが元の仕様と修正後の仕様を生成するために使用した参照情報*
