# WSL + PostgreSQL + DBeaver 開発環境構築手順（ローカル学習用）

## 🧱 1. PostgreSQLのインストール (WSL Ubuntu内)

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

### クラスタの確認

```bash
pg_lsclusters
```

---

## ⚙️ 2. PostgreSQLサーバーの起動

```bash
sudo pg_ctlcluster <バージョン> main start
# 例: sudo pg_ctlcluster 17 main start
```

---

## 👤 3. ユーザー・データベースの作成

```bash
sudo -u postgres psql
```

psql内で:

```sql
CREATE USER myuser WITH PASSWORD 'mypassword';
CREATE DATABASE mydb OWNER myuser;
\q
```

---

## ⚙️ 4. 外部接続を有効化

### postgresql.conf:

```bash
sudo nano /etc/postgresql/17/main/postgresql.conf
```

変更:

```conf
listen_addresses = '0.0.0.0'
```

### pg_hba.conf:

```bash
sudo nano /etc/postgresql/17/main/pg_hba.conf
```

追加:

```conf
host    all             all             0.0.0.0/0               scram-sha-256
```

---

## 🔄 5. PostgreSQL再起動

```bash
sudo pg_ctlcluster 17 main restart
```

---

## 🧠 6. PostgreSQLがリッスンしているか確認

```bash
ss -lntp | grep 5432
```

例:

```
LISTEN 0 128 0.0.0.0:5432 ...
```

---

## 🌐 7. WSL内のIPアドレスを取得 (Windowsからの接続用)

```powershell
wsl hostname -I
# 172.25.171.128 などが得られる
```

---

## 🧭 8. DBeaverの接続設定 (Windows)

| 項目     | 設定値                        |
|----------|-------------------------------|
| Host     | 172.25.171.128                |
| Port     | 5432                          |
| Database | mydb                          |
| Username | myuser                        |
| Password | mypassword                    |
| Auth     | Database Native (デフォルト) |

---

## ✅ 補足: ログエントリを増やしたい場合

`postgresql.conf` に以下を追加:

```conf
log_connections = on
log_disconnections = on
```

再起動:

```bash
sudo pg_ctlcluster 17 main restart
```

---

## 【実行完了後の状態】

- `pg_lsclusters` で Status = online
- `sudo -u postgres psql` で psql ログイン可
- DBeaver から接続成功

---

これで PostgreSQL を WSL で動かせて DBeaver から利用するための環境構築が完成です。
