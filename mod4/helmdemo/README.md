### Mod4 デモ: Helm でアプリケーションをデプロイしてみよう

* HOME ディレクトリに移動
  ```
  cd  ~
  ```
  
* ワーク用リポジトリを取得して移動
  ```
  cd eks-digital/mod4

  ```
* Chart.yaml の確認
  - helmdemo フォルダ(このフォルダ)にある Chart.yamlを参照


* Deployment の定義ファイルの確認
  - helmdemo フォルダ(このフォルダ)にある templates/deployment/hello-deployment.yamlを参照


* values.yaml の確認
  - helmdemo フォルダ(このフォルダ)にあるvalues.yamlを参照
  - レプリカ 2、コンテナイメージに tnobe/node-web-hello、コンテナイメージのバージョンに 1を指定していることがわかる。


* `hello-app` アプリケーションとしてデプロイする前にdry-run実行
  ```
  helm install --debug --dry-run hello-app helmdemo/
  ``` 
* `hello-app` アプリケーションとしてデプロイ
  ```
  helm install hello-app helmdemo/
  ``` 
* アプリケーションのステータスの確認
  ```
  helm status hello-app
  ```

　REVISION 1 のSTATUS が deployed になっている

* Pod,Deployment,Serviceの確認
  ```
  kubectl get svc,po,deploy
  ```
* service/my-service の EXTERNAL-IP に http:// をつけ、ブラウザでアクセス。
  - アプリケーションのバージョン 1 にアクセスできることを確認
  - (表示されるまで、しばらく時間がかかる場合があります。)
  - 背景色が青のページが表示される。このページはバージョン1のアプリにより表示されている。

* helmdemo/values.yaml の内容を変更してバージョン 2 のコンテナイメージを指定する

* アプリケーションをバージョン2 にアップグレードする
  ```
  helm upgrade hello-app helmdemo/
  ```
* アプリケーションのステータスの確認
  ```
  helm status hello-app
  ```

  REVISION 2 のSTATUS が deployed になっている

* Pod,Deployment,Serviceの確認
  ```
  kubectl get svc,po,deploy
  ```
  
 2つのPodが新たに起動され、以前の Pod がターミネイトされていることがわかる。

* service/my-service の EXTERNAL-IP に http:// をつけ、ブラウザでアクセス。
  - アプリケーションのバージョン2 にアクセスできることを確認
  - 背景色が緑のページが表示される。このページはバージョン2のアプリにより表示されている。
* アプリケーションのデプロイ履歴を表示
  ```
  helm history hello-app
  ```
* アプリケーションをバージョン1 へロールバック
  ```
  helm rollback hello-app 1
  ```
  （しばらく待つ）
  
* アプリケーションがバージョン1 に戻っていることを確認
* アプリケーションをアンデプロイ
  ```
  helm uninstall hello-app
  ```

---

# bitnami （ビットナミー） のリポジトリからインストールしてみよう

* bitnami のリポジトリを追加
  ```
  helm repo add bitnami https://charts.bitnami.com/bitnami
  ```

* 全てのリポジトリを表示
  ```
  helm search repo
  ```

* bitnami のリポジトリを表示
  ```
  helm search repo bitnami
  ```
  
* bitnami の nginx をインストール
  ```
  helm install my-nginx bitnami/nginx --version 19.0.0
  ```

* インストールした Helm アプリケーションの情報を表示
  ```
  helm ls
  ```

* kubectl で Kubernetes オブジェクトを確認
  ```
  kubectl get all
  ```
  - service/my-nginx の EXTERNAL-IP の値を http:// でアクセスし、nginx が動作していることを確認

* bitnami の　nginx の Chat パッケージを取得
  ```
  helm pull oci://registry-1.docker.io/bitnamicharts/nginx --version 19.0.0

  tar -xzvf nginx-19.0.0.tgz
  
  ```


* アプリケーションのアンインストール
  ```
  helm uninstall my-nginx
  ```

* アンインストールされたアプリケーションの情報が表示されないことを確認
  ```
  helm ls
  ```

後始末:

pull した Chart の tgzファイルや展開したフォルダを削除

リポジトリの削除

```
helm repo remove  bitnami 
```
  




