# Events

## Definition und Beispiele
Ein Event ist nach Scotts Definition "etwas, auf das ein laufendes Programm (ein Prozess) reagieren muss, das aber außerhalb des Programms und zu einem *unvorhersagbaren* Zeitpunkt auftritt." [PLP, S. 434] Typische Beispiele für Events sind Interaktionen mit einer grafischen Benutzeroberfläche (englisch: graphical user interface, kurz: GUI) wie Mausklicks oder Tastatureingaben. Aber auch Antworten auf asynchrone Ein-/Ausgabeoperationen erfüllen die Definition und können somit als Events aufgefasst werden.

## Implementierungsstrategien
Möchte man auf diese Art von Ereignis innerhalb eines Programms reagieren, so ist eine klassische Implementierung hierfür, das Ereignis explizit anzufragen, zu warten bis es auftritt und anschließend im Programmablauf fortzufahren. So bewirkt beispielsweise ein Aufruf der `scanf` Funktion innerhalb eines terminal-basierten C-Programms dieses Verhalten: die Funktion *blockiert* die Fortführung des Programms, bis die Eingabe terminiert wurde. Der Aufruf von `scanf` kann somit als *synchron* und *blockierend* charakterisiert werden [TODO: Definition synchron/blockierend]. Diese Art der Implementierung hat daher einen entscheidenden Nachteil: der aufrufende Thread kann während der Blockade weder weitere Operationen durchführen, noch auf andere Ereignisse reagieren. Dies ist für moderne Anwendungen mit grafischer Benutzeroberfläche jedoch nicht akzeptabel, da von ihnen erwartet wird, dass sie zu jeder Zeit auf verschiedene Arten von Events reagieren können. [PLP S.434]

Anstatt aktiv auf den Eintritt eines Events zu warten, kann es alternativ wünschenswert sein, eine spezielle Funktion registrieren zu können, die beim Eintritt eines bestimmten Events ausgeführt wird. Da hierbei das Laufzeitsystem in die eigentliche Anwendung "zurück ruft", werden solche Funktionen als *Callback* oder auch *Handler* bezeichnet. [PLP S.434]

### Sequentielle Handler
Ein traditionelles Implementierungsbeispiel für Event Handler ist der Unix Signal Mechanismus, der die "Zustellung" von Events an das Hauptprogramm mittels "spontaner" Funktionsaufrufe realisiert. Die Abbildung ... skizziert den Ablauf einer solchen Zustellung.

[Figure 8.7 von PLP S.435]

Dargestellt wird die Ausführung eines Programms/Prozesses, welches zuvor, über den Aufruf spezieller Routinen, Handler für bestimmte Events registriert hat. Beendet nun ein Hardwaregerät eine asynchrone Operation, so löst dies einen Hardware Interrupt zu einem unvorhersagbaren Zeitpunkt aus. Durch diesen wird der Ablauf des aktuell in Ausführung befindlichen Prozesses an der momentanen Stelle unterbrochen. Abstrahiert betrachtet stellt dies also den Eintritt eines Events dar. Es löst einen Kontextwechsel in den Betriebssystem Kernel und die Ausführung eines vom Betriebssystem verwalteten Interrupt Handler Codes aus, bei dem der Zustand des laufenden Prozesses (bestehend aus Laufzeitstack, Registerinhalten, etc.) gesichert wird. Anschließend wechselt der Kernel zurück in das Programm, stellt den Laufzeitstack wieder her und springt zu einem speziellem Codeblock des Laufzeitsystems, welches als *Signal Trampolin* bezeichnet wird. Das Trampolin erzeugt nun einen neuen Frame im Stack und ruft schließlich den zuvor registrierten Handler auf. Ist letzterer beendet, so wird der gesamte Zustand des Prozesses wiederhergestellt und die Ausführung an der unterbrochenen Stelle fortgeführt. [PLP S.435f]

Da hierbei das laufende Programm potentiell an jeder beliebigen Stelle unterbrochen werden kann, könnte dies beispielsweise auch mitten in einer Modifikation einer Datenstruktur geschehen. Sollte nun ein Event Handler Zugriff auf diese Struktur haben und während der Unterbrechung darauf zugreifen, könnte es also sein, dass sich diese in einem inkonsistenten Zustand befindet. Eine typische Methode dies zu verhindern ist es sicher zu stellen, dass Modifikationen von gemeinsamen Datenstrukturen nicht durch Signale unterbrechen werden können, also *atomar* sind. [PLP S.436]

## Thread-basiert Handler
Als weitere Handler Implementierung existiert der Ansatz, in einem separaten Thread auf den Eintritt von Events zu warten. Dieser wird von einigen bekannten GUI-Systemen, wie dem GNU Image Manipulation Program (GIMP) Tool Kit (Gtk), der Java Swing Bibliothek und der .NET Windows Presentation Foundation (WPF) verfolgt. Zur Verdeutlichung folgt ein Java Swing Beispiel mit einer groben Beschreibung der Funktionsweise.

```java
JButton pauseButton = new JButton("pause");
pauseButton.addActionListener(new ActionListener() {
  public void actionPerformed(ActionEvent e) {
     // do whatever needs doing
  }
});
```
[PLP S.437]

In der Java Swing Bibliothek sind Event-Handler Klassen-Instanzen, die das ActionListener Interface implementieren. Diese können, über die `addActionListener` Methode, als solche bei einem Zielobjekt (hier: `pauseButton`), registriert werden. Swing kümmert sich anschließend selbständig darum, dass in einem separaten Thread auf Events des Zielobjekts gewartet wird. Da hierbei nur dieser Thread blockiert wird, kann die Ausführung des Hauptprogramms nebenläufig fortgeführt werden. Tritt schließlich ein Event ein, erfolgt der Rücksprung aus dem blockierenden Warte-Aufruf und die Abarbeitung der registrierten ActionListener. Während dieser Abarbeitung wartet der Thread nicht auf weitere Events und kann daher nicht auf diese reagieren. Aus diesem Grund sollten Event-Handler Codes möglichst kurz sein oder aber in einem zusätzlichen Worker-Thread ausgeführt werden, um schnellstmöglich in den Wartezustand zurückkehren zu können. [PLP S.436ff]

Im Vergleich zu sequentiellen Handlern erfolgt also ein synchroner Rücksprung aus der Wartefunktion, womit eine asynchrone Unterbrechung an einer willkürrlichen Stelle im Code entfällt. Damit werden jedoch nicht die Probleme gemeinsamer Datenstrukturen umgangen. Zwar ist deren Konsistenz innerhalb eines Threads durch die synchrone Ausführung gewährleistet, da aber Event-Handler nebenläufig dazu ausgeführt werden, können überlappende Zugriffe stattfinden, die wiederum über geeignete Synchronisationsmechanismen reguliert werden müssen. [PLP S.436ff]

## Verwendung in JavaScript
Wie in den Kapiteln zuvor erwähnt, ist das ursprüngliche Einsatzgebiet von JavaScript der Webbrowser. Letzterer stellt dem Benutzer jede Web-Seite bzw. -Anwendung als eine Oberfläche bereit, mit der interagiert werden kann. Ein simples Beispiel hierfür ist das Ausfüllen eines Formulars: Text kann in Textfelder eingetippt, Optionen per Drop-Down-Menü selektiert und letztendlich das Formular per Klick oder Eingabetaste abgeschickt werden. Eine Anforderung an JavaScript ist in diesem Kontext, dass es dem Entwickler ermöglicht, auf all diese *Ereignisse* (bzw. *Events*) reagieren und gegenfalls das Standardverhalten des Browser modifizieren zu können.

Auch in einer Node.js Umgebung ohne grafische Benutzeroberfläche besteht ein Bedarf für event-basierte Verarbeitung. Da das JavaScript Laufzeitsystem auf einen Thread limitiert ist, würde jede Ein- bzw. Ausgabeoperation zwangsläufig zu einem Blockieren des Prozesses führen.
