## Tastenkombinationen im Terminal

- `CTRL + L`: Leert den Terminal Bildschirm.
  
- `CTRL + D`: Loggt einem aus dem Interface aus.
  
- `CTRL + Z`: Snoozed einen Befehl.
  
- `CTRL + C`: Bricht einen Befehl ab.
  
- `CTRL + H`: Ersetzt die `Backspace` Taste, damit kann man Elemente löschen.
  
- `CTRL + A`: Damit kann man zum Anfang der Eingabe gehen.
  
- `CTRL + W`: Löscht das letzte Wort vor dem cursor.
  
- `CTRL + U`: Löscht die gesamte Zeile in der Shell.
  
- `CTRL + E`: Bringt den cursor an das Ende der Zeile.

---

## Berechtigungen in Linux
7.2 Dateiberechtigungsmodi und chmod

Grundsätzlich setzt Linux für jedes erstellte File/directory permissions, welche für folgende gelten:

- Einen Besitzer
  
- Die Gruppenmitglieder
  
- Jeder andere
  

Es gibt drei Permission-typen unter Linux:

- Read (`r`) - `Oktalzahl = 4`
  
- Write (`w`) - `Oktalzahl = 2`
  
- Execute (`x`) - `Oktalzahl = 1`
  
- (minus-zeichen) "`-`" - `Oktalzahl = 0`
  

Dieses Konzept erlaubt es genau anzugeben, wer die Berechtigungen dazu hat, ein File zu lesen, zu beschreiben und auszuführen.

Wenn man jetzt z.B. einen `ls -l dirname` Befehl ausführt, kann man die Berechtigungen des Ordners sehen:

```shell
$ ls -l dirname
drwxr-xr-x 12 linuxize users 4.0K Apr  8 20:51 dirname
|[-][-][-]    [------] [---]
| |  |  |        |       |       
| |  |  |        |       +-----------> Group
| |  |  |        +-------------------> Owner
| |  |  +----------------------------> Others Permissions
| |  +-------------------------------> Group Permissions
| +----------------------------------> Owner Permissions
+------------------------------------> File Type
```

Der erste Buchstabe = Typ.

Die nächsten 9 Buchstaben repräsentieren die Berechtigungen für die jeweiligen Gruppen. Sie sind in 3er-Gruppen aufgeteilt.

In diesem Beispiel haben die Gruppen folgende Berechtigungen:

- Besitzer (ersten drei Zeichen): read (r), write (w) & execute (x), `4+2+1 = 7`
  
- Gruppe (nächsten drei Zeichen): read (r), execute (x), `4 + 0 + 1 = 5`
  
- Jeder andere (nächsten drei Zeichen): read (r), execute (x), `4 + 0 +1 = 5`
  

Die Oktalzahlen der Berechtigungen ergeben die folgende Ziffer: `755`. So werden die Berechtigungen des Verzeichnisses repräsentiert:

- Besitzer: 4+2+1 = 7
  
- Gruppe: 4+0+1 = 5
  
- Jeder ander: 4+0+1 = 5
  

Default-mäßig gibt Linux neu erstellten Dateien die Berechtigung `666` (read, write für alle) und neu erstellten Ordnern die Berechtigung `777` (read, write, execute für alle). Linux erlaubt es nicht, files mit einer execute permission zu erstellen.

---

**cat**: Man kann files damit ausgeben (also sich anzeigen lassen, z.B. "cat notes.txt", lange files sollten gemieden werden

**head**: Damit kann man sich die ersten paar Zeilen einer Datei ausgeben lassen

**less**: Damit lassen sich größere files anzeigen

**touch**: Files lassen sich damit modifizieren und erstellen. Es setzt den timestamp für das file zurück (current time).

**tail**: Damit kann man sich die letzten paar Zeilen einer Datei ausgeben lassen

**man**: mit diesem Befehl kann man sich die Dokumentation eines anderen Kommands ausgeben lassen (z.B. "man cat")

**cd**: change directory, man kann damit das Verzeichnis ändern

**echo**: man kann mit echo sich eine Zeile im Terminal ausgeben. Ebenso kann man damit viele andere Sache wie z.B.
Zeilen in Files einfügen, usw.

**ls**: List directory contents, gibt alle Inhalte im jeweiligen Verzeichnis aus

**rmdir**: Damit kann man ein leeres directory löschen

**exit**: ausloggen/schließen der terminal-session

**login**: man kann damit eine session im System beginnen (sich quasi einloggen)

**mkdir**: Erstellt ein directory in dem momentanen Verzeichnis    

**which**: Gibt aus, wo ein programm im System seine Files abliegt.

**whereis**: Kann man verwenden, falls "which" nicht funktioniert. Es sucht nach packages im ganzen System

**pwd**: Gibt das momentante Directory aus  

**echo $PWD**: Gibt das aktuelle working directory aus (greift auf die env variable zu)

**printenv | grep PWD**: Holt sich alle env variablen und gibt das PWD aus.

**cd -**: Kehrt zum home-verzeichnis zurück 

**!!**: Führt letzten ausgeführten Befehl aus (also der letzte in der history)
**tree**: Gibt den filesystem-tree aus. Also die Filestruktur des systems (tree -d = nur directories)

**ln file1 file2**: Erstelle einen harten link, also symbolische Links genannt. Das eine file führt also zum anderen

**ln -s**: Erstellt einen soft-link. Zum Beispiel kann man dadurch lange Verzeichnisnamen umgehen, indem 
man einfach einen Link dazu erstellt* Diese links können auch auf elemente linken, die nicht auf dem 
main fielsystem, auf der main partition oder auf dem main laufwerk sind.

**mv**: Rename / move file

**rm**: Remove file 

**rm -f**: Forcefully remove file

**rm -rf**: forcefully remove file, dirs and more. REKURSIV

**df (-h)**: Gibt einen report zur disk usage des Filesystems aus. Mit -h werden alle Werte in ihre jeweiligen Einheiten
angegeben (1000er), z.B. 1.1G  

**CTRL-D**: Diese Tastenkombination stopppt ein momentan laufendes Programm.

**CTRL+C**: Diese Tastenkombination schließt ein momentan laufendes Programm.

**jobs**: Zeigt die momentan gesnoozden befehle an (gestoppten Befehle)

**fg [numer]**: Resumed einen befehl, der vorher mit CTRL+Z gestoppt wurde.

**history**: Zeigt die zsh_history / bash_history an.

**cp -r**: Kopiert ein Verzeichnis mit der Option rekursiv

**cp -u**: Steht für update und kopiert das Verzeichnis nur dann, wenn der Inhalt des source directories neuer als der des destination drectories ist.

**grep**: Holt sich eine ressource und gibt sie, falls sie dem übergebenen Muster entspricht, aus.

**pacman**: Package manager Arch-based distros, z.B.: package installieren = "sudo pacman -S [pkg-name]"

**yay**: AUR-Helper, Packet-Manager für das Arch AUR. Funktioniert grundlegend mit den Befehlen von *pacman*.

**lspci**: Zeigt alle momentanen Devices an, die am PCI des hosts angeschlossen sind. Inklusive Treiber-Informationen.

**ssh-keygen -t ed25519 -C "[EMAIL]"**: Erzeugt einen SSH-Key mit dem ED25519 Algorithmus und weist diesem SSH-Key die EMAIL zu (EMAIL ist im dem Fall ein Kommentar, dient aber trotzdem zur Zuweisung).

**eval [argument...]**: Dieses Hilfsprogramm "baut" Befehle (übergebenen Argumente). Der konstruierte Befehl wird von der Shell gelesen und konstruiert.

**mount**: Mountet ein File System

**mount -l**: Alle eingehängten logischen sowie physikalischen Filesysteme werden aufgelistet

**stat**: Zeigt statistiken über ein file oder filesystem an

**file**: Zeigt die Fileextension/Fileart der jeweiligen Datei an. Z.b.: **file * **

**unalias [alias]**: Löscht den angegebenen Alias

**kill -9 [PID] / kill -SIGKILL [PID]**: Tötet den Prozess mit der jeweiligen PID sofort.

**[BEFEHL]&**: Der &-Operator steckt einen Befehl in den Hintergrund.

**jobs -l**: Zeigt alle momentanen Hintergrundjobs und deren PID an.

**ps -ef**: Zeigt Informationen über alle momentanen Prozesse im System an

**ps -eLf**: Zeigt Informationen über alle momentanen Prozesse inklusive allen Threads im System an.

**ps -ef | grep [Prozessname]**: Damit kann man sich schnell Informationen zu einem momentan laufenden Prozess holen.

**ps aux**: Zeigt alle momentan laufenden Prozesse im System an

**ps aux | grep [Prozessname]**: Damit kann man sich schnell Informationen zu einem momentan laufenden Prozess holen.

**pstree**: Zeigt den aktuellen Prozessbaum an. Damit die Prozesshierachie und Informationen zu den Threads.

**top**: Mutter von **htop**, wird alle 2 Sekunden aktualisiert.

**at [Zeitraum]**: Damit kann man einen Befehl in der Zukunft planen. Z.b. **at now + 2 days**. Daraufhin folgt die Eingabe der auszuführenden Befehle.

**crontab -e**: Öffnet den editor für den lokalen cron table. Damit lassen sich cronjobs planen.

**[MIN] [HOUR] [Day of Month] [MON] [Day of Week] [Command]**: Der Syntax für einen cronjob im cron table.

**sleep NUMBER[Suffix]**: Suspendiert einen Befehl und verzögert die weitere Ausführung um NUMBER.

**inxi**: Command Line system information script, zeigt Informationen zu dem System an.

---

**inxi -B**: Zeigt batterie-Daten an (charge, conditiu, extra informations)

**inxi -A**: Zeigt Informationen zum Audio-System an

**inxi -C**: Zeigt Informationen zu der CPU an. (also Kerne, Name, AMCP, AMP, MT, ...)

**inxi -N**: Zeigt Informationen zu den existenten Netzwerk-Geräten an (inklusive Treiber-Infos, unter Linux ganz wichtig)

---

**mount [device node, UUID, ...] [mounting point]**: Hängt/Mounted ein Filesystem an einen gewissen Mounting Point. Z.b.: **sudo mount /dev/sda5 /home**

Es ist auch möglich, folgendermaßen NFS einzuhängen: **sudo mount servername:/ordner /mounting/point**

**mount**: Zeigt alle kürzlich eingehängten Filesysteme an

**umount**: Hängt ein Filesystem wieder aus (unmounted es). Z.b.: **sudo umount /home**

**diff**: Zeigt den Unterschied zwischen zwei files an. Z.b.: **diff -c file1 file2**

**diff3**: Zeigt den Unterschied zwischen drei files an. Das mittlere wird dabei als "common file" (basis) verwendet. Z.b.: **diff3 MY-FILE COMMON-FILE YOUR-FILE**

**diff -Nur originalfile newfile > patchfile**: Erstellt ein Patchfile, welches den Unterschied zwischen originalfile und newfile enthält

**patch originalfile patchfile**: Man überträgt einen Patch (von einem z.B. neueren File) in ein anderes File ein. Die Unterschiede der Files werden quasi gepatched.

**rsync**: Programm mit welchem man sein System Backupen kann. Man kann damit Daten auf einen anderen Rechner übertragen (im Gegensatz zu **cp**). Z.b.: **rsync -r projektX archive-machine:archive/projektX**

**gzip**: Komprimieren/De-Komprimieren von Dateien.

**xz**: Kompieren/De-Komprimieren von Dateien. Effizienter als gzip/bzip2/zip. Wird im Linux Kernel verwendet.

**bzip2**: Kompieren/De-Komprimieren von Dateien. Gleiche Verwendung wie bei **gzip**.

**zip**: Komprimieren/De-Komprimieren von Dateien. Wird nicht oft verwendet, ist ein legacy Programm, wird für z.B. .zip Dateien von einem Windows System benötigt.

---

**tar xvf mydir.tar**: Extrahiert alle Dateien in mydir.tar in das mydir Verzeichnis.
  
**tar zcvf mydir.tar.gz mydir**: Erstelle ein Archiv und komprimiere es mit **gzip**
  
**tar jcvf mydir.tar.bz2 mydir**: Erstelle ein Archiv und komprimiere es mit **bz2**
  
**tar Jcvf mydir.tar.xz mydir**: Erstelle ein Archiv und komprimiere es mit **xz**
  
**tar xvf mydir.tar.gz**: Extrahiere alle Dateien von mydir.tar.gz in mydir.

---

**dd**: Disk-to-Disk-Copying. Man kann damit raw disk space kopieren. **Vorsicht! Kann Festplatten zerstören!** Z.b.: **dd if=/dev/sda of=/dev/sdb**. Überschreibt und löscht dabei alles, was sich auf der zweiten Festplatte befunden hat.

**useradd / userdel**: Fügt einen Benutzer zum System hinzu / Löscht einen Benutzer vom System (der Benutzer darf für die Löschung nirgendswo anderweitig verwendet werden). -> `/etc/passwd`

**groupdadd / groupdel**: Erstellt eine neue Gruppe im System / löscht eine neue Gruppe im System. -> `/etc/group`

**groups**: Zeigt alle Gruppen an, in denen sich der Benutzer momentan befindet.

**usermod**: Modifiziert einen Benutzer.

**usermod -aG [grpname] [usrname]**: Fügt einen Benutzer zu einer gewissen Gruppe hinzu.

**su**: Switch user. Damit kann man den Benutzer wechseln (auch, falls es wirklich nötig ist, zum root user).

**export VARIABLE=value**: Exportiert eine environment Variable für die momentane session.

---
**Umgebungsvariablen für die shell history:**
- `HISTFILE` -> Der Ort, an dem das history file gespeichert wird.
  
- `HISTFILESIZE` -> Die maximale Anzahl an Zeilen in dem history file (normalerweise 500).
  
- `HISTSIZE` -> Maximale Anzahl an Befehlen im history file.
  
- `HISTCONTROL` -> Wie werden Befehle gespeichert
  
- `HISTIGNORE` -> Welche Befehle können ignoriert werden

**z.B.:**:
```shell
$ set | grep HISTFILE

$ set | grep HISTSIZE

# ...
```

**chown**: Damit kann man den Besitzer einer Datei / eines Verzeichnisses ändern

**chgrp**: Damit kann man den Besitzer einer Gruppe ändern

**chmod** Damit kann man die Berechtigungen einer Datei / eines Verzeichnisses ändern




---

**eval "$(ssh-agent -s)"**: start ssh agent

**[command] 2> /dev/null [option]**: Leitet den stderr output an /dev/null weiter. Er wird also nicht angezeigt.
