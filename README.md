[12 ステップで作る組み込み OS 自作入門](https://www.amazon.co.jp/dp/4877832394)
====


実行環境
----

以下購入。

| 商品名                                                                                         | 価格     |
| --------------------------------------------------------------------------------------------- | ------- |
| H8/3069F ネット対応マイコン LAN ボード(完成品)                                                      | ￥3,750 |
| 超小型スイッチング AC アダプター 5V2A 入力 100V 〜 240V GF12-US0520                                  | ￥650   |
| UGREEN USB シリアル変換ケーブル RS232 USB 9ピン 変換 シリアルケーブル D-sub9ピン Windows Mac OS両対応 1m | ￥1,120 |


開発環境
----

書籍に従い、H8/3069F 向けクロス開発環境を用意。

```
$ uname -a
Linux koshigoe-t480s 4.15.0-43-generic #46-Ubuntu SMP Thu Dec 6 14:45:28 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

### binutils-2.19.1

```
$ wget http://kozos.jp/books/makeos/binutils-2.19.1.tar.gz
$ tar xvzf binutils-2.19.1.tar.gz
$ cd binutils-2.19.1
$ ./configure --target=h8300-elf --disable-nls --disable-werror
$ make
$ sudo make install
```

### gcc-3.4.6

書籍にかかれているとおり、ビルド中に `__open_missing_mode ();` に関するエラーが発生したのでソースコードを修正した。

```diff
--- gcc/collect2.c.orig 2019-02-02 17:58:46.528555638 +0900
+++ gcc/collect2.c      2019-02-02 17:59:06.253183166 +0900
@@ -1534,7 +1534,7 @@
   if (redir)
     {
       /* Open response file.  */
-      redir_handle = open (redir, O_WRONLY | O_TRUNC | O_CREAT);
+      redir_handle = open (redir, O_WRONLY | O_TRUNC | O_CREAT, 0755);

       /* Duplicate the stdout and stderr file handles
         so they can be restored later.  */
```

また、以下のエラーも発生したが、[ブログ記事](http://d.hatena.ne.jp/ryochack/20110515/1305400022)を参考にソースコードを修正した。

```
./libgcc2.c: In function `__muldi3':
./libgcc2.c:537: error: unrecognizable insn:
(insn 244 243 245 0 ./libgcc2.c:528 (set:HI (reg:HI 3 r3)
        (const_int 4294967222 [0xffffffb6])) -1 (nil)
    (nil))
./libgcc2.c:537: internal compiler error: in extract_insn, at recog.c:2083
Please submit a full bug report,
with preprocessed source if appropriate.
See <URL:http://gcc.gnu.org/bugs.html> for instructions.
libgcc.mk:134: recipe for target 'libgcc/./_muldi3.o' failed
```

```diff
--- gcc/config/h8300/h8300.c.orig	2019-02-02 18:14:42.940233971 +0900
+++ gcc/config/h8300/h8300.c	2019-02-02 18:16:48.690817226 +0900
@@ -52,8 +52,8 @@
 static int h8300_saveall_function_p (tree);
 static int h8300_monitor_function_p (tree);
 static int h8300_os_task_function_p (tree);
-static void h8300_emit_stack_adjustment (int, unsigned int);
-static int round_frame_size (int);
+static void h8300_emit_stack_adjustment (int, HOST_WIDE_INT);
+static HOST_WIDE_INT round_frame_size (HOST_WIDE_INT);
 static unsigned int compute_saved_regs (void);
 static void push (int);
 static void pop (int);
@@ -368,7 +368,7 @@
    SIZE to adjust the stack pointer.  */
 
 static void
-h8300_emit_stack_adjustment (int sign, unsigned int size)
+h8300_emit_stack_adjustment (int sign, HOST_WIDE_INT size)
 {
   /* H8/300 cannot add/subtract a large constant with a single
      instruction.  If a temporary register is available, load the
@@ -397,8 +397,8 @@
 
 /* Round up frame size SIZE.  */
 
-static int
-round_frame_size (int size)
+static HOST_WIDE_INT
+round_frame_size (HOST_WIDE_INT size)
 {
   return ((size + STACK_BOUNDARY / BITS_PER_UNIT - 1)
 	  & -STACK_BOUNDARY / BITS_PER_UNIT);
```

```
$ wget http://kozos.jp/books/makeos/gcc-3.4.6.tar.gz
$ tar xvzf gcc-3.4.6.tar.gz
$ cd gcc-3.4.6
$ ./configure \
  --target=h8300-elf \
  --disable-nls \
  --disable-threads \
  --disable-shared \
  --enable-languages=c
$ env SHELL=/bin/bash make
$ sudo make install
```

### cu

```
$ sudo apt install cu
```

### lrzsz

```
$ sudo apt install lrzsz
```
