# Linux Introduction - Kapitel 11

Verfasser: **Manuel Fellner**

Datum: **03.01.2023**

Thema: **Text Editos**



## 1. Basic Editors: nano und gedit

Irgendwann in seiner Linux-Benutzung wird man höchstwahrscheinlich über das Bearbeiten von Textdateien stolpern. 

Linux Systemadministratoren benutzen oft einen text editor anstatt einem wirklichen, graphischen Programm, um z.B. System Konfigurationsdateien zu editieren oder zu erstellen. Graphische Programme bieten zwar einen guten Funktionsumfang, sind aber in vielen Situationen sehr umständig und der Funktionsumfang ist in den meisten Fällen unnötig. Deswegen weicht man oft gerne auf Text editors aus.

> Grundsätzlich sind Office-Programme wie z.B. Word keine Text editors. Sie fügen, meistens unsichtbare, Formattierungen zu den Inhalten hinzu. Dies würde Dateien für die Systemkonfiguration nutzlos machen.

Es gibt verschiedene Text editoren unter Linux:

- **nano**

- **gedit**

- **vi**

- **emacs**

Die Editoren **nano** und **gedit** sind ziemlich einfach zu bedienen und zu lernen, währenddessen **vi** und **emacs** schon für fortgeschrittene sind.

![Text Editors in Linux](https://courses.edx.org/assets/courseware/v1/57bd3f905d0a25d34771843b351ff71a/asset-v1:LinuxFoundationX+LFS101x+1T2020+type@asset+block/LFS01_ch10_screen03.jpg)



## 2. Files ohne einen Text editor erstellen

Manchmal möchte man einfach nur schnell ein Textfile erstellen, ohne direkt einen Editor öffnen zu müssen.

Man könnte z.B. mit `echo` ein File erstellen und füllen:

```shell
# Das File erstellen
$ echo line one > myfile
# Das existierende File weiter füllen
$ echo line two >> myfile
$ echo line three >> myfile
```

> Ein einmalig verwendetes `>` Zeichen, wird den Output des Befehls in ein File geben. Zweimal verwendet wird der Output eines Befehls jedoch direkt an das File angehängt (`>>`).



Der andere Weg würde mit dem `cat` Befehl bestritten werden:

```shell
$ cat << EOF > myfile
> line one
> line two
> line three
> EOF
$
```



## 3. Nano und gedit

`Nano` ist ein Text editor, welcher sich direkt mit dem Befehl `nano [Datei]` öffnen lässt. Nano ist ein relativ einfacher und robuster Texteditor mit einem guten Funktionsumfang. Bei der Verwendung stehen alle Hilfen unten in einer Leiste.

Ein graphischer Texteditor wäre `gedit`. Dieser Editor ist Teil von dem GNOME Desktop-System. Das KDE-System hat auch einen graphischen Texteditor, `kwrite`.

Es gibt auch noch andere Varianten, welche dem Notepad von Windows ziemlich nahe sind, wie z.B. `Kate` von KDE.



## 4. vi und emacs

Grundsätzlich erspare ich mir jetzt viel nutzloses gerede, jedoch bevorzuge ich die Benutzung von `vim` und empfehle den sogenannten `vimtutor` für das lernen von `vim`.  
