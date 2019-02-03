kzload
====

```
$ make
/usr/local/bin/h8300-elf-gcc -c -Wall -mh -nostdinc -nostdlib -fno-builtin -I. -Os -DKZLOAD vector.c
/usr/local/bin/h8300-elf-gcc -c -Wall -mh -nostdinc -nostdlib -fno-builtin -I. -Os -DKZLOAD startup.s
/usr/local/bin/h8300-elf-gcc -c -Wall -mh -nostdinc -nostdlib -fno-builtin -I. -Os -DKZLOAD main.c
/usr/local/bin/h8300-elf-gcc -c -Wall -mh -nostdinc -nostdlib -fno-builtin -I. -Os -DKZLOAD lib.c
/usr/local/bin/h8300-elf-gcc -c -Wall -mh -nostdinc -nostdlib -fno-builtin -I. -Os -DKZLOAD serial.c
/usr/local/bin/h8300-elf-gcc vector.o startup.o main.o lib.o serial.o -o kzload -Wall -mh -nostdinc -nostdlib -fno-builtin -I. -Os -DKZLOAD -static -T ld.scr -L.
cp kzload kzload.elf
/usr/local/bin/h8300-elf-strip kzload
```

```
$ make image
/usr/local/bin/h8300-elf-objcopy -O srec kzload kzload.mot
```

```
1 2 3 4
    x
x x   x
```

```
$ sudo make write
../../../tools/h8write/h8write -3069 -f20 kzload.mot /dev/ttyUSB0
H8/3069F is ready!  2002/5/20 Yukio Mituiwa.
writing
WARNING:This Line dosen't start with"S".
Address Size seems wrong
WARNING:This Line dosen't start with"S".
Address Size seems wrong
.....
EEPROM Writing is successed.
```

```
1 2 3 4
x   x
  x   x
```

```
$ sudo chgrp root /dev/ttyUSB0
$ sudo cu -l /dev/ttyUSB0
Connected.
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
~.
Disconnected.
```
