# 授業で使える Ansible ラボ: プレイブックでマシンを構成する

[English](README.md) | [Français](README.fr.md) | [Español](README.es.md) | [日本語](README.ja.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-ansible-basics/actions/workflows/check.yml)

インフラストラクチャ・アズ・コードを教えるための、すぐ使える **Ansible ラボ**です。学校が生徒一人ひとりに専用のマシンとブラウザー上のターミナルを提供する [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)から提供しています。生徒ごとに **Ansible** が入った Ubuntu VM と、未完成の**プレイブック**を含むプロジェクトが用意されます。生徒はそれを仕上げて、**パッケージのインストール**、**変数を使った Jinja2 テンプレートからのファイル作成**、**systemd ユニットファイルの作成**を行い、もう一度実行して `changed=0` になることを確かめます。これが**冪等性**で、Ansible を学ぶ価値を生む考え方です。生徒のコンピューターには何もインストールしません。

## 生徒が行うこと

ラボは `~/ansible-lab` にプロジェクト（このマシン 1 台をローカル接続で指す `inventory.ini`、`ansible.cfg`、テンプレート、TODO が 3 つ残った書きかけの `site.yml`）を置き、ホームフォルダーに何をするかを説明する `README.md` を置きます。答えは書かれていません。

1. パッケージモジュールでパッケージをインストールする。
2. テンプレートから `/etc/ansible-lab/welcome.txt` を作る。所有者は root、モードは 0644。
3. 説明にサイト名の変数を含む systemd ユニットファイルを書く。
4. プレイブックをもう一度実行する。2 回目は `changed=0` と表示されなければならない。

## 採点のしくみ

このラボには**チェック**があります。提出された VM を、教員が中身を読まなくても採点するスクリプトです。保存されたマシンのコピーを新しく起動して実行します。YAML を読み、`shell`、`command`、`raw`、`script` のタスクだけでできたプレイブックや、include とロールは受け付けません。保存されたパッケージとファイルを読み、さらに**誰も見たことのないサイト名で生徒のプレイブックを 2 回実行します**。プレイブックが作ったものにそのサイト名が含まれていなければならず（手で書いたファイルでは満たせません）、2 回目は `changed=0` でなければなりません（毎回何かを変更するタスクでは満たせません）。出力の 1 行目に、何が正しくないかが表示されます。

チェックがしないこと: ユニットファイルは読みますが、サービスは起動しません。また、自分の VM で root 権限を持つ生徒なら、Ansible 自体を改ざんすることは可能です。これは授業のためのチェックであり、証明ではありません。

## ファイルの内容

`ansible-basics.lab.yaml` は、プラットフォームがそのまま実行する **VM ラボ定義**一式です。

| キー | 内容 |
| --- | --- |
| `cpus`、`memory`、`disk` | 生徒ごとの VM のサイズ: CPU 1 個、1024 MB、10 GB |
| `open` | ブラウザーで開くポート: なし。ラボはターミナルです |
| `setup` | 各生徒の VM の初回起動時に root で 1 回だけ実行されるスクリプト。Ubuntu のアーカイブから `ansible-core` をインストールし（約 10 秒）、プロジェクトと README を書き込みます |
| `check` | 採点スクリプト。新しく起動した後、保存されたディスクのコピー上で root として実行されます。終了ステータスが結果で、`0` なら合格です |

## プラットフォームで使う

**クラスの課題として配布するには:**

1. モジュールで課題を追加し、**Ubuntu VM ラボ**のカードを選びます。
2. Ansible のサンプルを押すか、`ansible-basics.lab.yaml` の内容を貼り付けます。サンプルを使うとテキストが置き換わりますが、元に戻すで自分のテキストに戻せます。
3. 公開して、まず自分用のコピーを試します。生徒ごとに専用の VM が用意され、セッションをまたいで保たれます。
4. ほかの課題と同じように配布します。提出されたら、クラス一覧の生徒の行でチェックを実行します。保存された生徒のマシンのコピーが起動し（保存し直されません）、合格したかどうかと出力が表示されます。

詳しくはヘルプマニュアルの[このラボのページ](https://mitsurutech.co.uk/ja/help/lab-ansible-basics)、[Ubuntu VM ラボのガイド](https://mitsurutech.co.uk/ja/help/labs)、[サンプルラボのページ](https://mitsurutech.co.uk/ja/help/sample-labs)をご覧ください。

学校のアカウントはまだですか。[最初の 1 年間は無料](https://mitsurutech.co.uk/ja/pricing)で、生徒 50 人まで利用できます。[登録](https://mitsurutech.co.uk/ja/signup)にはメールアドレスだけが必要です。

## ファイルの検証

push のたびに `.github/workflows/check.yml` が、ファイルが上記の 5 つのキーを持つ有効な YAML であること、`setup` と `check` がそれぞれプラットフォームの 16 KB の上限に収まっていることを確認します。VM は起動しません。それはプラットフォームのマシンで行われます。

## ラボでできないこと

VM から到達できるのは Ubuntu のパッケージアーカイブ、PyPI、npm、GitHub だけで、それ以外には接続できません。そのため、このラボは **Ansible のみ**です。Terraform は VM から届かないレジストリからプロバイダーをダウンロードするので、提供していません。対象は VM 自身で、1 台のマシンなのでローカル接続を使います。

## このラボについて

[Mitsuru Technologies](https://mitsurutech.co.uk/ja) が [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のために作りました。学校向けに、Python、Java、Go、Node、SQL、Linux をブラウザーで学べ、コンテナーラボ、VM ラボ、GitHub からデプロイする Web アプリ、自動採点のクイズも備えています。アカウントなしでコードを試せる[プレイグラウンド](https://mitsurutech.co.uk/ja/playground)もあります。

同じ提供元のほかのラボ: [lab-netlab](https://github.com/mitsurutech/lab-netlab)、[lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop)、[lab-dvwa](https://github.com/mitsurutech/lab-dvwa)、[lab-webgoat](https://github.com/mitsurutech/lab-webgoat)、[lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin)、[lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea)、[lab-sample](https://github.com/mitsurutech/lab-sample)。

## ライセンス

この作品は [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) でライセンスされています。Mitsuru Technologies Ltd のクレジットを表示し、商用に使わず、これをもとに作ったものを同じライセンスで共有するなら、学習と授業のために共有・改変できます。全文は `LICENSE` ファイルにあります。

[Ansible](https://www.ansible.com)（`ansible-core`、GPL-3.0）は VM の起動時に Ubuntu のアーカイブからインストールされます。ここでは何も配布していません。
