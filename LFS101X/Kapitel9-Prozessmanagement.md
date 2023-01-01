# Linux Introduction - Kapitel 9

Verfasser: **Manuel Fellner**

Datum: **30.12.2022**

Thema: **Linux Prozesse**

## 1. Was ist ein Prozess

Ein Prozess ist einfach gesagt eine Instanz von einem oder mehreren Abläufen (Threads) welche auf dem Computer ausgeführt werden. Es ist **NICHT** dasselbe wie ein **Programm** oder ein **Befehl**. Denn z.B.: ein Befehl könnte mehrere Prozesse starten.

Manche Prozesse sind unabhängig voneinander und manche nicht. Ein fehlgeschlagener Prozess könnte andere Prozesse auf dem System beeinflussen. Wenn jezt z.B. der Netzwerkprozess abstürzt, können andere Prozesse keine ressourcen mehr über das Internet holen.

**Prozesse verwenden Systemressourcen**. Zum Beispiel RAM, CPU Cycles, Peripheriegeräte, Netzwerkkarten, die Festplatte, das Display und noch vieles mehr. Sie greifen also auf alle Ressourcen des Systems zu.

Für die richtige Einteilung der Systemressourcen ist das Betriebssystem und darunter besonders der Kernel verantwortlich. Der Kernel sorgt für eine insgesamt optimierte Systembenutzung.

![Processes](https://courses.edx.org/assets/courseware/v1/219d348bf46fa4b3b8c83b3dbdf3fb31/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/LFS01_ch16_screen03.jpg)

## 2. Prozesstypen

Zum Beispiel ist ein Terminal ein Prozess, der solange läuft, wie er benötigt wird. Das Terminal erlaubt dem Benutzer andere Befehle und Programme auszuführen und ressourcen in einer aktiven Umgebung zu verwenden. Man kann auch Programme in der Shell starten, die im Hintergrund laufen. Das heißt also, sie entfernen sich von der shell und sind eigenständig.

Prozesse können in verschiedene Typen kategoriert werden. Ausschlaggebend ist der Typ des jeweiligen Zwecks des Prozesses:

### 2.1 Interaktive Prozesse

Interaktive Prozesse müssen von einem Benutzer durch Terminal oder Graphische Benutzeroberfläche gestartet werden. 

**Beispiele:** `Bash`, `Firefox`, `htop`

### 2.2 Batch Prozesse

Batch-Prozesse sind automatische Prozesse, welche im Terminal geplant werden und sich dann vom Terminal disconnecten. Diese Prozesse sind gereiht und arbeiten auf einer **FIFO** (**F**irst-**I**n, **F**irst-**O**ut) basis.

**Beispiele:** `updatedb`, `ldconfig`

### 2.3 Daemons

Daemons sind Server-Prozesse die kontinuierlich laufen. Viele von diesen Prozessen sind schon ab dem Systemstart aktiv und warten dann auf den Benutzer oder das System, welche den Prozess benötigen.

**Beispiele:** `httpd`, `sshd`, `libvirtd` 

### 2.4 Threads

Thread-Prozesse sind Prozesse, die unter einem main-Prozess laufen. Es sind also ein paar Threads die zusammen einen vollwertigen Prozess ergeben. Ein Thread ist lightweight. Threads können Systemressourcen untereinander aufteilen, werden aber vom System individuell geplant und eingesetzt. Ein individueller Thread kann enden, ohne den Hauptprozess damit zu beenden. Ebenso kann der Hauptprozess jederzeit neue Threads erstellen.

Heutzutage sind die meisten Prozesse Multi-Threaded.

**Beispiele:** `firefox`, `gnome-terminal-server`

### 2.5 Kernel Threads

Kernel Threads sind quasi Systemprozesse, worüber der Benutzer wenige Berechtigungen hat. Sie werden vom Benutzer weder gestartet noch gestoppt. Diese Kernel Threads gehen Aktionen nach, wie z.B. einen Thread von einer CPU zu einer anderen CPU zu transferieren. Oder sicherzugehen, dass input/output Operationen bei der Festplatte komplett sind.

**Beispiele:** `kthreadd`, `migration`, `ksoftirqd`

## 3. Prozessplanung und Statuse

Eine kritische Kernelfunktion - der **scheduler**. Er ist dafür verantwortlich, Prozesse konstant auf eine CPU und wieder runter zu leen. Dies wird durch eine **sharing time** geregelt, die besagt, wie viel Zeit auf der CPU ein Prozess bekommt. Beeinflussen tun dies die relative Priorität des Prozesses sowie die Zeit, die der Prozess bis jetzt schon bekommen hat.

Ein solcher Prozess ist ein einem sogenannten **<mark>running state</mark>**. Das heißt, dass dieser Prozess entweder momentan etwas auf der CPU ausführt oder momentan darauf wartet, dass ihm eine Zeit auf der CPU gegeben wird (ein **time slice**) damit er ausgeführt werden kann. Alle Prozesse, welche auf eine Zeiteinteilung warten, befinden sich in einer **Warteschlange (<mark>run queue</mark>)**. Auf einem Computer der mehrere CPUs oder Kerne besitzt, findet man auf jedem dieser CPUs / jedem Kern eine **run queue**.

Manchmal gehen Prozesse jedoch in einen sogenannten **<mark>sleep state</mark>**. Dieser Zustand wird generell erreicht, wenn ein Prozess auf eine Aktion wartet, mit Hilfe dessen er dann weiterarbeiten kann. Zum Beispiel könnte ein Prozess auf Usereingaben warten. In diesem Zustand befindet sich der Prozess ebenso in einer **Warteschlange**. Dies mal wird sie die **<mark>wait queue</mark>** genannt.

Ein weitere Zustand, ist der <mark>**Zombie state**</mark>. Dieser ist relativ selten, passiert aber zum Beispiel wenn ein Child-Prozess fertig ist, der Parent-Prozess dies aber nicht weiß. Also ist der Prozess nicht wirklich am leben, wird aber trotzdem noch im System als Prozess gekennzeichnet.

## 4. Prozesse und Thread IDs

Jederzeit werden in einem System mehrere Threads ausgeführt. Damit das Betriebssystem den Überblick nicht verliert, gibt es jedem Thread eine **<mark>unique Process ID (PID)</mark>**. Mit dieser PID kann das Betriebssystem den Prozess im Blick behalten: mit der Nummer kann es den Process state, die CPU usage, memory use und die location aller Ressourcen im RAM überwachen und noch vieles mehr überwachen.

Neuere PIDs werden normalerweise nach der Reihe vergeben. Also ist der **1. Prozess** der **init Prozess (initialization Process)** und so geht es dann mit den Nummern weiter. Je später der Prozess gestartet wird, desto höher wird seine PID.

Es gibt folgende PID typen:

- `Process ID (PID)` -> <mark>Unique Process ID Nummer</mark>

- `Parent Process ID (PPID)` -> **Der Prozess (Parent), der den Prozess gestartet hat. Wenn der parent stirbt, referenziert die PPID zu einem adoptiv parent (in neueren Kernel ist das kthreadd mit der PPID=2).**

- `Tread ID (TID)` -> **Thread ID Nummer. Diese Nummer ist dasselbe wie PID für single-threaded Prozesse. Für multi-threaded Prozesse ist es so, dass diese Prozesse die gleiche PID besitzen, die unterschiedlichen Threads aber unterschiedliche TIDs haben.**

### 4.1 Einen Prozess "töten"

Wenn man einen Prozess herunterfahren ("töten", "kill") möchte, kann man dies mit dem `kill` Befehl tun. Dieser Befehl wird, wenn richtig eingesetzt, einen Prozess eliminieren.

Um einen Prozess zu eliminieren, kann man den Befehl auf zwei Arten schreiben:

```shell
1.
$ kill -SIGKILL [PID]
2.
$ kill -9 [PID]
```

Also kann man z.B. den Prozess für das Programm `htop` folgendermaßen töten:

```shell
$ ps aux | grep htop
22907
$ kill -9 22907
```

Wir haben uns als erstes mit `ps aux` die PID des Prozesses `htop` geholt, und diese dann für den `kill` Befehl verwendet. 

**!**: Man kann als normaler Benutzer nur seine eigenen Prozesse töten. Die Prozesse eines anderen Benutzers (inkl. root) sind off-limits. Außer, natürlich, man ist root.

## 5. Benutzer und Gruppen IDs

Vielerlei Benutzer können ein System gleichzeitig bedienen und können ebenso mehrere Prozesse starten. 

Das Betriebssystem identifiziert den Benutzer, der den Prozess gestartet hat anhand der **Real User ID (RUID)** welche dem Benutzer gegeben wurde.

Der Benutzer, der die Zugriffsberechtigungen für die Benutzer verwaltet, wird durch die **Effective UID (EUID)** identifiziert. Die **EUID** kann oder kann auch nicht gleich der **RUID** sein.

Benutzer können in vielerlei Gruppen kategorisiert werden. Jede Gruppe ist identifizierbar anhand ihrere **Real Group ID (RGID)**. Die Zugriffsberechtigungen dieser Gruppe werden von der **Effective Group ID (EGID)** verwaltet. Jeder user kann ein Mitglied einer oder mehrere Gruppen sein.

![User and Group IDs](https://courses.edx.org/assets/courseware/v1/fbe122ffd13edf336ad978cddb953a7f/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/LFS01_ch16_screen07.jpg)

## 6. Prioritäten

Jederzeit laufen vielerlei Befehle auf einem System ab (sie befinden sich z.B. in der run queue). Eine CPU kann jedoch nur einer Aktion an einem Zeitpunkt nachgehen. So wie z.B. ein Auto nur einen Fahrer haben kann.

**Auf jeden Fall gibt es Prozesse, die wichtiger als andere sind.** Also lässt Linux dich Prozessprioritäten auch selbst setzen. Man kann Prozessprioritäten manipulieren. Prozesse mit einer höheren Priorität greifen bevorzugt auf die CPU zu.

 Die Priorität eines Prozesses wird anhand seiner **nice value** oder **niceness** festgelegt. Je niedriger die **nice value**, desto höher ist die Priorität des Prozesses. Niedrige Werte werden wichtigen, und höhere Werte unwichtigeren Prozessen zugeordnet. Diese "unwichtigeren" Prozesse können einfach länger warten. Ein Prozess mit einer hohen nice value gibt anderen Prozessen quasi den vortritt.

In Linux, eine nice value von **-20** repräsentiert die höchste, während eine nice value von **+19** die niedrigsten Priorität repräsentiert. Diese konvention stammt aus den jüngsten Tagen von UNIX.

Man kan ebenso eine **real-time priority** vergeben. Diese wird an tasks vergeben, die Zeitsensitive sind. Sie müssen also so schnell wie möglich erledigt werden. Zu diesen Tasks zählen z.B. das Kontrollieren von Maschinen von einem Computer aus oder das Sammeln von eingehenden Daten. Dies ist nur eine extrem hohe Prioritätsebene und sollte nicht mit etwas namens hard real-time verwechselt werden. Hard real-time beschäftigt sich nur damit, dass der Job i dem definierten Zeitfenster erledigt wird.

![Nice Values](https://courses.edx.org/assets/courseware/v1/fcc61556971b7cdefbafabf3d7abab22/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/LFS01_ch16_screen08.jpg)

## 7. Load Averages / Systemauslastung

Die **load averages** (Belastungsdurchschnitt, Systemauslastung) ist der Durchschnitt der load number für einen definierten Zeitpunkt. 

Es berücksichtigt folgende Aspekte:

- Prozesse werden aktiv auf der CPU ausgeführt

- Prozesse sind ausführbar, aber warten auf die CPU

- Prozesse sind sleeping, aber warten auf irgendeine ressource (z.B. I/O)

**Note:** Linux ist anders als andere UNIX-Betriebssysteme, da es sleeping Prozesse berücksichtigt. Es inkludiert die sogenannten **uninterruptible** sleepers, diese können nicht einfach aufgeweckt werden.

Die load Average eines Systems kann mittels den Tools `w`, `top` oder `htop` eingesehen werden:

![](/home/manu/.config/marktext/images/2022-12-30-18-51-26-image.png)

### 7.1 Systemauslastung interpretieren

Die Load average wird in meinem Screenshot mittels 3 Nummen repräsentiert (`1.14`, `1.12` und `1.02`). 

Wenn man davon ausgeht, dass das System nur eine CPU hat, kann man die Nummern folgendermaßen interpretieren:

- `1.14`: In der letzten Minute war das System durchschnittlich unter einer 114%iger last

- `1.12`: In den letzten 5 Minuten war das System durchschnittlich unter einer 112%iger last

- In den letzten 15 Minuten war das System durchschnittlich unter einer 102%iger Last

Wenn die Werte, so wie bei meinem System, über `1.00` sind, bedeutet das, dass das single CPU-System 100% durchschnittlich ausgelastet war. Das ist gut, wenn man ein System vollkommen auslasten möchte. Ein Wert über `1.00` bedeutet für eine single-CPU, dass das System überlastet war: es gab mehr Prozesse welche die CPU benötigt haben, als die CPU zur Verfügung stand.

Wenn wir mehr als eine CPU hätten, würden wir die load average nummer durch die nummer der CPU's teilen. Zum Beispiel, wenn man ein quad-core system hätte und eine load average von `4.00` hätte, wäre die CPU durchschnittlich zu 100% in der jeweiligen Zeit ausgelastet gewesen (`4/4.00`). <mark>Man teilt die load average number also durch die Kern-Anzahl der eigenen CPU. </mark> One-CPU-Systeme sind heutzutage quasi non-existent.

## 8. Hintergrund- und Fordergrundprozesse

Linux unterstützt die Ausführung von Hintergrund- und Fordergrundprozessen jobs. Ein Job in diesem Kontext ist einfach ein command, welcher vom Terminal aus gestartet worden ist.

**Fordergrund (foreground)** jobs laufen direkt von der shell. Wenn ein Fordergrundjob aktiv ist, müssen andere jobs warten, bis derjenige fertig ist. Das ist vollkommen okay wenn ein Job nicht lange braucht, könnte aber in's Auge gehen wenn der Job länger (sogar vielleicht mehrere Stunden) für die Ausführung braucht.

Falls ein Fordergrund job zu lange braucht, kann man diesen einfach in den Hintergrund befördern. Der Hintergrundjob wird dann mit einer niedrigeren Priorität ausegeführt, was dann, als Austausch, eine gute Ausführung von den interaktiven Tasks ermöglicht. Jobs werden standardmäßig im Fordergrund ausgeführt. Man kann einen **Job** mit dem suffix `&` in den **Hintergrund stecken**. Zum Beispiel: `updatedb &`.

Man kann auch `CTRL-Z` verwenden um einen Fordergrundjob zu suspendieren und `CTRL-C` um einen Fordergrundjob zu terminieren. Man kann auch immer `bg` und `fg` verwenden um einen Prozess im Hintergrund oder Fordergrund ausführen zu lassen.

### 8.1 Jobs verwalten

Mit dem Hilfsprogramm `jobs` kann man sich alle Hintergrundprozesse anzeigen lassen. Damit wird ebenso die job ID, den status und command name angezeigt.

`jobs -l` arbeitet genauso wie `jobs` , fügt jedoch noch die PID von dne Hintergrundprozessen hinzu.

Hintergrundjobs sind mit dem terminal verbunden. Wenn man sich also z.B. ausloggt, wird `jobs` nicht mehr die gestarteten Prozesse anzeigen.

```shell
$ sleep 100 &
$ jobs -l
[1]  + 24153 running    sleep 100
```

Hier erstellen wir einen `sleep 100` job und setzen ihn in den Hintergrund. Mit `jobs -l` lassen sich dann alle Informationen sowie die PID sehen.

## 9. Der ps Kommand (System V Style)

Mit dem `ps` tool kann man die momentan ausgeführten Prozesse mit der PID einsehen. Wenn man ein ständiges Statusupdate möchte, kann man Tools wie `top`, `htop`, `atop` oder `btop` verwenden. Diese Tools geben Echtzeitinformationen zu den momentan ausgeführten Prozessen.

`ps` besitzt vielerlei Optionen mit den man die Tasks, die Informationen über diese sowie präzise Outputinformationen festlegen kann.

So kann man sich z.B. mit `ps -u` alle Prozesse von einem bestimmten Benutzernamen anzeigen lassen. Mit `ps -ef` kann man dann alle Prozesse in dem System sehen und mit `ps -eLf` kann man sich dazu noch alle Prozesse im Systeme und alle Threads anzeigen lassen (ein Prozess hat mehrere Threads).

## 10. Der ps Kommand (BSD Style)

Der `ps` Kommand hat auch noch Darstellungen, die von der BSD Variante von UNIX kommen, wo es nicht üblich ist, stricke und Optionen einfach so hinter einen Befehl zu stellen. 

Also gibt es hier andere Varianten: mit `ps aux` kann man z.B. alle Prozesse des Systems einsehen, während man mit `ps saxo` dann alle spezifischen Parameter angeben kann.

```shell
$ ps aux | head -10
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0 170088 14704 ?        Ss   Dez29   0:04 /sbin/init
root           2  0.0  0.0      0     0 ?        S    Dez29   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   Dez29   0:00 [rcu_gp]
root           4  0.0  0.0      0     0 ?        I<   Dez29   0:00 [rcu_par_gp]
root           5  0.0  0.0      0     0 ?        I<   Dez29   0:00 [slub_flushwq]
root           6  0.0  0.0      0     0 ?        I<   Dez29   0:00 [netns]
root           8  0.0  0.0      0     0 ?        I<   Dez29   0:00 [kworker/0:0H-events_highpri]
root          10  0.0  0.0      0     0 ?        I<   Dez29   0:00 [mm_percpu_wq]
root          12  0.0  0.0      0     0 ?        S    Dez29   0:00 [rcu_tasks_kthre]

$ ps auxo stat,priority,pid,pcpu,comm | head -10
STAT PRI     PID %CPU COMMAND
Ss    20       1  0.0 systemd
S     20       2  0.0 kthreadd
I<     0       3  0.0 rcu_gp
I<     0       4  0.0 rcu_par_gp
I<     0       5  0.0 slub_flushwq
I<     0       6  0.0 netns
I<     0       8  0.0 kworker/0:0H-events_highpri
I<     0      10  0.0 mm_percpu_wq
S     20      12  0.0 rcu_tasks_kthre
```

## 11. Der Prozessbaum

Der Prozessbaum ermöglicht die Darstellung aller laufenden Prozesse in einem System in einer Baum-Darstellung. Hier kann man die jeweiligen Beziehungen der Prozesse zueinander sehr gut beobeachten. Wiederholte Einträge von Prozessen werden nicht angezeigt und Threads sind in geschwungenen Klammern untergebracht.

```shell
$ pstree
systemd─┬─AdskLicensingSe───21*[{AdskLicensingSe}]
        ├─ModemManager───2*[{ModemManager}]
        ├─NetworkManager─┬─dhclient
        │                └─3*[{NetworkManager}]
        ├─auditd───{auditd}
        ├─bluetoothd
        ├─crond
        ├─cupsd
        ├─dbus-daemon
        ├─dhcpcd─┬─dhcpcd───dhcpcd
        │        └─2*[dhcpcd]
        ├─dockerd─┬─containerd───14*[{containerd}]
        │         └─18*[{dockerd}]
        ├─kwalletd5───6*[{kwalletd5}]
        ├─mullvad-daemon───12*[{mullvad-daemon}]
        ├─packagekitd───2*[{packagekitd}]
        ├─pamac-daemon───3*[{pamac-daemon}]
        ├─polkitd───2*[{polkitd}]
        ├─rtkit-daemon───2*[{rtkit-daemon}]
        ├─sddm─┬─Xorg───14*[{Xorg}]
        │      ├─sddm-helper───startplasma-x11───{startplasma-x11}
        │      └─{sddm}
        ├─systemd─┬─(sd-pam)
        │         ├─agent───2*[{agent}]
        │         ├─at-spi-bus-laun─┬─dbus-daemon
        │         │                 └─3*[{at-spi-bus-laun}]
        │         ├─at-spi2-registr───2*[{at-spi2-registr}]
        │         ├─baloo_file───3*[{baloo_file}]
        │         ├─baloorunner───6*[{baloorunner}]
        │         ├─dbus-daemon
        │         ├─dconf-service───2*[{dconf-service}]
        │         ├─gmenudbusmenupr───2*[{gmenudbusmenupr}]
        │         ├─jetbrains-toolb───52*[{jetbrains-toolb}]
        │         ├─jetbrains-toolb───{jetbrains-toolb}
        │         ├─kaccess───6*[{kaccess}]
        │         ├─kactivitymanage───5*[{kactivitymanage}]
        │         ├─kdeconnectd───7*[{kdeconnectd}]
        │         ├─kded5───12*[{kded5}]
        │         ├─kglobalaccel5───2*[{kglobalaccel5}]
        │         ├─kio_http_cache_───{kio_http_cache_}
        │         ├─2*[kioslave5]
        │         ├─kscreen_backend───2*[{kscreen_backend}]
        │         ├─ksmserver───6*[{ksmserver}]
        │         ├─kwin_x11───28*[{kwin_x11}]
        │         ├─marktext-x86_64───{marktext-x86_64}
        │         ├─matray───8*[{matray}]
        │         ├─msm_kde_notifie───6*[{msm_kde_notifie}]
        │         ├─obexd
        │         ├─org_kde_powerde───5*[{org_kde_powerde}]
        │         ├─pamac-tray-plas───8*[{pamac-tray-plas}]
        │         ├─pipewire───{pipewire}
        │         ├─pipewire-media-───{pipewire-media-}
        │         ├─plasmashell─┬─dolphin───9*[{dolphin}]
        │         │             ├─firefox─┬─2*[Isolated Web Co───26*[{Isolated Web Co}]]
        │         │             │         ├─Isolated Web Co───32*[{Isolated Web Co}]
        │         │             │         ├─Isolated Web Co───27*[{Isolated Web Co}]
        │         │             │         ├─Isolated Web Co───25*[{Isolated Web Co}]
        │         │             │         ├─Privileged Cont───25*[{Privileged Cont}]
        │         │             │         ├─RDD Process───2*[{RDD Process}]
        │         │             │         ├─Socket Process───4*[{Socket Process}]
        │         │             │         ├─Utility Process───3*[{Utility Process}]
        │         │             │         ├─3*[Web Content───17*[{Web Content}]]
        │         │             │         ├─WebExtensions───26*[{WebExtensions}]
        │         │             │         └─174*[{firefox}]
        │         │             ├─konsole─┬─zsh
        │         │             │         └─6*[{konsole}]
        │         │             ├─konsole─┬─zsh───zsh───marktext─┬─marktext───marktext───20*[{marktext}]
        │         │             │         │                      ├─marktext───marktext
        │         │             │         │                      ├─marktext───4*[{marktext}]
        │         │             │         │                      ├─marktext───12*[{marktext}]
        │         │             │         │                      ├─marktext───20*[{marktext}]
        │         │             │         │                      └─33*[{marktext}]
        │         │             │         ├─2*[zsh]
        │         │             │         ├─zsh───pstree
        │         │             │         └─6*[{konsole}]
        │         │             ├─mullvad-gui─┬─mullvad-gui───mullvad-gui───22*[{mullvad-gui}]
        │         │             │             ├─mullvad-gui───mullvad-gui───mullvad-gui───11*[{mullvad-gui}]
        │         │             │             ├─mullvad-gui───4*[{mullvad-gui}]
        │         │             │             └─27*[{mullvad-gui}]
        │         │             ├─spotify─┬─spotify───spotify───14*[{spotify}]
        │         │             │         ├─spotify─┬─spotify───3*[{spotify}]
        │         │             │         │         └─spotify───17*[{spotify}]
        │         │             │         ├─spotify───5*[{spotify}]
        │         │             │         └─36*[{spotify}]
        │         │             └─67*[{plasmashell}]
        │         ├─polkit-kde-auth───8*[{polkit-kde-auth}]
        │         ├─pulseaudio─┬─gsettings-helpe───3*[{gsettings-helpe}]
        │         │            └─3*[{pulseaudio}]
        │         ├─spectacle───6*[{spectacle}]
        │         ├─2*[ssh-agent]
        │         ├─xdg-desktop-por───5*[{xdg-desktop-por}]
        │         ├─xdg-desktop-por───6*[{xdg-desktop-por}]
        │         ├─xdg-document-po─┬─fusermo 
        │         │                 └─5*[{xdg-document-po}]
        │         ├─xdg-permission-───2*[{xdg-permission-}]
        │         ├─xembedsniproxy───2*[{xembedsniproxy}]
        │         ├─6*[zsh───gitstatusd-linu───24*[{gitstatusd-linu}]]
        │         └─12*[zsh]
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-udevd
        ├─udisksd───4*[{udisksd}]
        ├─upowerd───2*[{upowerd}]
        ├─vmware-usbarbit
        └─wpa_supplicant
```

## 12. top

Die statischen Informationen über die Prozesse und den Zustand des Systems sind nützlich, jedoch ist eine dynamische Darstellung mit Echtzeitinformationen um einiges besser, um das System auch über einen längeren Zeitraum über zu überwachen.

So kann man z.B. tools wie `top` und `htop` verwenden, welche Systeminformationen in Echtzeit (standardmäßig 2 Sekunden Intervalle) anzeigen.

### 12.1 Die erste Zeile von top

Wenn man den `top` Befehl aufruft steht in der **1. Zeile** eine kurze Zusammenfassung von den Systemereignissen:

- Die Uptime des Systems

- Wie viele Benutzer sind angemeldet

- Die load average

Die load average gibt an, wie beschäftigt ein System ist. Eine load average von `1.00` pro CPU gibt an, dass das System ziemlich beschäftigt, aber nicht überladen ist. Eine load average über `1.00` pro CPU gibt jedoch an, dass Prozesse um CPU-Zeit "kämpfen".

```shell
$ top
top - 19:27:01 up 1 day,  7:20,  7 users,  load average: 0,80, 0,81, 0,90
...
```

Wenn die load average immer sehr hoch ist, deutet das darauf hin, dass das System ein Problem hat. Also z.B ein runaway process (ein Prozess in einem nicht-verwaltbaren status, aufgehängter Prozess).

### 12.2 Die zweite Zeile von top

Die **zweite Zeile** vom Output von `top` gibt die Summe der Prozesse sowie die Summe von running,sleepung, stopped und zombie-prozessen an. 

Der Vergleich der running Prozesse mit der load average hilft vielleicht um zu entscheiden, ob das System an seine Grenzen stößt. 

Gestoppte Prozesse sollten untersucht werden, es sollte der Grund für die stoppung gesucht werden.

```shell
$ top
...
Tasks: 339 total,   2 running, 337 sleeping,   0 stopped,   0 zombie
...
```

### 12.3 Die dritte Zeile von top

Die **dritte Zeile **vom Output von `top` indiziert, wie die CPU zwischen dem **Benutzer** (**us**) und dem **Kernel** (**sy**) aufgeteilt wird. Der Prozentwert of CPU time, die jeder verwendet hat, wird angezeigt.

Was auch noch gelistet ist, sind die **Jobs**, welche mit der **niedrigsten Priorität laufen** (**niceness - ni**). Der **Idle Mode **(**id**) sollte niedrig sein, wenn die load average hoch ist und umgekehrt. 

Der Prozentwert an **Jobs**, die auf **I/O warten** (**wa**) kommt danach. Danach kommen die **interrupts**, wobei man an **hardware** (**hi**) und **software** (**si**) interrupts unterscheidet. **Steal time** (**st**) wird generell mit virtuellen Maschinen verwendet, welche sich an der Idle CPU time bedient.

```shell
$ top
...
%Cpu(s):  2,4 us,  1,2 sy,  0,0 ni, 96,3 id,  0,1 wa,  0,0 hi,  0,1 si,  0,0 st
...
```

### 12.4 Die vierte und fünfte Zeile von top

Die **vierte und fünfte Zeile** von `top` zeigen die memomy usage an, welche in folgende Kategorien unterschieden wird:

- Physikalischer Arbeitsspeicher (RAM) - Zeile 4

- Swap Speicher - Zeile5

Um eine gute Systemperformance aufrecht zu erhalten, muss man die Memory usage gut im Auge behalten.

Sobald der physikalische Arbeitsspeicher aufgebraucht ist, wird auf den Swap Speicher (temporärer speicher auf der SSD) zugegriffen. Swap Speicher ist um einiges langsamer als RAM (ist ja auch temporärer SSD speicher). Wenn viel Swap gebraucht wird, wird die System performance hart darunter leiden.

```shell
$ top
...
MiB Mem :  14811,9 total,    306,1 free,   4654,1 used,   9851,7 buff/cache
MiB Swap:   9011,2 total,   9010,9 free,      0,3 used.   9731,0 avail Mem 
...
```

### 12.5 Prozessliste vom top Output

Die Prozessliste von `top` zeigt Informationen zu allen, laufenden Prozessen in dem System an. Normalerweise ist der Prozess mit dem höchsten CPU Verbrauch als erstes aufgelistet.

Die folgenden Informationen zu den Prozessen werden gegeben:

- Process Identification Number (**PID**)

- Process owner (**USER**)

- Priority (**PR**) and nice values (**NI**)

- Virtual (**VIRT**), physcial (**RES**) und shared memory (**SHR**)

- Status (**S**)

- Prozentwert an CPU (**%CPU**) und memory (**%MEM**) in verwendung

- Ausführungszeit (**TIME+**)

- Command (**COMMAND**)

![](/home/manu/.config/marktext/images/2022-12-30-19-46-51-image.png)

### 12.6 Coole Tasten für Konfiguration von top

Mit den folgenden Tasten kann man coole Sachen machen um `top` noch mehr zu konfigurieren:

- `t` -> <mark>Display or hide summary information (Zeile 2 & 3)</mark>

- `m` -> <mark>Display or hide memory information (Zeile 4 & 5)</mark>

- `A` -> <mark>Sortiere die Liste nach top Ressourcenkonsumenten</mark>

- `r` -><mark> Change priority of specific process</mark>

- `k` -><mark> Kill a specific process</mark>

- `f` -> <mark>Enter `top configuration` screen</mark>

- `o?` -><mark> interactively select a new sort order in the process list</mark>

## 13. Aufgaben in der Zukunft planen

Man möchte unbedingt eine Aufgabe in der Zukunft erledigt haben, ist an dem Zeitpunkt jedoch nicht am Computer? Das Tool `at` kann dabei helfen!

Man kann mit `at [Zeitpunktangabe]` eine gesamte Aufgabe planen!

Grundsätzlich kann als Zeitpunkt z.B. `at now + 2 days` verwendet werden. Das ist nun mal der genaue Zeitpunkt jetzt + 2 Tage.

Was man planen kann? bash Befehle.

Z.b. können wir folgendermaßen einen Befehl planen:

```shell
$ at now + 2 days
at> touch file1.txt
at> ps aux > file1.txt
at> cat file1.txt
at> <EOT>
job 3 at 2023-01-01 19:56
```

So haben wir eine Aufgabe geplant, die den Output von ps aux in file1.txt speichert und das File ausgibt. `<EOT>` repräsentiert dabei das Ende der Aufgabe. Am Ende drückt man dann `CTRL+D`.

## 14. cron

`cron` ist ein time-basierendes Planungsprogramm. Es kann routine Hintergrundjobs an bestimmten Zeiten und/oder Tagen ausführen. `cron` wird durch ein Konfigurationsfile in `/etc/crontab` gesteuert (**cron table**), welches vielerlei shell commands enthält, welche wiederrum an bestimmten Zeiten ausgeführt werden müssen. 

Es gibt system-weite **crontab files** und files, welche für bestimmte Benutzer exisiteren. Jede Zeile eines **crontab** files repräsentiert einen job, dieser ist als sogenannte **CRON expression** repräsentiert. Gefolgt wird die CRON expression von einem normalen shell command, der ausgeführt werden soll.

Der Befehl `crontab -e` öffnet einen crontab editor um existierende jobs zu bearbeiten oder neue zu erstellen. Jeder Eintrag eines **crontab** files beinhaltet 6 Felder:

- `MIN` -> <mark>Minutes (0 - 59)</mark>

- `HOUR` -> <mark>Hour Feld (0 - 23)</mark>

- `DOM` -> <mark>Day Of Month (1 - 31)</mark>

- `MON` -> <mark>Month Feld (1 - 12)</mark>

- `DOW` -> <mark>Days Of Week (0 - 6, 0 = Sonntag)</mark>

- `CMD` -> <mark>Command (Der command, der ausgeführt wird)</mark>

**Beispiele:**

Der Eintrag `* * * * * /usr/local/bin/execute/this/script.sh` plant einen Job, welcher `script.sh` ausführt, und das jede Minute jeder Stunde jeden Tag.

Der Eintrag `30 08 10 06 * /home/sysadmin/full-backup` plant einen Job, der ein `full-backup` um 8.30 Uhr am 10.06. macht. Egal an welchem Wochentag.

## 15. sleep

Manchmal muss ein job verzögert oder suspendiert werden. Wenn z.B. eine ressource momentan nicht verfügbar ist, kann die Applikation in den sleep (wait) modus gelegt werden, bis sie ihre Arbeit weitermachen kann.

`sleep` suspendiert die Ausführung für einen definierten Zeitbereich, welcher als Sekunden, Minuten, Stunden oder Tage übergeben werden kann.

Der Syntax lautet: 

`sleep NUMBER[Suffix]`

**SUFFIX kann folgendes sein:**

- **s** für sekunden (by default)

- **m** für minuten

- **h** für stunden

- **d** für Tage

`sleep` und `at` sind unterschiedlich. `Sleep` verzögert die Ausführung für eine spezifische Zeit, während `at` die Ausführung einfach nur zu einem späteren Zeitpunkt startet.
