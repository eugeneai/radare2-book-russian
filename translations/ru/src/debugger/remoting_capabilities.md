# Возможности удаленного доступа

Radare, как правило, запускается локально, но можно запускать серверный процесс и  контролировать его локальным radare2. Реализация управления использует подсистему ввода-вывода radare, которая абстрагирует доступ к system(), cmd() и все основные операции ввода-вывода для работы по сети.

Справка по командам, используемым для организации удаленного доступа к radare:

```
[0x00405a04]> =?
Usage:  =[:!+-=ghH] [...]   # Cоединиться с другим процессом r2

команды удаленного доступа:
| =                             пересилить все открытые соединения
| =<[fd] cmd                    послать вывод локальной команды на удаленный fd
| =[fd] cmd                     запустить команду на удаленном 'fd' (по умолчанию - последний открытый)
| =! cmd                        запустить команду через r_io_system
| =+ [proto://]host:port        соединиться с удаленным host:port (*rap://, raps://, tcp://, udp://, http://)
| =-[fd]                        удалить все хосты или 'fd'
| ==[fd]                        открыть удаленную сессию с хостом 'fd', 'q' -выход
| =!=                           запретить режим удаленной командной строки
| !=!                           установка режима удаленной командной строки

серверы:
| .:9000                        запуск одного серыера (echo x|nc ::1 9090 или curl ::1:9090/cmd/x)
| =:port                        запуск rap-сервера (o rap://9999)
| =g[?]                         запуск gdbserver-а
| =h[?]                         запуск http-вебсервера
| =H[?]                         запуск http-вебсервера вместе с браузером

другие:
| =&:port                       запуск rap-сервера в фоновом режиме (аналогично '&_=h')
| =:host:port cmd               запуск команды 'cmd' на удаленном сервере

примеры:
| =+tcp://localhost:9090/       соединиться:  r2 -c.:9090 ./bin
| =+rap://localhost:9090/       соединиться: r2 rap://:9090
| =+http://localhost:9090/cmd/  соединиться: r2 -c'=h 9090' bin
| o rap://:9090/                запуск rap-сервера на tcp-порту 9090
```

Инструкции по удаленным возможностям radare2 отображаются списком поддерживаемых плагинов ввода-вывода: `radare2 -L`.

Типичный удаленный сеанс выглядит следующим образом:

На удаленном хосте 1:

```
$ radare2 rap://:1234
```

На удаленном хосте 2:

```
$ radare2 rap://:1234
```

На локальном хосте:

```
$ radare2 -
```

Добавление хостов

```
[0x004048c5]> =+ rap://<host1>:1234//bin/ls
Connected to: <host1> at port 1234
waiting... ok

[0x004048c5]> =
0 - rap://<host1>:1234//bin/ls
```

Можно открывать удаленные файлы в режиме отладки (или с помощью любого подключаемого модуля ввода-вывода), указывая URI при добавлении хостов:

```
[0x004048c5]> =+ =+ rap://<host2>:1234/dbg:///bin/ls
Connected to: <host2> at port 1234
waiting... ok
0 - rap://<host1>:1234//bin/ls
1 - rap://<host2>:1234/dbg:///bin/ls
```

Выполнение команд на хосте 1:

```
[0x004048c5]> =0 px
[0x004048c5]> = s 0x666
```

Открыть сеанс связи с хостом 2:

```
[0x004048c5]> ==1
fd:6> pi 1
...
fd:6> q
```

Удаление узлов и закрытие подключения:

```
[0x004048c5]> =-
```

Можно также перенаправлять вывод radare на TCP- или UDP-сервер, например, при помощи `nc -l`. Сначала добавьте сервер при помощи '=+ tcp://' или '=+ udp://', затем можно перенаправить выходные данные команды на сервер:

```
[0x004048c5]> =+ tcp://<host>:<port>/
Connected to: <host> at port <port>
5 - tcp://<host>:<port>/
[0x004048c5]> =<5 cmd...
```

Команда `=<` отправит вывод команды `cmd` на удаленное подключение с номером N или на последнее подключение, если идентификатор не указан.
