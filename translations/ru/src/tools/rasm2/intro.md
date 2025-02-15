# Утилита Rasm2

Программа `rasm2` является ассемблером/дизассемблером. Изначально инструмент `rasm` разработан для внесения исправлений (patching) в двоичный файл. Основная функция заключается в получении кодов байтов, соответствующих заданной машинной инструкции (оп-кода).

```
$ rasm2 -h
Usage: rasm2 [-ACdDehLBvw] [-a arch] [-b bits] [-o addr] [-s syntax]
             [-f file] [-F fil:ter] [-i skip] [-l len] 'code'|hex|-
 -a [arch]    Задать архитектуру для процедур ассемблирования/дисассемблирования (смотрите -L)
 -A           Показать результаты анализа заданных шеснадцатеричных кодов
 -b [bits]    Задать разрядность регистров процессора (8, 16, 32, 64) (RASM2_BITS)
 -B           Вывод/ввод в двоичном виде (-l обязателен для двоичного вывода)
 -c [cpu]     Задать конкретный CPU (зависит от архитектуры)
 -C           Вывод в формате C
 -d, -D       Дизассемблировать из шестнадцатеричных кодов байтов (-D показывать оп-коды)
 -e           Задать прямой порядок (big endian) вместо обратного (little endian) порядок байтов
 -E           Выводить ESIL-выражения (вход тот же как в -d)
 -f [file]    Прочитать данные из файла
 -F [in:out]  Задать входной и/или выходной фильтр (att2intel, x86.pseudo, ...)
 -h, -hh      Показать данное сообщение, -hh дает больше информации
 -i [len]     Игнорировать/пропустить len байт входных данных
 -j           Выводить в формате json
 -k [kernel]  Задать операционную систему  (linux, windows, darwin, ..)
 -l [len]     Длина ввода/вывода
 -L           Перечислить плагины Asm: (a-ассемлирование, d-дизассемблирование, A-анализ, e-ESIL)
 -o [offset]  Задать начальное смещение (по умлочанию 0)
 -O [file]    Имя выходного файла (rasm2 -Bf a.asm -O a)
 -p           Выполнить SPP над входными данными для ассемблирования
 -q           "Тихий" режим
 -r           Представить результат в виде команд radare
 -s [syntax]  Задать синтаксис (intel, att)
 -v           Показать информацию о версии
 -w           Описание семантики оп-кода (что делает инструкция)
 Если значение '-l' больше длины вывода, вывод заполняется nop-ами
 Если последний аргумент '-' читается из stdin
Переменные среды:
 RASM2_NOPLUGINS  Не загружать динамические плагины (ускорение загрузки)
 RASM2_ARCH       То же, что и rasm2 -a
 RASM2_BITS       То же, что и rasm2 -b
 R_DEBUG          Отображать ли сообщения об ошибках и сигнал о сбое

```

Плагины для поддерживаемых архитектур перечисляются, используя флаг `-L`. Используемый плагин указывается именем в флаге `-a`

```
$ rasm2 -L
_dAe  8 16       6502        LGPL3   6502/NES/C64/Tamagotchi/T-1000 CPU
_dAe  8          8051        PD      8051 Intel CPU
_dA_  16 32      arc         GPL3    Argonaut RISC Core
a___  16 32 64   arm.as      LGPL3   as ARM Assembler (use ARM_AS environment)
adAe  16 32 64   arm         BSD     Capstone ARM disassembler
_dA_  16 32 64   arm.gnu     GPL3    Acorn RISC Machine CPU
_d__  16 32      arm.winedbg LGPL2   WineDBG's ARM disassembler
adAe  8 16       avr         GPL     AVR Atmel
adAe  16 32 64   bf          LGPL3   Brainfuck (by pancake, nibble) v4.0.0
_dA_  32         chip8       LGPL3   Chip8 disassembler
_dA_  16         cr16        LGPL3   cr16 disassembly plugin
_dA_  32         cris        GPL3    Axis Communications 32-bit embedded processor
adA_  32 64      dalvik      LGPL3   AndroidVM Dalvik
ad__  16         dcpu16      PD      Mojang's DCPU-16
_dA_  32 64      ebc         LGPL3   EFI Bytecode
adAe  16         gb          LGPL3   GameBoy(TM) (z80-like)
_dAe  16         h8300       LGPL3   H8/300 disassembly plugin
_dAe  32         hexagon     LGPL3   Qualcomm Hexagon (QDSP6) V6
_d__  32         hppa        GPL3    HP PA-RISC
_dAe             i4004       LGPL3   Intel 4004 microprocessor
_dA_  8          i8080       BSD     Intel 8080 CPU
adA_  32         java        Apache  Java bytecode
_d__  32         lanai       GPL3    LANAI
_d__  8          lh5801      LGPL3   SHARP LH5801 disassembler
_d__  32         lm32        BSD     disassembly plugin for Lattice Micro 32 ISA
_dA_  16 32      m68k        BSD     Capstone M68K disassembler
_dA_  32         malbolge    LGPL3   Malbolge Ternary VM
_d__  16         mcs96       LGPL3   condrets car
adAe  16 32 64   mips        BSD     Capstone MIPS disassembler
adAe  32 64      mips.gnu    GPL3    MIPS CPU
_dA_  16         msp430      LGPL3   msp430 disassembly plugin
_dA_  32         nios2       GPL3    NIOS II Embedded Processor
_dAe  8          pic         LGPL3   PIC disassembler
_dAe  32 64      ppc         BSD     Capstone PowerPC disassembler
_dA_  32 64      ppc.gnu     GPL3    PowerPC
_d__  32         propeller   LGPL3   propeller disassembly plugin
_dA_  32 64      riscv       GPL     RISC-V
_dAe  32         rsp         LGPL3   Reality Signal Processor
_dAe  32         sh          GPL3    SuperH-4 CPU
_dA_  8 16       snes        LGPL3   SuperNES CPU
_dAe  32 64      sparc       BSD     Capstone SPARC disassembler
_dA_  32 64      sparc.gnu   GPL3    Scalable Processor Architecture
_d__  16         spc700      LGPL3   spc700, snes' sound-chip
_d__  32         sysz        BSD     SystemZ CPU disassembler
_dA_  32         tms320      LGPLv3  TMS320 DSP family (c54x,c55x,c55x+,c64x)
_d__  32         tricore     GPL3    Siemens TriCore CPU
_dAe  32         v810        LGPL3   v810 disassembly plugin
_dAe  32         v850        LGPL3   v850 disassembly plugin
_dAe  8 32       vax         GPL     VAX
adA_  32         wasm        MIT     WebAssembly (by cgvwzq) v0.1.0
_dA_  32         ws          LGPL3   Whitespace esotheric VM
a___  16 32 64   x86.as      LGPL3   Intel X86 GNU Assembler
_dAe  16 32 64   x86         BSD     Capstone X86 disassembler
a___  16 32 64   x86.nasm    LGPL3   X86 nasm assembler
a___  16 32 64   x86.nz      LGPL3   x86 handmade assembler
_dA_  16         xap         PD      XAP4 RISC (CSR)
_dA_  32         xcore       BSD     Capstone XCore disassembler
_dAe  32         xtensa      GPL3    XTensa CPU
adA_  8          z80         GPL     Zilog Z80
```

> Обратите внимание, что «ad» в первой колонке означает, что и ассемблер и дизассемблер поддерживаются плагином. "_d" - доступен только дизассемблер, "a_" - доступен только ассемблер.


