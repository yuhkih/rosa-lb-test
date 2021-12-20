ROSA 上でこのファイルセットが動く事を確認

デプロイされる Pod は、ACMの test 用に作られた単純な nginx の Pod
AWS上には、新規に Classic LoadBalncer がデプロイされた。
アクセスできるようになるまで少し時間がかかる。

CLB の場合 http://a4d653831a50d458d8f7ace5ae1f516b-1746624012.ap-northeast-1.elb.amazonaws.com/
NLB の場合 http://a31fff5fab46d45fd8e05b0cd5de158b-e950f37eb406a33a.elb.ap-northeast-1.amazonaws.com/
のような長いドメイン名が自動で払い出される。

CLBは、ターゲットとしてインスタンスが列挙される。Master から Infra、Worker 全て列挙されるので Worker Node だけ残して「ロードバランサーから削除」しても動いた。（そもそも Worker Node だけターゲットにする方法があるのだと思う)
NLBは、ターゲットは、「リスナー」タブの「転送先」の箇所に「Target Groups」名で指定されている。これも中を見ると全ての Node が Target として指定されている。これも Worker Node だげ残して「Deregister」する事ができる。

CLBとNLBを同時にデプロイしても動く。
