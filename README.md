# WordPress 初心者ガイド (忘れないためのMemo)

## 目次
1. [WordPressの種類](#wordpressの種類)
2. [WordPressとは](#wordpressとは)
3. [WSLの準備](#wslの準備)
4. [WordPressの環境構築](#wordpressの環境構築)
5. [基本的な使い方](#基本的な使い方)
6. [テーマのカスタマイズ](#テーマのカスタマイズ)
7. [プラグインの活用](#プラグインの活用)
8. [コンテンツ管理](#コンテンツ管理)
9. [セキュリティ対策](#セキュリティ対策)

## WordPressの種類

WordPressには2つの主要なバージョンがあります：

### 1. WordPress.org（セルフホスティング版）（このガイドで説明するのはこちら）
- 自分でサーバーを用意してインストールする必要があります
- 完全なカスタマイズが可能
- プラグインやテーマの自由な使用が可能
- 技術的な知識が必要
- サーバー費用が別途必要

### 2. WordPress.com（ホスティング版）
- サーバー契約不要
- 簡単に始められる
- 無料プランと有料プランがある
- カスタマイズに制限がある
- プラグインの使用に制限がある
- 技術的な知識が少なくても始められる

### どちらを選ぶべきか？

#### WordPress.orgがおすすめの場合
- 完全なカスタマイズが必要
- 独自のドメインを使用したい
- 広告を表示したくない
- プラグインを自由に使用したい
- 技術的な学習意欲がある

#### WordPress.comがおすすめの場合
- すぐに始めたい
- 技術的な設定をしたくない
- サーバー管理をしたくない
- 小規模なブログやサイトを運営したい
- 予算を抑えたい（無料プランの場合）

## WordPressとは
WordPressは、世界で最も人気のあるコンテンツ管理システム（CMS）の1つです。ブログやウェブサイトの作成、管理を簡単に行うことができます。

### 主な特徴
- 直感的な管理画面
- 豊富なテーマとプラグイン
- カスタマイズの自由度が高い
- SEO対策がしやすい
- モバイルフレンドリー

## WSLの準備

### 1. WSLのインストール
1. Windowsの「スタート」メニューを右クリック
2. 「Windows PowerShell（管理者）」を選択
3. 以下のコマンドを実行：
```powershell
wsl --install
```
4. コンピュータを再起動

### 2. Ubuntuのインストール
1. Microsoft Storeを開く
2. 検索バーで「Ubuntu」を検索
3. 「Ubuntu 22.04 LTS」を選択してインストール
4. インストール後、Ubuntuを起動
5. ユーザー名とパスワードを設定（メモしておきましょう）

### 3. Ubuntuの初期設定
1. パッケージの更新：
```bash
# パッケージリストを最新の状態に更新
sudo apt update

# インストール済みのパッケージを最新バージョンに更新
sudo apt upgrade
```

2. 日本語環境の設定：
```bash
# 日本語の言語パッケージをインストール
sudo apt install language-pack-ja

# システムの言語設定を日本語に変更
sudo update-locale LANG=ja_JP.UTF-8
```

3. タイムゾーンの設定：
```bash
# システムのタイムゾーンを日本時間に設定
sudo timedatectl set-timezone Asia/Tokyo
```

## WordPressの環境構築

### 1. 必要なソフトウェアのインストール
```bash
# パッケージリストを最新の状態に更新
sudo apt update

# 必要なパッケージをインストール
# apache2: Webサーバー
# mysql-server: データベースサーバー
# php: PHP言語本体
# php-mysql: MySQLとPHPの連携用モジュール
# php-curl: HTTPリクエスト用モジュール
# php-gd: 画像処理用モジュール
# php-mbstring: マルチバイト文字列処理用モジュール
# php-xml: XML処理用モジュール
# php-xmlrpc: XML-RPC処理用モジュール
# php-soap: SOAP通信用モジュール
# php-intl: 国際化用モジュール
# php-zip: ZIPファイル処理用モジュール
sudo apt install apache2 mysql-server php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip
```

### 2. MySQLの設定
1. MySQLのセキュリティ設定：
```bash
# MySQLのセキュリティ設定を実行
# - ルートパスワードの設定
# - 匿名ユーザーの削除
# - リモートルートログインの無効化
# - テストデータベースの削除
sudo mysql_secure_installation
```
- パスワードの設定を求められたら、安全なパスワードを設定
- その他の質問には基本的に「Y」で回答

2. WordPress用のデータベースを作成：
```bash
# MySQLに管理者権限でログイン
sudo mysql

# 以下のコマンドを順番に実行
# wordpressという名前のデータベースを作成
CREATE DATABASE wordpress;

# wordpress_userというユーザーを作成し、パスワードを設定
CREATE USER 'wordpress_user'@'localhost' IDENTIFIED BY '設定したいパスワード';

# wordpress_userにwordpressデータベースへの全ての権限を付与
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress_user'@'localhost';

# 権限の変更を反映
FLUSH PRIVILEGES;

# MySQLからログアウト
EXIT;
```

### 3. WordPressのインストール
```bash
# Webサーバーのドキュメントルートに移動
cd /var/www/html

# WordPressの最新日本語版をダウンロード
sudo wget https://ja.wordpress.org/latest-ja.tar.gz

# ダウンロードしたファイルを展開
sudo tar -xzvf latest-ja.tar.gz

# WordPressのファイルの所有者をWebサーバーのユーザー（www-data）に変更
sudo chown -R www-data:www-data /var/www/html/wordpress

# WordPressのディレクトリとファイルに適切な権限を設定
# 755: 所有者は読み書き実行可能、グループとその他は読み実行のみ可能
sudo chmod -R 755 /var/www/html/wordpress
```

### 4. Apacheの設定
1. 設定ファイルの作成：
```bash
# Apacheの設定ファイルを編集
sudo nano /etc/apache2/sites-available/wordpress.conf
```

2. 以下の内容をコピー＆ペースト：
```apache
# 仮想ホストの設定
<VirtualHost *:80>
    # サーバー名をlocalhostに設定
    ServerName localhost
    # WordPressのインストールディレクトリを指定
    DocumentRoot /var/www/html/wordpress
    # WordPressのディレクトリ設定
    <Directory /var/www/html/wordpress>
        # .htaccessファイルの使用を許可
        AllowOverride All
    </Directory>
</VirtualHost>
```

3. 設定の有効化：
```bash
# WordPress用の設定ファイルを有効化
sudo a2ensite wordpress.conf

# URLリライト機能を有効化（WordPressのパーマリンク設定に必要）
sudo a2enmod rewrite

# Apacheを再起動して設定を反映
sudo systemctl restart apache2
```

### 5. WordPressの初期設定
1. ブラウザで `http://localhost/wordpress` にアクセス
2. 言語を「日本語」に設定
3. データベース接続情報を入力：
   - データベース名: `wordpress`
   - ユーザー名: `wordpress_user`
   - パスワード: 先ほど設定したパスワード
   - データベースのホスト: `localhost`
   - テーブル接頭辞: `wp_`（デフォルト）

4. サイトの基本情報を設定：
   - サイトのタイトル
   - 管理者のユーザー名
   - パスワード
   - メールアドレス

### 6. サービスの起動確認
WSLを再起動した場合は、以下のコマンドでサービスを起動：
```bash
# Webサーバー（Apache）を起動
sudo service apache2 start

# データベースサーバー（MySQL）を起動
sudo service mysql start
```

## 基本的な使い方

### 管理画面へのログイン
1. ブラウザで `http://localhost/wordpress/wp-admin` にアクセス
2. 設定したユーザー名とパスワードでログイン

### 主要な機能
- ダッシュボード：サイトの概要を確認
- 投稿：ブログ記事の作成・管理
- メディア：画像やファイルの管理
- ページ：固定ページの作成・管理
- 外観：テーマの設定・カスタマイズ
- プラグイン：機能の追加・管理
- ユーザー：ユーザー管理
- 設定：サイトの基本設定

## テーマのカスタマイズ

### テーマの選び方
1. 公式テーマディレクトリから選択
2. 有料テーマを購入
3. カスタムテーマを作成

### カスタマイズの基本
- カスタマイザーを使用
- ウィジェットの配置
- メニューの設定
- ヘッダー画像の設定

## プラグインの活用

### おすすめのプラグイン
- Yoast SEO：SEO対策
- WP Super Cache：キャッシュ管理
- Contact Form 7：お問い合わせフォーム
- Wordfence Security：セキュリティ対策

### プラグインのインストール方法
1. 管理画面の「プラグイン」→「新規追加」
2. プラグインを検索
3. 「今すぐインストール」をクリック
4. インストール後、「有効化」をクリック

## コンテンツ管理

### 投稿の作成
1. 「投稿」→「新規追加」
2. タイトルを入力
3. 本文を入力
4. カテゴリーとタグを設定
5. アイキャッチ画像を設定
6. 「公開」をクリック

### 固定ページの作成
1. 「ページ」→「新規追加」
2. タイトルと本文を入力
3. 必要に応じてテンプレートを選択
4. 「公開」をクリック

## セキュリティ対策

### 基本的な対策
- 定期的なバックアップ
- プラグインの更新
- 強力なパスワードの使用
- セキュリティプラグインの導入
- SSL証明書の導入

### バックアップの方法
1. データベースのバックアップ
2. ファイルのバックアップ
3. バックアッププラグインの使用

## トラブルシューティング

### よくある問題と解決方法
1. ホワイトスクリーン
   - プラグインを無効化
   - テーマをデフォルトに変更

2. 更新エラー
   - 手動で更新
   - キャッシュのクリア

3. パフォーマンスの問題
   - キャッシュプラグインの使用
   - 画像の最適化

## 参考リンク
- [WordPress公式ドキュメント](https://ja.wordpress.org/support/)
- [WordPress Codex](https://wpdocs.osdn.jp/)
- [WordPressフォーラム](https://ja.forums.wordpress.org/) 