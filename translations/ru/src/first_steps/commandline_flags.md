## Аргументы командной строки

Ядро Radare управляется множеством флагов, аргументов командной строки.

Вот кусочек сообщения справки по использованию флагов:
```
$ radare2 -h
Usage: r2 [-ACdfLMnNqStuvwzX] [-P patch] [-p prj] [-a arch] [-b bits] [-i file]
          [-s addr] [-B baddr] [-m maddr] [-c cmd] [-e k=v] file|pid|-|--|=
 --           запустить radare2 без открытия файла
 -            тоже, что 'r2 malloc://512'
 =            читать файл со стандартного ввода (используйте -i и -c для выполнения команд)
 -=           флаг !=! позволяет удаленно запускать команды
 -0           напечатать \x00 перед первой и после каждой команды
 -2           закрыть файловый дескриптор stderr (тихий режим без вывода сообщений)
 -a [arch]    установить asm.arch
 -A           запуск команды 'aaa' анализа ссылок в коде
 -b [bits]    задать asm.bits
 -B [baddr]   задать базовый адрес для PIE-бинариков
 -c 'cmd..'   запуск команды radare
 -C           отобразить в файл host:port (alias for -c+=http://%s/cmd/)
 -d           отлаживать запускаемый 'file' или процесс по 'pid'
 -D [backend] установить режим отладки (e cfg.debug=true)
 -e k=v       вычислить переменную конфигурации
 -f           размер блока приравнять к размеру файла
 -F [binplug] использовать известный всем плагин rbin насильно
 -h, -hh      показать сообщение о флагах и их предназначении, -hh - подробнее
 -H ([var])   напечатать переменную
 -i [file]    запустить файл-сценарий
 -I [file]    запустить файл-сценарий ПЕРЕД открытием основного файла
 -k [OS/kern] задать asm.os (linux, macos, w32, netbsd, ...)
 -l [lib]     загрузить плагин из файла
 -L           перечислить поддерживаемые плагины ввода-вывода
```
```
 -m [addr]    отобразить файл по указанному адресу (loadaddr)
 -M           не чудить с именами символов
 -n, -nn      не загружать информацию RBin (-nn загружает только двоичные структуры)
 -N           не загружать установки и сценарии пользователя
 -q           тихий режим без командной строки, выйти после исполнения -i
 -Q           тихий режим без командной строки, быстро затем выйти (quickLeak=true)
 -p [prj]     использовать проект, список, если не задан аргумент, загрузить, если не указан основной файл
 -P [file]    применить rapatch-файл и выйти
 -r [rarun2]  указать профиль rarun2 для загрузки (то же самое, что -e dbg.profile=X)
 -R [rr2rule] указать дополнительную директиву rarun2
 -s [addr]    начальный адрес смещения (seek)
 -S           запуск r2 в режиме "песочницы"
 -t           загрузить информацию rabin2 в "thread"
 -u           задать bin.filter=false для получения "грубых" имен sym/sec/cls
 -v, -V       показать версию radare2 (-V показывает версии библиотек)
 -w           открыть файл в режиме перезаписи
 -x           открыть без флага exec (asm.emu не будет работать), смотри io.exec
 -X           то же самое, что -e bin.usextr=false (используется в dyldcache)
 -z, -zz      не загружать строки, или загружать их в грубом формате
```

### Частые варианты использования

Открыть файл в режиме записи без анализа формата его заголовков.
```
$ r2 -nw file
```
Войти в оболочку r2, не открывая ни одного файла.
```
$ r2 -
```
Указать конкретный суббинарный файл в открываемом fatbin-файле:
```
$ r2 -a ppc -b 32 ls.fat
```
Запустить сценарий перед переходом в основную командную строку:
```
$ r2 -i patch.r2 target.bin
```
Выполнить команду и выйти, не входя в интерактивный режим:
```
$ r2 -qc ij hi.bin > imports.json
```
Установка значения переменной конфигурации:
```
$ r2 -e scr.color=0 blah.bin
```
Отладить программу:
```
$ r2 -d ls
```
Использовать существующий файл проекта:
```
$ r2 -p test
```
