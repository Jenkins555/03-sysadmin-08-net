# Домашнее задание к занятию «Компьютерные сети. Лекция 3»

### Цель задания

В результате выполнения задания вы:

* на практике познакомитесь с маршрутизацией в сетях, что позволит понять устройство больших корпоративных сетей и интернета;
* проверите TCP/UDP соединения на хосте — это обычный этап отладки сетевых проблем;
* построите сетевую диаграмму.

### Чеклист готовности к домашнему заданию

1. Убедитесь, что у вас установлен `telnet`.
2. Воспользуйтесь пакетным менеджером apt для установки.


### Инструкция к заданию

1. Создайте .md-файл для ответов на задания в своём репозитории, после выполнения прикрепите ссылку на него в личном кабинете.
2. Любые вопросы по выполнению заданий задавайте в чате учебной группы или в разделе «Вопросы по заданию» в личном кабинете.


### Дополнительные материалы для выполнения задания

1. [Зачем нужны dummy-интерфейсы](https://tldp.org/LDP/nag/node72.html).

------

## Задание

1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP.

 ```
telnet route-views.routeviews.org
Username: rviews
show ip route x.x.x.x/32
show bgp x.x.x.x/32
```    

  ```
  route-views>show ip route 50.7.93.83
Routing entry for 50.7.92.0/22
  Known via "bgp 6447", distance 20, metric 0
  Tag 2497, type external
  Last update from 202.232.0.2 2w0d ago
  Routing Descriptor Blocks:
  * 202.232.0.2, from 202.232.0.2, 2w0d ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 2497
      MPLS label: none

  ```
  
  ```   
   route-views>show bgp 50.7.93.83
BGP routing table entry for 50.7.92.0/22, version 567572
Paths: (20 available, best #19, table default)
  Not advertised to any peer
  Refresh Epoch 1
  3561 209 3356 174
    206.24.210.80 from 206.24.210.80 (206.24.210.80)
      Origin IGP, localpref 100, valid, external
      path 7F2B768DEF78 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3267 1299 174
    194.85.40.15 from 194.85.40.15 (185.141.126.1)
      Origin IGP, metric 0, localpref 100, valid, external
      path 7F2C541AEC60 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  19214 174
    208.74.64.40 from 208.74.64.40 (208.74.64.40)
      Origin IGP, localpref 100, valid, external
      Community: 174:21101 174:22010
      path 7F2BEDCB29A8 RPKI State not found
      rx pathid: 0, tx pathid: 0

  ```

2. Создайте dummy-интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.   

  ```
  vagrant@vagrant:~$ sudo modprobe dummy   
  vagrant@vagrant:~$ sudo ip link add name dummy0 type dummy   
  vagrant@vagrant:~$ sudo ip addr add 10.0.10.1/24 dev dummy0
  vagrant@vagrant:~$ sudo ip link set up dummy0   
  vagrant@vagrant:~$ sudo ip route add to 10.10.0.0/16 via 10.0.10.1
  vagrant@vagrant:~$ sudo ip route add to 10.12.0.0/16 via 10.0.10.1
  
  Создаётся виртуальный интерфейс dummy0, к которому привязывается IP-адрес 10.0.10.1/24. Затем, включается интерфейс dummy0, чтобы он мог принимать и отправлять сетевой трафик. Далее, с помощью команд sudo   ip route add добавляются два статических маршрута на подсети 10.10.0.0/16 и 10.12.0.0/16 через шлюз 10.0.10.1, то есть через созданный виртуальный интерфейс dummy0.
  
  vagrant@vagrant:~$ vagrant@vagrant:~$ ip route
  10.0.10.0/24 dev dummy0 proto kernel scope link src 10.0.10.1
  10.10.0.0/16 via 10.0.10.1 dev dummy0
  10.12.0.0/16 via 10.0.10.1 dev dummy0

 

  ```

3. Проверьте открытые TCP-порты в Ubuntu. Какие протоколы и приложения используют эти порты? Приведите несколько примеров.   

  ```   
   vagrant@vagrant:~$ sudo netstat -tlnp
   Active Internet connections (only servers)
   Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
   tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      656/systemd-resolve
   tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1586/sshd: /usr/sbi
   tcp6       0      0 :::22                   :::*                    LISTEN      1586/sshd: /usr/sbi

  Порт 53 используется для DNS-сервера systemd-resolve, который обеспечивает разрешение имен хостов на IP-адреса и наоборот.
  Порт 22 используется для SSH-сервера, который слушает входящие SSH-подключения и позволяет удаленным пользователям получать доступ к системе.Использует протокол tcp.

  ```

4. Проверьте используемые UDP-сокеты в Ubuntu. Какие протоколы и приложения используют эти порты?   

  ```
  vagrant@vagrant:~$ sudo netstat -ulnp
   Active Internet connections (only servers)
   Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
   udp        0      0 127.0.0.53:53           0.0.0.0:*                           656/systemd-resolve
   udp        0      0 10.0.2.15:68            0.0.0.0:*                           654/systemd-network   
   
   53: протокол DNS, используется приложением systemd-resolve.
   68: протокол DHCP, используется приложением systemd-network.
  ```

5. Используя diagrams.net, создайте L3-диаграмму вашей домашней сети или любой другой сети, с которой вы работали. 

*В качестве решения пришлите ответы на вопросы, опишите, как они были получены, и приложите скриншоты при необходимости.*

 ---
 
## Задание со звёздочкой* 

Это самостоятельное задание, его выполнение необязательно.

6. Установите Nginx, настройте в режиме балансировщика TCP или UDP.

7. Установите bird2, настройте динамический протокол маршрутизации RIP.

8. Установите Netbox, создайте несколько IP-префиксов, и, используя curl, проверьте работу API.

----

### Правила приёма домашнего задания

В личном кабинете отправлена ссылка на .md-файл в вашем репозитории.

-----

### Критерии оценки

Зачёт:

* выполнены все задания;
* ответы даны в развёрнутой форме;
* приложены соответствующие скриншоты и файлы проекта;
* в выполненных заданиях нет противоречий и нарушения логики.

На доработку:

* задание выполнено частично или не выполнено вообще;
* в логике выполнения заданий есть противоречия и существенные недостатки.  
 
Обязательными являются задачи без звёздочки. Их выполнение необходимо для получения зачёта и диплома о профессиональной переподготовке.

Задачи со звёздочкой (*) являются дополнительными или задачами повышенной сложности. Они необязательные, но их выполнение поможет лучше разобраться в теме.
