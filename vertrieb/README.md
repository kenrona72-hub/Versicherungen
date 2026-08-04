# Vertriebs-Cockpit

Eine einzelne HTML-Datei mit dem kompletten Vertriebssystem für die ersten zwei Monate:
82 Kontakte, 8-Wochen-Plan, Wochenrhythmus, Gesprächsleitfäden, Datenaufnahme,
Ordnerstruktur und Exporte für Kalender, Obsidian und iCloud.

## Starten

`vertrieb/index.html` im Browser öffnen – fertig. Kein Server, kein Internet, keine Installation.

Auf dem Mac einmalig in den Dock legen:
```bash
open -a Safari ~/Versicherung/vertrieb/index.html
```

## Wo liegen die Daten?

Ausschließlich im `localStorage` des Browsers, in dem du die Datei öffnest.

Das heißt konkret:
- **Immer denselben Browser benutzen.** Safari und Chrome haben getrennte Speicher.
- **Privates Fenster löscht alles.** Nicht im Private-Mode arbeiten.
- **Jeden Freitag ein JSON-Backup** über den Export-Tab ziehen und in iCloud ablegen.

## Die sechs Bereiche

| Tab | Wofür |
|---|---|
| Dashboard | Was heute ansteht, was überfällig ist, wie die Pipeline aussieht |
| Kontakte | Alle 82 Namen – suchen, filtern, Status pflegen, Notizen schreiben |
| 8-Wochen-Plan | Welche Kontakte in welcher Woche, mit Wochenzielen |
| Wochenrhythmus | Die festen Tagesblöcke – das Skelett des Ganzen |
| Leitfäden | Nachrichten, Telefonskripte, Analysegespräch, Einwände, Empfehlungen |
| Datenaufnahme | Pflichtdaten je Kunde + Unterlagenliste für den Kunden |
| Ordner | Ablagestruktur + Setup-Skript |
| Export | .ics, .md, .sh, .csv, .vcf, .json |

## Ersteinrichtung (20 Minuten)

1. **Startdatum setzen** – Tab *8-Wochen-Plan*, Feld „Start (Montag)".
   Voreingestellt ist der nächste Montag.
2. **Prioritäten korrigieren** – Tab *Kontakte*. Alles, was auf „Noch unklar"
   steht, brauchst du irgendwann als echtes Segment. Mach das nicht auf einmal;
   korrigier jeden Kontakt, bevor du ihn anschreibst.
3. **Wochen neu verteilen** – falls du viele Prioritäten geändert hast.
4. **Ordner anlegen** – Tab *Ordner* → `kunden-setup.sh` laden → im Terminal:
   ```bash
   bash ~/Downloads/kunden-setup.sh
   ```
   Vorher im Skript den Pfad `VAULT` an deinen Obsidian-Vault anpassen.
5. **Kalender füllen** – Tab *Export* → `.ics` laden → Doppelklick.
6. **Obsidian** – Tab *Export* → `Vertriebsplan.md` in den Vault ziehen.

## Was das Setup-Skript macht

- Legt in iCloud Drive unter `Versicherung/Kunden/` je Kontakt einen Ordner mit
  sieben Unterordnern an (Stammdaten, Bestandsverträge, Analyse, Angebote,
  Anträge/Policen, Schriftverkehr, Schadenfälle)
- Schreibt in jeden Ordner eine `README.md` mit Kurzprofil
- Erzeugt im Obsidian-Vault je Kontakt eine Notiz mit Frontmatter
  (`prio`, `segment`, `woche`, `status`, `ordner`) – damit lassen sich
  Dataview-Abfragen bauen
- Setzt einen Symlink `~/Versicherung` auf den iCloud-Ordner
- **Ist idempotent**: mehrfaches Ausführen überschreibt nichts. Wenn neue
  Kontakte dazukommen, einfach neu exportieren und nochmal laufen lassen.

## Was der Kalender-Export enthält

Pro Woche:
- ein Ganztags-Banner mit dem Wochenthema und allen Kontakten der Welle
  (Erinnerung Montag 8:00)
- vier Kontaktblöcke Mo–Do, 17:00–17:45 (Erinnerung 10 Min vorher)
- ein Nachfass-Block Freitag 16:00–17:00
- die Wochenplanung Sonntag 20:00 (Erinnerung 15 Min vorher)

Dazu jede Wiedervorlage, die du im Cockpit mit Datum eingetragen hast (9:00).

Die Termine sind ohne Zeitzone hinterlegt („schwebende Zeit"), laufen also
immer in deiner lokalen Zeit – auch wenn du unterwegs bist.

**Tipp:** Lege dafür in Apple Kalender einen eigenen Kalender „Vertrieb" an und
importiere dort hinein. Dann kannst du bei einer Neu-Planung alles auf einmal
löschen und frisch importieren.

## Arbeitsablauf im Alltag

**Täglich, 17:00 (45 Min):**
Cockpit auf → Dashboard → „Diese Woche" → drei Namen → Leitfaden-Text
kopieren (Button im Kontakt-Detail) → anpassen → senden → „✓ angeschrieben".
Das setzt automatisch die Wiedervorlage auf in 4 Tagen.

**Nach jedem Gespräch (2 Min):**
Kontakt öffnen → Status setzen → nächster Schritt + Datum → Notizen.

**Sonntag, 20:00 (20 Min):**
Nächste Welle ansehen, Termine blocken, JSON-Backup ziehen.

## Neue Kontakte

Empfehlungen kommen laufend dazu. Oben rechts „+ Kontakt" → Name, Beziehung,
Priorität, Woche. Danach im Tab *Ordner* das Skript neu laden und ausführen,
dann existiert auch der Ordner.

## Änderungen am Plan

Alles ist änderbar, ohne dass etwas kaputtgeht:

- **Startdatum verschieben** – alle Wochen und Termine rechnen sich neu
- **Kontakt in andere Woche** – im Detail-Drawer über „Kontaktwoche"
- **Komplett neu verteilen** – Button „Wochen neu verteilen" (nach Priorität)
- **Wochenthemen ändern** – im Quelltext das Array `WOCHEN_ROLLEN` bearbeiten
- **Tagesblöcke ändern** – Array `RHYTHMUS`
- **Leitfäden ändern** – Array `LEITFAEDEN`
- **Sparten/Datenpunkte ändern** – Arrays `SPARTEN` und `DATENPUNKTE`

Alle Arrays stehen ganz oben im `<script>`-Block und sind bewusst so geschrieben,
dass man sie ohne JavaScript-Kenntnisse anpassen kann.

## Drei Systeme, klare Trennung

Nicht dreifach dokumentieren, sonst hält es keine drei Wochen:

- **iCloud** → die echten Dokumente. PDFs, Fotos von Policen, Anträge.
- **Obsidian** → Wissen und Gesprächsnotizen. Verlinkt und durchsuchbar.
- **Cockpit** → der Zustand. Status, Termine, nächste Schritte.
