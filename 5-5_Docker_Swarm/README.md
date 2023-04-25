### Задача 1
*Дайте письменные ответы на вопросы:*  
•	*В чём отличие режимов работы сервисов в Docker Swarm-кластере: replication и global?*  
•	*Какой алгоритм выбора лидера используется в Docker Swarm-кластере?*  
•	*Что такое Overlay Network?*  

### Решение
+ Для сервисов в режиме **replicated** мы указываем количество контейнеров для Swarm-менеджера, для репликации задач на доступные узлы кластера (через опцию *--replicas*). А для сервисов в режиме global одна задача сама размещается на каждый доступный узел, соответствующий требованиям к ресурсам службы и ограничениям на размещение (Информация взята с docs.docker.com. Похоже, в описании задачи ошибка в названии режима.) 
+ Изначально «лидером» становится узел, с которого происходит инициализация кластера (командой `docker swarn init`).  С несколькими узлами, при выходе из строя «лидера», его назначение происходит «алгоритмом поддержания распределенного консенсуса» («*RAFT*»). 
+ *Overlay Network* -- это распределенная сеть между узлами Docker, расположенная «над» изначальной сетью. Она позволяет «изолировано» связывать контейнеры Docker виртуальным шифрованным каналом протоколом IPSEC.  (Создается командой `docker network create -d overlay <имя>` )
___
### Задача 2
*Создайте ваш первый Docker Swarm-кластер в Яндекс Облаке.
Чтобы получить зачёт, предоставьте скриншот из терминала (консоли) с выводом команды:
docker node ls*

### Решение
Собран Docker-кластер из 3-х узлов, 1 manager и 2 worker.

![5-5-2](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-5_Docker_Swarm/5-5-2.png)

(Имена 2-х ВМ указаны в малоинформативном виде, т.к. при их создании из CLI не указал параметр *--hostname*, только *--name*.)

___

### Задача 3
*Создайте ваш первый, готовый к боевой эксплуатации кластер мониторинга, состоящий из стека микросервисов.*
*Чтобы получить зачёт, предоставьте скриншот из терминала (консоли), с выводом команды:*
*docker service ls*

### Решение
![5-5-3](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-5_Docker_Swarm/5-5-3.png)

Файл docker-compose.yml для создания стека сервисов (docker stack deploy) брался из **virt-homeworks/05-virt-05-docker-swarm/src/ansible/monitoring/**, вместе с остальными конфигурационными файлами для описанных в нём сервисов. Разворачивалось на созданных выше ВМ.  

В задании требовался скриншот, но для большей читабельности, прикладываю скопированный текст в виде кода.

```
root@vm1:/home/rrr/monitoring# docker service ls
ID             NAME                                  MODE         REPLICAS   IMAGE                                          PORTS
mfvktvwp69ug   stack_1_monitoring_alertmanager       replicated   1/1        stefanprodan/swarmprom-alertmanager:v0.14.0
dep5qckorsl3   stack_1_monitoring_caddy              replicated   1/1        stefanprodan/caddy:latest                      *:3000->3000/tcp, *:9090->9090/tcp, *:9093-9094->9093-9094/tcp
uso6rzjd60y7   stack_1_monitoring_cadvisor           global       0/1        google/cadvisor:latest
xbvwh6pahfr6   stack_1_monitoring_dockerd-exporter   global       3/3        stefanprodan/caddy:latest
sdpbdl3xs2oe   stack_1_monitoring_grafana            replicated   1/1        stefanprodan/swarmprom-grafana:5.3.4
ib5b9mz6squn   stack_1_monitoring_node-exporter      global       3/3        stefanprodan/swarmprom-node-exporter:v0.16.0
yh1anrjnidmy   stack_1_monitoring_prometheus         replicated   1/1        stefanprodan/swarmprom-prometheus:v2.5.0
rztcldbp0qxg   stack_1_monitoring_unsee              replicated   1/1        cloudflare/unsee:v0.8.0

```
___
### Задача 4 (*)
*Выполните на лидере Docker Swarm-кластера команду, указанную ниже, и дайте письменное описание её функционала — что она делает и зачем нужна:*
```
# см.документацию: https://docs.docker.com/engine/swarm/swarm_manager_locking/
docker swarm update --autolock=true
```
### Решение

Команда `docker swarm update --autolock=true` включает функцию "автоблокировка", обеспечивающую взятие во владение и блокировку ключей TLS-шифрования и шифрования/дешифрования журналов Raft, шифруемых на диске по умолчанию. 
Без включения функции автоблокировки, при перезапуске Docker упомянутые ключи шифрования/дешифрования загружаются в память каждого управляющего узла. А с включенной функцией автоблокировки, при перезапуске Docker вначале понадобится разблокировать Swarn, используя дополнительный ключ шифрования, сгенерированный Docker-ом при включении блокировки приведенной в задании командой (показано на скриншоте).

![5-5-4](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-5_Docker_Swarm/5-5-4.png)
