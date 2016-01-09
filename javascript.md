# JavaScript

## Historischer Hintergrund

### Geschichte im Webbrowser-Kontext
Die Geschichte von JavaScript ist sehr eng an die frühe Geschichte der Webbrowser gekoppelt. Einer der in den 1990er Jahren erfolgreichen Browser Hersteller war Netscape Communications, Entwickler des Netscape Nagavitor. Netscape war der Auffassung, dass Web-Browser und -Server nicht bloß als Anwendungen, sondern mehr als eine neue Art verteiltes Betriebssystem zu verstehen seien. Entwickler sollten damit in die Lage versetzt werden, universelle Anwendungen für alle die Plattformen entwickeln zu können, auf denen ihr Browser lauffähig war. Um dies zu ermöglichen, musste folglich eine portable Programmiersprache im Browser eingesetzt werden können. [Severance]

Einer der Kandidaten war Java, welches die geforderte Portabilität besaß und daher als eine Lösung gewählt wurde. Als Komplement zum komplexen, kompilierten Java wollte Netscape jedoch noch ein leichtgewichtige, interpretierte Skriptsprache bereitstellen. Sie sollte leicht in Webseiten eingebettet und auch von nicht-professionellen Programmierern benutzt werden können. Daraufhin wurde der Entwickler Brendan Eich im Mai 1995 beauftragt, innerhalb von 10 Tagen einen funktionierenden Prototypen einer entsprechenden Sprache zu liefern. Als eine Bedingung galt dabei, dass sie einerseits Java syntaktisch ähneln sollte, andererseits sollte sie sich so weit von Java unterscheiden, dass sie nicht mit ihr in Konkurrenz tritt. Brendan Eich selber schilderte in einem Interview dazu, dass ein Klassenkonstrukt bereits zu Java-ähnlich gewesen wäre und er insgesamt aus marketingtechnischen Gründen dazu gezwungen war, eine "dumme kleine Brudersprache" von Java zu entwickeln. Die genannten Einschränkungen führten dazu, dass sich Eich unterschiedlicher Konzepte aus verschiedenen Programmiersprachen und -paradigmen bediente, auf die in den folgenden Kapiteln näher eingegangen wird. [Severance]

### Standardisierung als ECMAScript
Im November 1996 wurde JavaScript zwecks Standardisierung an die Ecma International herangetragen, um eine standardisierte Version der Sprache zu erarbeiten. Im Juni 1997 wurde die erste Edition unter dem Namen *ECMAScript* und der Spezifikation ECMA-262 veröffentlicht. Es folgten weitere Editionen, bishin zur sechsten Edition, die im Juni 2015 unter dem Namen *ECMAScript 2015* (kurz: *ES2015*) finalisiert wurde und Gegenstand dieser Arbeit ist. [Ecma262]

[Severance: http://www.computer.org/csdl/mags/co/2012/02/mco2012020007-abs.html]
[Ecma262: http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf]

## Host-Umgebungen
Sucht man nach in der ECMAScript Spezifikation (Stand: ES2015) nach `setTimeout`, einer wichtigen, häufig verwendeten und global ausführbaren Funktion innerhalb von JavaScript Programmen, so wird man feststellen, dass diese nicht zu dessen Umfang gehört. Dies liegt daran, dass die Sprache selber für den Einsatz innerhalb einer beliebigen *Host-Umgebung* konzipiert wurde. Die typischsten Host-Umgebungen für JavaScript sind Webbrowser, aber auch Node.js zählt zu diesen. Die Spezifikation bildet damit nur eine gemeinsame Basis für unterschiedliche Implementierungen und lässt umgebungsspezifische Details bewusst offen. So werden beispielsweise weder Ein-/Ausgabe Mechanismen, noch ein Nebenläufigkeits- bzw. Threading-Modell spezifiziert. Stattdessen fordert die Spezifikation, dass konkrete Ausführungsumgebungen fehlende Funktionalitäten und Verhalten über Objekte bereitstellen. So wird die `setTimeout` in Node.js als globale Funktion und innerhalb aller großer Webbrowser als Methode des `window`-Objekts bereitgestellt und ist somit ein Beispiel hierfür.

## Datentypen

## Objekte

### Definitionen und innerer Aufbau
In JavaScript sind Objekte eine Sammlung von *Properties* (Eigenschaften). Es existieren zwei Arten von Properties:
- Ein *Daten-Property* assoziiert einen *Key* mit einem Wert und einer Menge von *Attributen*.
- Ein *Accessor-Property* assoziiert einen *Key* mit einem oder zwei *Accessor-Funktionen* (*Getter*-/ *Setter*-Funktionen) und einer Menge von *Attributen*.

Alle direkten (nicht-geerbten) Properties eines Objekts, sind als *eigene Properties* (englisch: *own properties*) des Objekts definiert.

*Keys* sind Werte vom Typ String oder Symbol. Sie ermöglichen die Identifizierung von Properties, sowie den Zugriff auf diese. Keys vom Typ String werden auch *Name* einer Property genannt.

*Attribute* sind Werte, die den Zustand eines Property beschreiben. Folgende Attribute bestimmen den Zustand einer Property:
- *[[Enumerable]]*: Boolean-Wert, ob das Property in einer for-in-Schleife berücksichtigt wird.
- *[[Configurable]]*: Boolean-Wert, ob das Property gelöscht, die Property-Art (zu Daten- bzw. Accessor-Property) geändert oder Attribute geändert werden dürfen.
- *[[Value]]* (nur Daten-Property): der Wert einer Property, welcher durch einen *get* Zugriff zurückgegeben wird.
- *[[Writable]]* (nur Daten-Property): Boolean-Wert, ob das [[Value]] Attribut durch einen *set* Zugriff geändert werden darf.
- *[[Get]]* (nur Accessor-Property): Funktionsobjekt (oder *undefined*), welches bei einem *get* Zugriff ohne Argumente aufgerufen und dessen Rückgabe als Resultat des Zugriffs zurückgegeben wird.
- *[[Set]]* (nur Accessor-Property): Funktionsobjekt (oder *undefined*), welches bei einem *set* Zugriff mit einem einzigen Argument aufgerufen wird. Der Effekt dieses Aufrufs kann (muss aber nicht) sein, dass bei folgenden *get* Zugriffen ein modifizierter Wert zurückgegeben wird.

Objekte müssen in JavaScript keiner statisch vorgegebenen Struktur entsprechen, sondern können *dynamisch* aufgebaut werden, indem Properies zur Laufzeit hinzugefügt und auch entfernt werden können.

### Initialisierung
JavaScript ist eine objekt-orientierte Programmiersprache. Im Gegensatz zu anderen Sprachen dieser Art, wie Java oder C++, sind Objekte jedoch nicht klassenbasiert, sondern können auf unterschiedlichen Wegen erzeugt werden: über *Objekt-Literale*, über Funktionen die zusammen mit dem *new*-Operator als *Konstruktor* genutzt werden, über *Object.create* und über die in ES2015 eingeführten *Klassen*.

Die folgenden Code-Beispiele demonstrieren die unterschiedlichen Initialisierungs-Varianten. In allen wird ein Objekt mit dem Attribut `name` und der Methode `sayName` erzeugt und anschließend der Variable `person` zugewiesen. Schließlich wird die `sayName` aufgerufen.

```javascript
// Erstelle person-Objekt über ein Objektliteral
let person = {
  name: 'Alfred',
  sayName () { console.log('My name is:', this.name) }
}

person.sayName(); // => My name is: Alfred

console.log('persons proto === Object.prototype?',
  Object.getPrototypeOf(person) === Object.prototype); // => true
```

```javascript
// Person Konstruktor
function Person (name) {
  this.name = name // Setze name`
  this.sayName = () => console.log('My name is:', this.name) // Setze `sayName`
}

let person = new Person('Alfred'); // Initialisiere über new-Operator
person.sayName(); // => My name is: Alfred

console.log('persons proto === Person.prototype?',
  Object.getPrototypeOf(person) === Person.prototype); // => true
```

```javascript
// Erstelle Objekt mit `null` als Prototyp und einem Properties-Objekt als zweitem Argument
let person = Object.create(null, {
  // Deklariere `name` und `sayName` als Properties mit jeweils einem Value-Attribut
  name: { value: 'Alfred' },
  sayName: { value: function () { console.log('My name is:', this.name) } }
});

person.sayName(); // => My name is: Alfred

console.log('persons proto === null?',
  Object.getPrototypeOf(person) === null); // => true
```

```javascript
// Erstelle Objekt mit `null` als Prototyp und einem Properties-Objekt als zweitem Argument
let person = Object.create(null, {
  // Deklariere `name` und `sayName` als Properties mit jeweils einem Value-Attribut
  name: { value: 'Alfred' },
  sayName: { value: function () { console.log('My name is:', this.name) } }
});

person.sayName(); // => My name is: Alfred

console.log('persons proto === null?',
  Object.getPrototypeOf(person) === null); // => true
```

An dieser Stelle sei erwähnt, dass alle drei Konstrukte zu zwar ähnlichen, aber nicht exakt gleichen Ergebnisobjekten führen. So unterscheiden sich zum Beispiel die Property-Attribute. Weitere Unterschiede werden durch die nächsten Abschnitte erörtert.

### Prototyp-basierte Vererbung
JavaScript unterstützt Vererbung auf Basis von *Prototypen*. Jedes Objekt verfügt über eine Referenz auf ein weiteres Objekt oder `null`, die als *Prototyp* des Objekts bezeichnet wird. Da der Prototyp eines Objekts wiederum ein Objekt sein kann, welches ebenfalls einen Prototypen besitzt, entsteht eine *Prototype Chain*. Das Ende einer Prototype Chain bildet schließlich ein Objekt, dessen Prototyp `null` ist.

Der Zugriff auf den Wert eines Propertys erfolgt über die Verwendung des Punkt-Operators gefolgt vom gesuchten Key oder durch das Umschließen des Keys mittels eckiger Klammern. Erfolgt ein solcher Zugriff, wird zunächst im direkt referenzierten Objekt (also in den eigenen Properties) nach dem angegebenen Key gesucht. Wird keine entsprechende Property gefunden und ist der Prototyp ungleich `null`, werden die Properties des Prototypen nach einem Vorkommen des Keys untersucht. Diese Suche wird entlang der Prototypen-Kette rekursiv fortgeführt, bis der Key entweder gefunden oder `null` als Prototyp erreicht wurde. Wird der Key dabei nicht gefunden, wird `undefined` zurückgegeben. Alle Properties, die auf diese Art und Weise über ein Objekt erreichbar und keine eigenen Properties sind, werden als *geerbt* (englisch: `inherited`) bezeichnet.

Das Setzen des Prototyps erfolgt bei der Objektinitialisierung und, je nach Methodik, entweder implizit oder explizit:
- Objekte, die über Objekt-Literale initialisiert werden, erhalten implizit das `prototype` Property des `Object`-Konstruktors als Prototyp.
- Objekte, die über einen Konstruktor initialisiert werden, erhalten implizit das `prototype` Property des verwendeten Konstruktors als Prototyp.
- Objekte, die über `Object.create` erzeugt werden, erhalten explizit das erste Argument als Prototyp.
- Objekte, die über Klassen initialisiert werden, erhalten implizit das `prototype` Property der Klasse als Prototyp.

Alle Varianten, außer `Object.create`, haben also gemeinsam, dass das erzeugte Objekt anschließend den Wert der `prototype` Property eines Konstruktors als Prototyp referenziert (vorausgesetzt der Wert ist ein Objekt oder `null`). Weiterhin ist zu beachten, dass der Wert des `prototype` Propertys **nicht** mit dem Prototypen des Konstruktors verwechselt wird.

```javascript
console.log(Object.getPrototypeOf(Person) === Person.prototype); // => false
```

### Klassen und Unterschiede zur klassischen Objekt-Orientierung
Obwohl JavaScript seit ES2015 Klassenkonstrukte vorsieht, stellen sie im Grunde nur eine vereinfachende Syntaxerweiterung (*syntactic sugar*) dar und ändern das zugrunde liegende Prototyp-basierte Vererbungskonzept nicht.

### Das Global Object
Vor jeder Ausführung eines JavaScript Programms, erzeugt eine JavaScript Implementierung ein spezielles *Global Object*. Dieses Objekt dient als Container für alle globalen Funktionen und Variablen und existiert im *globalen Scope*. In allen populären Webbrowsern kann auf das Global Object über den Bezeichner `window` zugegriffen werden und ist dort eher unter dem Namen *Window Object* bekannt. Unter Node.js ist es über den Bezeichner `global` referenziert. Der Zugriff auf die Properties des global Objekts kann im gesamten Programm auch ohne die Verwendung des Bezeichners `window` bzw. `global` erfolgen, vorausgesetzt der Name der Property ist nicht überdeckt worden.

```javascript
// parseInt ist eine globale Funktion zum parsen von Ganzzahlen
console.log(parseInt === global.parseInt); // => "true": sowohl direkt, als auch über `global` erreichbar

function shadowParseInt () {
  let parseInt = () => 'Sorry, I shadowed parseInt';
  console.log(parseInt("99", 10)); // => "Sorry, I shadowed parseInt"
  console.log(global.parseInt("99", 10)); // => "99"
}
shadowParseInt();
```

## Funktionen

### Konstruktoren

### Arrow Functions

## Scopes

### Lexikalisches Scoping
Ein *Scope* ist nach Scott die textuelle Region, in der (abstrakt formuliert) die Bindung eines Namens an ein benamtes "Ding" gültig ist [PLP S.112]. Praktisch ist in JavaScript damit der Bereich des Quellcodes gemeint, in der ein Variablen-, Parameter- oder Funktions-Name eindeutig an einen bestimmten Speicherbereich gebunden ist. Es existieren im zwei Allgemeinen zwei *Scoping-Modelle*, die definieren, wie diese Gültigkeitsbereiche bestimmt werden: *statisches* Scoping (oft auch *lexikalisch* bezeichnet) und *dynamisches* Scoping. Im wesentlichen unterscheiden sich beide Modelle durch den Zeitpunkt, an dem Bindungen eindeutig aufgelöst werden können. JavaScript nutzt, wie z.B. auch C und Java, grundsätzlich (siehe Hinweis) das Modell des lexikalischen Scoping. Daher können Scopes bereits durch bloße Analyse des Quellcodes (zur "Compile-Zeit") bestimmt werden. Im Gegensatz dazu, ergeben sich diese beim dynamischen Scoping erst zur Laufzeit.

**Hinweis**: Das lexikalische Scoping gilt deswegen nur "grundsätzlich", da es Konstrukte wie `eval` oder `with` gibt, mit denen das Modell umgangen werden kann. Da die Nutzung dieser Konstrukte in der JavaScript Community jedoch als "bad practice" gilt und daher vermieden werden sollte, wird sie im Rahmen dieser Ausarbeitung jedoch nicht weiter behandelt.

### Funktions- und blockbasierte Scopes
JavaScript kennt zwei Formen von Scopes. Die allgemeinere und historisch ältere Form bilden Scopes, die über Funktionen definiert sind und daher *funktionsbasierte* Scopes genannt werden. Diese binden die Funktionsparameter, sowie alle über das Schlüsselwort `var` deklarierte Variablen und Funktionsdeklarationen, die unmittelbar innerhalb eines Funktionsblocks deklariert werden.

```javascript
function f (a) { // Parameter `a` ist an Scope von `f` gebunden
  // Beginn funktionsbasierter Scope von `f`
  var b = 1;

  // `g` wird innerhalb von `f` deklariert und ist somit an Scope von `f` gebunden
  function g (c) { // Parameter `c` ist an Scope von `g` gebunden
    // Beginn funktionsbasierter Scope von `g`
    console.log(c); // Ausgabe von `c`
    // Ende funktionsbasierter Scope von `g`
  }

  g(b);
  // Ende funktionsbasierter Scope von `f`
}
```

C und Java verwenden, im Gegensatz dazu, rein *blockbasierte* Scopes. Jeder Code-Block erzeugt in diesen Sprachen einen also neuen Scope. JavaScript kennt ebenfalls diese Form von Scope, jedoch werden diese nur bei der Verwendung von Variablen, die über die Schlüsselwörter `let` und `const` deklariert werden, sowie bei `catch` Blöcken, berücksichtigt.

```javascript
function f () {
  { // Beginn blockbasierter Scope
    var a = 1; // a ist innerhalb von ganz `f` sichtbar/gültig
    let b = 2; // b ist nur innerhalb des Blocks sichtbar/gültig
  } // Ende blockbasierter Scope
  console.log(a); // => 1
  console.log(b); // => ReferenceError: b is not defined
}
```

```javascript
function f () {
  function g (a) {
    i = 2; // `i` wird in jedem Schleifendurchlauf auf 2 gesetzt
    console.log(a * i);
  }

  for (var i = 0; i <= 10; i++) { // Endlosschleife
    g(i);
  }
}

f(); // Aufruf von f
```

Das Code-Beispiel [X] demonstriert ein Art von Problem, die entstehen kann, wenn funktionsbasierte Scopes in JavaScript fälschlicherweise als blockbasierte Scopes betrachtet werden und zusätzlich eine Deklaration vergessen wird. Innerhalb von `g` referenziert `i` das in im Schleifenkopf deklarierte `i`, da letzteres aufgrund der `var`-Deklaration innerhalb der gesamten Funktion und nicht nur im Schleifenblock gültig ist. Daher wird `i` in jedem Schleifendurchlauf auf 2 gesetzt, womit die Abbruchbedingung nie erfült wird und eine Endlosschleife entsteht. Es gibt zwei Lösungsansätze, um die intuitiv gewünschte Funktionsweise zu erzielen:
- Das `i` in `g` zusätzlich deklarieren (z.B. `var i = 2`), um das `i` aus der Schleife zu überdecken.
- Das `i` aus der Schleife mittels `let`-Deklaration nur im Schleifenblock gültig sein lassen und das `i` in `g` zusätzlich deklarieren.

### Scope Chain und globaler Scope
Scopes können ineinander geschachtelt sein und bilden damit mehrere Schachtelungs-*Ebenen*. Alle Bezeichner eines Scopes sind auch in den inneren Scopes sichtbar, können jedoch durch Deklarationen in dazwischen liegenden Ebenen *überdeckt* werden. Ausgehend von einem konkreten inneren Scope bilden die umschließenden äußeren Scopes eine sogenannte *Scope Chain*. Der Scope auf der höchsten Ebene ist der *globale Scope*.

Auf der obersten Ebene des Programms deklarierte Variablen und Funktionsbezeichner sind je nach JavaScript Implementierung entweder mit dem *globalen Scope* oder aber mit dem *Modul Scope* assoziiert. Ersteres ist der Fall in allen populären Webbrowsern, letzteres im modulbasierten Node.js.

### Hoisting
Mittels `var` deklarierte Variablen, sowie Funktions-Deklarationen, sind stets im gesamten funktionsbasierten Scope (bzw. globalen Scope) deklariert, unabhängig davon, an welcher Stelle sie deklariert wurden. Funktions-Deklarationen sind sogar initialisiert. In der Praxis bedeutet dies, dass z.B. Funktionen (innerhalb des Scopes) aufgerufen werden können, bevor Sie deklariert worden sind.

Im Vergleich zu C, wo Variablen und Funktionen erst im Code nach der Deklaration referenziert werden können, verhalten sich JavaScript Implementierungen so, als ob die Deklaration am Beginn des Scopes stattgefunden hätte. Dieses Verhalten wird als *Hoisting* bezeichnet.

Zu beachten ist, dass **nur** `var`-Varianblen und Funktions-Deklarationen vom Hoisting betroffen sind. Über `let` und `const` deklarierte Variablen, Funktions-Expressions, sowie Klassen-Deklarationen und -Expressions können erst **nach** ihrer Deklaration genutzt werden.

```javascript
console.log(a); // => "undefined", da durch `var` bereits deklariert, jedoch nicht initialisiert
console.log(b); // => "ReferenceError"
f(); // Funktioniert, da durch Hoisting bereits deklariert und initialisiert
g(); // => "TypeError: g is not a function", da zwar deklariert, jedoch nicht initialisiert

var a = 1;
let b = 2;
function f() {
  console.log('You can call me anywhere');
}
var g = () => console.log('You cannot call me anywhere'); // Arrow-Funktions-Expression
```
