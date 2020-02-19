

```
kind: Service 
apiVersion: v1 
metadata: 
  name: angular-service 
spec: 
  selector: 
    app: angular 
  ports: 
  - protocol: TCP 
    port: 80 
    targetPort: 80
  type: LoadBalancer
```

change LoadBalancer to NodePort, you have to add nodePort: 31000

```
kind: Service 
apiVersion: v1 
metadata: 
  name: angular-service 
spec: 
  selector: 
    app: angular 
  ports: 
  - protocol: TCP 
    port: 80 
    targetPort: 80
    nodePort: 31000
  type: NodePort 
```



```
Port-forward:

qzhaos-mbp:dockerhub qzhao$ kubectl get po
NAME                                  READY   STATUS    RESTARTS   AGE
angular-deployment-598858cb76-g92xf   1/1     Running   0          10m
angular-deployment-598858cb76-hfqn5   1/1     Running   0          10m


kubectl port-forward angular-deployment-598858cb76-g92xf 31000:80

qzhaos-mbp:dockerhub qzhao$ kubectl port-forward angular-deployment-598858cb76-g92xf 31000:80
Forwarding from 127.0.0.1:31000 -> 80

```
