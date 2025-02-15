# Управление переменными

Radare2 позволяет работать с локальными переменными независимо от их местоположения, в стеке они или в регистрах. Автоматический анализ переменных включен по умолчанию, но его можно отключить с помощью настройки `anal.vars`.

Основные команды управления переменными находятся в пространстве имен `afv`:

```
Usage: afv  [rbs]
| afv*                          вывести комнды r2 для добавления аргумента/локальной переменной в пространство флагов (flagspace)
| afv-([name])                  удалить все или заданные переменные
| afv=                          перечислить переменные функции и ее аргументы с сылками на дизассемблирование
| afva                          анализировать аргументы и локальные переменные функции
| afvb[?]                       изменять аргументы и локальные переменные, доступные по смащению в регистре bp
| afvd name                     вывести команды r2 для отображения значений аргументов и локальных переменных в отладчике
| afvf                          показать переменные во фрейме стека, задаваемого регистром bp
| afvn [new_name] ([old_name])  переименовать аргумент или локальную переменную
| afvr[?]                       изменить аргумент, соответствующий регистру
| afvR [varname]                перечислить адреса, где к переменной получают доступ (ЧТЕНИЕ)
| afvs[?]                       управление аргументами и локальными переменными, адрессируемыми при помощи регистра sp
| afvt [name] [new_type]        сменить тип для заданного аргумента или переменной
| afvW [varname]                перечислить адреса, где к переменным получают доступ (ИЗМЕНЕНИЕ)
| afvx                          показать перекресные ссылки на переменные функции (то же самое, что и afvR+afvW)
```

Команды `afvr`, `afvb` и `afvs` унифицированы, но позволяют манипулировать  аргументами и локальными переменными, хранимыми в регистрах, в стеке по смещениям в BP/FP, индексируемыми при помощи SP. Для группы команд `afvr` есть богатый набор операций над регистровыми переменными:

```
|Usage: afvr [reg] [type] [name]
| afvr                        перечислить переменные, соответствующие регистрам
| afvr*                       то же, что и afvr, но в виде команд r2
| afvr [reg] [name] ([type])  определить аргумент, соответствующий ркгистру
| afvrj                       показать перечень регистровых переменных в формате JSON
| afvr- [name]                удалить регистровый аргумент
| afvrg [reg] [addr]          определить get-интерфейс аргумента
| afvrs [reg] [addr]          определить set-интерфейс аргумента
```

Как и для многих других программных объектов, обнаружение переменных происходит автоматически, результаты можно изменить с помощью команд управления аргументами/локальными переменными. Этот вид анализа в значительной степени полагается на предварительно загруженные прототипы функций и соглашение о вызовах, таким образом загружая символы из отладочной информации, можно значительно улучшить результаты анализа. Более того, внеся изменения можно перезапустить анализ переменных с помощью команды `afva`. Довольно часто анализ переменных сопровождается [анализом типов](types.md), ознакомьтесь с инструкциями команды `afta`.

Самый важный аспект реверс-инжениринга — именование программных объектов и сущностей. После переименования переменных в текстах дизассемблирования автоматически меняются все ссылки на эти переменные. Переименование аргументов и локальных переменных осуществляется при помощи команды `afvn`. Удаление делается при помощи команды `afv-`.

Как упоминалось ранее, анализ в значительной степени зависит от информации о типах переменных. Следующая очень важная команда - `afvt`, она позволяет изменять тип переменной:

```
[0x00003b92]> afvs
var int local_8h @ rsp+0x8
var int local_10h @ rsp+0x10
var int local_28h @ rsp+0x28
var int local_30h @ rsp+0x30
var int local_32h @ rsp+0x32
var int local_38h @ rsp+0x38
var int local_45h @ rsp+0x45
var int local_46h @ rsp+0x46
var int local_47h @ rsp+0x47
var int local_48h @ rsp+0x48
[0x00003b92]> afvt local_10h char*
[0x00003b92]> afvs
var int local_8h @ rsp+0x8
var char* local_10h @ rsp+0x10
var int local_28h @ rsp+0x28
var int local_30h @ rsp+0x30
var int local_32h @ rsp+0x32
var int local_38h @ rsp+0x38
var int local_45h @ rsp+0x45
var int local_46h @ rsp+0x46
var int local_47h @ rsp+0x47
var int local_48h @ rsp+0x48
```

Пока еще редко используемый инструмент, находящаяся в стадии интенсивной разработки, — построение списков переменных, которые читаются и изменяются. Команда `afvR` перечисляет переменные, которые читаются, а `afvW` - изменяются. Обе команды предоставляют список адресов, где эти операции выполняются:

```
[0x00003b92]> afvR
local_48h  0x48ee
local_30h  0x3c93,0x520b,0x52ea,0x532c,0x5400,0x3cfb
local_10h  0x4b53,0x5225,0x53bd,0x50cc
local_8h  0x4d40,0x4d99,0x5221,0x53b9,0x50c8,0x4620
local_28h  0x503a,0x51d8,0x51fa,0x52d3,0x531b
local_38h
local_45h  0x50a1
local_47h
local_46h
local_32h  0x3cb1
[0x00003b92]> afvW
local_48h  0x3adf
local_30h  0x3d3e,0x4868,0x5030
local_10h  0x3d0e,0x5035
local_8h  0x3d13,0x4d39,0x5025
local_28h  0x4d00,0x52dc,0x53af,0x5060,0x507a,0x508b
local_38h  0x486d
local_45h  0x5014,0x5068
local_47h  0x501b
local_46h  0x5083
local_32h
[0x00003b92]>
```

## Вывод типа

Вывод типа для локальных переменных и аргументов хорошо интегрирован с командой `afta`. Посмотрим пример с простым бинарным файлом [hello_world](https://github.com/radareorg/radare2book/tree/master/examples/hello_world)

```
[0x000007aa]> pdf
|           ;-- main:
/ (fcn) sym.main 157
| sym.main ();
| ; var int local_20h @ rbp-0x20
| ; var int local_1ch @ rbp-0x1c
| ; var int local_18h @ rbp-0x18
| ; var int local_10h @ rbp-0x10
| ; var int local_8h @ rbp-0x8
| ; DATA XREF from entry0 (0x6bd)
| 0x000007aa  push rbp
| 0x000007ab  mov rbp, rsp
| 0x000007ae  sub rsp, 0x20
| 0x000007b2  lea rax, str.Hello          ; 0x8d4 ; "Hello"
| 0x000007b9  mov qword [local_18h], rax
| 0x000007bd  lea rax, str.r2_folks       ; 0x8da ; " r2-folks"
| 0x000007c4  mov qword [local_10h], rax
| 0x000007c8  mov rax, qword [local_18h]
| 0x000007cc  mov rdi, rax
| 0x000007cf  call sym.imp.strlen         ; size_t strlen(const char *s)
```

* После применения `afta`:

```
[0x000007aa]> afta
[0x000007aa]> pdf
| ;-- main:
| ;-- rip:
/ (fcn) sym.main 157
| sym.main ();
| ; var size_t local_20h @ rbp-0x20
| ; var size_t size @ rbp-0x1c
| ; var char *src @ rbp-0x18
| ; var char *s2 @ rbp-0x10
| ; var char *dest @ rbp-0x8
| ; DATA XREF from entry0 (0x6bd)
| 0x000007aa  push rbp
| 0x000007ab  mov rbp, rsp
| 0x000007ae  sub rsp, 0x20
| 0x000007b2  lea rax, str.Hello          ; 0x8d4 ; "Hello"
| 0x000007b9  mov qword [src], rax
| 0x000007bd  lea rax, str.r2_folks       ; 0x8da ; " r2-folks"
| 0x000007c4  mov qword [s2], rax
| 0x000007c8  mov rax, qword [src]
| 0x000007cc  mov rdi, rax                ; const char *s
| 0x000007cf  call sym.imp.strlen         ; size_t strlen(const char *s)
```

Также информация о типе извлекается из форматных строк, таких как `printf("fmt: %s, %u, %d",...)`, спецификации форматов находятся в `anal/d/spec.sdb`.

Можно создать новый профиль для указания набора символов определения формата в зависимости от различных библиотек/операционных систем/языков программирования, например:

```
win=spec
spec.win.u32=unsigned int
```
Затем изменить спецификацию по умолчанию на вновь созданную, используя переменную конфигурации `e anal.spec = win`

Для получения дополнительной информации о поддержке примитивных и определяемых пользователем типов в radare2 читайте раздел [типы](types.md).
