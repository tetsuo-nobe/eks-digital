# Amazon ECR にコンテナイメージを push する

* 下記が使用できる環境が必要
 - docker コマンド
 - AWS CLI 
 - 対象リポジトリへの push が許可されている IAM の認証情報

* レジストリに対して Docker クライアントを認証
  - 次の例では AWS アカウント ID が 000000000000 で 東京リージョンのレジストリに対して認証

  ```
  ACCOUNTID=000000000000

  REGION=ap-northeast-1
  ```

  ```
  aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ACCOUNTID}.dkr.ecr.${REGION}.amazonaws.com
  ```
* Docker イメージ をビルド
  - 次の例では hellodemo をタグとして指定してビルド
  ```
  docker build -t hellodemo .

  ```
* ビルド完了後、リポジトリにイメージをプッシュできるように、イメージにタグを付け
  ```
  docker tag hellodemo:latest ${ACCOUNTID}.dkr.ecr.${REGION}.amazonaws.com/hellodemo:latest

  ```
* リポジトリにこのイメージをプッシュ
  ```
  docker push ${ACCOUNTID}.dkr.ecr.${REGION}.amazonaws.com/hellodemo:latest

  ``` 
  
* Kubernetes の Deployment のマニフェストにプッシュしたイメージの URI を指定して利用する。（以下のファイルを参照）
  - deployment-hellodemo.yaml 
  - service-hellodemo.yaml

---

### Mod4 の Amazon ECR デモ用:

準備:  eks-digital/mod4/ecrdemo ディレクトリへ移動 

左側の Features & Settings の下の Scanning　をクリック
[スキャン設定]で[基本スキャン]と[拡張スキャン]を選択できることをみせる

マネコンで ECRリポジトリ hellodemo 作成

さらに [プッシュコマンドを表示] をクリック。

そこで表示されるコマンドを使って、VS Code からリポジトリへログイン、 Dockerイメージをビルドし、ECRへpush

マネコンで、latestがpushされたことをみせる

