# Linux Introduction Course - Kapitel 12

Verfasser: **Manuel Fellner**

Datum: **05.01.2023**

Thematik: **Benutzerumgebung**

## 1. Den momentanen Benutzer identifizieren

Um sich den aktuellen Benutzer ausgeben zu lassen, kann man den Befehl `whoami` verwenen:

```shell
$ whoami
manu
```

Um genauere Informationen zu allen momentan eingeloggten Benutzern zu bekommen, kann man `who` verwenden:

```shell
$ who
manu     tty1         2022-12-29 12:07 (:0)
manu     pts/0        2022-12-29 12:07 (:0)
manu     pts/1        2023-01-05 13:22 (:0)
manu     pts/4        2022-12-29 20:53 (:0)
manu     pts/5        2022-12-30 18:30 (:0)
manu     pts/10       2023-01-04 14:33 (:0)

$ who -a
           system boot  2022-12-29 12:07
manu     + tty1         2022-12-29 12:07  old         1228 (:0)
manu     + pts/0        2022-12-29 12:07  old         1308 (:0)
manu     - pts/1        2023-01-05 13:22   .        287598 (:0)
           pts/2        2023-01-05 02:54              6976 id=ts/2  term=0 exit=0
           pts/3        2023-01-05 02:54              6976 id=ts/3  term=0 exit=0
manu     - pts/4        2022-12-29 20:53 00:02        9625 (:0)
manu     - pts/5        2022-12-30 18:30 05:43       22836 (:0)
           pts/6        2023-01-05 02:54              6976 id=ts/6  term=0 exit=0
           pts/7        2023-01-05 02:54              6976 id=ts/7  term=0 exit=0
           pts/8        2023-01-05 02:54              6976 id=ts/8  term=0 exit=0
           pts/9        2023-01-05 02:54              6976 id=ts/9  term=0 exit=0
manu     - pts/10       2023-01-04 14:33 00:16      118358 (:0)
LOGIN      tty2         2023-01-05 18:43            299517 id=tty2
LOGIN      tty3         2023-01-05 18:43            299527 id=tty3
LOGIN      tty4         2023-01-05 18:44            299702 id=tty4
```

## 2. Benutzer Start-Dateien

In Linux verwendet die Kommandozeile (normalerweise **bash**, bei vielen anderne **zsh**) eines oder mehrere Dateien, welche beim Start ausgeführen werden und die Benutzerumgebung konfigurieren sollen. Dateien im `/etc/` Ordner legen globale Einstellungen für alle Benutzer fest, während konfigurationsdateien im Home-Directory des Benutzers auch die globalen Einstellungen festlegen können.

Start-Dateien können alles machen, was man normalerweise in der Kommandozeile machen würde.

![User Startup Files](https://courses.edx.org/assets/courseware/v1/a61fd2656f3894d6f93397e755157b4b/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/LFS01_ch09_screen07.jpg)

### 2.1 Reihenfolge der Start-Dateien

Standardmäßig, wenn man sich in Linux einloggt, wird das `/etc/profile` File gelesen und ausgeführt. Danach kommen die folgenden files:

1. `~/.bash_profile`

2. `~/.bash_login`

3. `~/.profile`

Wenn man ein neues Terminal-Fenster öffnet, wird nur das `~/.zshrc` / `~/.bshrc` file ausgefürt. 

![Order of the Startup Files](https://courses.edx.org/assets/courseware/v1/618e42fc4814cce9ff91eceac55438b9/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/bashinit.png)

## 3. Aliase erstellen

Aliases sind ziemlich praktisch, man kann sie mit `alias` setzen, und mit `unalias` wieder löschen. Wenn man aliase haben möchte, welche immer aktiv sind, sollte man diese in das jeweilige `~/.bshrc` / `~/.zshrc` packen.

Also z.B.:

```shell
$ echo "alias nf='neofetch'" >> ~/.zshrc
# Hier kann man sehen, dass man in einem Alias auch mehrere Befehle 
# inkludieren kann.
$ echo "alias startProgramm='cd ~/Apps; ./Program'" >> ~/.zshrc
```

Diese aliase sind nun immer aktiv.

## 4. Grundlagen von Benutzern und Gruppen

### 4.1 Theorie - Die Basics

In Linux wird jedem Benutzer eine unique user ID (**uid**) zugeordnet, welche grundsätzlich einfach ein Integer ist. Normale Benutzer starten mit einer uid von 1000 oder größer.

Die UID eines Benutzers kann man z.B. im `/etc/passwd` file ablesen. Die uid meines Benutzers lautet folgendermaßen:

```shell
$ cat /etc/passwd | grep $USER
manu:x:1000:1000:Manuel:/home/manu:/usr/bin/zsh
```

(Wir erinnern uns, wie dieser Eintrag aufgebaut ist:

- `manu` -> Benutzername

- `x` -> Passwort, x = das verschlüsselte Password des Benutzers ist in `/etc/shadows` zu finden

- `1000` -> **UID**, unique user ID

- `1000` -> **GID**, group ID

- `Manuel` -> Zusätzliches Feld für Kommentare oder Zusatzinformationen

- `/home/manu` -> Home-Verzeichnis des Benutzers

- `/usr/bin/zsh` -> Shell des Benutzers
  
  )

Linux verwendet **Gruppen** (**groups**) um Benutzer zu verwalten. Gruppen sind Sammlungen von Accounts mit geteilten Berechtigungen. Die Gruppenmitglieder werden durch das `/etc/group` File verwaltet, welches alles existierenden Gruppen mit ihren jeweiligen Mitgliedern auflistet. 

> Normalerweise befindet sich ein Benutzer in einer default oder primary Gruppe. Wenn sich ein Benutzer anmeldet, wird die Gruppenmitgliedschaft für die primäre Gruppe gesetzt und alle Mitglieder genießen das selbe Level an Berechtigungen.

Berechtigungen von verschiedenen Dateien und Ordnern können mittels Gruppen bearbeitet und konfiguriert werden.

Benutzer haben auch eine oder sogar mehrere **group IDs** (**gid**). Dies inkludiert die default gid, welche denselben Wert die die **uid** besitzt. Diese Nummern sind in `/etc/passwd` sowie `/etc/group` vorhanden und miteinander verknüpft.

Gruppen werden dazu verwendet, um Benutzer in Gruppierungen aufzuteilen. Mitglieder einer Gruppe haben ähnliche Interessen was die Berechtigungen sowie Sicherheitsmaßnahmen angehen. Berechtigungen zu Dateien (und Geräten) werden auf der Basis von dem Benutzer und der Grupppe dem sie gehören verteilt.

Zum Beispiel; die Einträge beider Dateien, worin mein Benutzer vorkommt:

```shell
# Informationen zu meinem Benutzer: Die Benutzerkonfiguration
$ cat /etc/passwd | grep $USER
manu:x:1000:1000:Manuel:/home/manu:/usr/bin/zsh

# Informationen zu den Gruppen, zu welcher der Benutzer angehört.
# Man kann hier auch schön die default-Gruppe mit der gid=1000 sehen.
$ cat /etc/group | grep $USER
wheel:x:998:manu
lp:x:991:cups,manu
sys:x:3:bin,manu
network:x:90:manu
power:x:98:manu
manu:x:1000:
docker:x:962:manu
```

### 4.2 Benutzer hinzufügen und löschen

Die meisten Distributionen haben für die Benutzerverwaltung mittlerweile schon GUI-Interfaces. Da wir diesen Kurs aber nicht ohne Grund beschreiten, schauen wir uns mal die Handhabung im Terminal an (Stichwort: **shell-scripting**):

Neue Benutzer kann man mit dem Befehl `useradd` hinzufügen, während man existierende Benutzer mit `userdel` vom System löschen kann (der Benutzer muss dafür überall ausgeloggt sein).

Wenn wir jetzt zum Beispiel einen Benutzer `schueler` erstellen wollen würden:

```shell
$ sudo useradd schueler
```

Dieser Befehl setzt normalerweise das Home-Verzeichnis zu `/home/schueler`, fügt ein paar Basic-Dateien (von `/etc/skel`) ein und fügt Zeilen zu `/etc/passwd` hinzu. Zum Beispiel:

```shell
$ cat /etc/passwd | grep schueler
schueler:x:1001:1002::/home/schueler:/bin/bash
```

Diesen Account können wir mit dem Befehl `userdel schueler` einfach wieder löschen. Dies wird das Home-Verzeichnis `/home/schueler` nicht löschen. Dies könnte bei z.B. einer temporären Ausschließung nützlich sein.

Um das Home-Verzeichnis zu löschen, während man den Benutzer löscht, gibt es die `-r` Option: `sudo userdel -r schueler`).

Der Befehl `id` mit keinen Argumenten gibt Informationen über den momentanen Benutzer aus, so wie z.B.:

```shell
$ id
uid=1001(schueler) gid=1002(schueler) groups=1002(schueler)
```

Mit `id [username]` kann man sich auch Informationen über andere Benutzer ausgeben lassen.

### 4.3 Gruppen hinzufügen und entziehen

Der `groupadd` Befehl kann dazu verwendet werden, eine neue Gruppe hinzuzufügen:

```shell
$ sudo groupadd schueler
```

Die Gruppe kann mit `groupdel` wieder gelöscht werden:

```shell
$ sudo groupdel schueler
```

Um einen Benutzer manuell zu einer existierenden Gruppe hinzuzufügen, benutzt man den `usermod` Befehl.

Zum Beispiel schaut man sich als erstes an, zu welchen Gruppen der Benutzer momentan gehört:

```shell
$ groups
sys network power docker lp wheel manu
```

Und dann kann man den Benutzer zu der gewünschten Gruppe hinzufügen:

```shell
# Wir fügen "manu" zur Gruppe "schueler" hinzu
$ sudo usermod -aG schueler manu

# Jetzt listen wir uns die Gruppenaufteilungen des Benutzers auf
# und siehe da! Manu ist in Schueler.
$ cat /etc/group | grep manu
...
schueler:x:1002:manu
...
```

- `-a` -> Append, man verhindert damit, schon existierende Gruppen zu entfernen

Der Befehl `groupmod` kann dazu verwendet werden, um eigenschaften einer Gruppe zu bearbeiten. Dazu gehören z.B. die Group ID (gid, `Option -g`) oder der Name (`Option -n`).

Einen Benutzer eine Gruppe zu entziehen ist schwieriger. Die `-G` Option von usermod muss eine komplette Liste von Gruppen ausgeben:

```shell
$ sudo usermod -G manu manu
```

### 4.4 Der root Account

Der `root`-Account ist extrem mächtig und hat vollen Zugriff auf da System (in anderen Betriebssystemen -> Administrator-Account). In Linux wird dieser Account oft als `superuser` bezeichnet.

Man muss extrem gut aufpassen, welchem Benutzer man vollen root-Zugriff gibt. Solche Aktionen sind selten, wenn überhaupt begründbar. Attacken haben oft das Ziel, von einem normalen Account aus an root-Berechtigungen zu kommen.

Jedoch kann man `sudo` verwenden, um mehr Berechtigungen an einem Benutzeraccount zu verteilen:

- Nur auf temporärer Basis

- Nur für eine bestimmte Gruppe/Art von Befehlen

### 4.5 su und sudo

`su` -> switch user, mit diesem Befehl kann man eine neue shell mit einem anderen Benutzer ausführen. Es ist davon abzuraten, `su` dazu zu verwenden, um root zu bekommen.

`sudo` -> Weniger gefährlichere Variante, mehr Berechtigungen zu erteilen. Normalerweise muss `sudo` manuell für einen Benutzer aktiviert werden. Jedoch ist es häufig der Fall, dass dies z.B. bei Distributionen wie Ubuntu schon getan worden ist.

### 4.6 Zum root-Account aufsteigen

Um temporär und für ein paar Befehle der superuser zu werden, kann man einfach nur `su` schreiben und das root-Passwort eingeben. Danach ist man als superuser eingeloggt.

Um nur einen Befehl als superuser auszuführen, kann man `sudo <Befehl>` schreiben. Wenn der Befehl fertig ist, werden die Berechtigungen wieder auf das normale Benutzer-Level heruntergesetzt.

Die `sudo` Konfigurationsdateie ist in `/etc/sudoers` gespeichert, sowie können Konfigurationen in dem Verzeichnis `/etc/sudoers.d/` gespeichert werden. Normalerwiese ist dieses Verzeichnis jedoch leer.

## 5. Umgebungsvariablen

Umgebungsvariablen (auch `environment variablen` genannt), sind Größen mit bestimmten Werten, die von der Befehlsshell (z.B. bash, zsh) oder auch anderen Programmen verwendet werden können. Die meisten Umgebungsvariablen werden schon vom System vor-konfiguriert (können jedoch überschrieben werden), wobei es andere gibt, welche manuell gesetzt werden. Entweder vom Benutzer oder in einem script.

Eine `environment variable` ist eigentlich nur ein character String, welcher Infromationen beinhaltet, die von einer oder mehreren Applikationen verwendet werden. Es gibt verschiedene Wege, wie man die Umgebungsvariablen setzen kann: `set`, `env` oder `export`. 

### 5.1 Umgebungsvariablen setzen

Normalerweise sind variablen, welche von einem script erstellt worden sind, limitert zur momentanen shell; child-Prozesse (sub-shells) können also nicht auf die Werte zugreifen. Um auch diesen child-Prozessen den Zugriff auf die Variablen zu erlauben, kann man den `export` Befehl verwenden:

- `show the value of a specific variable` -> `echo $SHELL` ($variablenname)

- `export a new variable value` -> `export VARIABLE=value` (oder `VARIABLE=value; export VARIABLE`)

- `add a variable permanently` - > `~/.bashrc` oder `~/.zshrc` editieren und folgende Zeile hinzufügen: `export VARIABLE=value`. Dann -> `source ~/.zshrc`

Man kann Umgebungsvariablen auch nur für einen bestimmten Befehl setzen:

```shell
$ # Die Variable PYTHONPATH wird nur für diesen Befehl gesetzt und verwendet
$ PYTHONPATH=/usr/lib/python3.10 python3 script.py
```

### 5.2 Die HOME Variable

`HOME` ist eine Variable welche das Home- (login-) Verzeichnis des Benutzers repräsentiert. `cd` ohne Argumente wird das momentane working directory auf den Wert von `$HOME` setzen. Das `~` Zeichen wird als Kurzform von `$HOME` verwendet. Die Aufrufe `cd ~` und `cd $HOME` sind also identisch:

```shell
$ echo $HOME
/home/manu
$ export HOME=/etc/
$ cd
# Nun befinen wir uns im /etc/ Verzeichnis! Nicht vergessen, die Variable
# wieder auf das default zu setzen.
```

### 5.3 Die PATH Variable

Die `$PATH` Variable ist eine geordnete Liste von Verzeichnissen, in denen sich executables von gewissen Programmen befindne. Wenn ein Befehl für das Finden des richtigen Programmes/Scriptes  gegeben wird, wird die `$PATH` Variable gescannt.

Der Aufbau ist folgendermaßen:

- `:path1:path2:path2`

- `path1::path2`

In dem Beispiel `:path1:path2` liegt ein null directory vor dem ersten colon (:). Das gleiche ist bei dem zweiten Beispiel `path1::path2` der Fall, nur dass das null directory hier zwischen `path1` und `path2` liegt.

### 5.4 Die SHELL Variable

Die `$SHELL` Variable gibt, wie der Name schon sagt, die default-shell des momentanen Benutzers aus.

```shell
$ echo $SHELL
/usr/bin/zsh
```

### 5.5 Die PS1 Variable und die Kommandozeile

Das **Promt Statement (PS)** wird dafür verwendet, den "prompt string" in deinem Terminal Fenster zu verändern, um die gewünschten Informationen zu erblicken.

`PS1` ist die primäre prompt variable, welche das Aussehen der Kommandozeile kontrolliert. Die folgenden speziellen Zeichen können in `PS1` inkludiert werden:

- `\u` User name

- `\h` Host name

- `\w` Current working directory

- `\!`  History number of this command

- `\d` Date

Zum Beispiel:

```shell
$ echo $PS1
$
$ export PS1='\u@\h:\w$'
schueler@beispiel.com:~$ # neue prompt
```

Um die Änderungen rückgängig zu machen:

```shell
$ export PS1='$'
```

Man sollte die alte Kommandozeile jedoch vorher Speichern, bevor man sie verändert:

```shell
$ OLD_PS1=$PS1


# Wenn man die Änderungen rückgängig machen will:
$ PS1=$OLD_PS1
```

## 6. Die Kommando-History

### 6.1 Vorher ausgeführte Befehle ausführen

Die mesiten Shells, also z.B. `bash` oder `zsh`, speichern die eingebenen Befehle und Statements in einem history buffer. Man kann vorher eingebenene Befehle einfach mit den Pfeiltasten **Down/Up** wieder aufrufen. 

Um die Liste an vorher eingebenen Befehlen zu sehen, kann man den `history` Befehl verwenden.

Diese Informationen sind, bei mir, in `~/.zsh_history`  gespeichert.

```shell
$ history | tail -20
 3936  export HOME=/home/manu
 3937  clear
 3938  cd
 3939  clear
 3940  echo $~
 3941  clear
 3942  echo $SHELL
 3943  echo $PS1
 3944  OLD_PS1=$PS1
 3945  PS1='[\d]\u@\h:\w$'
 3946  echo $PS1
 3947  echo OLD_PS1
 3948  echo $OLD_PS1
 3949  export PS1='[\d]\u@\h:\w$'
 3950  clear
 3951  ls
 3952  echo $PS1
 3953  clear
 3954  ls
 3955  la
```

### 6.2 History Umgebungsvariablen verwenden

Verschiedene Umgebungsvariablen können dazu verwendet werden, um Informationen über das `history` File zu bekommen:

- `HISTFILE` -> Der Ort, an dem das history file gespeichert wird.

- `HISTFILESIZE` -> Die maximale Anzahl an Zeilen in dem history file (normalerweise  500).

- `HISTSIZE` -> Maximale Anzahl an Befehlen im history file.

- `HISTCONTROL` -> Wie werden Befehle gespeichert

- `HISTIGNORE` -> Welche Befehle können ignoriert werden

Z.b.:

```shell
$ set | grep HISTFILE
HISTFILE=/home/manu/.zsh_history
_p9k__last_buffer='set | grep HISTFILE'

$ set | grep HISTSIZE
HISTSIZE=50000
_p9k__last_buffer='set | grep HISTSIZE'
```

### 6.3 Vorher ausgeführte Befehle finden

Man kann sich folgendermaßen Informationen über vorher ausgeführte Befehlen holen:

- `Up/Down Pfeiltasten`

- `!! (bang-bang)` -> Führe den letzten ausgeführten Befehl aus

- `CTRL-R` -> Nach vorher ausgeführten Befehlen suchen

### 6.4 Tastenkombinationen

Die folgenden Tastenkombinationen können im Terminal nützlich sein:

- `CTRL + L`: Leert den Terminal Bildschirm.

- `CTRL + D`: Loggt einem aus dem Interface aus.

- `CTRL + Z`: Snoozed einen Befehl.

- `CTRL + C`: Bricht einen Befehl ab.

- `CTRL + H`: Ersetzt die `Backspace` Taste, damit kann man Elemente löschen.

- `CTRL + A`: Damit kann man zum Anfang der Eingabe gehen.

- `CTRL + W`: Löscht das letzte Wort vor dem cursor.

- `CTRL + U`: Löscht die gesamte Zeile in der Shell.

- `CTRL + E`: Bringt den cursor an das Ende der Zeile.

## 7. Dateiberechtigungen

### 7.1 File Ownership

In Linux und anderen UNIX-Basierten Betriebssystemen ist jedes File mit einem Benutzer assoziert, der dieses besitzt. Jedes File ist ebenso mit einer Grupe assoziert, welche damit bestimmte Berechtigungen besitzen.

Die folgenden Hilfsprogramme können für die File Ownership-Verwaltung verwendet werden:

- `chown` -> Den Besitzer einer Datei/eines Verzeichnisses ändern

- `chgrp` -> Den Besitzer einer Gruppe ändern

- `chmod` -> Berechtigungen einer Datei verändern (kann getrennt für den Besitzer, die Gruppe und alle anderen getan werden)

Um z.B. den Besitzer einer Datei zu ändern, kann man folgenden Befehl verwenden:

```shell#
# Das erste "manu" -> Der Benutzer
# Das zweite "manu" -> Die Gruppe
# [Benutzer]:[Gruppe]
$ sudo chown manu:manu file
```

### 7.2 Dateiberechtigungsmodi und chmod

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
