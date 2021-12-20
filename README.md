ROSA 上でこのファイルセットが動く事を確認


### メモ
・デプロイされる Pod は、ACMの test 用に作られた単純な nginx の Pod
・AWS上には、新規に Classic LoadBalncer がデプロイされた。
・アクセスできるようになるまで少し時間がかかる。

CLB の場合
http://a4d653831a50d458d8f7ace5ae1f516b-1746624012.ap-northeast-1.elb.amazonaws.com/

NLB の場合 
http://a31fff5fab46d45fd8e05b0cd5de158b-e950f37eb406a33a.elb.ap-northeast-1.amazonaws.com/

のような長いドメイン名が自動で払い出される。

CLBとNLBを同時にデプロイしても動く。

### CLBについて
CLBは、ターゲットとしてインスタンスが列挙される。
Master から Infra、Worker 全て列挙されるので Worker Node だけ残して「ロードバランサーから削除」しても動いた。（そもそも Worker Node だけターゲットにする方法があるのかもしれない)

### NLBについて

NLBは、ターゲットは、「リスナー」タブの「転送先」の箇所に「Target Groups」名で指定されている。
これも中を見ると全ての Node が Target として指定されている。これも Worker Node だげ残して「Deregister」する事ができる。（そもそも Worker Node だけターゲットにする方法があるのかもしれない)

デフォルトで構成される NLBの Target gourp は全て Target Type が `IP`で、Master Node のIPが直指定されている。`Type:LoadBalancer` で作成したものだけ `Instance` になっている。
![image](https://user-images.githubusercontent.com/8530492/146731278-ccaa885d-f525-476a-a0b7-f2909b72bba6.png)

*Registered Target が IP の場合*
![image](https://user-images.githubusercontent.com/8530492/146731857-8415d4b3-627e-4b76-8c84-7b395d40c843.png)

*Registered Target が Instance の場合*
![image](https://user-images.githubusercontent.com/8530492/146731921-cce76e42-1f73-479a-8622-c03cdd40b825.png)


### 全てをデプロイした後

```
yuhki@yuhki-legion:/mnt/c/Users/yuhki/rosa/aws-lb-test$ oc get all
NAME                             READY   STATUS    RESTARTS   AGE
pod/nginx-116-7f4d9cfb5f-4ljfl   1/1     Running   0          52m
pod/nginx-116-7f4d9cfb5f-q6tlp   1/1     Running   0          52m
pod/nginx-116-7f4d9cfb5f-vk94v   1/1     Running   0          52m

NAME            TYPE           CLUSTER-IP      EXTERNAL-IP                                                                          PORT(S)        AGE
service/myclb   LoadBalancer   172.30.72.221   a8a563a1b607e42b3ba297bdf8b8dc6c-538531912.ap-northeast-1.elb.amazonaws.com          80:30209/TCP   24m
service/mynlb   LoadBalancer   172.30.73.131   a31fff5fab46d45fd8e05b0cd5de158b-e950f37eb406a33a.elb.ap-northeast-1.amazonaws.com   80:32163/TCP   19m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-116   3/3     3            3           52m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-116-7f4d9cfb5f   3         3         3       52m
yuhki@yuhki-legion:/mnt/c/Users/yuhki/rosa/aws-lb-test$
```
