## Секции

Понятие секций (разделов) привязано к информации, извлекаемой из двоичного файла. Наиболее часто — это исполняемый файл. Двоичными файлами в этой книге также обозначаются скомпилированные объектные файлы и им подобные. Информация о секциях отображается с помощью команды `i`.

Отображение информации о секциях:

```
[0x00005310]> iS
[Sections]
00 0x00000000     0 0x00000000     0 ----
01 0x00000238    28 0x00000238    28 -r-- .interp
02 0x00000254    32 0x00000254    32 -r-- .note.ABI_tag
03 0x00000278   176 0x00000278   176 -r-- .gnu.hash
04 0x00000328  3000 0x00000328  3000 -r-- .dynsym
05 0x00000ee0  1412 0x00000ee0  1412 -r-- .dynstr
06 0x00001464   250 0x00001464   250 -r-- .gnu.version
07 0x00001560   112 0x00001560   112 -r-- .gnu.version_r
08 0x000015d0  4944 0x000015d0  4944 -r-- .rela.dyn
09 0x00002920  2448 0x00002920  2448 -r-- .rela.plt
10 0x000032b0    23 0x000032b0    23 -r-x .init
...
```

Как вы, возможно, знаете, двоичные файлы состоят из секций и карт памяти. Секции определяют содержимое части файла, отображаемое в память (или нет). То, что отображается, определяется сегментами.

До рефакторинга ввода-вывода, выполненного condret, команда `S` использовалась для управления так называемыми картами. В настоящее время команда `S` устарела, поскольку `iS` и `om` теперь достаточно.

Загрузчики прошивки микроконтроллеров, загрузчики  двоичных файлов операционной системы обычно размещают секции по разным адресам в памяти. Radare интерпретирует работу загрузчика — команды группы `iS`. Инструкции представлены в `iS?`. Для перечисления всех созданных секций используйте `iS` (или `iSj` для получения результата в формате json). Команда `iS=` покажет карту разделов в ascii-art.

С помощью подкоманды `om` можно создать новое отображение следующим образом:
```
om fd vaddr [size] [paddr] [rwx] [name]
```

Пример:
```
[0x0040100]> om 4 0x00000100 0x00400000 0x0001ae08 rwx test
```

Также можно использовать команду `om` для просмотра сведений об отображаемых разделах:

```
[0x00401000]> om
 6 fd: 4 +0x0001ae08 0x00000100 - 0x004000ff rwx test
 5 fd: 3 +0x00000000 0x00000000 - 0x0000055f r-- fmap.LOAD0
 4 fd: 3 +0x00001000 0x00001000 - 0x000011e4 r-x fmap.LOAD1
 3 fd: 3 +0x00002000 0x00002000 - 0x0000211f r-- fmap.LOAD2
 2 fd: 3 +0x00002de8 0x00003de8 - 0x0000402f r-- fmap.LOAD3
 1 fd: 4 +0x00000000 0x00004030 - 0x00004037 rw- mmap.LOAD3
```

В результате выполнения `om?` будет выведен перечень всех подкоманд. Чтобы перечислить все ранее определенные карты памяти, используйте `om` (или `omj` для получения результата в формате json, `om*` — формат команд r2). Чтобы получить представление в ascii-art, используйте `om=`. Также можно удалить сопоставленный участок с помощью команды `om-mapid`.

Пример:
```
[0x00401000]> om-6
```
