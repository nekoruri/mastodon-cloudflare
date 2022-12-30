Mastodon with Cloudflare
========================

MastodonインスタンスをCloudflareと組み合わせて使うためのDocker Composeサンプルです。

![techreport_202212_figs](https://user-images.githubusercontent.com/210445/210085873-0cd5c8ea-4ed8-4a28-af7d-0ff01a1caa60.png)

## HOWTO

### セットアップ

あらかじめ以下を入手しておきます。

* Cloudflare R2のAPIトークン一式
* コンテンツ用R2バケット
  * `Setting` - `Add a acustom domain`からサブドメインを割り当てておく
* バックアップ用R2バケット
* Cloudflare Tunnelのトークン
  * `Create Tunnel`し、コネクタのインストールコマンドに含まれる`eyJ`ではじまる文字列

```
cp .env.production.sample .env.production
cp .env.db.production.sample .env.db.production
cp .env.redis.production.sample .env.redis.production
cp .env.cloudflared.production.sample .env.cloudflared.production

docker compose build

docker compose run --rm web rails mastodon:setup
```

`mastdon:setup`で生成された`.env.production`に以下を追加します。

```
S3_ENDPOINT=https://CLOUDFLARE_ACCOUNT_ID.r2.cloudflarestorage.com
S3_PERMISSION=
```

以下にバックアップ用R2バケットの情報を設定します。

* `.env.db.production`
* `.env.redis.production`

以下にCloudflare Tunnelのトークンを設定します。

* `.env.cloudfloared.production`

ここまでできたら起動できるようになっているはずです。

```
docker compose up -d
```

### バックアップ

`crontab -e`

```crontab
0 3 * * * cd /home/masa/mastodon-cloudflare && docker compose run db /usr/local/bin/wal-g-pg backup-push /var/lib/postgresql/data
0 3 * * * cd /home/masa/mastodon-cloudflare && docker compose run redis /usr/local/bin/wal-g-redis backup-push
```

### リストア

```
docker compose run db /usr/local/bin/wal-g-pg backup-list
docker compose run redis /usr/local/bin/wal-g-redis backup-list
```

```
docker compose run db /usr/local/bin/wal-g-pg backup-fetch /var/lib/postgresql/data base_00000002000000000000000F
docker compose run redis /usr/local/bin/wal-g-redis backup-fetch stream_20221230T064714Z
```
