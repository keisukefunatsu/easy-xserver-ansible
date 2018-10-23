# Ansibleを使ってxserverに WordPress 環境を作成します

Ansible, WP-CLI を用いて、実案件で使える程度の最低限の環境を作成することを目指します。

## できること（予定）

- wp-cli の導入
- WordPress の初期構築
- プラグインの導入
- バックアップ、アップデートスクリプトの設置、cron 設定

## 環境

macOS での動作を想定しています。
windows 版はこちら

- https://docs.ansible.com/ansible/latest/user_guide/windows.html

## 仕様、できること
- WP-CLIが使えるようになります。
- 本番環境、ステージング環境を作成でき、ともにディレクトリはwww以下に作られます。
- バックアップ、アップデートスクリプトの設置、cron設定が可能です。
  - バックアップはDB,`wp-content/uploads`以下のファイルに対して行います。
  - アップデートはコア、言語ファイル、テーマ、プラグインに対して実行されます。
- テストデータの挿入が可能です。
- 予め決めておいたプラグインを構築と同時に導入できます。
  - プラグインは各環境毎に独自のもの、どの環境にも共通のものに分けて設定することができます。
- 変数はconfig.ymlを別途作成して記入します。config-sample.ymlをリネームするなどして使ってください。


## 準備

homebrew で ansible をインストールしてください

- `brew install ansible`

## Xserverの設定
- xserver_host_name: "username.xsrv.jp"
- xserver_user_name: "username"
- xserver_db_host: "mysqlxxxx.xserver.jp"
- xserver_db_user: "db_user_name_here"
- xserver_db_password: "db_password_here"
- ssh_key_file_path: "~/.ssh/id_rsa"

**この設定の場合、毎日0:30に実行される**
- `cron_hour`: cronの実行時間を設定します。例）0
- `cron_minute`: cronの実行時間の分数を設定します。例）30
- `cron_enable`: cronを設定する場合はtrue 必要ない場合はfalse

**WordPressの設定は wordpress_info以下に辞書形式で書いていきます**
**詳しくはconfig-sample.ymlを参照してください**
- `directory`: ディレクトリ名、~/www以下に作成されます　例）"production"
- `db_name`: データベース名 例）user-name_production
- `db_prefix`: データベースの接頭辞（オプション）、デフォルトはwp_　例）"wp_pro_"


**以下はWordPressの設定項目**
- `admin_user_name`: 本番環境の管理者ユーザ名を設定します。
- `admin_password`: 本番環境の管理者のパスワードを設定します。
- `admin_mail`: 本番環境の管理者のメールアドレスを設定します。　例）test@example.com
- `site_url`: 本番環境のURLを設定します。　例）"https://easy-ansible-sakura.tk/"
- `site_title`:　本番環境のサイトタイトルを設定します。　例） "WordPress　demo site production"
- `extra_php` : wp-config.phpを作成する際に追記するphpを記入します。
例)

```
extra_php: |
  define( 'WP_DEBUG', true );
  define( 'WP_DEBUG_LOG', true );
  define( 'DISALLOW_FILE_EDIT',true );
```

- `test_data`: テストデータが必要な場合はtrue, 必要ない場合はfalse 例）true
- `plugins`: 本番環境のプラグイン（複数指定可能）を設定します。

**以下は共通設定の項目**
- `common_plugins`:　本番、ステージング両方の環境に入るプラグインを設定します。（複数指定可能）



### config.ymlをプロジェクトで共有する場合
安全のために以下の手順でconfig.ymlを暗号化してから共有することをおすすめします。
- `.gitignore`の中の`config.yml`を削除
- `ansible-vault encrypt config.yml`を実行し、パスワードを入力して暗号化した後push

## 参考リポジトリ

- https://github.com/youcune/wordpress-ansible-sakura
- https://github.com/wate/tools/tree/master/sakura_rentalserver
