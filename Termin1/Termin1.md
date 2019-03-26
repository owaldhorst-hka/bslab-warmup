# Termin 1 - Dateien, Verzeichnisse und die Unix Shell

## Definition Dateien / Dateisystemen

Aus [Wikipedia](https://de.wikipedia.org/wiki/Datei):

>  Eine Datei (englisch file) ist ein Bestand meist inhaltlich __zusammengehöriger Daten__, der auf einem __Datenträger oder Speichermedium__ gespeichert ist. Diese Daten können somit über die Laufzeit eines Programms hinaus existieren und werden als __persistent__ bezeichnet – sie sind bei Programmende nicht verloren.
>
> In der elektronischen Datenverarbeitung ist der Inhalt jeder Datei zunächst eine __eindimensionale Aneinanderreihung von Bits__, die normalerweise in __Byte-Blöcken__ zusammengefasst interpretiert werden. Erst der Anwender einer Datei bzw. ein Anwendungsprogramm oder das Betriebssystem selbst interpretieren diese Bit- oder Bytefolge beispielsweise als einen Text, ein ausführbares Programm, ein Bild oder eine Tonaufzeichnung. Eine Datei besitzt also ein Dateiformat.
>
> [...]
>
> Dateien werden in den meisten Betriebssystemen über __Dateisysteme__ verwaltet. Ein Dateisystem verwaltet das Speichermedium, indem in Listen vermerkt wird, welche Bereiche des Mediums durch welche Dateien belegt sind, welche Bereiche frei sind, sowie oft Protokolle zu geplanten und/oder abgeschlossenen Änderungen.
>
> Obwohl eine Aufgabe des Dateisystems darin besteht, vom konkreten Medium zu abstrahieren („alle gleich zu behandeln“), sind doch viele Dateisysteme an die üblichen technischen Eigenschaften der Speichermedien angepasst (z. B. Blockgröße 512 Byte für Festplatten).
>
> Für die meisten Dateisysteme ist 1 Byte die kleinste Verwaltungseinheit, d. h., die Länge des Dateiinhalt-Bitstroms muss auf ganze Bytes aufgehen (wobei im Allgemeinen auch 0 Byte = 0 Bit erlaubt sind).
>
> Das Dateisystem verwaltet neben __Verzeichnissen__ mit Dateinamen und -speicherort fast immer noch weitere Dateiattribute. Zu diesen gehören häufig der Dateityp (Verzeichnis, normale Datei, spezielle Datei), die Dateigröße (Anzahl der Bytes in der Datei), Schreib- und Leserechte, Zeitstempel („Datum“, der Erzeugung, des letzten Zugriffs und der letzten Änderung) sowie gegebenenfalls noch andere Informationen. Eine Datei kann in vielen Dateisystemen durch ein Attribut als versteckte Datei gekennzeichnet werden.
>
> Die in Dateinamen verwendbaren Zeichen sind abhängig von Dateisystem, Betriebssystem und gegebenenfalls Sprachoptionen. Bei Unix-kompatiblen Dateisystemen dürfen in einem Dateinamen kein Schrägstrich („/“) und kein Nullzeichen stehen, ferner ist die Länge des Dateinamens auf 255 Zeichen begrenzt. Die Zeichen können unterschiedlich codiert sein. Neuere Betriebssysteme unterstützen auch Unicode.

Wir beschäftigen uns in dieser Lehrveranstaltungen mit Dateien Dateisystemen und werden uns diesen heute Kommandozeilen-orientiert nähern.

## Terminal und BASH (Bourne again SHell)

Klassische Computersysteme wurden über Text-basierte __Terminals__ bedient. Heute haben wir jedoch meist grafische Benutzeroberflächen. __Terminalemulatoren__ wie `xterm` bieten die Möglichkeit zur Ausführung Text-basierter Programme.

Eine __Shell__ ist eine Text-basierte Benutzerschnittstelle für Unix-verwandte Betriebssysteme wie Linux und kann in einem Terminalemulator verwendet werden. Sie ermöglicht die Eingabe von Text-Kommandos über eine Eingabezeile, die dann sofort ausgeführt werden.

Ein Beispiel für eine Shell ist die __Bourne again SHell__ (__BASH__), die wir hier verwenden werden.

BASH führt die Kommandos immer innerhalb eines Arbeitsverzeichnisses aus. Welches Verzeichnis das aktuell ist, lässt sich mit dem Kommando `pwd` ausgeben.

<pre>
waldhorst@debian:~$ <b>pwd</b>
/home/waldhorst
</pre>

Das aktuelle Verzeichnis lässt sich mit dem Kommando `cd <verzeichnis>` wechseln, z.B. `cd /`

## Anzeigen von Verzeichnisinhalten
    
Den Inhalt des aktuellen Verzeichnisses kann man mit dem Befehl `ls` anzeigen lassen. Ist man an einem anderen Verzeichnis interessiert (z.B. am Verzeichnis `/etc`), kann mann `ls /etc` eingeben.

Wenn man den Namen eines Verzeichnisses nicht genau weiss, hilft das Feature __Tab-Completion__: Gibt man Beispielsweise `ls /e` ein und drückt die Tabulator-Taste, wird automatisch `/etc` ergänzt, soweit dies eindeutig möglich ist. Bei Mehrdeutigkeit werden die Alternativen aufgelistet. Dies funktioniert auch führ Kommando- und Dateinamen und kann eine Menge Tipparbeit sparen.

Mehr Informationen zu den möglichen Optionen für das Kommando `ls` findet man auf der Anleitungsseite (__man-page__): `man ls` (auch für alle anderen Kommandos eine gute Idee!)

### *Aufgabe 1: Erkunden Sie die ersten zwei Verzeichnisebenen unter `/home`!*

## Relative und absolute Pfade

Wenn Sie sich mit dem Kommando `pwd` das aktuelle Verzeichnis ausgeben lassen, beginnt der Pfad zum Verzeichnis mit einem "/". Ein solcher Pfad nennt sich __absoluter Pfad__, weil er von der Wurzel der Verzeichnishierarchie aus absolut festgelegt ist.

Sie können auch __relative Pfade__ zum aktuellen Verzeichnis angeben. Wollen Sie ein Unterverzeichnis des aktuellen Verzeichnisses benenne, reicht es, den Namen des Verzeichnisses anzugeben, und zwar ohne "/" am Anfang. Wenn Sie das Eltern-Verzeichnis des aktuellen Verzeichnisses benennen wollen, können Sie dieses mit `..` tun. Sie können z.B. mit `cd ..` ins Eltern-Verzeichnis wechseln.

Das Kommando `cd` (ohne Argumente) bringt Sie immer zurück in Ihr persönliches Verzeichnis.

### *Aufgabe 2: Wechseln Sie über einen relativen Pfad ins Verzeichnis `/etc`!*

## Eingebundene Dateisysteme

In Unix-basierten Systemen kann jedes Verzeichnis auf einem unterschiedlichen Datenträger mit einem Unterschiedlichen Dateisystem liegen. Umgekehrt heißt dass, dass Datenträger / Dateisysteme in ein Verzeichnis eingebunden werden. Beispielsweise wird ein eingebundener USB-Stick in einem Verzeichnis `/media/usb` eingebunden. Auf Windows-basierten Systemen würde er dagegen mit einem eigenen Laufwerksbuchstaben, z.B. `F:`, eingebunden.

Alle aktuell eingebundenen Dateisysteme lassen sich mit dem Kommando `mount` anzeigen.

## Erster Umgang mit Dateien

Eine (leere) Datei lässt sich mit dem Kommando `touch` anlegen:

<pre>
waldhorst@debian:~/bsp$ <b>touch 1.txt</b>
waldhorst@debian:~/bsp$ <b>ls</b>
1.txt 
</pre>

Dateien können mit dem Kommando `rm` gelöscht werden:

<pre>
waldhorst@debian:~/bsp$ <b>rm 1.txt</b> 
waldhorst@debian:~/bsp$ <b>ls</b>
waldhorst@debian:~/bsp$ 
</pre>

Dateien können durch die Angabe des kompletten Pfades im Verzeichnisbaum und des Namens eindeutig bestimmt werden. Dabei kann der Pfad relativ oder absolut angegeben werden.
	
### *Aufgabe 3: Erzeugen Sie eine Datei und löschen Sie diese wieder!*

## Dateiattribute

Wie im Wikipedia-Zitat oben beschrieben haben Dateien außer ihrem Namen noch weitere Attribute. Diese können mit dem Kommando `ls -l` angezeigt werden:
<pre>
waldhorst@debian:~/bsp$ <b>ls -l</b>
insgesamt 0
-rw-r--r-- 1 waldhorst waldhorst 0 Mär 26 21:55 1.txt
waldhorst@debian:~/bsp$ 
</pre>

(Die Zahl hinter `insgesamt` gibt hier die von den Dateien im Verzeichnis belegten Blöcke an.)

Die Attribute, die hier angegeben werden, sind:

* Die Berechtigungen für die Datei, [siehe unten](#Zugriffsrechte).
* Anzahl an Verweisen auf die Datei (eine Datei kann in Unix durch verschiedene Namen in verschiedenen Verzeichnissen (__hard links__) referenziert werden).
* Der Besitzer (__owner__) und die Gruppe (__group__) der Datei.
* Die Größe der Datei.
* Das letzte Änderungsdatum.
* Der Dateiname.

Eine detailliertere Ausgaben lässt sich mit dem Kommando `stat` generieren.

### Berechtigungen‚

Die Berechtigungen haben zehn Stellen, die jeweils ein Buchstabe oder ein "-" sein können:

* Die erste Stelle gibt an, ob es sich um eine Datei ("-"), ein Verzeichnis ("d") oder einen symbolischen Verweis (__soft link__) auf eine Datei handelt
* Die nächsten drei Stellen geben an, welche Rechte der Besitzer (owner) der Datei hat. "r" bedeutet hier lesen, "w" schreiben, "x" ausführen (im Fall von Verzeichnissen hinein wechseln). Ein "-" zeigt hier eine nicht vorhandene Berechtigung an.
* Die nächsten drei Stellen geben an, welche Rechte die Mitglieder der Gruppe der Datei haben, Bedeutung siehe oben.
* Die letzten drei Stellen geben an, welche Rechte alle anderen Benutzer haben.

Diese Berechtigungen lassen sich mit dem Kommando `chmod` ändern:

<pre>
waldhorst@debian:~/bsp$ <b>ls -l</b>
insgesamt 0
-rw-r--r-- 1 waldhorst waldhorst 0 Mär 26 21:55 1.txt
waldhorst@debian:~/bsp$ <b>chmod go-r 1.txt</b>
waldhorst@debian:~/bsp$ <b>ls -l</b>
insgesamt 0
-rw------- 1 waldhorst waldhorst 0 Mär 26 21:55 1.txt
</pre>

### *Aufgabe 4: Erzeugen Sie eine Datei `1.txt` und ändern Sie die Berechtigungen so, dass weder die Gruppe noch andere Benutzer diese lesen können.*

### *Aufgabe 5: "Berühren" Sie die Datei `1.txt` mit dem Kommando `touch`. Wie ändern sich die Zeiten, die vom Kommando `stat` angezeigt werden?*

## Umgang mit Verzeichnissen

Mit dem Kommando `mkdir` wird ein neues Unterverzeichnis angelegt. Als Argument wird das neue Verzeichnis mit absolutem oder relativem Pfad angegeben:

<pre>
waldhorst@debian:~/bsp$ <b>mkdir Unterverzeichnis</b>
waldhorst@debian:~/bsp$ ls
Unterverzeichnis
</pre>

Ein leeres Verzeichnis lässt sich mit dem Kommando `rmdir` löschen. Ist das Verzeichnis nicht leer, kann es mit dem Kommando `rm -rf` gelöscht werden. Achtung: '-rf' heißt "recursive" und "force", d.h. hier werden alle Unterverzeichnisse gelöscht und es wird nicht mehr nachgefragt!

### *Aufgabe 6: Erzeugen Sie zwei Ebenen von Unterverzeichnissen und löschen Sie diese wieder!*

## Umleiten von Aus- und Eingabe

In diesem Abschnitt verwenden wir das Kommando `echo`, dass eine Zeichenkette ausgibt, sowie das Kommando `cat`, dass den Inhalt einer Datei ausgibt

Die Ausgabe jedes beliebigen Kommandos kann in eine Datei umgeleitet werden, indem man am Ende des Kommandos ein `>` gefolgt vom Dateinamen angibt:

<pre>
waldhorst@debian:~/bsp$ <b>echo Hello world! > 1.txt</b>
waldhorst@debian:~/bsp$ <b>cat 1.txt</b> 
Hello world!
</pre>

Existiert die Datei bereits, wird sie überschrieben. Will man an die Datei anhängen, kann man den Operator `>>` verwenden:

<pre>
waldhorst@debian:~/bsp$ <b>echo Hello world, too! >> 1.txt</b>
waldhorst@debian:~/bsp$ <b>cat 1.txt</b> 
Hello world!
Hello world, too!
</pre>

Mit dem Operator `<` lässt sich die Eingabe für ein Kommando aus einer Datei lesen.

## Erweiterte Ausgabe von Dateien

Neben 'cat' sind noch die folgenden Kommandos für die Ausgabe von Dateien nützlich:

* `less` stellt die Möglichkeit zum Scrollen in der Datei bereit.
* `head` gibt die ersten Zeilen einer Datei aus.
* `tail` gibt die letzten Zeilen einer Datei aus. Nützlich - z.B. für die Anzeige von Log-Dateien - ist `tail -f`: Die Datei bleibt geöffnet und neue Zeilen werden direkt ausgegeben.
* `grep` ermöglicht das Suchen in einer Datei.
* `diff` vergleicht den Inhalt von zwei Dateien.
* `wc` zählt Buchstaben, Worte und Zeichen.

Mit Hilfe von `man` können Informationen zu diesen Kommandos gefunden werden.

### *Aufgabe 7: Geben Sie die ersten 20 Zeilen der Datei `[loreipsum.txt](loreipsum.txt)' aus.*

## Pipes

Mit Hilfe des Operators `|` kann die Ausgabe eines Kommandos direkt als Eingabe‚ eines anderen Kommandos verwendet werden. Man spricht hier von einer __Pipe__:

<pre>
waldhorst@debian:~/bsp$ <b>echo Hello world! | cat</b>
Hello world!
</pre>

### *Aufgabe 8: Geben Sie die Zeilen 11 bis 20 der Datei `[loreipsum.txt](loreipsum.txt)' aus.*

## Mehr zu Kommandos

Es gibt zwei Arten von Kommandos:

* Integrierte (__builtin__) Kommandos - diese sind in den Code der Shell integriert.
* Externe Kommandos - hier wird eine ausführbare Datei (Programm) ausgeführt, dass irgendwo in der Verzeichnishierarchie steht.

Z.B. lässt sich ein Programm [HelloWorld.sh](HelloWorld.sh) im aktuellen Verzeichnis ausführen mit:

<pre>
waldhorst@debian:~/bsp$ <b>./Helloworld.sh</b> 
Hello World!
</pre>

(Ggf. müssen die Berechtigungen zum Ausführen zuvor mit `chmod` gesetzt werden.)

Die meisten Kommandos wurden mit der Installation des Betriebssystems an einer passenden Stelle in der Verzeichnishierarchie installiert. Wo lässt sich mit dem Kommando 'which' herausfinden:

<pre>
waldhorst@debian:~/bsp$ <b>which ls</b>
/bin/ls
</pre>

Die Shell findet die externen Kommandos, ohne dass Sie den kompletten (absoluten oder relativen) Pfad angeben, weil die Ordner mit wichtigen Programmen in einer Umgebungsvariable für den __Suchpfand__ (__$PATH__) stehen. Diese Variable lässt sich ausgeben mit:

<pre>
waldhorst@debian:~/bsp$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
</pre>

Will man weitere Verzeichnisse in den Suchpfad aufnehmen, geht dies beispielsweise mit:

<pre>
waldhorst@debian:~/bsp$ <b>export PATH=$PATH:/home/waldhorst/bin</b>
waldhorst@debian:~/bsp$ <b>echo $PATH</b>
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/home/waldhorst/bin
</pre>