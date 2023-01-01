# Linux Introduction - Kapitel 10

Verfasser: **Manuel Fellner**

Datum: **01.01.2023**

Thema: **File Operations**

# 1. Einführun zu Filesystemen

In Linux (und allen weiteren UNIX-like OSs) wird oft das Prinzip "Everything is a file" verwendet. Also wird das System auch in diesem schema gehalten. Egal ob es jetzt normale Dokumente oder Geräte wie eine Soundkarte sind, man kann in Linux damit im Filesystem interagieren. Man kann sie also lesen, beschreiben usw. 

In vielen Systemen (inkl. Linux) ist das Filesystem in einer Baumstruktur strukturiert. Dieser Baum startet bei etwas, das man meistens **root directory** (root Verzeichnis) nennt, welches den Beginn des Filesystems repräsentiert. Grundsätzlich ist das root Verzeichnis unter der location **/** bekannt.

![Filesystems](https://courses.edx.org/assets/courseware/v1/6c6a76e5e83450a2f75777a86ba8e790/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/LFS01_ch08_screen_03.jpg)

## 1.1 Varianten von Filesystemen

Linux unterstützt ziemlich viele Filesysteme, von Entwicklern werden sie folgendermaßen genannt:

- ext3 (extended Filesystem V3)

- ext4 (extended Filesystem V4), wird Hauptsächlich im Linux bereich verwendet

- squashfs

- btrfs

Ebenso existieren folgende Filesysteme, welche zwar von Linux unterstützt werden, man aber normalerweise nicht in einem Linux System verwendet:

- NTFS, vfat (Windows)

- xfs (SGI)

- jfs (IBM)

- hfs, hfs+ (MacOS)

Viele andere, ältere Filesysteme wie z.B. FAT werden ebenso unterstützt.

Ein Filesystem ist elementar für ein System und oft werden auf einem System mehrere Filesysteme verwendet. Man hält sich hierbei aber bei den Varianten, die **journaling** unterstütuen: `ext4, btrfs und jfs`.

## 1.2 Linux Partitionen

Jedem Filesystem auf einem Linux System besitzt eine sogenannte **disk Partition**. Diese Partitionen helfen bei der Organisation der Daten auf der Festplatte, sie werden nach der Nutzung und dem Zweck aufgeteilt. Zum Beispiel trennt man normalerweise die `/boot/efi` Partition und die `root` Partition. Die root Partition wird normalerweise `root` oder `/` genannt. Auf dieser Partition befinden sich normalerweise alle Daten, welche von Benutzern des Systems verwendet werden (`/home`). 

Ebenso getrennt von anderen Partitionen ist die `linuxswap` Partition. Wie der Name schon sagt, enthält diese Partition den SWAP-Speicher für das Linux System.

Der Vorteil einer solchen funktionalen Trennung/Isolation ist, dass sich das System trotzdem noch normal verhalten könnte, auch wenn eine Partition voll ist.

Das folgende Bild repräsentiert meine momentanen Partitionen:

<img src="file:///home/manu/.config/marktext/images/2023-01-01-14-25-01-image.png" title="" alt="" width="705">

## 1.3 Mount Points

Bevor man ein Filesystem benutzen kann, muss man es an einem sogenannten **mount point** im Filesystem tree **mounten** (einhängen). Man sucht sich also z.B. dafür ein directory aus, welches leer oder auch nicht sein kann.

**Warnung:** Wenn man ein Filesystem in einem nicht leeren directory einhängt (mounted), dann ist der Inhalt des directories solange nicht erreichbar, wie das Filesystem eingehängt. Der Inhalt wird vom Filesystem quasi überdeckt. Deswegen sollten mounting points immer leere Ordner sein.

## 1.4 Mounting und Unmounting

Der `mount` Befehl wird verwendet, um Filesysteme an das System einzuhängen. Diese Filesysteme können im Netzwerk oder auch lokal im System liegen. Der Befehl wird folgendermaßen verwendet:

```shell
$ sudo mount /dev/sda5 /home
```

- `/dev/sda5` ist in dem Fall das Filesystem, welches wir mounten wollen

- `/home` ist das Directory, an welches wir `/dev/sda5` einhängen

Der Befehl wird nun die Disk Partition, welche mit dem device node `/dev/sda5` verbunden ist im mounting point `/home` einhängen.

Man kann statt dem device node auch noch z.B. das disk label oder die UUID verwenden.

```shell
$ sudo umount /home
```

Dieser Befehl würde das Filesystem dann vom mounting Point `/home` aushängen.

Beachte, dass **umount != unmount** ist. Nur ein root user hat normalerweise das Privileg, diesen Befehl auszuführen. 

Wenn man ein Filesystem direkt beim Systemstart mounten möchte, muss man das `/etc/fstab` File richtig bearbeiten (fstab = filesystem table). Wenn man sich das File anschaut, kann man bereits alle vorhandenen Filesysteme finden:

```shell
$ cat /etc/fstab
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a device; this may
# be used with UUID= as a more robust way to name devices that works even if
# disks are added and removed. See fstab(5).
#
# <file system>             <mount point>  <type>  <options>  <dump>  <pass>
UUID=5845-E88A                            /boot/efi      vfat    umask=0077 0 2
UUID=7201c808-bd25-4a94-a4f7-eed8e86b2559 /              ext4    defaults,noatime 0 1
UUID=7b25be36-2053-4ccc-8d60-3c946207b41c swap           swap    defaults,noatime 0 0
tmpfs                                     /tmp           tmpfs   defaults,noatime,mode=1777 0 0
```

Um sich genauere Informationen über `fstab` zu holen, kann man einfach die manpage mit `man fstab` aufschlagen. Darin sind dann alle Informationen über fstab und die Verwendung des files.

Wenn man einfach nur `mount` ohne Argumente eingibt, werden einem alle Filesysteme angezeigt, welche kürzlich eingehängt wurden.

Der Command `df -Th` (disk-free) wird dir alle Informationen über momentan eingehängte Filesysteme anzeigen (inkl. Filesystem usage und statistiken über verwendeten und verfügbaren Speicherplatz).

```shell
$ mount | head -10
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
sys on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
dev on /dev type devtmpfs (rw,nosuid,relatime,size=7575428k,nr_inodes=1893857,mode=755,inode64)
run on /run type tmpfs (rw,nosuid,nodev,relatime,mode=755,inode64)
efivarfs on /sys/firmware/efi/efivars type efivarfs (rw,nosuid,nodev,noexec,relatime)
/dev/nvme0n1p2 on / type ext4 (rw,noatime)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev,inode64)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
cgroup2 on /sys/fs/cgroup type cgroup2 (rw,nosuid,nodev,noexec,relatime,nsdelegate,memory_recursiveprot)

$ df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
dev            devtmpfs  7,3G     0  7,3G   0% /dev
run            tmpfs     7,3G  1,8M  7,3G   1% /run
/dev/nvme0n1p2 ext4      460G  128G  310G  30% /
tmpfs          tmpfs     7,3G   22M  7,3G   1% /dev/shm
tmpfs          tmpfs     7,3G  4,4M  7,3G   1% /tmp
/dev/nvme0n1p1 vfat      300M  312K  300M   1% /boot/efi
tmpfs          tmpfs     1,5G  100K  1,5G   1% /run/user/1000
```



## 1.5 NFS und Network Filesystems

Oft ist es nötig, Dateien von einem physikalischen System zu einem anderen zu transferieren. Diese können lokal im gleichen Netz oder irgendwo verstreut im Internet sein. Ein `network (distributed) filesystem` kann alle seine Daten auf einer Maschine oder auf mehreren Maschinen haben. 

Man kann sich ein Netzwerkfilesystem einfach nur als eine Gruppierung von low level filesystemen von verschiedenen Typen vorstellen.

![The client-server architecture of NFS](https://courses.edx.org/assets/courseware/v1/b29a567ddecc954ea6440a9e4dedd067/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/nfs.png)



> Viele Systemadministratoren mounten remote users' home directories auf einem Server um ihnen Zugriff zu den selben Files und Konfigurationen über mehrere Systemen verteilt zu geben. Dieses Vorgehen erlaubt dem Benutzer, dass er auf Daten eines anderen Rechners zugreift, während er sich immer noch auf der eigenen Maschine befindet.

Der Name für dieses Filesystem ist **NFS** (**N**etwork **F**ilesystem). Es hat eine längere Geschichte und wurde von Sun Microsystems entwickelt. Eine andere, häufig verwendete Implementation ist **CIFS** (auch **SAMBA** genannt) welches ursprünglich von Microsoft stammt.



### 1.5.1 NFS auf einem Server

Auf einem Server verwendet NFS **daemons** (build-in networking und service Prozesse in Linux). Diese und noch andere System servers werden mit dem folgendem Befehl gestartet:

```shell
$ sudo systemctl start nfs
# Bei Arch / Manjaro ist es der folgende Befehl:
$ sudo systemctl start nfs-server
```

Das Text-File `/etc/exports` beinaltet alle directories und Berechtigungen die der host mit anderen Systemen über NFS teilen möchte. Ein einfacher Eintrag könnte der folgende sein:

```shell
/projekte *.example.com(rw)
```

Dieser Eintrag erlaubt es Hosts in der `example.com` domain, das `/projekte` directory zu mounten und in diesem zu lesen und zu schreiben `(rw)`.

Nachdem man das `/etc/exports` File bearbeitet hat, kann man mit dem Befehl `exportfs -av` seinem System sagen, dass sich etwas geändert hat. Man kann den Service aber auch einfach restarten mit `sudo systemctl restart nfs-server`. Man sollte, wenn man den Service immer gestartet haben möchte, den Service mit `sudo systemctl enable nfs-server` enablen. Somit wird der Service direkt beim Systemstart gestartet.



Wenn man jetzt das nfs directory einer anderen Maschine mounten möchte, kann man entweder das `/etc/fstab` file bearbeiten, oder es direkt mit dem `mount` Befehl an einem Mounting point einhängen:

```shell
$ nano /etc/fstab
servername:/projekte /mnt/nfs/projekte nofail nfs default 0 0

$ sudo mount servername:/projekte /mnt/nfs/projekte
```

Nun haben wir die geteilten Inhalte von `/projekte` an unserem Mounting point `/mnt/nfs/projekte` gemounted. Man muss hierbei beachten, dass wenn man das `/etc/fstab` file nicht bearbeitet, die Partition bei einem Systemneustart wieder unmounted wird.



# 2. Filesystem Architektur

## 2.1 Übersicht der home-directories

Jeder user hat normalerweise ein eigenes directorie unter `/home/[Benutzername]`. Der root Benutzer hat ebenso sein eigenes Verzeichnis, nur unterr `/root`.



Auf einem System, welches von mehreren Benutzern verwendet wird, besteht das `/home` Verzeichnis meistens nur aus seperaten, gemounteten Filesystemen oder sogar exported (shared) auf einem remote Netzwerk mittels NFS.

Man kann auch Ordner im Home directory erstellen, welche der funktionalen Verwendung der Organisation gerecht werden.

Eine Schule könnte z.B. folgende Struktur verwenden:

`/home/faculty/`

`/home/staff/`

`/home/students/`



![home directories](https://courses.edx.org/assets/courseware/v1/9817790ba352d4047027eb1d61516db5/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/Home_directories.png)



## 2.2 Das /bin und /sbin Directory

Der `/bin` Ordner enthält ausführbare binarie sowie notwendige Befehle welche für das Hochfahren des Systems sowie für die Verwendung im single-user-mode verwendet werden. Ebenso notwendige Befehle für alle Systembenutzer, wie z.B. `cat`, `cp`, `mv`, `ps` und `rm`.



Der `/sbin` Ordner ist für notwendige binaries gedacht, welche für die Systemadministration notwendig sind. Z.b. wären dies `fsck` und `ip`.



Befehle, welche (theoretisch) nicht unbedingt notwendig für das Hochfahren sowie für die Verwendung im single-user-mode sind, befinden sich in `/usr/bin` sowie `/usr/sbin`.

Heutzutage sind in modernen Distributionen sind die `/bin` directories miteinander mittels Systemlinks verbunden (siehe Screenshot).

![](/home/manu/.config/marktext/images/2023-01-01-15-43-43-image.png)



## 2.3 Das /proc Filesystem

Manche Filesysteme, wie z.B. das mounted Filesystem an `/proc`, werden **pseudo-Filesysteme** genannt. Dies ist der Fall, da sie keine permanente Präsenz auf der Festplatte haben.

Das `/proc` Filesystem beinhaltet **virtual files** (files, welche nur im Arbeitsspeicher existieren) welche Kernel daten anzeigen. Diese Kernel daten ändern sich (logischerweise) in Echtzeit. `/proc` beinhaltet Dateien und Ordner, welche die Kernelstruktur nachahmen und Konfigurationsinformationen beinhalten.

Es beinhaltet keine echten Dateien, jedoch runtime system informationen, z.B. system memory devices mounted, hardware configuration, etc.

Die folgenden Ordner sind sehr wichtige Einträge in `/proc`:

- `/proc/cpuinfo`

- `/proc/interrupts`

- `/proc/meminfo`

- `/proc/mounts`

- `/proc/partitions`

- `/proc/versions`

Das `/proc` Filesystem inkludiert auch sub-Ordner, wie z.B. momentan laufende Prozesse sowie sonstige Systeminformationen:

- `/proc/<ProzessID>/`

- `/proc/sys`



Wie bereits erwähnt, existieren in `/proc` Ordner für jeden Prozess im gesamten System. Diese Ordner beinhalten vitale Informationen zu den jeweiligen Prozessen.

Der `/proc/sys` Ordner beinhaltet viele Infromationen über das gesamte System, genauer gesagt über die Hardware Konfiguration. Die Informationen in `/proc` sind sehr nützlich, in Echtzeit und werden niemals permanent auf der Festplatte gespeichert.

![](/home/manu/.config/marktext/images/2023-01-01-15-59-28-image.png)



## 2.4 Der /dev Ordner

Der `/dev` Ordner beinhaltet sogenannte **device nodes**, ein Typ von pseudo-files welcher von dne meisten hardware und software Geräten verwendet wird. Die Außnahme sind hier Netzwerkgeräte.

Das directory:

- Ist leer auf der Partition wenn es nicht gemounted ist

- Beinhaltet Einträge, welce von dem **udev** System generiert worden sind. Das **udev** System erstellt und verwaltet device nodes in Linux. Die Einträge werden dynamisch erstellt, sobald die Geräte gefunden worden sind. 

- Das `/dev` directory beinhaltet Sachen wie z.B.:
  
  - `/dev/sda1` (erste Partition auf der ersten Festplatte)
  
  - `/dev/lp1` (der zweite Drucker)
  
  - `/dev/random` (eine Quelle für Zufallszahlen)

![](/home/manu/.config/marktext/images/2023-01-01-15-59-03-image.png)



## 2.5 Der /var Ordner

Der `/var` Ordner beinhaltet Dateien, bei denen man ein Wachstum in der Größe bei zunehmender Systemlaufzeit erwartet (var = variable).

Also kann das `/var` directory z.B. folgende Einträge besitzen:

- System log Dateien: `/var/log`

- Packages und database Dateien: `/var/lib`

- Druckwarteschlangen: `/var/spool`

- Temporäre Dateien: `/var/temp`



![The /var Directory](https://courses.edx.org/assets/courseware/v1/948dafcdc47f674bd2c0b5c1560ebb7c/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/varfolders.png)

Das `/var` directory kann auch auf einem eigenen Filesystem platziert werden, damit ein drastisches Wachstum der Dateien nicht das System fatal beeinflussen.

Ordner für Netzwerk Services (z.B. `/var/ftp`-> FTP Service) und für Web Services (z.B. `/var/www` -> HTTP web service) sind ebenso in `/var` anzutreffen.

![](/home/manu/.config/marktext/images/2023-01-01-16-04-52-image.png)



## 2.5 Der /etc Ordner

Im `/etc` Ordner befinden sich Dateien für die Systemkonfiguration. 

So ist z.B. im File `/etc/resolv.conf` festgelegt, wo das System hingehen soll, um sich eine IP Adresse zu holen (DNS). Auch dateien wie `passwd`, `shadow` und `group` für die Benutzerverwaltung sind in `/etc` anzutreffen.



**Note:** Die Konfigurationsdateien in `/etc/` gelten Systemweit, können also nur vom Superuser geändert oder erstellt werden.

![](/home/manu/.config/marktext/images/2023-01-01-16-08-44-image.png)



## 2.6 Das /boot Verzeichnis

Der `/boot` Ordner beinhaltet alle Files, welche für den System-boot benötigt werden. Für jeden auf dem System installierten Kernel finden sich folgende 4 Dateien wieder:

1. `vmlinuz`
   
   Der komprimierte Linxu Kernel, für den boot notwendig.

2. `initramfs`
   
   Das initial ram filesystem, für den boot notwendig, manchmal `initrd` genannt.

3. `config` 
   
   Das Kernel Konfigurationsfile, wird nur für debuggung und bookkeeping verwendet

4. `System.map` 
   
   Die Kernel Symboltabelle, wird nur für das debugging verwendet



Jedes dieser Dateien hat die Kernelversion hinten an dem Namen drann.

Was ebenso noch im `/boot` Verzeichnis zu finden ist, ist der Grand Unified Bootloader (GRUB). Dieser repräsentiert sich mit Dateien wie z.B. `/boot/grub/grub.cfg` .

![](/home/manu/.config/marktext/images/2023-01-01-16-14-01-image.png)



## 2.7 Das /lib und /lib64 Verzeichnis

Der `/lib` Ordner beinhaltet libraries (Code, welcher von Applikationen geteilt und benötigt wird) für alle essentiellen Programme in `/bin` und `/sbin`. Die Dateinamen dieser libraries starten entweder mit `ld` oder `lib` . Z.b.: `libmbim-glib.so.4.7.0`.

Die meisten libraries hier sind auch bekannt als dynamically loaded libraries (shared libraries). 

In manchen Linux Distributionen gibt es einen `/lib64` Ordner, in welchem sich die libraries im 64-bit-Format befinden, während die libraries in `/lib` im 32-bit-Format sind.

![](/home/manu/.config/marktext/images/2023-01-01-16-18-24-image.png)

Kernel module (kernel code, meistens device drivers welche ohne System neustart geladen und ungeladen werden können) befinden sich unter `/lib/modules/<kernel-version-nummer>`.



## 2.8 Auswerfbare Medien: /media, /run und /mnt

Man verwendet selbst heutzutage immer noch gerne auswerfbare Medien wie z.B. einen USB-Stick. Diese Filesysteme müssen an den richtigen locations gemounte werden. Die meisten Linux Systeme mounten solche Medien automatisch wenn das System den Input bemerkt.

In der Vergangenheit wurden die Geräte unter `/media` gemounted, wobei moderne Linux Distributionen die Geräte nun unter `/run` mounten. 

Zum Beispiel: Wenn ich nun einen USB-Stick namens `usb120` in meinen Laptop stecke, wird er unter der folgenden Location gemounted:

- `/run/media/manu/usb120` 

![](/home/manu/.config/marktext/images/2023-01-01-16-23-30-image.png)



Der `/mnt` Ordner wird schon sehr lange für das mounten von Filesystemen verwendet. Meistens sind dies Network Filesysteme, welche normalerweise nicht gemounted sind. Oder temporäre Partitionen oder sogenannte **loopback** Filesysteme, welche Files sind, die sich als Partitionen ausgeben.



## 2.9 Sonstige Ordner unter /:

- `/opt` -> Optionale software packages für Applikationen

- `/sys` -> Virtuelles pseude-filesystem welches Informationen über das System und die Hardware beinhaltet. Kann für die Veränderung von System Parametern und für debugging verwendet werden

- `/srv` -> Site-specific data welche von dem System kommen. Selten verwendet.

- `/tmp` -> Temporäre Files; in manchen Distributionen gelöscht nach einem reboot und/oder ein ramdisk in memory

- `/usr` -> Multi-user Applikationen, Hilfsprogramme und daten



## 2.10 Die /usr Baumstruktur

Der `/user` Ordner beinaltet theoretische nicht-notwendige Programme und Scripts (sie werden nicht für das Hochfahren des Systems benötigt). 

Der Ordner hat die folgenden Unterordner:

- `/usr/include` -> Header files welche für das Kompillieren von Applikationen verwendet werden

- `/usr/lib` -> Libraries für Programme in `/usr/bin` und `/usr/sbin`

- `/usr/lib64` -> 64-bit Libraries für 64-bit Programme

- `/usr/sbin` -> Nicht-elementare System binaries, z.B. system daemons

- `/usr/share` -> Von Applikationen geteilte Daten, abhängig von der Architektur

- `/usr/src` -> Source code, normalerweise für den Linux kernel

- `/usr/local` -> Daten und Programme spezifisch für die lokale Maschine

- `/usr/bin` -> Das primäre Verzeichnis für das Ausführen von Befehlen auf dem System



# 3. Comparing Files and File Types



## 3.1 Comparing Files with diff

Der Befehl `diff` wird verwendet, um files und Verzeichnisse miteinander zu vergleichen (-> `man diff` für mehr). 

`diff` besitzt die folgenden Optionen:

- `-c` -> Gibt eine Liste von den Unterschieden zurück. Inkludiert lines of context davor und nachdem Unterschied.

- `-r` -> Vergleicht Verzeichnisse rekursiv

- `-i` -> Groß- und Kleinschreibung ignorieren

- `w` -> Die Unterschiede in spaces und tabs (white spaces) ignorieren

- `-q` -> Be quiet: Einfach nur ein report, ob files Unterschiede besitzen, ohne diese anzuzeigen.

Anwendung:

Syntax: `diff [option] <filename1> <filename2>`.

```shell
$ diff -ciw file1.txt file2.txt
  root           1  0.0  0.0 170308 14748 ?        Ss   Dez29   0:04 /sbin/init
  root           2  0.0  0.0      0     0 ?        S    Dez29   0:00 [kthreadd]
  root           3  0.0  0.0      0     0 ?        I<   Dez29   0:00 [rcu_gp]
+ root           4  0.0  0.0      0     0 ?        I<   Dez29   0:00 [rcu_par_gp]
  root           5  0.0  0.0      0     0 ?        I<   Dez29   0:00 [slub_flushwq]
  root           6  0.0  0.0      0     0 ?        I<   Dez29   0:00 [netns]
  root           8  0.0  0.0      0     0 ?        I<   Dez29   0:00 [kworker/0:0H-events_highpri]
```



## 3.2 diff3 und patch verwenden

Man kann auch 3 Files gleichzeitig vergleichen. Mit `diff3` ist es möglich.

`diff3` funktioniert genauso wie `diff`, nur dass das zweite File als Basis für die anderen zwei Files agiert.

Syntax: `diff3 [option] file1.txt basis-file.txt file2.txt`

z.B. 

```shell
$ diff3 MY-FILE COMMON-FILE YOUR-FILE
```



Die meisten Modifikationen in source code werden mit einem `patch` übertragen. Ein `patch` File beinhaltet die deltas (die Unterschiede) welche dazu benötigt werde, die ältere Version einer Datei zu einer neuern Version aktualisieren. 

Patch files werden folgendermaßen produziert:

```shell
$ diff -Nur originalfile newfile > patchfile
```

Nur den patch zu veröffentlichen ist effizienter und übersichtlicher, da sich so nur wenige Zeilen ändern muss, anstatt das gesamte Konstrukt.



Um einen Patch anzuwenden, gibt es zwei Optionen:

```shell
$ patch -p1 < patchfile
$ patch originalfile patchfile
```



## 3.3 Das Hilfsprogramm file

In Linux, die File extension ist meistens unwichtig. Jemand kann nicht davon ausgehen, dass z.B. `file1.txt` einfach nur ein Textfile und kein ausführbares Programm ist. 

In Linux ist der Filename eher für den Benutzer und weniger für das System gedacht. Die meisten Applikationen nehmen direkt den Inhalt eines Files um den Filetyp festzustellen, anstatt sich auf die Extension zu verlassen. 

Dies ist in Windows z.B. nicht so, Windows klassifiziert alle Files anhand ihrer extension. Z.b. ist ein `.exe` file unter windows strikt ein ausführbares binary file.

Das `file` Programm nimmt den Inhalt eines Files und findet anhand diesen Heraus, welcher Typ es ist.



Anwendung:

Wir haben nun ein Java File und ein Python file. Jedoch ist nur der Inhalt der jeweiligen Programmiersprache zuzuordnen. Die File-extension wurde nicht gesetzt.

```shell
# Damit kann man alle Files in einem Directory inspizieren
$ file *
File1: Java source, ASCII text
File2: Python script, ASCII text executable
```



# 4. Backing Up and Compressing Data



## 4.2 Daten Backupen

Es gibt vielerlei Wege wie man Daten oder sogar das gesamte System einem Backup unterziehen kann. Man kann z.B. auch einfach alle Daten mit `cp` kopieren und das robustere `rsync` verwenden.

Beides kann dazu verwendet werden um gesamte Verzeichnisbäume zu synchronisieren. Jedoch ist `rsync` effizienter weil es überprüft, ob das kopierte File bereits existiert. `rsync` beugt also Duplikate vor und kopiert nur die Bereiche, welche geändert wurden.

`cp` kann nur dazu verwendet werden um files lokal auf der eigenen Maschine zu kopieren (außer man hat ein Netzwerklaufwerk mit NFS oder ähnliches gemounted), während `rsync` Daten von einem auf dem anderen Rechner übertragen kann.



### 4.2.1 rsync anwenden

`rsync` ist ein mächtiges Tool. Zum Beispiel kann man mit dem folgendem Befehl ein gesamtes Directory backupen:

```shell
$ rsync -r project-X archive-machine:archive/project-x
```

**Note:** `rsync` kann sehr zerstörerisch sein! Die Falsche Verwendung des Programms kann sehr viel Schaden anrichten. Man sollte genau auf alle Pfade und Optionen achten. Der erste Befehl sollte auch die Option `--dry-run` beinhalten.



Um `rsync` normal in der Kommandozeile zu verwenden, kann man den Syntax `rsync sourcefile destinationfile`  verwenden. Der Inhalt von `sourcefile` wird zum `destinationfile` kopiert.

Eine gute Kombination von optionen ist das folgende:

```shell
$ rsync --progress -avrxH --delete sourcedir destdir
```



## 4.3 Files komprimieren

Das komprimieren von Dateien spart Zeit und Platz bei z.B. der Übertragung über das Netzwerk.

In Linux gibt es folgende Tools, mit denen es möglich ist, files zu komprimieren:

- `gzip` -> Das am häufigsten verwendete in Linux

- `bzip2`-> Komprimiert die Dateien um einiges kleiner als mit **gzip**

- `xz` -> Das effizienteste tool in Linux, wenn man den Platz beachtet

- `zip` -> Wird oft dazu benötigt, um Archives von anderen Betriebssystemen zu untersuchen oder zu de-komprimieren.



Ebenso wird das `tar` Programm häufig verwendet, um Files in einem Archiv zu gruppieren und dann dieses ganze Archiv zu komprimieren.



### 4.3.1 gzip verwenden

- `gzip *` -> Komprimiert alle Dateien in einem Verzeichnis. Die Dateien werden komprimiert und mit einer **.gz** extension versehen

- `gzip -r projectX` -> Komprimiert alle Dateien und Verzeichnisse in dem projectX-Ordner 

- `gunzip foo` -> De-Komprimiert **foo**. Man kann auch einfach `gzip -d` schreiben.



### 4.3.2 bzip2 verwenden

- `bzip2 *` -> Komprimiert alle Dateien in einem Verzeichnis. Die Dateien werden komprimiert und mit einer **.bz2** extension versehen.

- `bunzip2 *.bz2` -> De-Komprimiert alle Files mit der **.bz2** extension. Man kann auch `bzip2 -d` schreiben.



### 4.3.3 xz verwenden

`xz` ist das effizeinteste Programm in Linux. Es wird unter anderem auch dazu verwendet, Archive im Linux Kernel zu verstauen. Es ist zwar langsamer aber dafür effizienter.

- `xz *` -> Komprimiert alle Files in einem Verzeichnis. Alle Files werden mit einer **.xz** extension versehen.

- `xz foo` -> Komprimiert foo in **foo.xz** mit dem feault Komprimierungslevel (-6) und entfernt foo.

- `xz -dk bar.xz` -> De-Komprimiert bar.xz in bar. bar.xz wird nicht entfernt.

- `xz -dcf a.txt b.txt.xz > abcd.txt` -> De-Komprimiert ein mix von komprimierten und nicht komprimierten in einen standard output.

- `xd -d *.xz` -> De-Komprimiert alle komprimierten files, welche xz benutzen



### 4.3.4 zip verwenden

`zip` wird in Linux nicht oft verwendet. Es wird jedoch dann benötigt, wenn man z.B. komprimierte Dateien von einem anderen Betriebssystem bekommt.

Wenn ein Linux user z.B. ein `.zip` Archiv von einem Windows user bekommt, muss ideser `zip` verwenden. Es ist ein legacy Programm.

- `zip backup *` -> Komprimiert alle Dateien in dem momentanen Verzeichnis und speichert sie in **backup.zip**.

- `zip -r backup.zip ~` -> Archiviert das Home directory (~) und packt alles in backup.zip.

- `unzip backup.zip` -> De-Komprimiert backup.zip und packt es in das momentane Verzeichnis.



## 5. Archivierung und Komprimierung mit tar

Früher stand `tar` für "**t**ape **a**rchive" und wurde dazu verwendet, Daten auf einem magnetischen Tape zu speichern. Es erlaubt einem, dateien von einem archivierten File zu erstellen oder dieses File zu extrahieren. Die komprimierten Dateien werden oft **tarball** genannt. 

- `tar xvf mydir.tar` -> Extrahiert alle Dateien in mydir.tar in das mydir Verzeichnis.

- `tar zcvf mydir.tar.gz mydir` -> Erstelle ein Archiv und komprimiere es mit **gzip**

- `tar jcvf mydir.tar.bz2 mydir` -> Erstelle ein Archiv und komprimiere es mit **bz2**

- `tar Jcvf mydir.tar.xz mydir` -> Erstelle ein Archiv und komprimiere es mit **xz**

- `tar xvf mydir.tar.gz` -> Extrahiere alle Dateien von mydir.tar.gz in mydir.



## 6. Disk-To-Disk Copying (dd)

Das `dd` Programm ist sehr nützlich um raw disk space zu kopieren. Zum Beispiel: um den Master Boot Record (MBR) (der erstn 512-bytes Sektor auf der Festplatte), könnte man dies mit dem folgendem Befehl schreiben:

```shell
$ dd if=/dev/sda of=sda.mbr bs=512 count=1
```



**WARNUNG!**

Der Folgende Befehl:

```shell
$ dd if=/dev/sda of=/dev/sdb
```

kopiert eine Festplatte auf eine andere. Jedoch wird der Inhalt der zweiten Festplatte KOMPLETT ÜBERSCHRIEBEN und GELÖSCHT!

**Man sollte mit dem dd Kommand nicht experimentieren. Dieser Befehl kann eine ganze Festplatte löschen!**



![Disk-to-Disk Copying (dd)](https://courses.edx.org/assets/courseware/v1/6ed7efb57f544c1bbac9baf55f75e535/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/LFS01_ch08_screen_41.jpg)


