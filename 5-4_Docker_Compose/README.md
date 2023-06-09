### Задача 1
*Создайте собственный образ любой операционной системы (например ubuntu-20.04) с помощью Packer (инструкция).
Чтобы получить зачёт, вам нужно предоставить скриншот страницы с созданным образом из личного кабинета YandexCloud.*

#### Решение.
Cкриншот страницы из личного кабинета YandexCloud с созданным программой Packer образом.  

![Image alt](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-4_Docker_Compose/5-4-1.png)
___

### Задача 2
*2.1. Создайте вашу первую виртуальную машину в YandexCloud с помощью web-интерфейса YandexCloud.  
2.2.\* (Необязательное задание)
Создайте вашу первую виртуальную машину в YandexCloud с помощью Terraform (вместо использования веб-интерфейса YandexCloud). Используйте Terraform-код в директории (src/terraform).
Чтобы получить зачёт, вам нужно предоставить вывод команды terraform apply и страницы свойств, созданной ВМ из личного кабинета YandexCloud.*

#### Решение.
2.1. Скриншот страницы свойств ВМ, созданной из YandexCloud.  

![5-4-2-1](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-4_Docker_Compose/5-4-2-1.png)

2.2.\* (Необязательное задание временно пропущено, ввиду нехватки времени к крайнему сроку.)  
___

### Задача 3
*С помощью Ansible и Docker Compose разверните на виртуальной машине из предыдущего задания систему мониторинга на основе Prometheus/Grafana. Используйте Ansible-код в директории (src/ansible).*

Чтобы получить зачёт, вам нужно предоставить вывод команды "docker ps" , все контейнеры, описанные в docker-compose, должны быть в статусе "Up".

#### Решение.

![docker_ps_screenshot](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-4_Docker_Compose/5-4-3.png)

___
### Задача 4
*Откройте веб-браузер, зайдите на страницу http://<внешний_ip_адрес_вашей_ВМ>:3000.  
Используйте для авторизации логин и пароль из .env-file.  
Изучите доступный интерфейс, найдите в интерфейсе автоматически созданные docker-compose-панели с графиками(dashboards).  
Подождите 5-10 минут, чтобы система мониторинга успела накопить данные.  
Чтобы получить зачёт, предоставьте скриншот работающего веб-интерфейса Grafana с текущими метриками.*  

#### Решение.

![Panels_screenshot](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-4_Docker_Compose/5-4-4_Panels.png)
![Monitor_Services_screenshot](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-4_Docker_Compose/5-4-4_Monitor_graph.png)
![Docker_Host_screenshot](https://github.com/Roman-K-E/devops-homeworks/blob/main/5-4_Docker_Compose/5-4-4_Docker_graph.png)



