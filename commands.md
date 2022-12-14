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

**cp -r**: Kopiert ein Verzeichnis mit der Option rekursiv

**cp -u**: Steht für update und kopiert das Verzeichnis nur dann, wenn der Inhalt des source directories neuer als der des destination drectories ist.

**grep**: Holt sich eine ressource und gibt sie, falls sie dem übergebenen Muster entspricht, aus.


