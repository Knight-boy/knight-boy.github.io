---
title: "Linux 操作手册"
date: 2019-03-06
image: /assets/img/blog/linux.gif
description: >
  常用命令整理中...
author: author2
comments: true
---

**linux**常用命令的学习

- 列出目标目录中的所有子目录 **`ls`**，`-a`列出目录下所有文件，包括`.`(当前目录)和返回上一级的`..`隐含目录；`-l` 列出文件名以及文件的权限、所有者、文件大小和日期等信息；`-o`类似`-l`，显示除组信息外的详细信息；`-t`以文件修改时间排序；

- **`fdisk`**是一个创建和维护分区的程序；`-l`列出所有的分区表，`-lu`显示分区数目

- **`pwd`**查看当前工作目录的完整路径

- 删除文件夹 **`rm -rf`**，`-r`目录深层次递归，`-f`强制删除，不出现提示信息

- **`rmdir`**不能删除非空目录；`-p`当子目录被删除后使它也成为空目录的话，则一并删除

- **`which`**用于查找文件, 将给出所查找内容的绝对路径;

- **`whereis`**会在特定目录中查找符合条件的文件, 这些文件可以属于原始文件, 二进制文件或是帮助文件; `-b`只查找二进制文件, `-m`只查找说明文件, `-s`只查找原始代码文件,`-u`查找不包含指定类型的文件;

- **`df`**显示磁盘的使用情况, `-m` 以`--block-size=1048576`表示磁盘的使用信息,  `-h`使用人类可读的格式显示磁盘信息, 以K，M，G为单位，提高信息的可读性。

- **`du`**显示指定目录或文件所占用的磁盘空间;

- **`tree`**以树状图的形式列出目录的组织形式; `-C`在文件和目录清单上加上颜色, 便于区分各种类型; `-D`列出文件或目录的更改时间;`-f`在每个文件或目录前,显示完整的相对路径; `-s`列出文件或目录的大小; `-t`按文件或目录的修改时间排序;

- **`wget`**用来从指定的URL下载文件; `-a`在指定的日志中记录资料的执行过程; `-A<后缀名>`指定要下载的后缀名,多个用逗号隔开; `-b`进行后台的方式运行`wget`; `-c`继续执行上次终端的任务;  `-r`递归方式下载; `-nc`文件存在时, 下载时不覆盖原有文件;`-q`不显示指令执行过程; `-v`显示详细过程; `-V`显示版本信息;

- **`kill`**命令来删除执行中的程序或工作。`-a`当处理当前进程时，不限制命令名和进程号的对应关系；`-l`列出所有的信息名称；`-p`指定kill命令只打印相关进程的进程号，而不发送任何信号；`-s`指定要送出的信息；`-u`指定用户；常常用`ps`查找进程，然后用`kill`杀掉；

- **`killall`**使用此命令杀死进程. `-e`对长名称进行精确匹配;`-l`忽略大小写;`-p`杀死进程所属的进程组;`-i`杀死进程前需要确认;`-l`打印所有已知信号列表;`-q`如果没有进程被杀死,则不输出任何信息;`-r`使用正则表达式匹配要杀死的进程名称;`-s`用指定的进程号代替默认信号"SIGTERM";`-u`杀死指定用户的进程;`vi`杀死所有同名进程;

- **`ps`**用于报告系统的进程状态,搭配`kill`指令随时中断,删除不必要的程序.`-a`显示所有终端机下执行的程序; `c`列出程序时,显示每个程序真正的指令名称,不包含路径;`e`显示每个程序所用的环境变量;`f`用ASCII字符显示树状结构,表达程序间的相互关系;`-H`显示树状结构,表示程序间的相互关系;`r`只列出现行终端机正在执行中的程序;`u`以用户为主的格式来显示程序状况.

- **`free`**显示当前系统未使用的和已使用的内存数目,还可以显示被内核使用的内存缓冲区;`-b`,`-k`,'-m'分别以Byte/KB/MB为单位显示内存使用情况;`-o`不显示缓冲区调节列;`-s<间隔毫秒数>持续观察内存的使用情况;`-t`显示内存总列和;`-V`显示版本信息;

- **`netstat -tunlp | grep 端口号`**查看某一端口的情况  `netstat -ntlp`查看当前多有TCP端口；`netstat -ntulp |grep 80`查看80端口的使用情况；`-t` (tcp) 仅显示tcp相关选项;`-u` (udp)仅显示udp相关选项;`-n` 拒绝显示别名，能显示数字的全部转化为数字;`-l`仅列出在Listen(监听)的服务状态;`-p`显示建立相关链接的程序名；

- pwd: print work directory 打印当前目录 显示出当前工作目录的绝对路径
  ps: process status(进程状态，类似于windows的任务管理器)
  
  常用参数：－auxf
  
  ps -auxf 显示进程状态
  
  df: disk free 其功能是显示磁盘可用空间数目信息及空间结点信息。换句话说，就是报告在任何安装的设备或目录中，还剩多少自由的空间。
  du: Disk usage
  rpm：即RedHat Package Management，是RedHat的发明之一
  rmdir：Remove Directory（删除目录）
  rm：Remove（删除目录或文件）
  cat: concatenate 连锁
  cat file1file2>>file3 把文件1和文件2的内容联合起来放到file3中
  insmod: install module,载入模块
  ln -s : link -soft 创建一个软链接，相当于创建一个快捷方式
  mkdir：Make Directory(创建目录)
  touch: touch
  man: Manual
  su：Swith user(切换用户)
  cd：Change directory
  ls：List files
  ps：Process Status
  mkdir：Make directory
  rmdir：Remove directory
  mkfs: Make file system
  fsck：File system check
  uname: Unix name
  lsmod: List modules
  mv: Move file
  rm: Remove file
  cp: Copy file
  ln: Link files
  fg: Foreground
  bg: Background
  chown: Change owner
  chgrp: Change group
  chmod: Change mode
  umount: Unmount
  dd: 本来应根据其功能描述"Convert an copy"命名为"cc"，但"cc"已经被用以代表"CComplier"，所以命名为"dd"
  tar：Tape archive （磁带档案）
  ldd：List dynamic dependencies
  insmod：Install module
  rmmod：Remove module
  lsmod：List module
  文件结尾的"rc"（如.bashrc、.xinitrc等）：Resource configuration
  Knnxxx /Snnxxx（位于rcx.d目录下）：K（Kill）；S(Service)；nn（执行顺序号）；xxx（服务标识）
  .a（扩展名a）：Archive，static library
  .so（扩展名so）：Shared object，dynamically linked library
  .o（扩展名o）：Object file，complied result of C/C++ source file
  RPM：Red hat package manager
  dpkg：Debian package manager
  apt：Advanced package tool（Debian或基于Debian的发行版中提供）
  
- bin = Binaries (二进制文件)

  /dev = Devices (设备)

  /etc = Etcetera (等等)

  /lib = LIBrary

  /proc = Processes

  /sbin = Superuser Binaries (超级用户的二进制文件)

  /tmp = Temporary (临时)

  /usr = Unix Shared Resources

  /var = Variable (变量)

  FIFO = First In, First Out

  GRUB = GRand Unified Bootloader

  IFS= Internal Field Seperators

  LILO = LInux LOader

  MySQL = My 是最初作者女儿的名字，

  SQL = Structured QueryLanguage

  PHP = Personal Home Page Tools = PHP HypertextPreprocessor

  PS = Prompt String

  Perl = “Pratical Extraction and Report Language”(实际的抽取和报告语言) =”Pathologically Eclectic Rubbish Lister”

  Python 得名于电视剧Monty Python’s Flying Circus

  Tcl = Tool Command Language

  Tk = ToolKit

  VT = Video Terminal

  YaST = Yet Another Setup Tool

  apache = “a patchy” server

  apt = Advanced Packaging Tool

  ar = archiver

  as = assembler

  awk = “Aho Weiberger and Kernighan”三个作者的姓的第一个字母

  bash = Bourne Again SHell

  bc = Basic (Better) Calculator

  bg = BackGround

  biff = 作者HeidiStettner在U.C.Berkely养的一条狗,喜欢对邮递员汪汪叫。

  cal = Calendar (日历)

  cat = Catenate (链接)

  cd = Change Directory

  chgrp = Change Group

  chmod = Change Mode

  chown = Change Owner

  chsh = Change Shell

  cmp = compare

  cobra = Common Object Request BrokerArchitecture

  comm = common

  cp = Copy

  cpio = CoPy In and Out

  cpp = C Pre Processor

  cron = Chronos 希腊文时间

  cups = Common Unix Printing System

  cvs = Current Version System

  daemon = Disk And Execution MONitor

  dc = Desk Calculator

  dd = Disk Dump (磁盘转储)

  df = Disk Free

  diff = Difference

  dmesg = diagnostic message

  du = Disk Usage

  ed = editor

  egrep = Extended GREP

  elf = Extensible Linking Format

  elm = ELectronic Mail

  emacs = Editor MACroS

  eval = EVALuate

  ex = EXtended

  exec = EXECute (执行)

  fd = file descriptors

  fg = ForeGround

  fgrep = Fixed GREP

  fmt = format

  fsck = File System ChecK

  fstab = FileSystem TABle

  fvwm = F*** Virtual Window Manager

  gawk = GNU AWK

  gpg = GNU Privacy Guard

  groff = GNU troff

  hal = Hardware Abstraction Layer

  joe = Joe’s Own Editor

  ksh = Korn SHell

  lame = Lame Ain’t an MP3 Encoder

  lex = LEXical analyser

  lisp = LISt Processing = Lots of IrritatingSuperfluous Parentheses

  ln = Link

  lpr = Line PRint

  ls = list

  lsof = LiSt Open Files

  m4 = Macro processor Version 4

  man = MANual pages

  mawk = Mike Brennan’s AWK

  mc = Midnight Commander

  mkfs = MaKe FileSystem

  mknod = Make Node

  motd = Message of The Day

  mozilla = MOsaic GodZILLa

  mtab = Mount TABle

  mv = Move

  nano = Nano’s ANOther editor

  nawk = New AWK

  nl = Number of Lines

  nm = names

  nohup = No HangUP

  nroff = New ROFF

  od = Octal Dump

  passwd = Passwd

  pg = pager

  pico = PIne’s message COmposition editor

  pine = “Program for Internet News &Email” = “Pine is not Elm”

  ping = 拟声 又 = Packet Internet Grouper

  pirntcap = PRINTer CAPability

  popd = POP Directory

  pr = pre

  printf = Print Formatted

  ps = Processes Status

  pty = pseudo tty

  pushd = PUSH Directory

  pwd = Print Working Directory

  rc = runcom = run command, rc还是plan9的shell

  rev = REVerse

  rm = ReMove

  rn = Read News

  roff = RunOFF

  rpm = RPM Package Manager = RedHat PackageManager

  rsh, rlogin, rvim中的

  r = Remote

  rxvt = ouR XVT

  seamoneky = 我

  sed = Stream Editor

  seq = SEQuence

  shar = Shell ARchive

  slrn = S-Lang rn

  ssh = Secure Shell

  ssl = Secure Sockets Layer

  stty = Set TTY

  su = Substitute User

  svn = SubVersion

  tar = Tape ARchive

  tcsh = TENEX C shell

  tee = T (T形水管接口)

  telnet = TEminaL over Network

  termcap = terminal capability

  terminfo = terminal information

  tex = τέχνη的缩写，希腊文art

  tr = traslate

  troff = Typesetter new ROFF

  tsort = Topological SORT

  tty = TeleTypewriter

  twm = Tom’s Window Manager

  tz = TimeZone

  udev = Userspace DEV

  ulimit = User’s LIMIT

  umask = User’s MASK

  uniq = UNIQue

  i = VIsual = Very Inconvenient

  vim = Vi IMproved

  wall = write all

  wc = Word Count

  wine = WINE Is Not an Emulator

  xargs = eXtended ARGuments

  xdm = X Display Manager

  xlfd = X Logical Font Description

  xmms = X Multimedia System

  xrdb = X Resources DataBase

  xwd = X Window Dump

  yacc = yet another compiler compiler

  Fish = the Friendly Interactive SHell

  su = Switch User

  MIME = Multipurpose Internet Mail Extensions

  ECMA = European Computer ManufacturersAssociation