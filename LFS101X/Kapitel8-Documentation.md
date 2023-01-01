# Linux Introduction - Kapitel 8

Verfasser: **Manuel Fellner**

Datum: **30.12.2022**

Thema: **Finding Linux Documentation**



# 1. Linux Documentation Sources

Grundsätzlich wird man, egal auf welcher Erfahrungsstufe man sich befindet, immer wieder auf Hilfe zurückgreifen müssen. 

**Wichtige Linux documentation sources:**

- Die man pages (**manual pages**)

- GNU Info

- Der help Kommand und --help option

- Andere documentation sources, zum Beispiel [Arch Wiki](https://wiki.archlinux.org/)



## 1.1 Man pages

Die Man pages sind die meistverwendeten documentation sources. Sie präsentieren eine tiefe Dokumentationen von Befehlen, sonstigen Hilfswerkzeugen und auch Themen die Konfigurationsfiles und API programming beinhalten.



Aufgerufen kann die man page im Web (unter https://man7.org/linux/man-pages/) oder einfach im Terminal als `man [Befehl/Thema]` werden. 

Wenn wir zum Beispiel genauere Informationen zu dem Hilfsprogramm `htop` bekommen möchten, tippen wir das folgende in die Konsole:

```shell
$ man htop
```

Und schon wird uns die Dokumentation von `htop` angezeigt. Mit "/[Inhalt]" kann diese Dokumentation nach beliebigen Inhalten durchsucht werden.

Mit dem Befehl `man -f [Befehl/Thema]` kann man den Befehl `whatis` ersetzen. Damit kann man sich schnell Informationen zu Befehlen holen bzw. es wird direkt der Verwendungszweck der Befehle angezeigt. Zum Beispiel:

```shell
$ man -f pwd
pwd (1)              - print name of current/working directory
pwd (1p)             - return working directory name
pwd (n)              - Return the absolute path of the current working directory

$ man -f htop
htop (1)             - interactive process viewer
```

Kurz und knackige Beschreibungen.

Was man ebenso noch gut mit `man` anfangen kann, ist der `man -k` Befehl. Dieser ersetzt `apropos` und mit Hilfe diesen ist es möglich, einfach nach Befehlen für den jeweiligen Verwendungszweck zu suchen. So kann man z.B. mit folgendem Befehl nach einem Befehl suchen, der files kopieren kann.

```shell
$ man -k copy | grep "copy files"
bsdcpio (1)          - copy files to and from archives
cp (1)               - copy files and directories
cp (1p)              - copy files
install (1)          - copy files and set attributes
```

Manchmal besitzt die manpage eines Themas mehrere Seiten, wenn man eine Nummer vor dem Namen stellt, kann man sich eine spezifische Seite der manpage anzeigen lassen. Oder auch kann man sich alle Seiten anzeigen lassen:

```shell
$ man 7 socket | grep head -6 

$ man -a socket
```

Der erste Befehl zeigt uns die 7. Seite des Themas `socket` an, während uns der zweite Befehl alle Seiten anzeigt.



## 1.2 GNU Info System

Das GNU Info System ist eine man alternative. Grundsätzlich kann man sich mit dem alleinigen `info` Befehl alle verfügbaren Themen anschauen. Hier kann man mit Hilfe der Pfeiltasten einfach durchscrollen. 

Wenn man jetzt aber Informationen zu einem spezifischen Thema haben möchte, kann man den Befehl auch so formulieren: `info [thema]`.

```shell
$ info htop
-> Vielerlei Informationen über das Programm. 1:1 gleich wie man.
```



Wenn man sich momentan in einem Thema befindet, nennt sich das `Node`. Man befindet sich also momentan in einer `node`. In dieser Node kann man mit folgenden Tasten navigieren:

`n -> Zur nächsten Node | Next node (nächste Seite) `

`p -> Zur vorherigen Node | Previous node (vorherige Seite)`

`u -> Eine node nach oben im Index | Move one node up in the index`



## 1.3 --help Option

Wenn man mal nicht mehr weiter weiß mit einem Befehl, kann man die `--help` version dieses Befehles verwenden. Die `--help` Option ist meistens schneller und spezifischer als `man` oder `info` pages.

Jedoch muss man im Hinterkopf behalten, dass man damit den Befehl ausführt. Wenn man also einen Befehl hat, den man nicht zu 100% vertraut, sollte man erstmals `man` oder `info` pages durchblättern.



## 1.4 Package documentation

Linux documentation ist auch verfügbar als Teil des package management systems. Meistens wird diese Dokumentation direkt vom upstream source code geholt, kann aber auch Informationen über normale packages enthalten.

Solche Informationen sind unter `/usr/share/doc` hinterlegt. Die packages sind als Ordner gegliedert, meistens auch mit der Versionsnummer im Namen.
