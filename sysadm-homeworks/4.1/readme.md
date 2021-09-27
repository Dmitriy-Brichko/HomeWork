####1. Какие значения переменным c,d,e будут присвоены? Почему?
   * c = a+b
   * d = 1+2
   * e = 3

   Так произошло, потому что:
   * в переменную "c" было просто записано выражение a+b
   * в переменную "d" были записаны значения переменных a + b
   * в переменную "e" было записано значение, полученное при сложении переменных a и b, итого получилось 3.

####2. На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным. В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить
    
   Не хватает ")" 18 символ в первой строке. Можно добавить в восьмой строке sleep 60, чтобы сократить размер файла записями ошибок соединения.

```bash
while (( 1 == 1 ))
do
curl https://localhost:4757
if (($? != 0))
then
date >> curl.log
fi
sleep 60
done
```
####3. Необходимо написать скрипт, который проверяет доступность трёх IP: 192.168.0.1, 173.194.222.113, 87.250.250.242 по 80 порту и записывает результат в файл log. Проверять доступность необходимо пять раз для каждого узла.

```bash
ip=(192.168.0.1 173.194.222.113 87.250.250.242)
timeout=1
for i in {1..5}
do
date >> ip.log
for h in ${ip[@]}
do
curl -Is --connect-timeout $timeout $h:80 > /dev/null
echo "check" $h status=$? >> ip.log
done
done
```
####4. Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается

```bash
ip=(192.168.0.1 173.194.222.113 87.250.250.242)
timeout=1
notError=0
while (($notError == 0))
do
for h in ${ip[@]}
do
curl -Is --connect-timeout $timeout $h:80 > /dev/null
notError=$?
if (($notError != 0))
then
date >> ip_Error.log
echo "ERROR on " $h status=$? >> ip_Error.log
fi
done
done
```