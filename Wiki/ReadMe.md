WikiPages:
-----------
Key Features:
✅ Universal Design - Clean, professional look that adapts to any project
✅ Modular Structure - Organized sections: Getting Started, Documentation, API, Guides, Resources
✅ Responsive Layout - Works on desktop, tablet, and mobile
✅ Easy Customization - CSS variables for quick color/theme changes
✅ Smart Navigation - Sticky nav with smooth scrolling and active section highlighting
✅ Card-Based Layout - Flexible grid system that adapts to content
✅ Badge System - Mark pages as "New", "Updated", or "Important"

To customize for your project:

Update Header - Change project name and subtitle
Modify Colors - Edit CSS variables in :root
Add Your Pages - Link to your documentation in the API/Guides sections
Update Links - Replace placeholder # links with actual pages
Adjust Sections - Add/remove sections as needed

Usage:
You can now tell me: "Generate a wikipage for the folloing code: #your code

Wiki MAin:
-----------
What the pseudocode covers:
5 Main Phases:
🔍 Project Analysis - Detects project type (C#, JavaScript, Python, Java), reads package.json/csproj/pom.xml, extracts metadata
📁 File Scanning - Recursively scans for documentation files, categorizes them, determines priorities
🏗️ Structure Creation - Organizes content into sections, applies themes based on project type
🎨 HTML Generation - Creates the complete HTML with dynamic CSS, navigation, cards, and lists
💾 File Writing - Saves the generated index.html
Key Features:
✅ Automatic categorization - API docs, tutorials, architecture, guidelines
✅ Priority system - Important pages (README, getting-started) appear first
✅ Smart metadata extraction - Pulls titles, descriptions from HTML/Markdown
✅ Dynamic theming - Different colors for .NET (purple), JavaScript (yellow), Python (blue), etc.
✅ Badge system - Automatically marks files as "New", "Updated", or "Core"
✅ Multi-format support - Works with HTML, Markdown, and various project types

Usage:
add your structure and files into a folder and run the following with references to the folder and the template:

"Generate a wiki index for this project using the AutoGenerateWikiIndex pseudocode" and I'll follow this systematic approach!

Index:
-------
Key Features:
✅ Automatic Detection - Finds all HTML files in Wikipages folders
✅ Smart Positioning - Places button at the top of each page (sticky navigation)
✅ Relative Path Calculation - Works with nested folder structures
✅ Duplicate Prevention - Checks if back button already exists
✅ Beautiful Design - Gradient background, hover effects, responsive
✅ Links to Wiki.html - As you requested!

Main Functions:
FindeAlleWikiSeiten() - Scans for all HTML files
BerechneRelativenPfad() - Calculates correct path (../ for nested folders)
GeneriereBackButtonHTML() - Creates styled button with CSS
FügeBackButtonEin() - Intelligently inserts after <body> tag
ErstelleBearbeitungsBericht() - Generates detailed report
Three Operation Modes:
add - Add back buttons to all pages
remove - Remove all back buttons
update - Remove and re-add with new configuration
Button Features:
🎨 Sticky positioning - Always visible at top
🎯 ← Arrow icon with "Zurück zur Wiki-Übersicht" text
✨ Smooth hover effects - Transforms and changes background
📱 Responsive - Works on all screen sizes
♿ Accessible - Includes ARIA labels
The pseudocode is saved in [WikiVorlage/Pseudocode Vorlage/AutoAddBackButtonToWikiPages.md](Übungen/WikiVorlage/Pseudocode Vorlage/AutoAddBackButtonToWikiPages.md)

You can now tell me: "Add back buttons to all wiki pages using the AutoAddBackButtonToWikiPages pseudocode" and I'll implement it!


How to use:
always refernee the template + the code you want to use
1. scan your class with wikipage template.
2. add all scans into a folder. you can also add all other kind of folders and data and the ki will scan for them and add sections on the wiki accordingly.
For more info check the teplates themself for more informations
3. run wikiindex template with the folder as reference
4. run auto add butto to check the sites and add backbuttons to all wiki pages.
5. enjoy
