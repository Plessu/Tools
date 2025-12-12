# PSEUDOCODE: Automatische Wiki Index.html Generierung

## Zweck
Dieser Pseudocode beschreibt den Algorithmus zur automatischen Generierung einer Wiki.html für ein Projekt-Wiki basierend auf vorhandenen Dateien und Projektstruktur.

---

## HAUPTALGORITHMUS: GenerateWikiIndex()

```pseudocode
FUNKTION GenerateWikiIndex(projektPfad)
    
    // ========== PHASE 1: PROJEKTANALYSE ==========
    projektInfo = AnalysiereProject(projektPfad)
    
    // ========== PHASE 2: DATEIEN SCANNEN ==========
    verfügbareSeiten = ScanneWikiSeiten(projektPfad)
    
    // ========== PHASE 3: STRUKTUR ERSTELLEN ==========
    wikiStruktur = ErstelleWikiStruktur(projektInfo, verfügbareSeiten)
    
    // ========== PHASE 4: HTML GENERIEREN ==========
    htmlInhalt = GeneriereHTML(wikiStruktur)
    
    // ========== PHASE 5: DATEI SCHREIBEN ==========
    SchreibeDatei(projektPfad + "/index.html", htmlInhalt)
    
    RETURN "Wiki Index erfolgreich erstellt"
    
ENDE FUNKTION


// ========== PHASE 1: PROJEKTANALYSE ==========

FUNKTION AnalysiereProject(projektPfad)
    projektInfo = NEU Dictionary
    
    // 1.1 Projektname ermitteln
    WENN ExistiertDatei(projektPfad + "/package.json") DANN
        packageJson = LeseJSON(projektPfad + "/package.json")
        projektInfo["name"] = packageJson["name"]
        projektInfo["version"] = packageJson["version"]
        projektInfo["description"] = packageJson["description"]
        projektInfo["typ"] = "JavaScript/Node.js"
    
    SONST WENN ExistiertDatei(projektPfad + "/*.csproj") DANN
        csprojDatei = FindeDatei(projektPfad, "*.csproj")
        projektInfo["name"] = ExtrahiereNameAusDateiname(csprojDatei)
        projektInfo["typ"] = "C#/.NET"
        projektInfo["version"] = ExtrahiereVersionAusCsproj(csprojDatei)
    
    SONST WENN ExistiertDatei(projektPfad + "/pom.xml") DANN
        pomXml = LeseXML(projektPfad + "/pom.xml")
        projektInfo["name"] = pomXml["artifactId"]
        projektInfo["version"] = pomXml["version"]
        projektInfo["typ"] = "Java/Maven"
    
    SONST WENN ExistiertDatei(projektPfad + "/requirements.txt") DANN
        projektInfo["name"] = ExtrahiereOrdnerName(projektPfad)
        projektInfo["typ"] = "Python"
        projektInfo["version"] = "1.0.0"
    
    SONST
        // Fallback: Ordnername verwenden
        projektInfo["name"] = ExtrahiereOrdnerName(projektPfad)
        projektInfo["typ"] = "Generic"
        projektInfo["version"] = "1.0.0"
    ENDE WENN
    
    // 1.2 README analysieren (falls vorhanden)
    WENN ExistiertDatei(projektPfad + "/README.md") DANN
        readmeInhalt = LeseDatei(projektPfad + "/README.md")
        projektInfo["readme"] = ParseMarkdown(readmeInhalt)
        projektInfo["features"] = ExtrahiereFeaturesAusReadme(readmeInhalt)
    ENDE WENN
    
    // 1.3 Git-Informationen
    WENN IstGitRepository(projektPfad) DANN
        projektInfo["repository"] = HoleGitRemoteURL(projektPfad)
        projektInfo["branch"] = HoleAktuellenBranch(projektPfad)
    ENDE WENN
    
    RETURN projektInfo
    
ENDE FUNKTION


// ========== PHASE 2: DATEIEN SCANNEN ==========

FUNKTION ScanneWikiSeiten(projektPfad)
    verfügbareSeiten = NEU Liste
    wikiPfade = [
        projektPfad + "/docs",
        projektPfad + "/documentation",
        projektPfad + "/wiki",
        projektPfad + "/Wikipages",
        projektPfad + "/public/Wikipages"
    ]
    
    FÜR JEDEN pfad IN wikiPfade
        WENN ExistiertOrdner(pfad) DANN
            dateien = SucheDateienRekursiv(pfad, ["*.html", "*.md"])
            
            FÜR JEDE datei IN dateien
                seitenInfo = NEU Dictionary
                seitenInfo["pfad"] = datei
                seitenInfo["relativerPfad"] = ErstelleRelativenPfad(projektPfad, datei)
                seitenInfo["name"] = ExtrahiereNameAusDateiname(datei)
                seitenInfo["typ"] = BestimmeDateiTyp(datei)
                seitenInfo["kategorie"] = BestimmeKategorie(datei)
                seitenInfo["priorität"] = BestimmePriorität(datei)
                seitenInfo["titel"] = ExtrahiereTitelAusDatei(datei)
                seitenInfo["beschreibung"] = ExtrahiereBeschreibungAusDatei(datei)
                seitenInfo["lastModified"] = HoleDateiÄnderungsdatum(datei)
                
                FÜGE seitenInfo ZU verfügbareSeiten HINZU
            ENDE FÜR
        ENDE WENN
    ENDE FÜR
    
    // Seiten sortieren nach Priorität und Namen
    SORTIERE verfügbareSeiten NACH (priorität DESC, name ASC)
    
    RETURN verfügbareSeiten
    
ENDE FUNKTION


// ========== HILFS-FUNKTIONEN FÜR KATEGORISIERUNG ==========

FUNKTION BestimmeKategorie(dateipfad)
    dateiname = ExtrahiereNameAusDateiname(dateipfad).ToLower()
    pfad = dateipfad.ToLower()
    
    // API Dokumentation
    WENN pfad.Enthält("api") ODER 
         pfad.Enthält("reference") ODER
         dateiname.Enthält("helper") ODER
         dateiname.Enthält("service") ODER
         dateiname.Enthält("controller") DANN
        RETURN "api-docs"
    
    // Tutorials & Guides
    SONST WENN pfad.Enthält("tutorial") ODER
              pfad.Enthält("guide") ODER
              dateiname.Enthält("howto") ODER
              dateiname.StartsWith("getting") DANN
        RETURN "tutorials"
    
    // Architektur
    SONST WENN pfad.Enthält("architecture") ODER
              dateiname.Enthält("design") ODER
              dateiname.Enthält("structure") ODER
              dateiname.Enthält("model") DANN
        RETURN "architecture"
    
    // Guidelines & Standards
    SONST WENN pfad.Enthält("guideline") ODER
              dateiname.Enthält("standard") ODER
              dateiname.Enthält("convention") ODER
              dateiname.Enthält("best-practice") DANN
        RETURN "guidelines"
    
    // Default
    SONST
        RETURN "general"
    ENDE WENN
    
ENDE FUNKTION


FUNKTION BestimmePriorität(dateipfad)
    dateiname = ExtrahiereNameAusDateiname(dateipfad).ToLower()
    
    // Höchste Priorität
    WENN dateiname.Enthält("readme") ODER
         dateiname.Enthält("getting-started") ODER
         dateiname.Enthält("quickstart") DANN
        RETURN 100
    
    // Hohe Priorität
    SONST WENN dateiname.Enthält("installation") ODER
              dateiname.Enthält("setup") ODER
              dateiname.Enthält("config") DANN
        RETURN 80
    
    // Mittlere Priorität
    SONST WENN dateiname.Enthält("api") ODER
              dateiname.Enthält("reference") DANN
        RETURN 60
    
    // Normale Priorität
    SONST
        RETURN 50
    ENDE WENN
    
ENDE FUNKTION


FUNKTION ExtrahiereTitelAusDatei(dateipfad)
    inhalt = LeseDatei(dateipfad)
    
    // Für HTML-Dateien
    WENN dateipfad.EndetMit(".html") DANN
        match = SucheRegex(inhalt, "<title>(.*?)</title>")
        WENN match.Erfolg DANN
            RETURN match.Gruppe(1)
        ENDE WENN
        
        match = SucheRegex(inhalt, "<h1[^>]*>(.*?)</h1>")
        WENN match.Erfolg DANN
            RETURN EntferneHTMLTags(match.Gruppe(1))
        ENDE WENN
    
    // Für Markdown-Dateien
    SONST WENN dateipfad.EndetMit(".md") DANN
        zeilen = SplitteZeilen(inhalt)
        FÜR JEDE zeile IN zeilen
            WENN zeile.StartsWith("# ") DANN
                RETURN zeile.Substring(2).Trim()
            ENDE WENN
        ENDE FÜR
    ENDE WENN
    
    // Fallback: Dateiname verwenden
    RETURN VerschönereDateiname(ExtrahiereNameAusDateiname(dateipfad))
    
ENDE FUNKTION


FUNKTION ExtrahiereBeschreibungAusDatei(dateipfad)
    inhalt = LeseDatei(dateipfad)
    
    // Für HTML-Dateien
    WENN dateipfad.EndetMit(".html") DANN
        match = SucheRegex(inhalt, '<meta name="description" content="(.*?)"')
        WENN match.Erfolg DANN
            RETURN match.Gruppe(1)
        ENDE WENN
        
        match = SucheRegex(inhalt, '<p[^>]*>(.*?)</p>')
        WENN match.Erfolg DANN
            text = EntferneHTMLTags(match.Gruppe(1))
            RETURN KürzeText(text, 150)
        ENDE WENN
    
    // Für Markdown-Dateien
    SONST WENN dateipfad.EndetMit(".md") DANN
        zeilen = SplitteZeilen(inhalt)
        inÜberschrift = FALSCH
        
        FÜR JEDE zeile IN zeilen
            WENN zeile.StartsWith("#") DANN
                inÜberschrift = WAHR
                WEITER
            ENDE WENN
            
            WENN inÜberschrift UND zeile.Trim() != "" UND NICHT zeile.StartsWith("#") DANN
                RETURN KürzeText(zeile, 150)
            ENDE WENN
        ENDE FÜR
    ENDE WENN
    
    RETURN "Keine Beschreibung verfügbar"
    
ENDE FUNKTION


// ========== PHASE 3: STRUKTUR ERSTELLEN ==========

FUNKTION ErstelleWikiStruktur(projektInfo, verfügbareSeiten)
    struktur = NEU Dictionary
    
    // 3.1 Grundinformationen
    struktur["projektName"] = projektInfo["name"]
    struktur["projektTyp"] = projektInfo["typ"]
    struktur["version"] = projektInfo["version"]
    struktur["beschreibung"] = projektInfo.HoleWennVorhanden("description", "Projekt Dokumentation")
    
    // 3.2 Navigations-Abschnitte
    struktur["sections"] = [
        {
            "id": "home",
            "titel": "Start",
            "icon": "🏠",
            "seiten": []
        },
        {
            "id": "getting-started",
            "titel": "Erste Schritte",
            "icon": "🚀",
            "seiten": FilterSeiten(verfügbareSeiten, "tutorials", priorität >= 70)
        },
        {
            "id": "documentation",
            "titel": "Dokumentation",
            "icon": "📖",
            "seiten": FilterSeiten(verfügbareSeiten, ["architecture", "general"])
        },
        {
            "id": "api",
            "titel": "API Referenz",
            "icon": "🔧",
            "seiten": FilterSeiten(verfügbareSeiten, "api-docs")
        },
        {
            "id": "guides",
            "titel": "Anleitungen",
            "icon": "📝",
            "seiten": FilterSeiten(verfügbareSeiten, "tutorials", priorität < 70)
        },
        {
            "id": "resources",
            "titel": "Ressourcen",
            "icon": "💡",
            "seiten": FilterSeiten(verfügbareSeiten, "guidelines")
        }
    ]
    
    // 3.3 Quick Links (Top 3 wichtigste Seiten)
    struktur["quickLinks"] = HoleTop(verfügbareSeiten, 3, SORTIERT_NACH: "priorität")
    
    // 3.4 Theme/Farben (basierend auf Projekttyp)
    struktur["theme"] = BestimmeTheme(projektInfo["typ"])
    
    RETURN struktur
    
ENDE FUNKTION


FUNKTION BestimmeTheme(projektTyp)
    theme = NEU Dictionary
    
    WÄHLE projektTyp
        FALL "C#/.NET":
            theme["primary"] = "#512BD4"    // Lila (.NET)
            theme["secondary"] = "#2C3E50"
            theme["accent"] = "#E74C3C"
        
        FALL "JavaScript/Node.js":
            theme["primary"] = "#F7DF1E"    // Gelb (JavaScript)
            theme["secondary"] = "#323330"
            theme["accent"] = "#68A063"     // Grün (Node.js)
        
        FALL "Python":
            theme["primary"] = "#3776AB"    // Blau (Python)
            theme["secondary"] = "#FFD43B"
            theme["accent"] = "#306998"
        
        FALL "Java/Maven":
            theme["primary"] = "#007396"    // Java Blau
            theme["secondary"] = "#F89820"
            theme["accent"] = "#5382A1"
        
        DEFAULT:
            theme["primary"] = "#3498db"    // Standard Blau
            theme["secondary"] = "#2c3e50"
            theme["accent"] = "#e74c3c"
    ENDE WÄHLE
    
    RETURN theme
    
ENDE FUNKTION


// ========== PHASE 4: HTML GENERIEREN ==========

FUNKTION GeneriereHTML(struktur)
    html = ""
    
    // 4.1 DOCTYPE und Head
    html += GeneriereHTMLHead(struktur)
    
    // 4.2 Header
    html += GeneriereHeader(struktur)
    
    // 4.3 Navigation
    html += GeneriereNavigation(struktur["sections"])
    
    // 4.4 Hauptinhalt
    html += '<div class="container">'
    
    // Welcome Section
    html += GeneriereWelcomeSection(struktur)
    
    // Alle Sections
    FÜR JEDE section IN struktur["sections"]
        WENN section["id"] != "home" UND section["seiten"].Länge > 0 DANN
            html += GeneriereSection(section)
        ENDE WENN
    ENDE FÜR
    
    html += '</div>'
    
    // 4.5 Footer
    html += GeneriereFooter(struktur)
    
    // 4.6 JavaScript
    html += GeneriereJavaScript()
    
    // 4.7 Schließende Tags
    html += '</body></html>'
    
    RETURN html
    
ENDE FUNKTION


FUNKTION GeneriereHTMLHead(struktur)
    css = GeneriereDynamischesCSS(struktur["theme"])
    
    html = '<!DOCTYPE html>\n'
    html += '<html lang="de">\n'
    html += '<head>\n'
    html += '    <meta charset="UTF-8">\n'
    html += '    <meta name="viewport" content="width=device-width, initial-scale=1.0">\n'
    html += '    <title>' + struktur["projektName"] + ' - Wiki</title>\n'
    html += '    <meta name="description" content="' + struktur["beschreibung"] + '">\n'
    html += '    <style>\n' + css + '\n    </style>\n'
    html += '</head>\n'
    html += '<body>\n'
    
    RETURN html
    
ENDE FUNKTION


FUNKTION GeneriereDynamischesCSS(theme)
    css = ':root {\n'
    css += '    --primary-color: ' + theme["primary"] + ';\n'
    css += '    --secondary-color: ' + theme["secondary"] + ';\n'
    css += '    --accent-color: ' + theme["accent"] + ';\n'
    css += '    --background-color: #f5f5f5;\n'
    css += '    --card-background: #ffffff;\n'
    css += '    --text-color: #333;\n'
    css += '    --border-color: #dee2e6;\n'
    css += '    --hover-color: #ecf0f1;\n'
    css += '}\n\n'
    
    // Füge restliche CSS-Regeln hinzu (wie im Template)
    css += LADE_CSS_TEMPLATE()
    
    RETURN css
    
ENDE FUNKTION


FUNKTION GeneriereHeader(struktur)
    html = '<header>\n'
    html += '    <div class="header-content">\n'
    html += '        <h1>📚 ' + EscapeHTML(struktur["projektName"]) + '</h1>\n'
    html += '        <p class="subtitle">' + EscapeHTML(struktur["beschreibung"]) + '</p>\n'
    
    WENN struktur.EnthältSchlüssel("version") DANN
        html += '        <p class="version">Version: ' + struktur["version"] + '</p>\n'
    ENDE WENN
    
    html += '    </div>\n'
    html += '</header>\n'
    
    RETURN html
    
ENDE FUNKTION


FUNKTION GeneriereNavigation(sections)
    html = '<nav>\n'
    html += '    <div class="nav-content">\n'
    
    FÜR JEDE section IN sections
        WENN section["seiten"].Länge > 0 ODER section["id"] == "home" DANN
            html += '        <a href="#' + section["id"] + '">'
            html += section["icon"] + ' ' + section["titel"]
            html += '</a>\n'
        ENDE WENN
    ENDE FÜR
    
    html += '    </div>\n'
    html += '</nav>\n'
    
    RETURN html
    
ENDE FUNKTION


FUNKTION GeneriereSection(section)
    html = '<section id="' + section["id"] + '">\n'
    html += '    <h2 style="margin-bottom: 1.5rem; color: var(--secondary-color);">'
    html += section["icon"] + ' ' + section["titel"]
    html += '</h2>\n'
    
    WENN section["seiten"].Länge > 0 DANN
        
        // Entscheide zwischen Card-Layout und Listen-Layout
        WENN section["id"] == "api" DANN
            html += GeneriereSeitenListe(section["seiten"])
        SONST
            html += GeneriereSeitenCards(section["seiten"], section["id"])
        ENDE WENN
        
    SONST
        html += '    <p style="color: #666;">Keine Seiten in dieser Kategorie.</p>\n'
    ENDE WENN
    
    html += '</section>\n'
    
    RETURN html
    
ENDE FUNKTION


FUNKTION GeneriereSeitenCards(seiten, kategorie)
    html = '    <div class="cards-grid">\n'
    
    FÜR JEDE seite IN seiten
        cssKlasse = "card " + seite["kategorie"]
        
        html += '        <div class="' + cssKlasse + '">\n'
        html += '            <h3>' + EscapeHTML(seite["titel"]) + '</h3>\n'
        html += '            <p>' + EscapeHTML(seite["beschreibung"]) + '</p>\n'
        html += '            <div class="card-links">\n'
        html += '                <a href="' + seite["relativerPfad"] + '">Öffnen</a>\n'
        html += '            </div>\n'
        html += '        </div>\n'
    ENDE FÜR
    
    html += '    </div>\n'
    
    RETURN html
    
ENDE FUNKTION


FUNKTION GeneriereSeitenListe(seiten)
    html = '    <div class="page-list">\n'
    html += '        <ul>\n'
    
    FÜR JEDE seite IN seiten
        html += '            <li>\n'
        html += '                <a href="' + seite["relativerPfad"] + '">\n'
        html += '                    ' + EscapeHTML(seite["titel"]) + '\n'
        
        // Badge hinzufügen basierend auf Priorität
        WENN seite["priorität"] >= 80 DANN
            html += '                    <span class="badge badge-important">Core</span>\n'
        SONST WENN IstNeueDatei(seite["lastModified"], 7) DANN
            html += '                    <span class="badge badge-new">Neu</span>\n'
        SONST WENN IstAktualisiert(seite["lastModified"], 30) DANN
            html += '                    <span class="badge badge-updated">Aktualisiert</span>\n'
        ENDE WENN
        
        html += '                </a>\n'
        html += '            </li>\n'
    ENDE FÜR
    
    html += '        </ul>\n'
    html += '    </div>\n'
    
    RETURN html
    
ENDE FUNKTION


// ========== HILFSFUNKTIONEN ==========

FUNKTION FilterSeiten(seiten, kategorie, bedingung = NULL)
    ergebnis = NEU Liste
    
    FÜR JEDE seite IN seiten
        passt = FALSCH
        
        WENN IstListe(kategorie) DANN
            passt = kategorie.Enthält(seite["kategorie"])
        SONST
            passt = seite["kategorie"] == kategorie
        ENDE WENN
        
        WENN passt UND (bedingung == NULL ODER EvaluiereBedingung(seite, bedingung)) DANN
            FÜGE seite ZU ergebnis HINZU
        ENDE WENN
    ENDE FÜR
    
    RETURN ergebnis
    
ENDE FUNKTION


FUNKTION IstNeueDatei(lastModified, tageGrenze)
    heute = AktuellesDatum()
    differenz = heute - lastModified
    RETURN differenz.Tage <= tageGrenze
    
ENDE FUNKTION


FUNKTION VerschönereDateiname(dateiname)
    // "MyAPIHelper" -> "My API Helper"
    // "passwordHelper" -> "Password Helper"
    
    ergebnis = ""
    FÜR index = 0 BIS dateiname.Länge - 1
        zeichen = dateiname[index]
        
        WENN IstGrossbuchtabe(zeichen) UND index > 0 DANN
            ergebnis += " "
        ENDE WENN
        
        WENN index == 0 DANN
            ergebnis += ZuGrossbuchstabe(zeichen)
        SONST
            ergebnis += zeichen
        ENDE WENN
    ENDE FÜR
    
    RETURN ergebnis
    
ENDE FUNKTION


FUNKTION EscapeHTML(text)
    text = text.Ersetze("&", "&amp;")
    text = text.Ersetze("<", "&lt;")
    text = text.Ersetze(">", "&gt;")
    text = text.Ersetze('"', "&quot;")
    text = text.Ersetze("'", "&#39;")
    RETURN text
    
ENDE FUNKTION


// ========== PHASE 5: DATEI SCHREIBEN ==========

FUNKTION SchreibeDatei(pfad, inhalt)
    VERSUCHE
        ordner = ExtrahiereOrdnerPfad(pfad)
        WENN NICHT ExistiertOrdner(ordner) DANN
            ErstelleOrdner(ordner)
        ENDE WENN
        
        SchreibeTextDatei(pfad, inhalt, encoding: "UTF-8")
        
        LoggeNachricht("INFO", "Wiki Index erfolgreich erstellt: " + pfad)
        RETURN WAHR
        
    FANGE fehler
        LoggeNachricht("FEHLER", "Fehler beim Schreiben der Datei: " + fehler.Message)
        RETURN FALSCH
    ENDE VERSUCHE
    
ENDE FUNKTION


// ========== HAUPTPROGRAMM EINSTIEGSPUNKT ==========

HAUPTPROGRAMM
    
    // Konfiguration
    projektPfad = ARGUMENT[0] ODER AktuellesVerzeichnis()
    ausgabePfad = projektPfad + "/index.html"
    
    // Wiki generieren
    Logge("Starte Wiki-Generierung für: " + projektPfad)
    ergebnis = GenerateWikiIndex(projektPfad)
    
    WENN ergebnis == ERFOLGREICH DANN
        Logge("✓ Wiki erfolgreich erstellt: " + ausgabePfad)
        ZeigeDateiImBrowser(ausgabePfad)
    SONST
        Logge("✗ Fehler bei Wiki-Generierung")
        BEENDE_MIT_FEHLER()
    ENDE WENN
    
ENDE HAUPTPROGRAMM
```

---

## VERWENDUNG

### Als Copilot-Anweisung:
```
"Generiere eine Wiki index.html für dieses Projekt basierend auf dem 
AutoGenerateWikiIndex Pseudocode. Scanne alle verfügbaren Dokumentations-
dateien und erstelle eine strukturierte Übersichtsseite."
```

### Als Automatisierungs-Script:
```bash
# Node.js Beispiel
node generateWiki.js /pfad/zum/projekt

# Python Beispiel
python generate_wiki.py /pfad/zum/projekt

# PowerShell Beispiel
.\GenerateWiki.ps1 -ProjectPath "C:\Projects\MeinProjekt"
```

---

## ERWEITERUNGEN & ANPASSUNGEN

### Zusätzliche Funktionen die implementiert werden können:

1. **Automatische Updates**
   - Überwache Dateisystem auf Änderungen
   - Regeneriere Index wenn neue Dokumentation hinzugefügt wird

2. **Suchfunktion**
   - Generiere Such-Index aller Seiten
   - Füge Client-seitige Suche hinzu

3. **Versionierung**
   - Vergleiche mit vorheriger Version
   - Markiere neue/aktualisierte Seiten automatisch

4. **Multi-Sprachen Support**
   - Erkenne Sprache aus Dateinamen (z.B. readme.de.md, readme.en.md)
   - Generiere mehrsprachige Navigation

5. **Custom Templates**
   - Lade CSS/HTML Templates aus Konfigurationsdatei
   - Unterstütze verschiedene Wiki-Themes

---

## ABHÄNGIGKEITEN & REQUIREMENTS

- Dateisystem-Zugriff (Lesen/Schreiben)
- JSON/XML Parser
- Regex-Engine
- Markdown Parser (optional)
- HTML/CSS Template Engine (optional)

---

*Pseudocode erstellt für automatisierte Wiki-Generierung*  
*Version: 1.0 | Datum: 12. Dezember 2025*
