```
helm search redis
helm install stable/redis
```

```
qzhao-mbp:~ qzhao$ helm install --name myredis stable/redis
NAME:   myredis
LAST DEPLOYED: Thu Oct 17 09:56:17 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME            DATA  AGE
myredis         3     1s
myredis-health  6     1s

==> v1/Pod(related)
NAME              READY  STATUS   RESTARTS  AGE
myredis-master-0  0/1    Pending  0         1s
myredis-slave-0   0/1    Pending  0         0s

==> v1/Secret
NAME     TYPE    DATA  AGE
myredis  Opaque  1     1s

==> v1/Service
NAME              TYPE       CLUSTER-IP     EXTERNAL-IP  PORT(S)   AGE
myredis-headless  ClusterIP  None           <none>       6379/TCP  1s
myredis-master    ClusterIP  10.102.0.188   <none>       6379/TCP  1s
myredis-slave     ClusterIP  10.100.26.220  <none>       6379/TCP  1s

==> v1/StatefulSet
NAME            READY  AGE
myredis-master  0/1    1s
myredis-slave   0/2    1s


NOTES:
** Please be patient while the chart is being deployed **
Redis can be accessed via port 6379 on the following DNS names from within your cluster:

myredis-master.default.svc.cluster.local for read/write operations
myredis-slave.default.svc.cluster.local for read-only operations


To get your password run:

    export REDIS_PASSWORD=$(kubectl get secret --namespace default myredis -o jsonpath="{.data.redis-password}" | base64 --decode)

To connect to your Redis server:

1. Run a Redis pod that you can use as a client:

   kubectl run --namespace default myredis-client --rm --tty -i --restart='Never' \
    --env REDIS_PASSWORD=$REDIS_PASSWORD \
   --image docker.io/bitnami/redis:5.0.5-debian-9-r141 -- bash

2. Connect using the Redis CLI:
   redis-cli -h myredis-master -a $REDIS_PASSWORD
   redis-cli -h myredis-slave -a $REDIS_PASSWORD

To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace default svc/myredis-master 6379:6379 &
    redis-cli -h 127.0.0.1 -p 6379 -a $REDIS_PASSWORD

```

```
qzhao-mbp:~ qzhao$ kubectl get secret --namespace default myredis -o yaml
apiVersion: v1
data:
  redis-password: ZElJUjdEZFdaSw==
kind: Secret
metadata:
  creationTimestamp: "2019-10-17T13:56:17Z"
  labels:
    app: redis
    chart: redis-9.3.1
    heritage: Tiller
    release: myredis
  name: myredis
  namespace: default
  resourceVersion: "867"
  selfLink: /api/v1/namespaces/default/secrets/myredis
  uid: f40d153a-b106-4aa0-b388-98eeddf274fd
type: Opaque
qzhao-mbp:~ qzhao$ echo ZElJUjdEZFdaSw== | base64 --decode
```

```
qzhao-mbp:~ qzhao$ kubectl run --namespace default myredis-client --rm --tty -i --restart='Never' \
>     --env REDIS_PASSWORD=$REDIS_PASSWORD \
>    --image docker.io/bitnami/redis:5.0.5-debian-9-r141 -- bash
If you don't see a command prompt, try pressing enter.
I have no name!@myredis-client:/$ echo $REDIS_PASSWORD
dIIR7DdWZK
I have no name!@myredis-client:/$ redis-cli -h myredis-master -a $REDIS_PASSWORD
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
myredis-master:6379> set mykey "Hello"
OK
myredis-master:6379> Get mykey
"Hello"
I have no name!@myredis-client:/$ exit
exit
qzhao-mbp:~ qzhao$ kubectl get pvc
qzhao-mbp:~ qzhao$ helm list
NAME       	REVISION	UPDATED                 	STATUS  	CHART      	APP VERSION	NAMESPACE
dunking-olm	1       	Thu Oct 17 09:55:57 2019	DEPLOYED	redis-9.3.1	5.0.5      	default
myredis    	1       	Thu Oct 17 09:56:17 2019	DEPLOYED	redis-9.3.1	5.0.5      	default
qzhao-mbp:~ qzhao$ helm delete --purge dunking-olm myredis
release "dunking-olm" deleted
release "myredis" deleted
```

```
qzhao-mbp:helm-test qzhao$ helm install mychart/
NAME:   running-warthog
LAST DEPLOYED: Thu Oct 17 10:32:53 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Deployment
NAME                     READY  UP-TO-DATE  AVAILABLE  AGE
running-warthog-mychart  0/1    1           0          0s

==> v1/Pod(related)
NAME                                      READY  STATUS             RESTARTS  AGE
running-warthog-mychart-7c79d9c87d-spxpn  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                     TYPE       CLUSTER-IP    EXTERNAL-IP  PORT(S)  AGE
running-warthog-mychart  ClusterIP  10.100.6.149  <none>       80/TCP   0s


NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=running-warthog" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl port-forward $POD_NAME 8080:80

qzhao-mbp:helm-test qzhao$ kubectl get pods
NAME                                       READY   STATUS    RESTARTS   AGE
running-warthog-mychart-7c79d9c87d-spxpn   1/1     Running   0          50s
qzhao-mbp:helm-test qzhao$ kubectl get deployment
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
running-warthog-mychart   1/1     1            1           76s
qzhao-mbp:helm-test qzhao$ export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=running-warthog" -o jsonpath="{.items[0].metadata.name}")
qzhao-mbp:helm-test qzhao$ echo $POD_NAME
running-warthog-mychart-7c79d9c87d-spxpn
qzhao-mbp:helm-test qzhao$ kubectl port-forward $POD_NAME 8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
^Z
[1]+  Stopped                 kubectl port-forward $POD_NAME 8080:80
qzhao-mbp:helm-test qzhao$ bg
[1]+ kubectl port-forward $POD_NAME 8080:80 &
qzhao-mbp:helm-test qzhao$ curl http://127.0.0.1:8080
Handling connection for 8080
qzhao-mbp:helm-test qzhao$ fg
kubectl port-forward $POD_NAME 8080:80
^Cqzhao-mbp:helm-test qzhao$
qzhao-mbp:helm-test qzhao$ curl http://127.0.0.1:8080
curl: (7) Failed to connect to 127.0.0.1 port 8080: Connection refused
qzhao-mbp:helm-test qzhao$ helm delete --purge running-warthog
release "running-warthog" deleted
```
---

How do I un-expose (undo expose) service?

Assuming you have a deployment called hello-world, and do a kubectl expose as follows:

```kubectl expose deployment hello-world --type=ClusterIP --name=my-service```

this will create a service called my-service, which makes your deployment accessible for debugging, as you described.

To display information about the Service:

```kubectl get services my-service```

To delete this service when you are done debugging:

```kubectl delete service my-service```

Now your deployment is un-exposed.
