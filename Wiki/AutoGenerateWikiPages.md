# WIKI-GENERIERUNG VORLAGE
## Anweisungen für GitHub Copilot

### PHASE 1: CODE-ANALYSE
Wenn der Benutzer Code einfügt, führe folgende Schritte aus:

1. **Code-Struktur identifizieren**
   - BESTIMME: Programmiersprache (C#, JavaScript, Python, etc.)
   - ERKENNE: Klassen, Methoden, Eigenschaften, Konstanten
   - IDENTIFIZIERE: Namespaces, using-Direktiven, Imports
   - PRÜFE: Zugriffsmodifikatoren (public, private, internal, protected)
   - ERFASSE: Parameter, Rückgabewerte, Datentypen

2. **XML-Dokumentation extrahieren**
   - SUCHE: Alle /// XML-Kommentare
   - PARSE: <summary>, <param>, <returns>, <remarks> Tags
   - SPEICHERE: Beschreibungstexte für spätere Verwendung

3. **Funktionalität verstehen**
   - ANALYSIERE: Was macht jede Methode/Funktion?
   - IDENTIFIZIERE: Abhängigkeiten und verwendete Bibliotheken
   - ERKENNE: Design-Patterns und Architekturmuster
   - BESTIMME: Sicherheitsrelevante Aspekte

   
### PHASE 2: KOMMENTARE GENERIEREN
Erstelle vereinfachte Kommentare nach folgenden Regeln:

**REGEL 1: Kommentar-Stil**
WENN Klasse DANN: // [Kurze Beschreibung des Zwecks der Klasse]
WENN Konstante DANN: // [Name]: [Wert] - [Erklärung des Zwecks]
WENN Methode DANN: // [Was die Methode tut] - [Wann sie verwendet wird]
WENN komplexer Code-Block DANN: // Inline-Kommentar: [Erklärung der spezifischen Zeile/des Blocks]
**REGEL 2: Kommentar-Inhalt**
- VERWENDE: Einfache, klare Sprache
- VERMEIDE: XML-Tags (<summary>, <param>, etc.)
- FOKUS: Praktischer Nutzen und Verwendungszweck
- LÄNGE: Maximal 80 Zeichen pro Zeile
- SPRACHE: Gleiche Sprache wie Original-Kommentare

**REGEL 3: Kommentar-Platzierung**
// Methoden-Kommentar ÜBER der Methodensignatur
public void MeineMethode()
{
    // Inline-Kommentar ÜBER oder NACH wichtiger Code-Zeile
    var result = ComplexOperation();  // Was hier passiert
}


### PHASE 3: HTML-DOKUMENTATION ERSTELLEN
Generiere HTML nach diesem Template:

**STRUKTUR:**

<!DOCTYPE html> <html lang="de"> <head> <meta charset="UTF-8"> <title>[KLASSENNAME] - API Dokumentation</title> <style>[CSS VON PASSWORDHELPER.HTML KOPIEREN]</style> </head> <body> <div class="container"> <!-- SEKTION 1: HEADER --> <h1>[KLASSENNAME] [KLASSENTYP]</h1>

  <!-- SEKTION 2: BESCHREIBUNG -->
    <div class="class-description">
        <p><strong>Namespace:</strong> [NAMESPACE]</p>
        <p><strong>Assembly:</strong> [ASSEMBLY]</p>
        <p>[KLASSENBESCHREIBUNG AUS XML ODER KOMMENTAR]</p>
    </div>
    
    <!-- SEKTION 3: WICHTIGE HINWEISE (optional) -->
    WENN (Sicherheit ODER Performance ODER Besonderheit) DANN:
    <div class="security-note">
        [RELEVANTE HINWEISE]
    </div>
    
    <!-- SEKTION 4: INHALTSVERZEICHNIS -->
    <div class="toc">
        <h3>Inhaltsverzeichnis</h3>
        <ul>
            WENN (Konstanten vorhanden) DANN: <li><a href="#constants">Konstanten</a></li>
            WENN (Properties vorhanden) DANN: <li><a href="#properties">Eigenschaften</a></li>
            WENN (Methoden vorhanden) DANN: <li><a href="#methods">Methoden</a></li>
            WENN (Events vorhanden) DANN: <li><a href="#events">Events</a></li>
            <li><a href="#full-source">Quellcode auf GitHub</a></li>
        </ul>
    </div>
    
    <!-- SEKTION 5: KONSTANTEN -->
    FÜR JEDE Konstante:
    <div class="field">
        <div>
            [BADGES: private/public, const, static]
        </div>
        <h3>[KONSTANTENNAME]</h3>
        <div class="signature">[SIGNATUR]</div>
        <p>[BESCHREIBUNG]</p>
    </div>
    
    <!-- SEKTION 6: EIGENSCHAFTEN -->
    FÜR JEDE Property:
    <div class="field">
        <div>
            [BADGES: Zugriffsmodifikatoren]
        </div>
        <h3>[PROPERTYNAME]</h3>
        <div class="signature">[SIGNATUR]</div>
        <p>[BESCHREIBUNG]</p>
    </div>
    
    <!-- SEKTION 7: METHODEN -->
    FÜR JEDE Methode:
    <div class="method">
        <div>
            [BADGES: Zugriffsmodifikatoren]
        </div>
        <h3 id="method-[NAME]">[METHODENNAME]</h3>
        <div class="signature">[SIGNATUR]</div>
        <p>[BESCHREIBUNG]</p>
        
        WENN (Parameter vorhanden):
        <dl class="parameter-list">
            <dt>Parameter:</dt>
            FÜR JEDEN Parameter:
            <dd><code>[NAME]</code> [TYP] - [BESCHREIBUNG]</dd>
        </dl>
        
        WENN (Rückgabewert != void):
        <dl class="returns">
            <dt>Rückgabewert:</dt>
            <dd>[TYP] - [BESCHREIBUNG]</dd>
        </dl>
        
        WENN (Verwendungsbeispiel sinnvoll):
        <p><strong>Verwendungsbeispiel:</strong></p>
        <div class="signature">[CODE-BEISPIEL]</div>
    </div>
    
    <!-- SEKTION 8: TECHNISCHE DETAILS (optional) -->
    WENN (besondere technische Aspekte):
    <h2>Technische Details</h2>
    [DETAILS ZU IMPLEMENTIERUNG, ALGORITHMEN, PATTERNS]
    
    <!-- SEKTION 9: ABHÄNGIGKEITEN -->
    WENN (externe Abhängigkeiten):
    <h2>Abhängigkeiten</h2>
    <ul>
        FÜR JEDE Abhängigkeit:
        <li><code>[NAMESPACE]</code> - [ZWECK]</li>
    </ul>
    
    <!-- SEKTION 10: QUELLCODE-LINK -->
    <div class="code-container" id="full-source">
        <h2>📄 Vollständiger Quellcode</h2>
        <p>Der vollständige Quellcode ist im GitHub-Repository verfügbar:</p>
        <div class="github-link">
            <a href="https://github.com/Plessu/Elektrogrosshandel/blob/[BRANCH]/[DATEIPFAD]" 
               target="_blank" 
               class="btn-github">
                🔗 Quellcode auf GitHub ansehen
            </a>
        </div>
        <p><small>Branch: [BRANCH] | Datei: [DATEIPFAD]</small></p>
    </div>
    
    <!-- SEKTION 11: FOOTER -->
    <footer>
        <p>Generiert für: [PROJEKTNAME]</p>
        <p>Dokumentationsstand: [DATUM]</p>
    </footer>
</div>
</body> 
</html>

### PHASE 4: SYNTAX-HIGHLIGHTING
Wende folgende Farben im Code-Block an:

**C# SYNTAX:**
Kommentare:     color: #6a9955;  // Grün Keywords:       color: #569cd6;  // Blau (if, else, return, class, etc.) Klassennamen:   color: #4ec9b0;  // Türkis Methodennamen:  color: #dcdcaa;  // Gelb Typen:          color: #4ec9b0;  // Türkis (int, string, bool, etc.) Strings:        color: #ce9178;  // Orange Zahlen:         color: #b5cea8;  // Hellgrün


**JAVASCRIPT/TYPESCRIPT SYNTAX:**
Kommentare:     color: #6a9955;  // Schlüsselwörter:      color: #569cd6;  // Funktionen:   color: #4ec9b0;  // Variablen:        color: #dcdcaa;  // Strings:        color: #ce9178;  // Zahlen:         color: #b5cea8;  // Booleans:         color: #4ec9b0;  // Null:          color: #4ec9b0;  // Undefined:       color: #4ec9b0;  // Objekte:         color: #4ec9b0;  // Arrays:         color: #4ec9b0;  // Reguläre Ausdrücke:     color: #4ec9b0;  // Klassen:         color: #4ec9b0;  // Module:         color: #4ec9b0;  // Namensräume:    color: #4ec9b0;  // Router:         color: #4ec9b0;  // Instanzen:        color: #4ec9b0;  // Promise:         color: #4ec9b0;  // Async/Await:    color: #4ec9b0;  // Spread Operator:    color: #4ec9b0;  // Rest Parameter:  color: #4ec9b0;  // Destrukturierung:  color: #4ec9b0;  // Template Literals:  color: #4ec9b0;  // Optional Chaining:  color: #4ec9b0;  // Nullish Coalescing:  color: #4ec9b0;  // Static Block:   color: #4ec9b0;  // Abkürzungen:      color: #4ec9b0;  // Getter/Setter:    color: #4ec9b0;  // Decorator:     color: #4ec9b0;  // Operatorüberladung:   color: #4ec9b0;  //



**PYTHON SYNTAX:**
Kommentare:     color: #6a9955;  // Schlüsselwörter:      color: #569cd6;  // Funktionen:   color: #4ec9b0;  // Variablen:        color: #dcdcaa;  // Strings:        color: #ce9178;  // Zahlen:         color: #b5cea8;  // Booleans:         color: #4ec9b0;  // None:          color: #4ec9b0;  // Lambda:         color: #4ec9b0;  // Dekoratoren:    color: #4ec9b0;  // Konstanten:      color: #4ec9b0;  // Listen:         color: #4ec9b0;  // Tupel:         color: #4ec9b0;  // Mengen:         color: #4ec9b0;  // Dictionaries:    color: #4ec9b0;  // Fstrings:        color: #4ec9b0;  // Typ-Anmerkungen:    color: #4ec9b0;  // Hilfsfunktionen:    color: #4ec9b0;  // List Comprehensions:   color: #4ec9b0;  // Generator Expressions:    color: #4ec9b0;  //

**BADGES:**
WENN public     DANN: <span class="badge badge-public">public</span> WENN private    DANN: <span class="badge badge-private">private</span> WENN protected  DANN: <span class="badge badge-private">protected</span> WENN internal   DANN: <span class="badge badge-private">internal</span> WENN static     DANN: <span class="badge badge-static">static</span> WENN const      DANN: <span class="badge badge-const">const</span> WENN abstract   DANN: <span class="badge badge-static">abstract</span> WENN virtual    DANN: <span class="badge badge-static">virtual</span> WENN override   DANN: <span class="badge badge-static">override</span> WENN sealed     DANN: <span class="badge badge-const">sealed</span> WENN async      DANN: <span class="badge badge-public">async</span>

### QUALITÄTSKRITERIEN
Stelle sicher, dass:
- ✅ Alle Kommentare verständlich und präzise sind
- ✅ HTML valide und vollständig ist
- ✅ CSS von PasswordHelper.html übernommen wurde
- ✅ Syntax-Highlighting korrekt angewendet ist
- ✅ Alle Sektionen logisch strukturiert sind
- ✅ Inhaltsverzeichnis alle Ankerpunkte enthält
- ✅ Code-Beispiele funktionsfähig sind
- ✅ Badges korrekt zugeordnet sind
- ✅ GitHub-Links korrekt formatiert und funktionsfähig sind

### GITHUB-LINK GENERIERUNG
Beim Erstellen des GitHub-Links:
- Repository: https://github.com/Plessu/Elektrogrosshandel
- Branch: Aktuellen Branch ermitteln (z.B. Webentwicklung)
- Dateipfad: Relativen Pfad zur Datei verwenden (z.B. Elektrogrrosshandel/Functions/PasswordHelper.cs)
- Format: https://github.com/Plessu/Elektrogrosshandel/blob/[BRANCH]/[DATEIPFAD]

### BEISPIEL-ANWENDUNG

BENUTZER:

Erstelle Wiki-Dokumentation für folgenden Code:
[DEIN CODE HIER]




