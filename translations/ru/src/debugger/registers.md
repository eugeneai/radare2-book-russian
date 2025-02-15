# Регистры

Регистры являются частью среды пользователя, они хранятся в одной из структур среды (контексте), используемой планировщиком. Эту структуру можно изменять для получения и установки значений регистров, и, например, на хостах Intel можно напрямую манипулировать даже аппаратными регистрами DR0-DR7 для установки аппаратных точек останова.

Есть разные команды для получения значений регистров. Для регистров общего назначения используют

```
[0x4A13B8C0]> dr
r15 = 0x00000000
r14 = 0x00000000
r13 = 0x00000000
r12 = 0x00000000
rbp = 0x00000000
rbx = 0x00000000
r11 = 0x00000000
r10 = 0x00000000
r9 = 0x00000000
r8 = 0x00000000
rax = 0x00000000
rcx = 0x00000000
rdx = 0x00000000
rsi = 0x00000000
rdi = 0x00000000
oeax = 0x0000003b
rip = 0x7f20bf5df630
rsp = 0x7fff515923c0

[0x7f0f2dbae630]> dr rip ; get value of 'rip'
0x7f0f2dbae630

[0x4A13B8C0]> dr rip = esp   ; set 'rip' as esp
```

Взаимодействие между плагином и ядром осуществляется командами, возвращающими инструкции radare. Это используется, например, для установки флагов в ядре и значений регистров.

```
[0x7f0f2dbae630]> dr*      ; Добавление '*' покажет команды radare
f r15 1 0x0
f r14 1 0x0
f r13 1 0x0
f r12 1 0x0
f rbp 1 0x0
f rbx 1 0x0
f r11 1 0x0
f r10 1 0x0
f r9 1 0x0
f r8 1 0x0
f rax 1 0x0
f rcx 1 0x0
f rdx 1 0x0
f rsi 1 0x0
f rdi 1 0x0
f oeax 1 0x3b
f rip 1 0x7fff73557940
f rflags 1 0x200
f rsp 1 0x7fff73557940

[0x4A13B8C0]> .dr*  ; include common register values in flags
```

Старая копия регистров сохраняется на все время отладки, она позволяет отслеживать изменения, сделанные во время выполнения анализируемой программы. Доступ к копии можно получить с помощью `oregs`.

```
[0x7f1fab84c630]> dro
r15 = 0x00000000
r14 = 0x00000000
r13 = 0x00000000
r12 = 0x00000000
rbp = 0x00000000
rbx = 0x00000000
r11 = 0x00000000
r10 = 0x00000000
r9 = 0x00000000
r8 = 0x00000000
rax = 0x00000000
rcx = 0x00000000
rdx = 0x00000000
rsi = 0x00000000
rdi = 0x00000000
oeax = 0x0000003b
rip = 0x7f1fab84c630
rflags = 0x00000200
rsp = 0x7fff386b5080
```
Текущее состояние регистров

```
[0x7f1fab84c630]> dr
r15 = 0x00000000
r14 = 0x00000000
r13 = 0x00000000
r12 = 0x00000000
rbp = 0x00000000
rbx = 0x00000000
r11 = 0x00000000
r10 = 0x00000000
r9 = 0x00000000
r8 = 0x00000000
rax = 0x00000000
rcx = 0x00000000
rdx = 0x00000000
rsi = 0x00000000
rdi = 0x7fff386b5080
oeax = 0xffffffffffffffff
rip = 0x7f1fab84c633
rflags = 0x00000202
rsp = 0x7fff386b5080
```

Значения, хранящиеся в eax, oeax и eip, изменились.

Если надо сохранять и восстанавливать значения регистров,  сбрасывайте их дамп командой 'dr*' на диск, а затем можно снова проинтерпретировать его:

```
[0x4A13B8C0]> dr* > regs.saved ; сохранить регистры
[0x4A13B8C0]> drp regs.saved ; восстановить их
```

Аналогичным образом меняются EFLAGS. Например, установка выбранных флагов:

```
[0x4A13B8C0]> dr eflags = pst
[0x4A13B8C0]> dr eflags = azsti
```

Получение информации об изменении регистров - команда  `drd` (diff registers):

```
[0x4A13B8C0]> drd
oeax = 0x0000003b was 0x00000000 delta 59
rip = 0x7f00e71282d0 was 0x00000000 delta -418217264
rflags = 0x00000200 was 0x00000000 delta 512
rsp = 0x7fffe85a09c0 was 0x00000000 delta -396752448
```
