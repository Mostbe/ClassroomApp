# 📱 Digitales Notenbuch – Anleitung für iPad

## 🚀 So startest du die App (Einmalig)

### Auf dem iPad:

1. **Safari öffnen** und diese URL eingeben:  
   ```
   https://mostbe.github.io/ClassroomApp
   ```
   *(Die App ist über GitHub Pages unter `docs/` gehostet.)*

2. **Auf dem Startbildschirm speichern** (App-Icon erstellen):
   - Tippe auf das **Teilen-Symbol** (↗️) oben rechts
   - Scrolle nach unten und tippe **"Zum Startbildschirm hinzufügen"**
   - Gib einen Namen ein (z. B. "Notenbuch") und tippe **"Hinzufügen"**
   - Jetzt hast du ein App-Symbol auf deinem Homescreen!

---

## ✍️ So benutzt du die App (Täglich)

### 1️⃣ Klasse auswählen oder erstellen

- **Oben links**: Tippe auf eine bereits erstellte Klasse (z. B. "7A"), um sie zu öffnen
- **Oben rechts**: Gib in "Neues Notenbuch anlegen" die Klasse ein (z. B. "7B") und tippe **"Anlegen"**

### 2️⃣ Schüler hinzufügen

- Gib den Namen des Schülers ein (z. B. "Max Müller")
- Tippe **"+ Schüler hinzufügen"** oder drücke Enter

### 3️⃣ Stundennoten eintragen

- Neue **Spalte für neue Stunde**: Gib das Datum ein und tippe **"+ Spalte hinzufügen"**
- Unter jeder Stundenspalte kannst du die Noten eingeben:
  - **Symbole**: `++`, `+`, `0`, `-`, `--`
  - Das System berechnet automatisch die empfohlene mündliche Note

### 4️⃣ Klausuren, Quartale und mündliche Noten

- **Klausuren (K1–K6)**: Gib Noten 1–6 oder Symbole ein
- **Quartalsnoten (Q1–Q4)**: Gib die Quartalsnoten ein (1–6 oder Symbole)
- **Ø Mündlich**: 
  - **Oben angezeigt**: Automatische Empfehlung basierend auf Stunden
  - **Feld unten**: Du kannst die tatsächliche mündliche Note manuell eingeben
- **Endnote**:
  - Berechnet sich aus (Ø Mündlich + Klausurdurchschnitt) / 2
  - Du kannst die Endnote auch manuell überschreiben

---

## 💾 So sicherst du deine Daten (Sehr wichtig!)

### Automatisches Speichern
- ✅ Die App speichert **automatisch** lokal auf deinem iPad beim Tippen
- Du siehst unten: **"💾 Zuletzt gespeichert: [Uhrzeit]"**
- ⚠️ Das ist aber nicht 100% sicher → Mache regelmäßig Backups!

### Backup-Optionen

#### **Option 1: 🔗 Share-Button (Empfohlen für iPad)**
1. Tippe oben **"🔗 Share"**
2. Wähle:
   - **"In Dateien sichern"** → speichert die Datei in iCloud Drive oder lokale Dateien
   - **"Mail"** → sendet dir selbst die Backup-Datei per E-Mail
   - **"iCloud Drive"** (wenn verfügbar)
3. Die Datei heißt: `gradebook-backup-[Datum].json`

#### **Option 2: 💾 Export-Button (Download)**
1. Tippe auf **"💾 Export"**
2. Safari lädt eine `gradebook-backup.json` herunter
3. Speichere sie in den **iPad-Dateien** oder **iCloud Drive**

#### **Option 3: 📂 Import (Daten wiederherstellen)**
1. Wenn du ein Backup hast, tippe **"📂 Import"**
2. Wähle die `gradebook-backup.json` Datei aus der Dateien-App
3. Alle Daten werden wiederhergestellt ✅

---

## ⚙️ Wichtige Hinweise

### iPad Private/Inkognito-Modus
- ⚠️ **NICHT im Privat-/Inkognito-Modus nutzen!**
- Im privaten Modus werden Daten beim Schließen gelöscht
- Nutze den **normalen Safari-Modus**

### Daten verlieren vermeiden
- **Wöchentliches Backup:** Jede Woche `Share` oder `Export` drücken
- **Mehrere Sicherungsorten:**
  - 1× in iCloud Drive
  - 1× per Mail an dich selbst
  - 1× lokal auf dem iPad

### Speicherplatz prüfen
- Wenn das iPad voll wird, kann localStorage gelöscht werden
- **Deshalb: Regelmäßige Backups sind essentiell!**

---

## 🎯 Empfohlener Workflow (Täglich)

1. **App öffnen** → Klasse auswählen
2. **Stunde eingeben:**
   - Neues Datum → + Spalte hinzufügen
   - Noten in den Feldern eintragen (++ / + / 0 / - / --)
3. **Am Ende der Stunde:** Einfach tippen und die App speichert automatisch
4. **Freitags Backup:** 1× pro Woche `Share` drücken → in Dateien/Mail speichern

---

## 🆘 Troubleshooting

| Problem | Lösung |
|---------|--------|
| App öffnet nicht | Internetverbindung prüfen; Safari Cache löschen (Einstellungen → Safari → Verlauf löschen) |
| Daten weg nach Neustart | Das ist ein Browser-Cache-Problem. Nutze `Export` regelmäßig! |
| Export/Share funktioniert nicht | Stelle sicher, dass du nicht im Privat-Modus bist |
| Datei nicht zu finden nach Download | Öffne die Dateien-App, gehe zu "Downloads" und verschiebe sie in iCloud Drive |
| Zu viele Dateien in Dateien-App | Lösche alte Backups und behalte nur die letzten 2–3 |

---

## 📞 Fragen oder Probleme?

Wenn etwas nicht funktioniert:
1. Cache löschen (Safari Einstellungen)
2. Seite neu laden (↻)
3. Ein Backup wiederherstellen, wenn Daten verloren gingen

**Wichtig:** Mache **vor jeder Maßnahme** ein Backup! 🔗 Share oder 💾 Export nutzen.

---

*Viel Spaß mit deinem digitalen Notenbuch! 📚✏️*
