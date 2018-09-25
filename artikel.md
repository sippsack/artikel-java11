# Java geht in die 11. Runde und schneidet alte Zöpfe ab

Durch den neuen halbjährlichen Release-Zyklus seit Java 9 (September 2017) kann man als interessierter Java-Entwickler 
gar nicht mehr so schnell schauen, wie bereits die nächste Major-Version der meist genutzten Programmiersprache erschienen ist. 
Gerade ist mit Java 11 das erste Long Term Support (LTS) Release seit Java 8 fertig geworden und wir wollen einen Blick auf die 
Neuerungen der Sprache und die Änderungen in der API werfen. Da der normale Support (kostenlose Updates) für das Oracle JDK 8 bereits im Januar 
2019 auslaufen wird, steht nun also für viele bereits der Sprung von Java 8 zu 11, dem nächsten offiziellen LTS Release, an.
Selbst Java 8 ist erst jetzt so richtig im Mainstream angekommen. 
Die gerade mal 4 Jahre alten Features wie Lambdas, Streams, Interface Default Methods usw. werden nun von einer breiten 
Masse an Entwicklern verwendet, da sollen wir bereits auf die nächsten Versionen umsteigen.
 
Laut diversen Studien wurde der Umstieg Versionen nach Java 8 bisher eher gescheut. Gerade die großen
Änderungen durch das Java 9 Modulsystem und die dafür notwendige Migration wurde bisher meist verdrängt und vor sich
hergeschoben. Und selbst Josh Bloch, Schöpfer des Java Collection API, sagt:

> It is too early to say whether modules will achieve widespread use outside of the JDK itself. In the meantime, it seems best to avoid them unless you have a compelling need.
(Josh Bloch in "Effective Java: Third Edition")


Zudem waren die letzten Wochen und Monate stark geprägt von den Debatten zu Oracles neuer Support- bzw. Lizenzpolitik 
und der Frage, ob Java bzw. das JDK überhaupt kostenlos bleiben.  
Um es gleich vorweg zu nehmen, werden wir Java und das JDK auch in Zukunft weiterhin frei nutzen können und zudem auch für einen ausreichenden Zeitraum kostenlose Updates und Security-Patches erhalten.
Doch dazu am Ende mehr.

## Was ist neu?

Die Neuerungen von Java 11 fallen relativ übersichtlich aus, was durch den kurzen Zeitraum seit der Veröffentlichung der 
vorangegangenen Version aber nicht weiter verwundert. Die folgenden Java Enhancement Proposals (JEPs) wurden umgesetzt:

* 181: Nest-Based Access Control
* 309: Dynamic Class-File Constants
* 315: Improve Aarch64 Intrinsics
* 318: Epsilon: A No-Op Garbage Collector
* 320: Remove the Java EE and CORBA Modules
* 321: HTTP Client (Standard)
* 323: Local-Variable Syntax for Lambda Parameters
* 324: Key Agreement with Curve25519 and Curve448
* 327: Unicode 10
* 328: Flight Recorder
* 329: ChaCha20 and Poly1305 Cryptographic Algorithms
* 330: Launch Single-File Source-Code Programs
* 331: Low-Overhead Heap Profiling
* 332: Transport Layer Security (TLS) 1.3
* 333: ZGC: A Scalable Low-Latency Garbage Collector
* 335: Deprecate the Nashorn JavaScript Engine
* 336: Deprecate the Pack200 Tools and API

Aus Entwicklersicht sind nur einige wenige Punkte wirklich relevant. 
So wurde im JEP 323 eine Erweiterung der in Java 10 eingeführten Local-Variable Type Inference umgesetzt. 
Typinferenz ist das Schlussfolgern auf Datentypen aus den restlichen Angaben des Codes und den Typisierungsregeln heraus. 
Das spart Schreibarbeit und bläht den Quellcode nicht unnötig auf, wodurch sich wiederum die Lesbarkeit erhöht. 
Seit Java 10 können lokale Variablen mit dem Schlüsselwort `var` folgendermassen dekariert werden:

```java
// Funktioniert seit Java 10
// int
var zahl = 5;
// String
var string = "Hello World";
// BigDecimal
var objekt = BigDecimal.ONE;
```

Neu in Java 11 ist, dass man nun auch Lambda-Parameter mit var definieren kann. 
Das mag auf den ersten Blick nicht sonderlich sinnvoll sein, da man den Typ von Lambda-Parametern sowieso weglassen und über die Typinferenz ermittelt kann. 
Nützlich wird die Erweiterung aber für die Verwendung von Type Annotations wie @Nonnull und @Nullable.

```java
// Inference von Lambda Parametern
Consumer<String> printer = (var s) -> System.out.println(s); // statt s -> System.out.println(s);

// aber keine Mischung von "var" und deklarierten Typen möglich
// BiConsumer<String, String> printer = (var s1, String s2) -> System.out.println(s1 + " " + s2);

// Nützlich für Type Annotations
BiConsumer<String, String> printer = (@Nonnull var s1, @Nullable var s2) -> System.out.println(s1 + (s2 == null ? "" : " " + s2));
```

Die nächste interessante Neuerung ist die Standardisierung der bisherigen Inkubationsversion der neuen HTTP Client API, die mit JDK 9 eingeführt und in JDK 10 aktualisiert wurde (JEP 110).
Neben HTTP/1.1 werden nun auch HTTP/2, WebSockets, HTTP/2 Server Push, Synchrone und Asynchrone Aufrufe und Reactive Streams unterstützt. 
Garniert mit einem gut lesbaren Fluent Interface ist die Verwendung von anderen HTTP-Clients (z. B. von Apache) dann nicht mehr notwendig. 

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



Durch den JEP 330 (Launch Single-File Source-Code Programs) können jetzt Klassen gestartet werden, die sich in einer Quellcode-Datei befinden. 

Programme mit einer einzigen Datei - wobei das gesamte Programm in eine einzige Quelldatei passt - sind in den frühen Phasen des Erlernens von Java und beim Schreiben kleiner Hilfsprogramme üblich. In diesem Zusammenhang ist es eine reine Zeremonie, das Programm vor der Ausführung kompilieren zu müssen. Darüber hinaus kann eine einzelne Quelldatei zu mehreren Klassendateien kompiliert werden, was dem einfachen Ziel, "dieses Programm auszuführen", einen Paketierungs-Overhead hinzufügt. Es ist wünschenswert, das Programm direkt aus der Quelle mit dem Java-Launcher starten zu können:

```java
java HelloWorld.java
```
Beschreibung
Ab JDK 10 arbeitet der Java-Launcher in drei Modi: Starten einer Klassendatei, Starten der Hauptklasse einer JAR-Datei oder Starten der Hauptklasse eines Moduls. Hier fügen wir einen neuen, vierten Modus hinzu: das Starten einer Klasse, die in einer Quelldatei deklariert ist.

Übersetzt mit www.DeepL.com/Translator

Desweiteren wird nun der Unicode 10 Standard unterstützt.

Mit dem Flight Recorder wurde zudem ein bisher kommerzielles Profiling-Tool open-sourced. Das Ziel des Flight Recorder ist das möglichst effiziente Aufzeichen von 

Provide a low-overhead data collection framework for troubleshooting Java applications and the HotSpot JVM.

Goals
Provide APIs for producing and consuming data as events
Provide a buffer mechanism and a binary data format
Allow the configuration and filtering of events
Provide events for the OS, the HotSpot JVM, and the JDK libraries 


```shell
# java HelloWorld.java
# is informally equivalent to

javac -d <memory> HelloWorld.java
java -cp <memory> hello.World
```

# API-Änderungen

An der Java Klassenbibliothek gab es natürlich unzählige kleine Änderungen. Besonders viel lässt sich über Strings berichten:

```java
// Unicode zu String
jshell> Character.toString(100)
$10 ==> "d"
jshell> Character.toString(66)
$7 ==> "B"

// Zeichen mit Faktor multiplizieren
jshell> "-".repeat(80)
$11 ==> "----------------------------------------------------------------------------------------------------"

// Enthält ein Text keine Zeichen (höchstens Leerzeichen)?
jshell> String msg = "hello"
msg ==> "hello"
jshell> msg.isBlank()
$22 ==> false

jshell> String msg = "  "
msg ==> ""
jshell> msg.isBlank()
$24 ==> true

// Abschneiden von führenden oder nachgelagerten Leerzeichen
jshell> " hello world ".strip()
$29 ==> "hello world"
jshell> "hello world    ".strip()
$30 ==> "hello world"
jshell> "hello world    ".stripTrailing()
$31 ==> "hello world"
jshell> "        hello world    ".stripLeading()
$32 ==> "hello world    "
jshell> "    ".strip()
$33 ==> ""

jshell> String content =  "this is a multiline content\nMostly obtained from some file\rwhich we will break into lines\r\nusing the new api"
content ==> "this is a multiline content\nMostly obtained fro ... ines\r\nusing the new api"
jshell> content.lines().forEach(System.out::println)
this is a multiline content
Mostly obtained from some file
which we will break into lines
using the new api

```



## Was wurde entfernt?

In Java 11 wurden nun auch die Ankündigungen in Form von Deprecation in Version 9 und 10 Wirklichkeit. Im JEP 320 wurden diverse Java Enterprise Packages aus Java SE entfernt. 


In Version 11 wurden auch wieder Teil als veraltet markiert und können damit in den nächsten Versionen entfernt werden. Dazu zählen die Pack200 Tools (JAR Kompressionsschema) und auch die JavaScript Engine Nashorn, die sich nie so richtig gegen die Konkurrenz wie Node.js behaupten konnte. Zudem hat man mit der GraalVM ein vielversprechenden Ansatz in der Entwicklung, verschiedene Sprachen (JavaScript, Python, ...) nativ auf dem System laufen zu lassen. 


jre für server, nicht mehr für desktop

## Lizenzpolitik Oracle

https://www.infoworld.com/article/3284164/java/oracle-now-requires-a-subscription-to-use-java-se.html
https://www.inside-it.ch/articles/51671 Kommentar ganz unten

Welche Auswirkungen hat das neue Abomodell auf die Kosten?

Für Kunden, die bisher das „Java SE Advanced“-Programm genutzt haben, um auch über das Ende der öffentlich verfügbaren Updates hinaus noch ältere Versionen von Java sicher nutzen zu können, werden sich die Kosten mit dem neuen Subskriptions-Modell deutlich verringern. Wer seine Java-Versionen bisher jedoch einigermaßen aktuell gehalten hat, musste dafür keine Kosten aufwenden und konnte trotzdem eine sichere Version von Java einsetzen. Ohne weitere Kosten aufzuwenden wird dafür zukünftig ein Wechsel auf OpenJDK und eine halbjährliche Aktualisierung auf die jeweils nächste Major-Version notwendig sein. Wer diesen Aktualisierungsaufwand vermeiden will, sollte sich das neue Subskriptions-Modell ansehen und Alternativen anderer Anbieter damit vergleichen.

Wird es teurer? Besser zugeschnitten?

Das Subskriptions-Modell scheint von den veröffentlichen Bedingungen einfacher in der Kostenkalkulation zu sein. Trotzdem bleiben bisher Fragen offen. Zum Beispiel wird nicht klar angegeben, wie die Prozessoren beim Einsatz von Virtualisierung oder bei Containern zu zählen sind. Sollten die Bedingungen aus dem Oracle-Datenbankbereich, die für Virtualisierungsumgebungen gelten, auf Java übertragen werden, so dürfte so mancher Kunde die eine oder andere Überraschung beim Berechnen der fälligen Subskriptionskosten erleben.

Wird man Java SE weiterhin gratis nutzen können?

Dies wird auf dem Server zunehmend nur mit einem OpenJDK oder anderen Alternativen möglich sein. Markus Karg (JUG Goldstadt): "Aus Sicht der Übergabe von Java EE an die Eclipse Foundation erscheint gerade OpenJ9 als sehr interessante Alternative, auch weiterhin kostenfrei ein professionelles JDK nutzen zu düfen.".

Oder ist das unrealistisch? Wieso?

Markus Karg (JUG Goldstadt): "Viele Anwender scheuen sich leider, alternative Java SE Distributionen zu nutzen, die durchaus seit längerem kostenlos zur Verfügung stehen. Wer diesen Schritt aber nicht geht, für den ist das "Free Lunch" bald vorbei. Dabei bietet gerade OpenJ9 (Eclipse Foundation) eine auch technisch sehr interessante Alternative. Auch von Azul ist ein kostenloses JDK zu erhalten. Beides erfordert aber ein gewisses Quäntchen Mut, Oracle lebewohl zu sagen."

Wie schätzen sie den Schritt von Oracle grundsätzlich ein?

Seit längerem zeichnet sich ab, dass Oracle Java nicht mehr nur als Plattform sieht, die sie erworben haben und auch aus eigenem Interesse weiter entwickeln, sondern Java mehr und mehr als ein Oracle-Produkt vermarktet wird, bei dem auch unprofitable Teile nicht mehr weitergeführt werden. Markus Karg (JUG Goldstadt): "Oracle fördert mit diesem Schritt die Pluralität des Marktes. Das ist grundsätzlich positiv. In einem Jahr wird der Markt ganz anders aussehen. OpenJ9, Azul Zulu, sowieso sicherlich einige neue Anbieter werden den Markt unter sich aufteilen.".

# LTS Releases


## Fazit

Die großen Überraschungen sind sicherlich ausgeblieben. 
Vielmehr finalisiert Java 11 angefangene Arbeiten aus Java 9 und 10, damit es als LTS Release für die nächsten 3 Jahre gut da steht. 
Dazu wurden alte Zöpfe abgschnitten und zum Beispiel JavaFX und diverse Java EE Packages aus dem JDK entfernt.  
