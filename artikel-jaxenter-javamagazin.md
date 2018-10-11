# Java geht in die 11. Runde und schneidet alte Zöpfe ab

Durch den neuen halbjährlichen Release-Zyklus seit Java 9 (September 2017) kann man als interessierter Java-Entwickler 
gar nicht mehr so schnell schauen, wie bereits die nächste Major-Version der [verbreitetsten Programmiersprache](https://www.tiobe.com/tiobe-index/) 
veröffentlicht wurde. 

Gerade ist mit [Java 11](http://openjdk.java.net/projects/jdk/11/) das erste Long Term Support (LTS) Release seit Java 8 fertig geworden und wir wollen einen Blick auf die 
Neuerungen der Sprache und die Änderungen in der API werfen. Da der normale Support (kostenlose Updates) für das Oracle JDK 8 bereits im Januar 
2019 auslaufen wird, stellt sich für viele nun auch die Frage, in naher Zukunft von Java 8 auf 11 zu wechseln. 
Dabei ist Java 8 erst jetzt so richtig im Mainstream angekommen. 
Die gerade mal 4 Jahre alten Features rund um Lambdas und Streams werden mittlerweile von einer breiten 
Masse an Entwicklern verwendet. Und nun sollen wir uns bereits mit den nächsten Versionen beschäftigen und auf die aktuelle LTS-Version umsteigen? 

Genau das scheint Oracle zu beabsichtigen. Allerdings scheuen laut diversen Studien viele den Umstieg auf die Versionen nach Java 8 
bisher. Gerade die großen Änderungen durch das Java 9 Modulsystem und die dafür notwendige Migration wird vielerorts aufgeschoben. 
Selbst Josh Bloch, Schöpfer des Java Collection Framework, rät im Moment von der Verwendung des Modulsystems ab:

> It is too early to say whether modules will achieve widespread use outside of the JDK itself. In the meantime, it seems best to avoid them unless you have a compelling need.
(Josh Bloch in "Effective Java: Third Edition")

Natürlich lassen sich Java 9, 10 und 11 auch ohne das Modulsystem bzw. mit minimal invasivem Eingriff in die bestehenden
Anwendungen verwenden. 
Nichtsdestotrotz ist der Respekt groß, immerhin springen auch die meisten Hersteller von Bibliotheken und Frameworks bisher
nur sehr zögerlich auf die Neuerungen auf.

Die letzten Wochen und Monate waren zudem stark geprägt von den Debatten zu Oracles neuer Support- bzw. Lizenzpolitik 
und der Frage, ob Java bzw. das JDK überhaupt kostenlos bleiben.  
Um es gleich vorweg zu nehmen, wir werden Java und das JDK auch in Zukunft weiterhin frei nutzen können und zudem auch 
für einen ausreichenden Zeitraum kostenlose Updates und Security-Patches erhalten. Doch dazu am Ende mehr.

## Was ist neu?

Die Neuerungen von Java 11 fallen relativ übersichtlich aus. Das verwundert durch den kurzen Zeitraum seit der Veröffentlichung der 
vorangegangenen Version aber nicht weiter. Die folgenden Java Enhancement Proposals (JEPs) wurden umgesetzt:

* 181: Nest-Based Access Control
* 309: Dynamic Class-File Constants
* 315: Improve Aarch64 Intrinsics
* 318: Epsilon: A No-Op Garbage Collector
* => 320: Remove the Java EE and CORBA Modules
* => 321: HTTP Client (Standard)
* => 323: Local-Variable Syntax for Lambda Parameters
* 324: Key Agreement with Curve25519 and Curve448
* => 327: Unicode 10
* => 328: Flight Recorder
* 329: ChaCha20 and Poly1305 Cryptographic Algorithms
* => 330: Launch Single-File Source-Code Programs
* 331: Low-Overhead Heap Profiling
* 332: Transport Layer Security (TLS) 1.3
* 333: ZGC: A Scalable Low-Latency Garbage Collector
* 335: Deprecate the Nashorn JavaScript Engine
* 336: Deprecate the Pack200 Tools and API

Aus Entwicklersicht sind nur einige wenige Punkte wirklich relevant, welche in der Liste mit einem Pfeil markiert wurden. 

So wurde im JEP 323 eine Erweiterung der in Java 10 eingeführten Local-Variable Type Inference umgesetzt. 
Typinferenz ist das Schlussfolgern der Datentypen aus den restlichen Angaben des Quellcodes und den Typisierungsregeln heraus. 
Das spart Schreibarbeit und bläht den Quellcode nicht unnötig auf, wodurch sich wiederum die Lesbarkeit erhöht. 

Seit Java 10 können lokale Variablen mit dem Schlüsselwort `var` folgendermaßen deklariert werden:

```java
// Funktioniert seit Java 10

var zahl = 5; // int
var string = "Hello World"; // String
var objekt = BigDecimal.ONE; // BigDecimal
```

Neu in Java 11 ist, dass man nun auch Lambda-Parameter mit `var` deklarieren kann. 
Das mag auf den ersten Blick nicht sonderlich sinnvoll erscheinen, da man den Typ von Lambda-Parametern sowieso weglassen und 
über die Typinferenz ermitteln lassen kann. 
Nützlich wird die Erweiterung aber für die Verwendung von Type Annotations wie @Nonnull und @Nullable.

```java
// Inference von Lambda Parametern
Consumer<String> printer = (var s) -> System.out.println(s); // statt s -> System.out.println(s);

// aber keine Mischung von "var" und deklarierten Typen möglich
// BiConsumer<String, String> printer = (var s1, String s2) -> System.out.println(s1 + " " + s2);

// Nützlich für Type Annotations
BiConsumer<String, String> printer = (@Nonnull var s1, @Nullable var s2) -> System.out.println(s1 + (s2 == null ? "" : " " + s2));
```

Die nächste interessante Neuerung ist die Standardisierung der bisher noch experimentellen neuen HTTP Client API, 
die mit JDK 9 eingeführt und in JDK 10 aktualisiert wurde (JEP 110).
Neben HTTP/1.1 werden nun auch HTTP/2, WebSockets, HTTP/2 Server Push, synchrone und asynchrone Aufrufe und Reactive Streams 
unterstützt. 
Garniert mit einem gut lesbaren Fluent Interface wird die Verwendung von anderen HTTP-Clients (z. B. von Apache) dann 
in Zukunft voraussichtlich obsolet sein. 

```java
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder()
      .uri(URI.create("http://openjdk.java.net/"))
      .build();
client.sendAsync(request, asString())
      .thenApply(HttpResponse::body)
      .thenAccept(System.out::println)
      .join();
```


Durch den JEP 330 (Launch Single-File Source-Code Programs) können jetzt Klassen gestartet werden, die noch nicht 
kompiliert wurden. 
Programme mit einer einzigen Datei sind heutzutage beim Schreiben kleiner Hilfsprogramme üblich und insbesondere
die Domäne von Skriptsprachen. 
Nun kann man sich auch in Java die unnötige Arbeit sparen und das verringert zugleich die Einstiegshürde für Java-Neulinge. 

Bis Java 10 konnten Java-Programme auf drei Arten gestartet werden: 
* als *.class-Datei
* als Hauptklasse in einer *.jar-Datei
* als Hauptklasse in einem Modul

Neu ab Java 11 ist nun das Starten einer Klasse, die in einer Quellcodedatei deklariert ist:
 
```shell
# java HelloWorld.java
// statt
# javac HelloWorld.java
# java -cp . hello.World
```

Auf unixoiden Betriebssystemen können Java-Dateien als Shebang-Files sogar direkt ausgeführt werden:

```java
#!/path/to/java --source version
```

```shell
# ./HelloWorld.java
```

Weitere erwähnenswerte Änderungen sind die Unterstützung des Unicode 10 Standards und die Integration des bisher nur 
mit einer kommerziellen Lizenz verwendbaren Profilers Flight Recorder in das OpenJDK (wurde bisher nur mit dem
Oracle JDK ausgeliefert). Das Ziel des Flight Recorders ist das möglichst effiziente Aufzeichnen von Anwendungsdaten,
um bei Problemen die Java-Anwendung und die JVM analysieren zu können.

# API-Änderungen

An der Java Klassenbibliothek gab es natürlich auch unzählige kleine Änderungen. Besonders viel hat sich bei Zeichenketten
getan:

```java
|  Welcome to JShell -- Version 11
|  For an introduction type: /help intro
// Unicode zu String
jshell> Character.toString(100)
$1 ==> "d"
jshell> Character.toString(66)
$2 ==> "B"

// Zeichen mit Faktor multiplizieren
jshell> "-".repeat(20)
$3 ==> "--------------------"

// Enthält ein Text keine Zeichen (höchstens Leerzeichen)?
jshell> String msg = "hello"
msg ==> "hello"
jshell> msg.isBlank()
$5 ==> false
jshell> String msg = "  "
msg ==> "  "
jshell> msg.isBlank()
$7 ==> true

// Abschneiden von führenden oder nachgelagerten Leerzeichen
jshell> " hello world ".strip()
$8 ==> "hello world"
jshell> "hello world    ".strip()
$9 ==> "hello world"
jshell> "hello world    ".stripTrailing()
$10 ==> "hello world"
jshell> "        hello world    ".stripLeading()
$11 ==> "hello world    "
jshell> "    ".strip()
$12 ==> ""

// Texte zeilenweise verarbeiten
jshell> String content =  "this is a multiline content\nMostly obtained from some file\rwhich we will break into lines\r\nusing the new api"
content ==> "this is a multiline content\nMostly obtained fro ... ines\r\nusing the new api"
jshell> content.lines().forEach(System.out::println)
this is a multiline content
Mostly obtained from some file
which we will break into lines
using the new api
```
## Was wurde entfernt?

Die Ankündigungen in Form von Deprecations in den Versionen 9 und 10 sind nun in Java 11 Wirklichkeit geworden. 
Im JEP 320 wurden diverse Java Enterprise Packages aus Java SE entfernt, dazu zählen JAX-WS (XML basierte SOAP Webservices 
inklusive den Tools wsgen und wsimport), JAXB (Java XML Binding inklusive den Tools schemagen und xjc), 
JAF (Java Beans Activation Framework), Common Annotations (@PostConstruct, @Resource, ...), CORBA 
und JTA (Java Transaction API). 

Neu ist auch, dass das Oracle JDK kein JavaFX mehr enthalten wird (mit dem OpenJDK wurde es übrigens noch nie ausgeliefert). 
Stattdessen wird JavaFX über [OpenJFX](http://openjdk.java.net/projects/openjfx/) als separater Download angeboten und kann
wie jede andere Bibliothek in beliebigen Java-Anwendungen verwendet werden. Neben JavaFX wird auch der Support für Applets 
und Java Web Start eingestellt. Die Opensource Community plant allerdings bereits [Nachfolgeprojekte](https://dev.karakun.com/webstart/). 
Wenn man im Moment noch Java Web Start nutzen möchte, muss man zunächst beim Oracle JDK 8 bleiben und entweder 
ohne Sicherheits-Updates leben oder für den kommerziellen Support ab 2019 Geld ausgeben.

Neu als Deprecated markiert wurde in Java 11 die JavaScript Engine Nashorn. Es ist davon auszugehen, 
dass sie in zukünftigen Java-Versionen verschwinden wird. Nashorn hat sich allerdings nie so richtig als serverseitige 
JavaScript-Implementierung gegenüber Node.js durchsetzen können. Und mit der GraalVM geht Oracle mittlerweile alternative Wege,
um andere Programmiersprachen nativ auf der JVM auszuführen. 

Übrigens wird es ab Java 11 die Java Laufzeitumgebung (JRE) nur noch in der Server-Variante und nicht mehr für Desktops geben.
Allerdings kann man für Desktop-Anwendungen mit dem Modulsystem und dem Werkzeug jlink mittlerweile selbst sehr einfach 
in der Größe angepasste Laufzeitumgebungen erstellen.  

## Support- und Lizenzpolitik von Oracle

Mit Java 11 führt Oracle eine neue Lizenz- und Support-Strategie ein. Freie Updates für die letzte Long Term Support (LTS) 
Version 8 wird es ab Januar 2019 nicht mehr geben. Zudem ist ab Java 11 das Oracle JDK nur noch in der Entwicklung kostenlos
nutzbar, in Produktion muss ein [Support-Vertrag](https://blogs.oracle.com/java-platform-group/a-quick-summary-on-the-new-java-se-subscription) 
mit Oracle gegen entsprechende Gebühren abgeschlossen werden.

Allerdings ist das Oracle JDK ab sofort binär kompatibel mit dem OpenJDK, welches Opensource unter der GNU General Public License v2 (with the Classpath Exception - GPLv2+CPE) 
veröffentlichtet ist. Um das JDK weiterhin in Produktion kostenlos einsetzen zu können, kann man also ab Java 11 das OpenJDK 
verwenden. Allerdings bietet Oracle immer nur Updates für die aktuelle OpenJDK Version, d. h. mit dem nächsten halbjährlichen Major-Release
muss man potentiell die Anwendung bereits auf die nächste Version updaten. 

Alternative Anbieter wie Azul und IBM haben im Rahmen des AdoptOpenJDK-Projekts allerdings bereits angekündigt, die 
LTS-Versionen (z. B. Java 11) auch bis zu vier Jahre mit kostenlosen Updates zu versorgen. Für uns Anwender der Java Plattform
gibt es also genügend Optionen zwischen kommerziellen Lösungen von Oracle (und auch Azul Zulu, IBM OpenJ9 usw.) und weiterhin freien 
Lösungen mit dem OpenJDK und dem verlängerten Support des [AdoptOpenJDK-Projekts](https://adoptopenjdk.net/). Aber 
natürlich gehört etwas Mut dazu, Oracle den Rücken zu kehren und alternativen Java Plattformen eine Chance zu geben.

Bei Oracle zeichnet sich nun seit Längerem ab, dass man Java nicht mehr nur als Plattform sieht, die man erworben 
hat und insbesondere aus eigenem Interesse weiterentwickelt. Vielmehr wird Java mehr und mehr als ein Oracle-Produkt vermarktet, 
bei dem unprofitable Teile ggf. eingestellt werden. Damit wird aber auch der Wettbewerb auf dem Markt angekurbelt, 
was grundsätzlich positiv ist. Seien wir also gespannt, wie sich der Markt in einem Jahr präsentieren wird.

## Fazit

Die großen Überraschungen sind sicherlich ausgeblieben. 
Vielmehr finalisiert Java 11 angefangene Arbeiten aus den beiden vorangegangenen Versionen, damit es als LTS Release für die nächsten 3 Jahre gut gewappnet ist. 
Dazu wurden auch einige alte Zöpfe abgeschnitten und zum Beispiel JavaFX und diverse Java EE Packages aus dem JDK entfernt.  

Java 12 steht bereits in den Startlöchern und wird voraussichtlich im März 2019 erscheinen. Aktuell sind nur zwei Neuerungen 
geplant, weitere werden aber in den nächsten Wochen folgen:

* JEP 325: Switch Expressions
* JEP 326: Raw String Literals

Beide Sprach-Features stammen aus sogenannten Inkubator-Projekten ([Amber](http://openjdk.java.net/projects/amber/), 
[Valhalla](http://openjdk.java.net/projects/valhalla/), [Loom](http://openjdk.java.net/projects/loom/)), in denen kleinere,
die Entwicklerproduktivität steigernde Sprach- und VM-Features ausgebrütet und dann als Java Enhancement Proposals (JEPs) akzeptiert 
werden.

Switch Statements können demnach in Zukunft auch als Expression verwendet werden, die das Ergebnis des entsprechenden
Case-Zweigs direkt zurückliefert.

```java
int numLetters = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> 6;
    case TUESDAY                -> 7;
    case THURSDAY, SATURDAY     -> 8;
    case WEDNESDAY              -> 9;
};
```

Mit Raw String Literals wird Java endlich die Möglichkeit bekommen, mehrzeilige Zeichenketten zu definieren, die 
zusätzlich Escape-Sequenzen (\..) ignorieren. Damit lässt sich viel einfacher mit regulären Ausdrücken und 
Windows-Dateipfaden umgehen. Einzig das Ersetzen von Variablen (Stringinterpolation) ist im Moment noch nicht geplant, 
ein Feature welches alternative Sprachen wie Groovy, Ruby und JavaScript schon länger unterstützen. 

```java
Runtime.getRuntime().exec(`"C:\Program Files\foo" bar`);
// statt
Runtime.getRuntime().exec("\"C:\\Program Files\\foo\" bar");

String script1 = `function hello() {
                    print('"Hello World"');
                 }
				
                 hello();`;
// statt
String script2 = "function hello() {\n" +
                "   print(\'\"Hello World\"\');\n" +
                "}\n" +
                "\n" +
                "hello();\n";
```

Wenn sich die Unsicherheiten zur neuen Release-, Lizenz- und Supportpolitik endlich gelegt haben, dürfen wir 
Java Entwickler wieder frohgemut in die Zukunft mit vielen kleinen Sprachverbesserungen blicken. In diesem Sinne 
viel Spaß beim Ausprobieren der neuen Funktionen von Java 11.
