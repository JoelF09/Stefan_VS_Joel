# 🏁 Stefan VS Joel – ACC Rundenzeiten-Vergleich

Eine Single-Page-Webanwendung zum Vergleich von Hotlap-Zeiten in **Assetto Corsa Competizione**.

**Live:** <https://joelf09.github.io/Stefan_VS_Joel/>

---

## ✨ Features

- **Rundenzeiten-Tabelle** – alle Strecken im direkten Vergleich, die schnellere Zeit wird pro Strecke grün hervorgehoben
- **Statistik-Karten** – gewonnene Strecken pro Fahrer und größter Abstand auf einen Blick
- **Detail-Ansicht** – Klick auf eine Strecke zeigt die Differenz als animiertes Balkendiagramm (auf die Differenz skaliert, damit auch kleine Abstände sichtbar werden)
- **Admin-Modus** – nach Login lassen sich Strecken hinzufügen, Zeiten bearbeiten und Einträge löschen; ohne Login ist die Seite reine Ansicht
- **Persistente Daten** – alle Zeiten liegen in einer Supabase-Datenbank, Änderungen sind sofort für alle Besucher sichtbar
- **Racing-Design** – dunkles Motorsport-Theme mit animierter Circuit-Map im Hintergrund (blauer und roter Runden-Puls für die beiden Fahrer)

## 🛠️ Tech-Stack

| Bereich | Technologie |
|---|---|
| Frontend | Vanilla HTML / CSS / JavaScript (eine einzige `index.html`, keine Build-Tools) |
| Backend / Datenbank | [Supabase](https://supabase.com/) (PostgreSQL + Auth, via `@supabase/supabase-js` CDN) |
| Hosting | GitHub Pages |
| CI/CD | GitHub Actions (automatisches Deployment bei jedem Push auf `main`) |
| Schriften | Chakra Petch · Inter · JetBrains Mono (Google Fonts) |

## 📂 Projektstruktur

```
Stefan_VS_Joel/
├── index.html              # Gesamte App: Markup, Styles, Logik
├── .github/
│   └── workflows/
│       └── deploy.yml      # Deployment auf GitHub Pages
└── README.md
```

## ⚙️ Funktionsweise

### Datenmodell

Die Zeiten liegen in Supabase im eigenen Schema **`racing`** in der Tabelle **`lap_times`**:

| Spalte | Typ | Beschreibung |
|---|---|---|
| `id` | `uuid` | Primärschlüssel |
| `track` | `text` | Streckenname (z. B. *Nürburgring*) |
| `joel_time` | `text` | Rundenzeit Joel im Format `m:ss.mmm` |
| `stefan_time` | `text` | Rundenzeit Stefan im Format `m:ss.mmm` |
| `sort_order` | `int` | Reihenfolge in der Tabelle |
| `created_at` / `updated_at` | `timestamptz` | Zeitstempel |

### Rechte & Login

- **Lesen:** öffentlich – jeder Besucher sieht alle Zeiten (Row Level Security erlaubt `SELECT` für alle).
- **Schreiben:** nur eingeloggte Admins – `INSERT` / `UPDATE` / `DELETE` erfordern eine gültige Supabase-Auth-Session.
- Die Bearbeitungs-Buttons (Hinzufügen, Stift, Papierkorb) werden erst nach erfolgreichem Login eingeblendet (`body.admin`-Klasse). Die eigentliche Absicherung passiert serverseitig über RLS – das Ausblenden ist nur Komfort.

### Zeitformat

Eingaben werden client-seitig gegen das Format `m:ss.mmm` validiert (z. B. `1:43.250`), Differenzen werden in Sekunden mit drei Nachkommastellen berechnet.

## 🚀 Lokale Entwicklung

Es gibt keinen Build-Schritt – die Seite kann direkt geöffnet werden:

```bash
# Variante 1: Datei direkt im Browser öffnen
start index.html

# Variante 2: lokaler Server
npx serve .
```

## 📦 Deployment

Das Deployment läuft vollautomatisch über GitHub Actions ([deploy.yml](.github/workflows/deploy.yml)):

1. Push auf den Branch `main`
2. Der Workflow lädt das Repository-Root als statisches Pages-Artefakt hoch
3. GitHub Pages veröffentlicht die neue Version

Ein manueller Deploy ist jederzeit über den **Actions-Tab → „Deploy to GitHub Pages" → Run workflow** möglich.

---

*Möge der Schnellere gewinnen.* 🏎️💨
