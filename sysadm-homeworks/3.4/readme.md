1. unit-файл для node_exporter во вложении "1. node_exporter.service.PNG"
   - sudo systemctl enable node_exporter
   - EnvironmentFile=-/etc/sysconfig/node_exporter
   - во вложении "1. после перезагрузки автоматически поднимается.PNG"
2. /usr/sbin/node_exporter --help
   --collector.cpu.info --collector.cpu --collector.cpufreq
   --collector.meminfo --collector.meminfo_numa
   --collector.diskstats --collector.diskstats.ignored-devices="^(ram|loop|fd|(h|s|v|xv)d[a-z]|nvme\\d+n\\d+p)\\d+$"
   --collector.netclass --collector.netdev --collector.netstat --collector.network_route
3. во вложении "3. netdata.PNG"
4. dmesg | grep "Hypervisor detected" Если система физическая, то никаких данных не будет.
5. fs.nr_open = 1048576
   Максимальное количество открытых файлов для всех процессов.
   ulimit [-SHabcdefiklmnpqrstuvxPT] [limit]
6. Не могу сделать sleep 1h с PID1, получилось только /bin/bash с помощью лекции:
   screen
   unshare -f --pid --mount-proc /bin/bash
   ps aux
7. Этой командой создается функция, которая циклически удваивается и не может закончится. Это будет продолжаться вплоть до того, пока не исчерпает системные ресурсы.
   "Это существо проработало почти 6 минут, было страшно за этим наблюдать в netdata"
   Чтобы завершить эту fork бомбу можно попробовать  
   while (sleep 100 &!) do; done
   Может помочь справиться с :
	Ограничить количества процессов, которые пользователь может запустить одновременно
	Ограничить количество выделяемой виртуальной памяти и другие системные ресурсы
	Интеллектуальное распознавание fork-бомбы средствами самой операционной системы
	настроить можно в /etc/security/limits.conf
   
   