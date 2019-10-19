# kubernetesのAPIドキュメントの分解例

## 確認環境

- rails(Rails 6.0.0)
- ruby(ruby 2.5.3p105 (2018-10-18 revision 65156) [x86_64-darwin18])

## 準備

SwaggerEditor(UI)で開いたりするので以下の準備をしてください。

```bash
$ brew cask install chromedriver
$ docker pull swaggerapi/swagger-ui:latest
$ docker pull swaggerapi/swagger-editor:latest
```

## 試し方

`r2-oas` の設定に関しては、 `config/environments/development.rb` をご覧ください。

OpenAPI(V3)形式に変換したAPIドキュメントが `k8s.yaml` として用意してあるのでそれを使います。

### 分析・分解

```bash
$ OAS_FILE=./k8s.yaml bundle exec rake routes:oas:analyze
```

### SwaggerUIで表示

```bash
$ # 全体を表示する
$ bundle exec routes:oas:ui
$ # 特定のpathsファイル(単体)だけ表示する
$ PATHS_FILE=oas_docs/src/paths/rbac_authorization_v1.yml bundle exec routes:oas:ui
$ # 特定のpathsファイル(複数)だけ表示する
$ echo 'rbac_authorization_v1.yml' >> oas_docs/.paths
$ echo 'logs.yml' >> oas_docs/.paths
$ echo 'batch_v1beta1.yml' >> oas_docs/.paths
$ bundle exec routes:oas:ui
```

### SwaggerEditorで編集

k8sのAPIドキュメントの場合、全体が5MBを超えており、ブラウザのローカルストレージの最大容量(5MB)を超えているので
全体を表示して編集は出来ない。

```bash
$ # 全体を編集する
$ bundle exec routes:oas:editor  # 5MBを超えているので出来ない。
$ # 特定のpathsファイル(単数)だけを編集する
$ PATHS_FILE=oas_docs/src/paths/rbac_authorization_v1.yml bundle exec routes:oas:editor
$ # 特定のpathsファイル(複数)だけ編集する
$ echo 'rbac_authorization_v1.yml' >> oas_docs/.paths
$ echo 'logs.yml' >> oas_docs/.paths
$ echo 'batch_v1beta1.yml' >> oas_docs/.paths
$ bundle exec routes:oas:editor
```

### テキストエディタで編集する場合

git管理しない `oas_docs/swagger_doc.yml` を `monitor` コマンドで管理する事で差分を検知します。

vscodeを使っている場合は、[SwaggerViewer](https://marketplace.visualstudio.com/items?itemName=Arjun.swagger-viewer)プラグインが便利

```bash
$ # 全体を編集する
$ bundle exec routes:oas:monitor   # oas_docs/swagger_doc.ymlファイルを編集する。
$ # 特定のpathsファイル(単数)だけを編集する
$ PATHS_FILE=oas_docs/src/paths/rbac_authorization_v1.yml bundle exec routes:oas:monitor
$ # 特定のpathsファイル(複数)だけ編集する
$ echo 'rbac_authorization_v1.yml' >> oas_docs/.paths
$ echo 'logs.yml' >> oas_docs/.paths
$ echo 'batch_v1beta1.yml' >> oas_docs/.paths
$ bundle exec routes:oas:monitor
```

### 書いたドキュメントを配布する

必要な分だけ配布用のドキュメントを生成する事が出来ます。

```bash
$ # 全体を配布する
$ bundle exec routes:oas:dist
$ # 特定のpathsファイル(単数)だけを配布する
$ PATHS_FILE=oas_docs/src/paths/rbac_authorization_v1.yml bundle exec routes:oas:dist
$ # 特定のpathsファイル(複数)だけ配布する
$ echo 'rbac_authorization_v1.yml' >> oas_docs/.paths
$ echo 'logs.yml' >> oas_docs/.paths
$ echo 'batch_v1beta1.yml' >> oas_docs/.paths
$ bundle exec routes:oas:dist
```

### pathsファイル一覧を取得する

```bash
$ bundle exec routes:oas:paths_ls
```
