1. 37500,0 КБайт
2. скорость упадет в 1000 раз

3. #если задержка 1мс, скорость 100 Мбит/с, то: 
   #размер буфера TCP = 0.001 * 100/8 = 0,0125 МБ = 12,8 КБ
   #12,8 * 1024 / 0,001 = 13107200 Б/с = 13,1 МБ/с = 104,8 Мбит/с
   (100 / 8) * 1000000 = 12500000 Б/с
   13107200 / 1500 = 8333 пакетов/с
   1460 * 8333 = 12166180 Б/с полезных данных. 333820 Б/с на заголовки
   (12166180 / 1000000) * 8 = 97 Мбит/с - максимальная скорость передачи данных

   #Допустимый % потерь провайдера 0.5 %. Если учесть это при расчете, то максимальная реальная скорость при 100 Мбит/с и 52 ms будет 7.10 Mbit/sec (При выполнении "ping 104.22.49.171 -t -l 13000" потери составили 3%, файл: потери.png)
   Чем меньше размер фрейма тем меньше полезных передаваемых данных, соответственно меньше скорость перачи данных.

4. Ответы на вопросы преподавателя:
   1) маска подсети отвечает за количество адресов ip в подсети. Обращение к шлюзу происходит, если ip destination находится за пределами нашего пула адресов
   2) роутер или маршрутизатор это умная железка, которая умеет строить наиболее быстрые маршруты до хостов и связывает между собой сети.
      коммутаторы: свитч - железка, которая имеет arp таблицу. если в этой таблице записан MAC хоста, для которого предназначены полученные данные, то свитч отправляет эти данные только в порт, где находится этот хост
                   хаб - железка, которая всегда отправляет полученные пакеты во все остальные порты
   3) ip адресу присваивается тектовое имя. Имеет древовидную структуру
      DNS-сервер - это ПК + ПО для обслуживания DNS. Он ответственный за зону DNS или если не ответственный, то перенаправляет запрос другому серверу.
   4) Клиент запрашивает у сервера информацию, а сервер предоставляет ответ
      Рекурсивный, нерекурсивный, итеративный
   

   Мой браузер отправляет запрос сетевой карте, чтобы та дала ip netology.ru.
   Так как у меня в сети нет DNS-сервера запрос отправляет на мой роутер (маршрутизатор)
   Т.к. мой компьютер не знает mac-адреса роутера, то отправляет широковещательный arp-запрос с нужным адресом и ждёт ответа
   Запрос приходит на мой роутер. Роутер отправляет полученный пакет моему провайдеру, который знает, где находится корневой DNS-сервер
   root сервер отправляет к a.root-servers.net.,затем пакет идет к a.dns.ripn.net., после этого к cloe.ns.cloudflare.com., который знает, где находится netology.ru.
   Затем пакет идет обратно по тому же пути с нужным мне ip 
   Мой ПК получает адрес 104.22.48.171 (ip netology.ru.) и порт 80 (для протокола HTTP) и вызывает сокет ТСР. Внутри которого будет создан HTTP-запрос:
strace -e network curl -s '104.22.48.171:80'
AF_INET, SOCK_STREAM
   Запрас передается на транспортный уровень и создается сегмент TCP. Порт назначения ставится в заголовок
   Далее сегмент передается на сетевой уровень. ip серера и мой добавляются к сегменту и образуется пакет
   Затем пакету добавляется мой МАС и МАС шлюза (моего роутера)
   К пакету добавляется бит SIN
   Моим роутерер формирует аналоговый сигнал полученного пакета, и отправляется в сеть
   Роутер получатель преобразует аналоговый сигнал обратно в пакет
   Если TTL не закончился по пути, то он доходит до удаленного сервера
   Удаленный сервер и мой пк образуют ТСР соединение, посмотреть можно с помощью tcpdump (удаленный сервер получает мой SYN [S], и отправляет мне ACK (SYN+1) [S.], а мой пк после этого отвечает ACK (ACK+1) [.] ):
root@vagrant:/home/vagrant# tcpdump -i eth0 -c 5 -ttttnnvvS host 104.22.48.171
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
2021-05-23 10:50:10.649139 IP (tos 0x0, ttl 64, id 14063, offset 0, flags [DF], proto TCP (6), length 60)
    10.0.2.15.55836 > 104.22.48.171.80: Flags [S], cksum 0xa4fe (incorrect -> 0xab24), seq 2015937513, win 64240, options [mss 1460,sackOK,TS val 2154522160 ecr 0,nop,wscale 7], length 0
2021-05-23 10:50:10.699142 IP (tos 0x0, ttl 64, id 934, offset 0, flags [none], proto TCP (6), length 44)
    104.22.48.171.80 > 10.0.2.15.55836: Flags [S.], cksum 0xf426 (correct), seq 43968001, ack 2015937514, win 65535, options [mss 1460], length 0
2021-05-23 10:50:10.699243 IP (tos 0x0, ttl 64, id 14064, offset 0, flags [DF], proto TCP (6), length 40)
    10.0.2.15.55836 > 104.22.48.171.80: Flags [.], cksum 0xa4ea (incorrect -> 0x10f3), seq 2015937514, ack 43968002, win 64240, length 0
2021-05-23 10:50:10.700004 IP (tos 0x0, ttl 64, id 14065, offset 0, flags [DF], proto TCP (6), length 116)
   http://netology.ru перенаправляет меня на https://netology.ru
   При отправке данных в обе стороны используется шифрование
   Удаленный сервер отправляет мне запрошенные данные, режет их на части и нумерует, используется [S] от сервера и [S.] от моего пк
   Если какой-либо пакет потерялся, то мой пк говорит номер пакета, которого не хватает и сервер пересылает его снова
   После передачи запрошенных мной данный мой браузер собирает полученные данные в соответстии с нумерацией и показывает мне в читаемом виде.
5. Итеративные запросы:
   dig NS . @1.1.1.1 +noall +answer
   dig NS uk. @a.root-servers.net. +noall +authority
   dig NS co. @nsa.nic.uk. +noall +answer
   dig NS www.google. @1.1.1.1 +noall +authority	Ответ:ns-tld1.charlestonroadregistry.com.
   4 запроса.
6. 126
   524288
7. в /23 больше адресов чем в /24 в два раза
8. можно
   /15

   
   