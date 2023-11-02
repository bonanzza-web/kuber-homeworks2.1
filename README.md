# Домашнее задание к занятию «Хранение в K8s. Часть 1»

### Цель задания

В тестовой среде Kubernetes нужно обеспечить обмен файлами между контейнерам пода и доступ к логам ноды.

------

### Чеклист готовности к домашнему заданию

1. Установленное K8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключенным GitHub-репозиторием.

------

### Дополнительные материалы для выполнения задания

1. [Инструкция по установке MicroK8S](https://microk8s.io/docs/getting-started).
2. [Описание Volumes](https://kubernetes.io/docs/concepts/storage/volumes/).
3. [Описание Multitool](https://github.com/wbitt/Network-MultiTool).

------

### Задание 1 

**Что нужно сделать**

Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории.
3. Обеспечить возможность чтения файла контейнером multitool.
4. Продемонстрировать, что multitool может читать файл, который периодоически обновляется.
5. Предоставить манифесты Deployment в решении, а также скриншоты или вывод команды из п. 4.

------

### Задание 2

**Что нужно сделать**

Создать DaemonSet приложения, которое может прочитать логи ноды.

1. Создать DaemonSet приложения, состоящего из multitool.
2. Обеспечить возможность чтения файла `/var/log/syslog` кластера MicroK8S.
3. Продемонстрировать возможность чтения файла изнутри пода.
4. Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

------


### Ответы:

# Задание 1:

```
ubuntu@server-kube:~$ micro get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
busy-mult   1/1     1            1           4m36s
ubuntu@server-kube:~$ micro get po
NAME                        READY   STATUS    RESTARTS   AGE
mult-daemon-hxdnz           1/1     Running   0          4m41s
busy-mult-5656966b6-rt7cf   2/2     Running   0          4m43s
ubuntu@server-kube:~$ micro exec -it busy-mult-5656966b6-rt7cf -c multitools -- bash
busy-mult-5656966b6-rt7cf:/# ls
bin     certs   dev     docker  etc     home    lib     media   mnt     opt     proc    root    run     sbin    srv     sys     tmp     usr     var
busy-mult-5656966b6-rt7cf:/# cat /tmp/logs/logs.txt
It is a log -- Thu Nov  2 13:10:11 UTC 2023
It is a log -- Thu Nov  2 13:10:16 UTC 2023
It is a log -- Thu Nov  2 13:10:21 UTC 2023
It is a log -- Thu Nov  2 13:10:26 UTC 2023
It is a log -- Thu Nov  2 13:10:31 UTC 2023
It is a log -- Thu Nov  2 13:10:36 UTC 2023
It is a log -- Thu Nov  2 13:10:41 UTC 2023
It is a log -- Thu Nov  2 13:10:46 UTC 2023
It is a log -- Thu Nov  2 13:10:51 UTC 2023
It is a log -- Thu Nov  2 13:10:56 UTC 2023
It is a log -- Thu Nov  2 13:11:01 UTC 2023
It is a log -- Thu Nov  2 13:11:06 UTC 2023
It is a log -- Thu Nov  2 13:11:11 UTC 2023
It is a log -- Thu Nov  2 13:11:16 UTC 2023
It is a log -- Thu Nov  2 13:11:21 UTC 2023
It is a log -- Thu Nov  2 13:11:26 UTC 2023
It is a log -- Thu Nov  2 13:11:31 UTC 2023
It is a log -- Thu Nov  2 13:11:36 UTC 2023
It is a log -- Thu Nov  2 13:11:41 UTC 2023
It is a log -- Thu Nov  2 13:11:46 UTC 2023
It is a log -- Thu Nov  2 13:11:51 UTC 2023
It is a log -- Thu Nov  2 13:11:56 UTC 2023
It is a log -- Thu Nov  2 13:12:01 UTC 2023

```

Манифест https://github.com/bonanzza-web/kuber-homeworks2.1/blob/main/files/deploy.yaml    

# Задание 2:

```
ubuntu@server-kube:~$ micro get ds
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
mult-daemon   1         1         1       1            1           <none>          8m30s
ubuntu@server-kube:~$ micro exec -it mult-daemon-hxdnz -- tail -10 /syslogs/syslog
Nov  2 13:20:49 server-kube systemd[1]: run-containerd-runc-k8s.io-729821ef94928f117afb658ea480d2100d085471bdb815cf39f181fe802262dd-runc.kVmC7K.mount: Deactivated successfully.
Nov  2 13:20:57 server-kube systemd[6414]: Started snap.microk8s.microk8s-ec671951-7ab6-457b-b41f-b8e4bfd17be5.scope.
Nov  2 13:20:57 server-kube systemd[1]: run-containerd-runc-k8s.io-bd01924e0f963e41a23c508eabb016d4ea603c5707c3bcd5fab73b5a483e5f67-runc.bdD8An.mount: Deactivated successfully.
Nov  2 13:20:57 server-kube microk8s.daemon-containerd[2911]: time="2023-11-02T13:20:57.464764725Z" level=error msg="Failed to resize process \"7f808be871ddb64f8f42f165b6cd4799a2b788a1cc747b30101e7c97516258fe\" console for container \"bd01924e0f963e41a23c508eabb016d4ea603c5707c3bcd5fab73b5a483e5f67\"" error="cannot resize a stopped container: unknown"
Nov  2 13:20:57 server-kube microk8s.daemon-containerd[2911]: time="2023-11-02T13:20:57.466981575Z" level=info msg="Container exec \"7f808be871ddb64f8f42f165b6cd4799a2b788a1cc747b30101e7c97516258fe\" stdin closed"
Nov  2 13:20:57 server-kube microk8s.daemon-kubelite[3094]: E1102 13:20:57.477108    3094 upgradeaware.go:425] Error proxying data from client to backend: write tcp 10.0.3.25:39538->10.0.3.25:10250: write: broken pipe
Nov  2 13:20:59 server-kube systemd[1]: run-containerd-runc-k8s.io-729821ef94928f117afb658ea480d2100d085471bdb815cf39f181fe802262dd-runc.ruR8QZ.mount: Deactivated successfully.
Nov  2 13:20:59 server-kube systemd[1]: run-containerd-runc-k8s.io-729821ef94928f117afb658ea480d2100d085471bdb815cf39f181fe802262dd-runc.r2b2Gk.mount: Deactivated successfully.
Nov  2 13:21:04 server-kube systemd[6414]: Started snap.microk8s.microk8s-d8c4aa91-bf6b-449b-b34d-70ac50d638ce.scope.
Nov  2 13:21:04 server-kube systemd[1]: run-containerd-runc-k8s.io-d6efcc12dbdd1d43808db0b22f5ad4d1c7da6fc01dfa389aa90a8b5f5cf68c7e-runc.UzQvd4.mount: Deactivated successfully.

```


Манифест https://github.com/bonanzza-web/kuber-homeworks2.1/blob/main/files/daemon.yaml


------


### Правила приёма работы

1. Домашняя работа оформляется в своём Git-репозитории в файле README.md. Выполненное задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl`, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.

------