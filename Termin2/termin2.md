# Der G++-Compiler und Makefiles

## Vorbereitung

Für diesen Termin werden einige Dateien aus dem GitLab-Projekt benötigen. Für den Zugriff können Sie am Besten das komplette Projekt "klonen":

    git clone <todo>
    
Die Dateien für diesen Termin liegen im Unterverzeichnis `bslab-warmup/Termin2`.

## Übersetzen eines C++-Programms

_Wir verwenden zunächst das C++-Programm [helloworld.cc](./helloworld/helloworld.cc) im Verzeichnis `helloworld`._

Wir übersetzen dieses Programm mit dem Kommando

    g++ helloworld.cc

Schauen wir uns das Resultat mit dem Kommando `ls` an, sehen wir eine ausführbare Datei `a.out`. Diese können wir mit `./a.out` ausführen.

Der Name `a.out` ist etwas ungeschickt. Wir können aber direkt beim Übersetzen einen besseren Namen wählen, z.B. mit

	g++ helloworld.cc -o helloworld

### Übung: Probieren Sie diese Schritte selber aus!

## Übersetzen und Linken

_Wir verwenden nun die Quelldateien im Verzeichnis `helloworld2`._

Beim Übersetzen eines Programms werden zwei Schritte durchgeführt:

1. _Übersetzen_: Übersetzung einer Quelldatei in einer Hochsprache (hier: C++) in eine _Objektdatei_ mit Maschinencode. Die Objektdatei ist noch nicht ausführbar und kann z.B. Referenzen auf Funktionen und Variablen enthalten, die in anderen Objektdateien oder Bibliotheken definiert sind.
2. _Linken_: Erzeugt aus einer oder mehreren Objektdateien ein ausführbares Programm. Dabei werden insbesondere alle Referenzen aufgelöst. Daher müssen alle verwendeten Funktionen und Variablen in genau einer Objektdate definiert sein.

Der Befehl oben führt beide Schritte auf einmal durch. Wenn man ein Projekt mit mehreren Quelldateien besitzt, kann es aber sinnvoll sein, diese einzeln zu übersetzen und am Schluss zu linken. Dann muss man z.B. bei einer Änderung einer Quelldatei nur dieses neu übersetzen und das Linken wiederholen.

Als Beispiel wurde das Programm von oben in zwei Programmteile zerlegt: `helloworld.cc` enthält die Startfunktion `main()`, mit der die Programmausführung beginnt. Die Funktion verwendet eine Funktion `printhelloworld()`, die in `printhelloworld.cc` definiert ist. Beim Übersetzen von `helloworld.cc` muss bekannt sein, dass es die Funktion `printhelloworld()` (irgendwo) gibt. Dazu muss die Funktion deklariert sein. Damit man ein Deklaration nicht in jeder Quelldatei wiederholen muss, lagert man Deklarationen in Header-Dateien aus, in diesem Fall in [printhelloworld.h](./helloworld2/printhelloworld.h). Die Header-Datei wird dann über eine Include-Direktive in die Quelldatei eingebunden, hier mit `#include "printhelloworld.h"`.

Wenn wir nun die Quelldateien einzeln übersetzen und anschließend linken wollen, gehen wir wie folgt vor:

	g++ -c helloworld.cc
	g++ -c printhelloworld.cc
	g++ helloworld.o printhelloworld.o -o helloworld

Das Übersetzen erzeugt dabei Objektdateien mit der Dateiendung `.o`, also `helloworld.o` und `printhelloworld.o`.
	
### Übung: Fügen Sie einen weiteren Funktionsaufruf in `main()` ein und definieren Sie die Funktion in einer neuen Quelldatei mit entsprechender Header-Datei.

## Fehler beim Übersetzen und Linken

Wenn der Compiler beim Übersetzen auf eine Funktion oder Variable stößt, die nicht _deklariert_ ist, gibt es eine Fehlermeldung mit dem Text `... was not declared in this scope`. Ändert man beispielsweise den Funktionsaufruf in [helloworld.cc](./helloworld2/helloworld.cc) von `printhelloworld()` in `printhelluwurld()`, passiert beim Übersetzen folgendes:

<pre>
waldhorst@debian:~/bslab-warmup/Termin2/helloworld2$ <b>g++ -c helloworld.cc</b>
helloworld.cc: In function ‘int main(int, char**)’:
helloworld.cc:4:18: error: ‘printhelluwurld’ was not declared in this scope
  printhelluwurld();
                  ^
</pre>

Wenn hingegen beim Linken eine Funktion oder Variable verwendet wird, die in keiner Objektdatei _definiert_ ist, gibt es eine Fehlermeldung `undefined reference to ...`. Übersetzt man beispielsweise [helloworld.cc](./helloworld2/helloworld.cc) (nachdem man die Änderung oben rückgängig gemacht hat) und versucht dann ohne `printhelloworld.o` zu linken, passiert das:

<pre>
waldhorst@debian:~/bslab-warmup/Termin2/helloworld2$ <b>g++ -c helloworld.cc</b>
waldhorst@debian:~/bslab-warmup/Termin2/helloworld2$ <b>g++ helloworld.o -o helloworld</b>
helloworld.o: In function `main':
helloworld.cc:(.text+0x10): undefined reference to `printhelloworld()'
collect2: error: ld returned 1 exit status
</pre>

Beim automatischen Übersetzen (s.u.) eines komplexen Projekts kann man also anhand der Fehlermeldung unterscheiden, was bei der Verwendung von Funktionen und Variablen schief gelaufen ist:

* `... was not declared in this scope` deutet auf eine fehlende Deklaration beim _Übersetzen_ hin, meist durch eine nicht eingebundene Header-Datei.
* `undefined reference to ...` deutet auf eine fehlende Referenz beim _Linken_ hin, meist durch eine nicht mit an den Linker übergebene Objekt-Datei. 

## Verwendung von Makefiles

_Wir verwenden nun die Quelldateien im Verzeichnis `helloworld3`._

Bei großen Projekten kann das manuelle Übersetzen und Linken ziemlich müßig sein. Hier helfen _Makefiles_, die die notwendigen Schritte automatisieren und dabei Änderungen an Dateien automatisch erkenne, um ggf. Schritte einzusparen. Eine gute Einführung in Makefiles findet sich z.B. [hier](http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/).

Ein Makefile zum Übersetzen unseres Beispielprojekts könnte so aussehen:

	helloworld: helloworld.o printhelloworld.o
		g++ helloworld.o printhelloworld.o -o helloworld
		
	helloworld.o: helloworld.cc
		g++ -c helloworld.cc
		
	printhelloworld.o: printhelloworld.cc
		g++ -c printhelloworld.cc
		
	clean:
		rm -f helloworld *.o


Dabei definiert jede nicht eingerückte Zeile eine _Regel_. Am Anfang einer Regel steht ein _Ziel_ (engl. _target_), gefolgt von einem Doppelpunkt und ggf. einer oder mehreren _Abhängigkeiten_ (engl. _dependencies_). Dies ist so zu lesen: Wenn das Ziel (in der Regel eine Datei) nicht existiert oder älter ist als mindestens eine Abhängigkeit (zumeist auch Dateien), wird das Kommando in der nächsten, eingerückten Zeile ausgeführt. Im Beispiel wird z.B. das Programm `helloworld` gelinkt, wenn sich `helloworld.o` oder `printhelloworld.o` geändert haben. Mehrzeilige Kommandos lassen sich darstellen, indem am Ende einer Zeile ein `\` angehängt wird. 

Für jedes Ziel muss es eine Kette von Regeln geben, mit dem es aus existierenden Quelldateien erzeugt werden kann. Z.B. benötigt das Ziel `helloworld` die Objektdateien `helloworld.o` und `printhelloworld.o`. Für letztere gibt es Regeln, die diese aus `helloworld.cc` bzw. `printhelloworld.cc` erstellen.

Zu beachten ist, dass die Regel für das Ziel `clean` keine Datei mit dem Namen `clean` erzeugt, also immer angewendet wird.

Ein 'Makefile' lässt sich mit dem Kommando <pre>make <em>&lt;ziel&gt;</em></pre> übersetzen. Wird kein Ziel angegeben, wird das erste Ziel der ersten Regel im Makefile verwendet. 

Unser Beispielprojekt lässt sich übersetzen mit `make` bzw. `make helloworld`:

<pre>
waldhorst@debian:~/bslab-warmup/Termin2/helloworld3$ <b>make</b>
g++ -c helloworld.cc
g++ -c printhelloworld.cc
g++ helloworld.o printhelloworld.o -o helloworld
</pre>

Führt man das Kommando erneut aus, wird über Regeln und Abhängigkeiten erkannt, dass nichts zu tun ist:

<pre>
waldhorst@debian:~/bslab-warmup/Termin2/helloworld3$ <b>make</b>
make: 'helloworld' is up to date.
</pre>

Wenn man nun eine der Objektdateien mit dem Kommando `touch` verändert, wird dieses von 'make' erkannt und alle betroffenen Zeile werden neu erstellt.

<pre>
waldhorst@debian:~/bslab-warmup/Termin2/helloworld3$ <b>touch helloworld.cc</b>
waldhorst@debian:~/bslab-warmup/Termin2/helloworld3$ <b>make</b>
g++ -c helloworld.cc
g++ helloworld.o printhelloworld.o -o helloworld
</pre>

### Übung: Erweitern Sie das Makefile so, dass Ihr Projekt aus der vorherigen Übung mit neuer Quell- und Header-Datei übersetzt wird.

Zu beachten ist, dass Änderungen an Header-Dateien im vorliegenden Makefile nicht erkannt werden, da die Header-Dateien nicht als Abhängigkeiten aufgeführt worden sind.

## Muster in Makefiles

_Wir verwenden nun die Quelldateien im Verzeichnis `helloworld4`._

Beim Makefile oben fällt auf, dass viele Regeln eine sehr ähnliche Form haben. Beispielsweise wird eine `.o`-Datei aus einer `.cc`-Datei erstellt. Dies lässt sich auch einfacher darstellen:

    %.o: %.cc
	
Beide Vorkommen von `%` werden hier durch die selbe Zeichenkette ersetzt, wenn ein Ziel mit entsprechendem Muster erstellt werden soll.

Nun ist die Frage, wie mann dann ein passendes Kommando für die Regel definieren kann, wenn man vorher nicht weiß, wie das Muster ausgewertet wird. Hier helfen die folgenden Platzhalter weiter:

|Platzhalter|Bedeutung|
|:---|:---|
|`$<`|Die erste Abhängigkeit|
|`$@`|Name des Ziels|
|`$+`|Liste aller Abhängigkeiten|
|`$^`|Liste aller Abhängigkeiten ohne doppelte Vorkommen|

Nützlich sind bei der Regeldefinition auch Variablen. Diese werden in Großbuchstaben geschrieben und am Zeilenanfang gefolgt von einem `=` definiert. Die Variable kann mit <pre>... $(<em>Variable</em>) ...</pre> verwendet werden.

Eine kompakte Darstellung unseres Makefiles von oben könnte dann z.B. so aussehen:

<pre>
BIN= helloworld
OBJS = helloworld.o printhelloworld.o

$(BIN): $(OBJS)
	g++ $^ -o helloworld
	
%.o: %.cc
	g++ -c $<
	
clean:
	rm -f helloworld *.o
</pre>

### Übung: Erweitern Sie dieses Makefile so, dass Ihr Projekt von oben mit neuer Quell- und Header-Datei übersetzt wird. 



