# oracle-developer-days-2020-cloudnativeのデモソース

## ソースコードの構成

```
devdays-demo-backend_kohaku => 紅白歌合戦2020の出場歌手(一部)を表示する(Helidon)
devdays-demo-backend_trendword => 流行語2020を取得するためのバックエンドAPI(Helidon)
devdays-demo-frontend => フロントエンドアプリ(Vue.js(Nuxt.js))
k8s_manifest => Kubernetesのマニフェストファイル
```

## ビルド方法

各アプリケーションは、各ディレクトリ(devdays-demo-backend_kohaku/devdays-demo-backend_trendword/devdays-demo-frontend)に配置されているDockerfileを利用してビルドできます。  
  (mavenコマンドを利用してビルドすることもできます)  
ビルドコマンドは以下です。

```sh
docker build -t <任意のイメージ名> .  
```

Kubernetesへデプロイする場合は、任意のレジストリにpushします。

```sh
docker login <任意のレジストリURL>

docker push <ビルド時に設定した任意のイメージ名>
```

docker login時に利用するレジストリの認証情報が必要です。

Oracle Cloud Infrastructureを利用する場合は、以下のコマンドでOCIR(Oracle Cloud Infrastructure Registry)にpushできます。

```sh
docker login <リージョンコード>.ocir.io

docker push <ビルド時に設定した任意のイメージ名>
```

OCIRのリージョンコードは[公式ハンズオンドキュメント](https://oracle-japan.github.io/paasdocs/documents/containers/handson/k8s-walkthrough/#2-ociroke)の手順2.1および2.2を参考にしてください。

コンテナレジストリにpushしたら、各manifestの19行目部分に記載されている`image`フィールドをご自身の環境に合わせて編集してください。

```yaml
image: phx.ocir.io/orasejapan/devdays2020/frontend-app
```

## Kubernetesへのデプロイ方法

k8s_manifestディレクトリに移動し、以下のコマンドでデプロイできます。

```sh
kubectl apply -f .
```

Oracle Cloud Infrastructureを利用する場合は、[公式ハンズオンドキュメント](https://oracle-japan.github.io/paasdocs/documents/containers/common/)の手順にしたがって、OKEのプロビジョニングとkubectlコマンドのセットアップを実施してください。

## アプリケーションへのアクセス

このデモでは、Load Balancerを利用したアクセスを利用します。  
Load Balancer以外でのアクセスを利用する場合は、マニフェストファイルを適宜編集してください。

Kubernetesにマニフェストをデプロイすると、以下のServiceがデプロイされます。  
Oracle Cloud Infrastructureを利用する場合は、OCI Load Balancerがデプロイされています。

```sh
[opc@oke-client k8s_manifest]$ kubectl get service/frontend
NAME       TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)          AGE
frontend   LoadBalancer   10.96.138.125   XXX.XXX.XXX.XXX   3000:31049/TCP   2d23h
```

上記のLBを利用して、EXTERNAL-IP経由でアクセスできます。ポートは3000番ポートです。  

```
http://XXX.XXX.XXX.XXX:3000
```
