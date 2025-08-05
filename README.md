# contact-form
お問い合わせフォーム作成

## 開発環境
- GitHub からlaravel-docker-template.gitをクローン
```bash
git clone git@github.com:Estra-Coachtech/laravel-docker-template.git
```
- プロジェクト名を「contact-form」に変更
```bash
mv laravel-docker-template contact-form
```
- GitHubの新規リポジトリ(contact-form)作成し、作成したリポジトリのurlを取得
- ローカルリポジトリから紐付け先を変更
```bash
cd contact-form
# 紐づけされているリンクを確認
git remote -v
# 紐付け先変更
git remote set-url origin 作成したリポジトリのurl
```
- 現在のローカルリポジトリのデータをリモートリポジトリに反映
```bash
git push origin main
```

## docker-compose
```bash
docker-compose up -d --build
```
**補足**<br>
- docker-compose.ymlのバージョンの記述は不要だが、<br/>削除するとmysqlのDockerイメージでエラー発生するので、WARNING出ても使用する。
```yml
version: '3.8'
```
- php.iniのUTFの記述はPHP8.2以降では非推奨
```php
mbstring.internal_encoding = "UTF-8"
```

## Laravelのパッケージインストール
- composer.jsonやcomposer.lockを元に必要なパッケージをインストール
```bash
# PHPコンテナ内にログイン
docker-compose exec php bash
```
```php
// PHPコンテナ内にて
composer install
```
php version=8.2 だと、nette/schema などのパッケージは PHP 8.2未満でしか動作しない 仕様なのでエラー発生する。その時は以下でPHP 8.2対応のパッケージへ更新
```php
composer update
```

## timezoneの変更
- src/configにあるapp.phpの'timezone'を日本時間に変更する
```php
// 変更前
'timezone' => 'UTC',
// 変更後
'timezone' => 'Asia/Tokyo',
```
- php artisan tinker で時刻を確認
```php
// PHPコンテナ内にて
php artisan tinker
// 以下のコマンドを入力
echo Carbon\Carbon::now();
```
以下のように現在時刻が出力されることを確認する。<br>
output
```php
2022-04-05 17:26:36
```
- 時刻が合わない場合は、以下のコマンドで設定を反映させて再度確認する
```php
php artisan config:clear
```

## .envファイルの作成
- .env.exampleファイルをコピーして.envファイルを作成
```php
// PHPコンテナ内にて
cp .env.example .env
```
- .envファイルの11行目以降を以下のように修正(データベース接続)
```php
// 前略

DB_CONNECTION=mysql
- DB_HOST=127.0.0.1
+ DB_HOST=mysql
DB_PORT=3306
- DB_DATABASE=laravel
- DB_USERNAME=root
- DB_PASSWORD=
+ DB_DATABASE=laravel_db
+ DB_USERNAME=laravel_user
+ DB_PASSWORD=laravel_pass

// 後略
```
- http://localhost:8080/ にアクセスして「laravel_db」への接続を確認
