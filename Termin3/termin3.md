# Umgang mit Dateien in C

## Vorbereitung

Für diesen Termin werden wieder einige Dateien aus dem GitLab-Projekt benötigt. Falls Sie das Projekt bereits geklont haben, können Sie es aktualisieren mit:

    git pull

Wenn Sie das Projekt erneut klonen möchten, müssen Sie eingeben:

    git clone https://iz-gitlab-01.hs-karlsruhe.de/waol0001/bslab-warmup
    
Die benötigten Dateien liegen im Unterverzeichnis `bslab-warmup/Termin3`.

## Einführung

Shells und grafische Werkzeuge sind nicht die einzige Möglichkeit, um mit Dateien umzugehen. Eigentlich sind diese nur Programme (bzw. rufen Programme auf), die über vom Betriebsystem bereitgestellte Funktionen mit den Dateisystemen interagieren. Jede Programmiersprache bietet (mehr oder weniger komfortable) Methoden, die dem Programmierer den Zugang zu diesen Betriebssystemfunktionen ermöglichen.

Wir werden hier einige grundlegende Methoden kennenlernen, die von der Standard C-Bibliothek _libc_ bzw. ihrer freien GNU-Implementierung _glibc_ bereitgestellt werden.

## Öffnen und Schließen von Dateien mit Dateideskriptoren

Bevor ein Programm eine Datei verarbeiten kann, muss diese zunächst _geöffnet_ werden. Dabei wird z.B. geprüft ob die Datei existiert, ob der Benutzer, der das Programm gestartet hat, die notwendigen Berechtigungen besitzt, und wo sich die Datei auf dem Datenträger befindet.

Die C-Bibliothek stellt zum Öffnen von Dateien die Methode `open()` bereit. Die Verwendung der Methode kann mit Hilfe des Befehls `man 2 open` auf der entsprechenden Hilfsseite nachgeschlagen werden. Die Hilfsseite zeigt auch, welche Header-Dateien die Deklarationen der Funktion enthalten.

Neben dem Pfad zur Datei `pathname` hat `open` einen Parameter `flags`. Dieser erlaubt es, genauere Angaben dazu zu machen, wozu die Datei geöffnet werden soll. Gebräuchliche Werte für `flags` sind z.B.:

|Wert|Bedeutung|
|---|---|
|O_EXCL | Datei wird vom Programm exklusiv geöffnet |
| O_RDONLY | Datei wird nur zum Lesen geöffnet | 
| O_WRONLY | Datei wird nur zum Schreiben geöffnet |
|O_RDWR | Datei wird zum Lesen und Schreiben geöffnet |
|O_CREAT | Datei wird neu angelegt |
|O_TRUNC | Inhalt der Datei wird beim Öffnen gelöscht |

Weitere Werte für `flags` sind auf der Hilfsseite zu `open()` zu finden.

Es besteht die Möglichkeit, mehrere Werte für Mode zu verwenden, indem diese mit einem logischen "oder" `|` verknüpft werden, z.B. `O_CREAT | O_RDWR`.

Ein Beispiel für die Verwendung von `open` wäre

    int fd;
    fd = open("1.txt", O_CREAT | O_RDWR);
    
Hier ist zu beachten, dass `open` einen Integer-Wert als Rückgabe liefert. Dieser wird als _Dateideskriptor_ bezeichnet und kann später verwendet werden, wenn auf die geöffnete Datei zugegriffen wird.

Wenn die Datei wieder geschlossen werden soll, kann das mit der Methode `close()` erfolgen. Die Verwendung kann mit `man 2 close` auf der entsprechenden Hilfsseite nachgeschlagen werden.

`close()` bekommt als Argument nicht etwa den Pfad zur Datei, sondern nur einen Integer-Wert `fd`. Dies ist genau der von `open()` zurückgelieferte Dateideskriptor, der daher gespeichert werden sollte, bis die Datei geschlossen wurde.

### Übung: Schreiben Sie ein Programm, das die Datei `loreipsum.txt` zum Lesen öffnet und wieder schließt. Sie können als Grundlage dafür [openandread.cc](./openandread.cc) verwenden.

## Fehler und Fehlernummern

Es kann passieren, dass beim Öffnen und Schließen von Dateien etwas schief geht, z.B. weil die Datei nicht existiert. Das Verhalten von `open()` und `close()` im Fehlerfall kann auf den entsprechenden Hilfeseiten nachgelesen werden. Von beiden Methoden wird im Fehlerfall ein Rückgabewert von `-1` zurückgeliefert.

Um zu wissen, was genau schief gegangen ist, schreibt die C-Bibliothek im Fehlerfall einen Fehlercode in die globale Variable `errno`. Diese Variable hat nur dann einen Aussagekräftigen Wert, wenn eine libc-Methode einen Fehler signalisieret hat. Wenn z.B. `open()` den Rückgabewert `-1` geliefert hat, kann danach `errno` überprüft werden, um den Fehler genauer zu bestimmen:

	int fd;
	fd= open("1.txt", O_RDONLY);
	if(fd == -1) {
		std::cerr << "Error: Could not open file, code " << errno << std::endl;
		exit(EXIT_FAILURE);
	}
	
Die Methode `exit()` bricht hier die Programmausführung ab. Der aufrufenden Shell wird ein Fehlercode `EXIT_FAILURE` zurückgeliefert.

Mögliche Werte für `errno` sind in `errno.h` definiert und können mit `man errno` auf der Hilfsseite nachgeschlagen werden. Mit Hilfe der definierten Konstanten kann z.B. `errno` mit `ENOENT` verglichen werden, um festzustellen, ob eine Datei existierte oder ein anderer Fehler vorlag:

	if(fd == -1) {
		if(errno == ENOENT) {
			std::cerr << "Error: File does not exist" << std::endl;
		} else {
			std::cerr << "Error: Could not open file, code " << errno << std::endl;
		}
		exit(EXIT_FAILURE);
	}

## Lesen aus Dateien

Aus einer geöffneten Datei kann über die Methode `read()` gelesen werden, siehe Hilfsseite. Aus welcher Datei gelesen werden soll, wird wieder über den Dateideskriptor angegeben. `read()` liest `count` Bytes aus der Datei, die dann in dem Speicherbereich abgelegt werden, auf den der Zeiger `buf` zeigt. Dieser Speicherbereich muss mindestens `count` Bytes groß sein. `count` hat den Typ `size_t`

Wo der zu lesende Bereich der Datei beginnt, wird dem Kommando nicht explizit übergeben. Dieses bestimmt sich implizit aus einer Position, die mit dem Dateideskriptor verknüpft ist. Diese Position ist nach öffnen der Datei `0` (Dateianfang) und wird entweder implizit durch `read()`-Operationen oder explizit durch `lseek()` gesetzt.

War das Lesen erfolgreich, liefert `read()` die Anzahl der gelesenen Bytes zurück. Die Dateiposition wird um diesen Wert weitergesetzt. Der Wert kann kleiner als `count` sein, wenn beim Lesen das Ende der Datei erreicht wurde.

Beispielsweise kann so aus einer Datei gelesen werden:

	int count= 10;
	char *buf= new char[count + 1];
	int ret;
	ret= read(fd, buf, count);
	buf[ret]= 0;
	std::cout << buf << std::endl; 

Bitte beachten: Ein String muss in C mit einem 0-Byte terminiert sein, die von `read()` gelesenen Bytes sind dieses in der Regel nicht. Daher wird hier explizit das letzte Byte auf 0 gesetzt!

Die Fehlerbehandlung für `read()` funktioniert analog zu oben und kann auf den entsprechenden Hilfe-Seiten nachgelesen werden.

### Übung: Erweitern Sie Ihr Programm von oben so, dass die komplette Datei `loreipsum.txt` ausgegeben wird. Behandeln Sie dabei auch mögliche Fehler der libc-Methoden.

## Schreiben in Dateien

Analog zu `read()` gibt es die Methode `write()`, die auf den entsprechenden Hilfe-Seiten definiert ist.

Die Methode kann z.B. so verwendet werden:

	const char *buf= "Hello world!\n";
	int ret;
	ret= write(fd, buf, strlen(buf));

`strlen()` bestimmt dabei die Länge der Zeichenkette, Verwendung siehe entsprechende Hilfsseite.

### Übung: Entwickeln Sie ein Programm, das ein paar Zeilen in eine Datei schreibt, mit entsprechender Fehlerbehandlung. Setzen Sie dazu auf [write.cc](./write.cc) auf.

## Einschub: Kommandozeilen-Parameter

Ein Programm, dass von der Kommandozeile gestartet wird, kann Kommandozeilen-Parameter übergeben bekommen, die z.B. Optionen oder Attribute darstellen können. Diese werden der Funktion `main()` übergeben und in der Variable `argv` als Zeichenketten abgelegt. `argv[0]` ist dabei der Pfad zum Programm selber. Die Anzahl der Parameter steht in `argc`. Zum Beispiel können alle Parameter so ausgegeben werden, wie es in [printargs.cc](./printargs.cc) dargestellt ist.
	
### Übung: Schreiben Sie ein Programm `copy`, dass zwei Dateinamen als Argumente bekommt und den Inhalt der Datei mit dem ersten Namen in eine Datei mit dem zweiten Namen kopiert. Setzen Sie dazu auf [copy.cc](./copy.cc) auf.

## Lesen und Schreiben von beliebigen Datentypen

Oben haben wir `read()` und `write()` immer auf Arrays vom Typ `char` angewendet. Der Übergebene Zeiger hat aber den Type 'void *' und kann auf beliebige Speicherbereiche und damit auch auf komplexe Datenstrukturen zeigen. Beispielsweise können Strukturen so geschrieben werden: 

	struct MyStruct {
		int a, b, c;
	};
	...
		MyStruct s;
	...
		bytesWritten= write(fd, &s, sizeof(MyStruct));
	...
	
### Aufgabe: Schreiben Sie ein Programm, das die Struktur `MyStruct` in eine Datei schreibt und wieder aus dieser liest. Setzen Sie dazu auf [writeandreadstructs.cc](./writeandreadstructs.cc) auf.