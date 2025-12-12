# PSEUDOCODE: Automatisches Hinzufügen von Back-Buttons zu Wiki-Seiten

## Zweck
Dieser Pseudocode beschreibt den Algorithmus zum automatischen Hinzufügen eines "Zurück"-Buttons zu allen HTML-Dateien in einem Wikipages-Ordner, um zur Hauptseite (Wiki.html) zurückzukehren.

---

## HAUPTALGORITHMUS: AddBackButtonsToWikiPages()

```pseudocode
FUNKTION AddBackButtonsToWikiPages(projektPfad, wikiHauptseite = "Wiki.html")
    
    // ========== PHASE 1: WIKI-SEITEN FINDEN ==========
    wikiSeiten = FindeAlleWikiSeiten(projektPfad)
    
    // ========== PHASE 2: BACK-BUTTON KONFIGURATION ==========
    backButtonConfig = ErstelleBackButtonKonfiguration(wikiHauptseite)
    
    // ========== PHASE 3: SEITEN ANALYSIEREN & MODIFIZIEREN ==========
    ergebnisse = NEU Liste
    
    FÜR JEDE seite IN wikiSeiten
        ergebnis = BearbeiteWikiSeite(seite, backButtonConfig)
        FÜGE ergebnis ZU ergebnisse HINZU
    ENDE FÜR
    
    // ========== PHASE 4: BERICHT ERSTELLEN ==========
    bericht = ErstelleBearbeitungsBericht(ergebnisse)
    
    RETURN bericht
    
ENDE FUNKTION


// ========== PHASE 1: WIKI-SEITEN FINDEN ==========

FUNKTION FindeAlleWikiSeiten(projektPfad)
    wikiSeiten = NEU Liste
    
    // Mögliche Pfade für Wiki-Seiten
    möglichePfade = [
        projektPfad + "/Wikipages",
        projektPfad + "/public/Wikipages",
        projektPfad + "/docs/pages",
        projektPfad + "/wiki/pages",
        projektPfad + "/documentation/pages"
    ]
    
    FÜR JEDEN pfad IN möglichePfade
        WENN ExistiertOrdner(pfad) DANN
            // Finde alle HTML-Dateien rekursiv
            htmlDateien = SucheDateienRekursiv(pfad, "*.html")
            
            FÜR JEDE datei IN htmlDateien
                seitenInfo = NEU Dictionary
                seitenInfo["pfad"] = datei
                seitenInfo["relativerPfadZurHauptseite"] = BerechneRelativenPfad(datei, projektPfad)
                seitenInfo["ordnerTiefe"] = BerechneOrdnerTiefe(datei, pfad)
                seitenInfo["dateiname"] = ExtrahiereNameAusDateiname(datei)
                
                FÜGE seitenInfo ZU wikiSeiten HINZU
            ENDE FÜR
            
            Logge("Gefunden: " + htmlDateien.Länge + " HTML-Dateien in " + pfad)
        ENDE WENN
    ENDE FÜR
    
    WENN wikiSeiten.Länge == 0 DANN
        Logge("WARNUNG: Keine Wiki-Seiten gefunden!")
    SONST
        Logge("GESAMT: " + wikiSeiten.Länge + " Wiki-Seiten gefunden")
    ENDE WENN
    
    RETURN wikiSeiten
    
ENDE FUNKTION


FUNKTION BerechneRelativenPfad(quellDatei, zielBasisPfad)
    ordnerTiefe = BerechneOrdnerTiefe(quellDatei, zielBasisPfad)
    
    WENN ordnerTiefe == 0 DANN
        RETURN "Wiki.html"
    SONST
        // Erstelle "../" für jede Ordnerebene
        relativerPfad = ""
        FÜR i = 1 BIS ordnerTiefe
            relativerPfad += "../"
        ENDE FÜR
        relativerPfad += "Wiki.html"
        
        RETURN relativerPfad
    ENDE WENN
    
ENDE FUNKTION


FUNKTION BerechneOrdnerTiefe(dateipfad, basisPfad)
    relativerPfad = dateipfad.Ersetze(basisPfad, "")
    trennzeichen = ZähleZeichen(relativerPfad, "/") + ZähleZeichen(relativerPfad, "\\")
    
    // Ziehe 1 ab für die Datei selbst
    RETURN Maximum(0, trennzeichen - 1)
    
ENDE FUNKTION


// ========== PHASE 2: BACK-BUTTON KONFIGURATION ==========

FUNKTION ErstelleBackButtonKonfiguration(wikiHauptseite)
    config = NEU Dictionary
    
    // Button-Styles
    config["styles"] = {
        "position": "sticky",
        "top": "0",
        "left": "0",
        "zIndex": "1000",
        "background": "linear-gradient(135deg, #3498db, #2c3e50)",
        "padding": "15px 30px",
        "boxShadow": "0 2px 10px rgba(0,0,0,0.2)",
        "marginBottom": "20px"
    }
    
    // Button-Design
    config["buttonStyles"] = {
        "display": "inline-flex",
        "alignItems": "center",
        "gap": "10px",
        "padding": "10px 20px",
        "background": "rgba(255, 255, 255, 0.2)",
        "color": "white",
        "textDecoration": "none",
        "borderRadius": "5px",
        "border": "2px solid rgba(255, 255, 255, 0.3)",
        "fontSize": "16px",
        "fontWeight": "bold",
        "transition": "all 0.3s ease",
        "cursor": "pointer"
    }
    
    // Hover-Effekt
    config["hoverStyles"] = {
        "background": "rgba(255, 255, 255, 0.3)",
        "borderColor": "rgba(255, 255, 255, 0.5)",
        "transform": "translateX(-5px)"
    }
    
    // Button-Text und Icon
    config["icon"] = "←"
    config["text"] = "Zurück zur Wiki-Übersicht"
    config["ariaLabel"] = "Zurück zur Hauptseite"
    
    // Ziel-Seite
    config["targetPage"] = wikiHauptseite
    
    RETURN config
    
ENDE FUNKTION


// ========== PHASE 3: SEITEN ANALYSIEREN & MODIFIZIEREN ==========

FUNKTION BearbeiteWikiSeite(seitenInfo, backButtonConfig)
    ergebnis = NEU Dictionary
    ergebnis["datei"] = seitenInfo["dateiname"]
    ergebnis["pfad"] = seitenInfo["pfad"]
    
    VERSUCHE
        // 1. HTML-Inhalt laden
        htmlInhalt = LeseDatei(seitenInfo["pfad"])
        
        // 2. Prüfen ob bereits ein Back-Button existiert
        WENN HatBereitsBackButton(htmlInhalt) DANN
            Logge("INFO: " + seitenInfo["dateiname"] + " hat bereits einen Back-Button - wird übersprungen")
            ergebnis["status"] = "übersprungen"
            ergebnis["grund"] = "Back-Button bereits vorhanden"
            RETURN ergebnis
        ENDE WENN
        
        // 3. Relativen Pfad zur Hauptseite berechnen
        relativerPfad = seitenInfo["relativerPfadZurHauptseite"]
        
        // 4. Back-Button HTML generieren
        backButtonHTML = GeneriereBackButtonHTML(backButtonConfig, relativerPfad)
        
        // 5. Back-Button in HTML einfügen
        modifiziertesHTML = FügeBackButtonEin(htmlInhalt, backButtonHTML)
        
        // 6. Backup erstellen (optional)
        WENN BACKUP_AKTIVIERT DANN
            backupPfad = seitenInfo["pfad"] + ".backup"
            SchreibeDatei(backupPfad, htmlInhalt)
        ENDE WENN
        
        // 7. Modifizierte Datei speichern
        SchreibeDatei(seitenInfo["pfad"], modifiziertesHTML)
        
        ergebnis["status"] = "erfolg"
        ergebnis["nachricht"] = "Back-Button erfolgreich hinzugefügt"
        
        Logge("✓ " + seitenInfo["dateiname"] + " - Back-Button hinzugefügt")
        
    FANGE fehler
        ergebnis["status"] = "fehler"
        ergebnis["nachricht"] = fehler.Message
        Logge("✗ FEHLER bei " + seitenInfo["dateiname"] + ": " + fehler.Message)
    ENDE VERSUCHE
    
    RETURN ergebnis
    
ENDE FUNKTION


FUNKTION HatBereitsBackButton(htmlInhalt)
    // Suche nach charakteristischen Merkmalen des Back-Buttons
    suchMuster = [
        "back-to-wiki",
        "wiki-back-button",
        "Zurück zur Wiki",
        "class=\"wiki-navigation\""
    ]
    
    FÜR JEDES muster IN suchMuster
        WENN htmlInhalt.Enthält(muster) DANN
            RETURN WAHR
        ENDE WENN
    ENDE FÜR
    
    RETURN FALSCH
    
ENDE FUNKTION


FUNKTION GeneriereBackButtonHTML(config, relativerPfad)
    html = ""
    
    // CSS für den Back-Button
    html += "<style>\n"
    html += "    .wiki-navigation {\n"
    html += "        position: " + config["styles"]["position"] + ";\n"
    html += "        top: " + config["styles"]["top"] + ";\n"
    html += "        left: " + config["styles"]["left"] + ";\n"
    html += "        z-index: " + config["styles"]["zIndex"] + ";\n"
    html += "        background: " + config["styles"]["background"] + ";\n"
    html += "        padding: " + config["styles"]["padding"] + ";\n"
    html += "        box-shadow: " + config["styles"]["boxShadow"] + ";\n"
    html += "        margin-bottom: " + config["styles"]["marginBottom"] + ";\n"
    html += "        width: 100%;\n"
    html += "        box-sizing: border-box;\n"
    html += "    }\n\n"
    
    html += "    .back-to-wiki {\n"
    html += "        display: " + config["buttonStyles"]["display"] + ";\n"
    html += "        align-items: " + config["buttonStyles"]["alignItems"] + ";\n"
    html += "        gap: " + config["buttonStyles"]["gap"] + ";\n"
    html += "        padding: " + config["buttonStyles"]["padding"] + ";\n"
    html += "        background: " + config["buttonStyles"]["background"] + ";\n"
    html += "        color: " + config["buttonStyles"]["color"] + ";\n"
    html += "        text-decoration: " + config["buttonStyles"]["textDecoration"] + ";\n"
    html += "        border-radius: " + config["buttonStyles"]["borderRadius"] + ";\n"
    html += "        border: " + config["buttonStyles"]["border"] + ";\n"
    html += "        font-size: " + config["buttonStyles"]["fontSize"] + ";\n"
    html += "        font-weight: " + config["buttonStyles"]["fontWeight"] + ";\n"
    html += "        transition: " + config["buttonStyles"]["transition"] + ";\n"
    html += "        cursor: " + config["buttonStyles"]["cursor"] + ";\n"
    html += "    }\n\n"
    
    html += "    .back-to-wiki:hover {\n"
    html += "        background: " + config["hoverStyles"]["background"] + ";\n"
    html += "        border-color: " + config["hoverStyles"]["borderColor"] + ";\n"
    html += "        transform: " + config["hoverStyles"]["transform"] + ";\n"
    html += "    }\n\n"
    
    html += "    .back-icon {\n"
    html += "        font-size: 20px;\n"
    html += "        font-weight: bold;\n"
    html += "    }\n"
    html += "</style>\n\n"
    
    // HTML für den Back-Button
    html += "<nav class=\"wiki-navigation\">\n"
    html += "    <a href=\"" + relativerPfad + "\" class=\"back-to-wiki\" "
    html += "aria-label=\"" + config["ariaLabel"] + "\">\n"
    html += "        <span class=\"back-icon\">" + config["icon"] + "</span>\n"
    html += "        <span>" + config["text"] + "</span>\n"
    html += "    </a>\n"
    html += "</nav>\n\n"
    
    RETURN html
    
ENDE FUNKTION


FUNKTION FügeBackButtonEin(htmlInhalt, backButtonHTML)
    // Strategie 1: Nach <body> einfügen (bevorzugt)
    WENN htmlInhalt.Enthält("<body>") DANN
        position = htmlInhalt.IndexOf("<body>") + 6
        RETURN htmlInhalt.EinfügenAn(position, "\n" + backButtonHTML)
    
    // Strategie 2: Nach <body mit Attributen einfügen
    SONST WENN htmlInhalt.Enthält("<body ") DANN
        regex = SucheRegex(htmlInhalt, "<body[^>]*>")
        WENN regex.Erfolg DANN
            position = regex.Ende
            RETURN htmlInhalt.EinfügenAn(position, "\n" + backButtonHTML)
        ENDE WENN
    
    // Strategie 3: Vor erstem <div>, <header> oder <main> einfügen
    SONST
        tags = ["<header", "<main", "<div", "<section", "<article"]
        
        FÜR JEDES tag IN tags
            WENN htmlInhalt.Enthält(tag) DANN
                position = htmlInhalt.IndexOf(tag)
                RETURN htmlInhalt.EinfügenAn(position, backButtonHTML + "\n")
            ENDE WENN
        ENDE FÜR
    ENDE WENN
    
    // Strategie 4: Fallback - Am Anfang einfügen nach DOCTYPE und <html>
    WENN htmlInhalt.Enthält("<html") DANN
        regex = SucheRegex(htmlInhalt, "<html[^>]*>")
        WENN regex.Erfolg DANN
            position = regex.Ende
            RETURN htmlInhalt.EinfügenAn(position, "\n" + backButtonHTML)
        ENDE WENN
    ENDE WENN
    
    // Wenn nichts funktioniert, am Anfang einfügen
    RETURN backButtonHTML + "\n" + htmlInhalt
    
ENDE FUNKTION


// ========== PHASE 4: BERICHT ERSTELLEN ==========

FUNKTION ErstelleBearbeitungsBericht(ergebnisse)
    bericht = NEU Dictionary
    
    // Statistiken
    gesamt = ergebnisse.Länge
    erfolg = ZähleStatus(ergebnisse, "erfolg")
    übersprungen = ZähleStatus(ergebnisse, "übersprungen")
    fehler = ZähleStatus(ergebnisse, "fehler")
    
    bericht["gesamt"] = gesamt
    bericht["erfolg"] = erfolg
    bericht["übersprungen"] = übersprungen
    bericht["fehler"] = fehler
    bericht["erfolgQuote"] = (erfolg / gesamt * 100) + "%"
    
    // Details
    bericht["details"] = ergebnisse
    
    // Zusammenfassung
    bericht["zusammenfassung"] = FormatiereBerichtZusammenfassung(bericht)
    
    RETURN bericht
    
ENDE FUNKTION


FUNKTION FormatiereBerichtZusammenfassung(bericht)
    zusammenfassung = "\n"
    zusammenfassung += "========================================\n"
    zusammenfassung += "  BACK-BUTTON BEARBEITUNGS-BERICHT\n"
    zusammenfassung += "========================================\n\n"
    
    zusammenfassung += "📊 STATISTIK:\n"
    zusammenfassung += "  • Gesamt verarbeitet: " + bericht["gesamt"] + "\n"
    zusammenfassung += "  • ✓ Erfolgreich:      " + bericht["erfolg"] + "\n"
    zusammenfassung += "  • ⊘ Übersprungen:     " + bericht["übersprungen"] + "\n"
    zusammenfassung += "  • ✗ Fehler:           " + bericht["fehler"] + "\n"
    zusammenfassung += "  • Erfolgsquote:       " + bericht["erfolgQuote"] + "\n\n"
    
    // Fehler-Details (falls vorhanden)
    WENN bericht["fehler"] > 0 DANN
        zusammenfassung += "⚠ FEHLER-DETAILS:\n"
        FÜR JEDES ergebnis IN bericht["details"]
            WENN ergebnis["status"] == "fehler" DANN
                zusammenfassung += "  • " + ergebnis["datei"] + ": " + ergebnis["nachricht"] + "\n"
            ENDE WENN
        ENDE FÜR
        zusammenfassung += "\n"
    ENDE WENN
    
    zusammenfassung += "========================================\n"
    
    RETURN zusammenfassung
    
ENDE FUNKTION


FUNKTION ZähleStatus(ergebnisse, status)
    zähler = 0
    
    FÜR JEDES ergebnis IN ergebnisse
        WENN ergebnis["status"] == status DANN
            zähler += 1
        ENDE WENN
    ENDE FÜR
    
    RETURN zähler
    
ENDE FUNKTION


// ========== ERWEITERTE FUNKTIONEN ==========

FUNKTION EntferneAlleBackButtons(projektPfad)
    // Nützlich zum Zurücksetzen oder Neuerstellen
    wikiSeiten = FindeAlleWikiSeiten(projektPfad)
    
    FÜR JEDE seite IN wikiSeiten
        htmlInhalt = LeseDatei(seite["pfad"])
        
        // Entferne Back-Button HTML und CSS
        modifiziertesHTML = EntferneBackButtonAusHTML(htmlInhalt)
        
        WENN modifiziertesHTML != htmlInhalt DANN
            SchreibeDatei(seite["pfad"], modifiziertesHTML)
            Logge("✓ Back-Button entfernt aus: " + seite["dateiname"])
        ENDE WENN
    ENDE FÜR
    
ENDE FUNKTION


FUNKTION EntferneBackButtonAusHTML(htmlInhalt)
    // Entferne CSS
    htmlInhalt = EntferneZwischen(htmlInhalt, 
        "<style>\n    .wiki-navigation {", 
        "</style>")
    
    // Entferne HTML
    htmlInhalt = EntferneZwischen(htmlInhalt,
        "<nav class=\"wiki-navigation\">",
        "</nav>")
    
    RETURN htmlInhalt
    
ENDE FUNKTION


FUNKTION AktualisiereBackButtons(projektPfad, neueKonfiguration)
    // Entferne alte Buttons
    EntferneAlleBackButtons(projektPfad)
    
    // Füge neue Buttons mit aktualisierter Konfiguration hinzu
    RETURN AddBackButtonsToWikiPages(projektPfad, neueKonfiguration)
    
ENDE FUNKTION


// ========== KONFIGURIERBARE OPTIONEN ==========

KONSTANTE BACKUP_AKTIVIERT = WAHR
KONSTANTE LOG_LEVEL = "INFO"  // DEBUG, INFO, WARNING, ERROR
KONSTANTE DRY_RUN = FALSCH    // Wenn WAHR, keine Änderungen schreiben


// ========== HAUPTPROGRAMM EINSTIEGSPUNKT ==========

HAUPTPROGRAMM
    
    // Konfiguration
    projektPfad = ARGUMENT[0] ODER AktuellesVerzeichnis()
    wikiHauptseite = ARGUMENT[1] ODER "Wiki.html"
    modus = ARGUMENT[2] ODER "add"  // add, remove, update
    
    Logge("========================================")
    Logge("Back-Button Automatisierung gestartet")
    Logge("Projekt: " + projektPfad)
    Logge("Wiki-Hauptseite: " + wikiHauptseite)
    Logge("Modus: " + modus)
    Logge("========================================\n")
    
    // Führe gewünschte Aktion aus
    WÄHLE modus
        FALL "add":
            bericht = AddBackButtonsToWikiPages(projektPfad, wikiHauptseite)
        
        FALL "remove":
            bericht = EntferneAlleBackButtons(projektPfad)
        
        FALL "update":
            neueConfig = ErstelleBackButtonKonfiguration(wikiHauptseite)
            bericht = AktualisiereBackButtons(projektPfad, neueConfig)
        
        DEFAULT:
            Logge("FEHLER: Ungültiger Modus: " + modus)
            BEENDE_MIT_FEHLER()
    ENDE WÄHLE
    
    // Zeige Bericht
    Logge(bericht["zusammenfassung"])
    
    WENN bericht["fehler"] == 0 DANN
        Logge("✓ Alle Operationen erfolgreich abgeschlossen!")
        BEENDE_MIT_SUCCESS()
    SONST
        Logge("⚠ Einige Operationen sind fehlgeschlagen. Siehe Details oben.")
        BEENDE_MIT_WARNUNG()
    ENDE WENN
    
ENDE HAUPTPROGRAMM
```

---

## VERWENDUNG

### Als Copilot-Anweisung:
```
"Füge allen HTML-Seiten im Wikipages-Ordner automatisch einen 
Back-Button hinzu, der zurück zu Wiki.html führt. Verwende dazu 
den AutoAddBackButtonToWikiPages Pseudocode."
```

### Als Automatisierungs-Script:
```bash
# Back-Buttons hinzufügen
node addBackButtons.js /pfad/zum/projekt Wiki.html add

# Back-Buttons entfernen
node addBackButtons.js /pfad/zum/projekt Wiki.html remove

# Back-Buttons aktualisieren
node addBackButtons.js /pfad/zum/projekt Wiki.html update

# PowerShell Beispiel
.\AddBackButtons.ps1 -ProjectPath "C:\Projects\Wiki" -Mode "add"
```

---

## ANPASSUNGSMÖGLICHKEITEN

### Button-Design ändern:
Die Funktion `ErstelleBackButtonKonfiguration()` enthält alle Style-Definitionen:
- Farben anpassen
- Position ändern (sticky, fixed, static)
- Icon/Text ändern
- Hover-Effekte modifizieren

### Verschiedene Button-Styles für verschiedene Seiten:
```pseudocode
FUNKTION BestimmeButtonStyle(seitenTyp)
    WÄHLE seitenTyp
        FALL "api":
            RETURN API_BUTTON_CONFIG
        FALL "tutorial":
            RETURN TUTORIAL_BUTTON_CONFIG
        DEFAULT:
            RETURN DEFAULT_BUTTON_CONFIG
    ENDE WÄHLE
ENDE FUNKTION
```

### Mehrere Navigation-Buttons:
```pseudocode
// Breadcrumb-Navigation
buttons = [
    {"text": "Home", "link": "../Wiki.html"},
    {"text": "API", "link": "../api/index.html"},
    {"text": "Diese Seite", "link": "#"}
]
```

---

## SICHERHEITSHINWEISE

1. **Backup-Funktion**: Immer aktiviert, erstellt .backup Dateien vor Änderungen
2. **Validierung**: Prüft ob Back-Button bereits existiert
3. **Fehlerbehandlung**: Try-Catch für jede Datei-Operation
4. **Dry-Run Modus**: Testet Änderungen ohne zu schreiben

---

## ERWEITERTE FEATURES

### Responsive Back-Button:
- Passt sich an mobile Geräte an
- Sticky Position für einfache Navigation
- Accessible (ARIA-Label)

### Multi-Level Navigation:
- Berechnet automatisch relativen Pfad basierend auf Ordnertiefe
- Funktioniert mit verschachtelten Wiki-Strukturen

### Batch-Processing:
- Verarbeitet alle Dateien auf einmal
- Detaillierter Bericht mit Statistiken
- Fehler-Protokollierung

---

*Pseudocode erstellt für automatische Back-Button-Integration*  
*Version: 1.0 | Datum: 12. Dezember 2025*
