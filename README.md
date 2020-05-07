# raventid_platform
## HW-1 Kubernetes Intro
### Install

Установка kubectl: 

https://kubernetes.io/docs/tasks/tools/install-kubectl/

Устновка minicube:

https://kubernetes.io/docs/tasks/tools/install-minikube/

Установка kind:

https://kind.sigs.k8s.io/docs/user/quick-start/

Установка Kubernetes CLI

https://k9scli.io/

### Запуск окружения
```sh
raven:~/raventid_platform $ minikube start
😄  minikube v1.9.2 on Darwin 10.13.6
✨  Using the docker driver based on existing profile
👍  Starting control plane node m01 in cluster minikube
🚜  Pulling base image ...
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.18.0 on Docker 19.03.2 ...
    ▪ kubeadm.pod-network-cidr=10.244.0.0/16
🌟  Enabling addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube"
```

Убедимся, что все запустилось
``` sh
raven:~/raventid_platform $ kubectl cluster-info
Kubernetes master is running at https://127.0.0.1:32768
KubeDNS is running at https://127.0.0.1:32768/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```


### Восстановление подов при удалении
При удалении подов они восстанавливаются.
``` sh
raven:~/raventid_platform $ kubectl delete pod --all -n kube-system
pod "coredns-66bff467f8-8z6s7" deleted
pod "coredns-66bff467f8-s7rpb" deleted
pod "etcd-minikube" deleted
pod "kindnet-s8plb" deleted
pod "kube-apiserver-minikube" deleted
pod "kube-controller-manager-minikube" deleted
pod "kube-proxy-4vqld" deleted
pod "kube-scheduler-minikube" deleted
pod "storage-provisioner" deleted
```
Поды в неймспейсе kube-system пересоздаются, так как являются статическими подами, описаными в папке с манифестами.

Core-dns восстанавливается как Deployment.

``` sh
raven:~/raventid_platform $ kubectl get deployment --namespace=kube-system -o wide

NAME      READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                     SELECTOR
coredns   2/2     2            2           17h   coredns      k8s.gcr.io/coredns:1.6.7   k8s-app=kube-dns
```

Kube-proxy восстанавливается как DaemonSet.

``` sh
raven:~/raventid_platform $ kubectl get ds --namespace=kube-system -o wide

NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE     CONTAINERS    IMAGES                          SELECTOR
kindnet      1         1         1       1            1           <none>                   2d17h   kindnet-cni   kindest/kindnetd:0.5.3          app=kindnet
kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   2d17h   kube-proxy    k8s.gcr.io/kube-proxy:v1.18.0   k8s-app=kube-proxy
```

### Web pod (основное задание)
Перейти в рабочую директорию:
`$ cd kubernetes-intro`

При условии запущенного и сконфигурированного kubernetes нужно выполнить:
`$ kubectl apply -f web-pod.yaml`

Чтобы пробросить порт выполните
`$ kubectl port-forward --address 0.0.0.0 pod/web 8000:8000`

Для того, чтобы убедится в правильности выполненого задания нужно просто перейти
по ссылке http://localhost:8000



### Сборка frontend (задание со *)

Был собран образ `frontend` и загружен на `Docker Hub`.

После запуска проекта:
``` sh
$ cd kubernetes-intro`
$ kubectl apply -f frontend-pod.yaml
```

В логах видно, что не установлены необходимые переменные окружения.

Переменные добавлены в конфигурацию `frontend-healthy.yaml`, которую можно
запустить, как:

``` sh
$ cd kubernetes-intro
$ kubectl apply -f frontend-pod-healthy.yaml
```
