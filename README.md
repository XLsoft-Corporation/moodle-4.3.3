# Moodle 4.3 日本語版 Docker イメージ

日本語言語パックが動作するように、[containers/bitnami/moodle/4\.3/debian\-12 at main · bitnami/containers · GitHub](https://github.com/bitnami/containers/tree/main/bitnami/moodle/4.3/debian-12) のファイルを元に修正した [Dockerfile](./Dockerfile) でビルドしたイメージを `docker compose up -d` するためのメモ

## 準備

- 大本の [GitHub \- bitnami/containers: Bitnami container images](https://github.com/bitnami/containers) を `Git clone` する
- `bitnami/moodle/4.3/` の `debian-12` を本フォルダにコピーする
- [Dockerfile](./Dockerfile) を次のように書き換える (参考: [docker composeで「moodle」を構築する \| mebee](https://mebee.info/2021/05/07/post-33655/))

```txt
RUN echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen && locale-gen
```

の下に

```txt
RUN echo 'ja_JP.UTF-8 UTF-8' >> /etc/locale.gen && locale-gen
```

を追加する

```txt
ENV APACHE_HTTPS_PORT_NUMBER="" \
    APACHE_HTTP_PORT_NUMBER="" \
    APP_VERSION="4.3.3" \
    BITNAMI_APP_NAME="moodle" \
    LANG="en_US.UTF-8" \
    LANGUAGE="en_US:en" \
```

の `LANG` と `LANGUAGE` を以下に書き換える (不要かもしれない。)

```txt
    LANG="ja_JP.UTF-8" \
    LANGUAGE="ja_JP:ja" 
```


- [docker-compose.yml](./docker-compose.yml) を次のように書き換える

```yml
  moodle:
    image: docker.io/bitnami/moodle:4.3
    ports:
      - '80:8080'
      - '443:8443'
```

を

```yml
  moodle:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - '8080:8080'
      - '8443:8443'
```

に書き換える。(Portは環境に応じて書き換えてください。)


## 実行

```sh
docker compose up -d
```

## 実行後の作業

日本語化を行う。(参考: [「moodle」を日本語化する \| mebee](https://mebee.info/2021/05/08/post-33686/))

- ログインページ http://<SERVER_IP>:8080/login/index.php にアクセスし、`user/bitnami` でログイン
- `Site Administration > General タブ > Language > Language packs` に遷移
- `Available language packs` から `Japanese/日本語(ja)` を選択し、`Install selected language pack(s)` をクリック
- ユーザーの `Language` メニューから `日本語(ja)` を選択

以後、新しいユーザーの画面は日本語になると思われる。

