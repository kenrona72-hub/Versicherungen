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

## Aufbau

Die App startet auf einer **Startseite mit Kacheln**. Jeder Bereich ist eine eigene Seite –
nichts liegt mehr auf einem Haufen. Über die Kachel „Start" oben rechts kommst du immer zurück.

| Kachel | Wofür |
|---|---|
| 🔥 Heute loslegen | Der Arbeitsplatz: 3 automatisch ausgewählte Kontakte, Nachfassen, nächste Termine |
| 👥 Kontakte | Alle Namen – suchen, filtern, Status pflegen, Notizen |
| 🗓 8-Wochen-Plan | Welche Kontakte in welcher Woche, mit Wochenzielen |
| ⏱ Wochenrhythmus | Die festen Tagesblöcke |
| 💬 Leitfäden | Sechs Unterkacheln: Erstkontakt, Telefon, Analysegespräch, Abschluss, Empfehlungen, Mindset |
| 📋 Datenaufnahme | Pflichtdaten je Kunde + Unterlagenliste |
| 📁 Ordner & Ablage | Ablagestruktur + Setup-Skript |
| 📊 Auswertung | Trichter, Quote je Segment, Aktivität der letzten 14 Tage |
| ⬇️ Export & Backup | .ics, .md, .sh, .csv, .vcf, .json |

## Was die App automatisch macht

**WhatsApp mit fertiger Nachricht.** Ein Klick auf den grünen Button öffnet WhatsApp mit dem
Chat des Kontakts und der passenden Nachricht – Vorname eingesetzt, Text passend zum Segment
(Familie klingt anders als Selbstständige). Danach springt der Status automatisch auf
*Kontaktiert* und die Wiedervorlage steht auf in 4 Tagen.

**Folgeschritt nach Statuswechsel.** Du setzt nur den Status, alles andere kommt von allein:

| Status | Nächster Schritt | Wiedervorlage |
|---|---|---|
| Kontaktiert | Nachfassen | + 4 Tage |
| Termin | Analysegespräch führen | + 7 Tage |
| Analyse | Angebot vorbereiten | + 3 Tage |
| Angebot | Angebot nachfassen | + 4 Tage |
| Abschluss | Unterlagen + Empfehlung fragen | + 2 Tage |
| Später | Wiedervorlage | + 6 Wochen |

**Tagesauswahl.** Der Tab *Heute* sucht dir die drei Kontakte für heute selbst zusammen:
überfällige zuerst, dann die aktuelle Wochenwelle, dann Liegengebliebenes.

**Nachzügler einsammeln.** Wer aus einer vergangenen Woche noch nicht angesprochen ist,
erscheint als Hinweis auf der Startseite und wird per Klick in die aktuelle Woche geholt.

**Backup-Erinnerung.** Liegt das letzte Backup mehr als 7 Tage zurück, sagt die Startseite Bescheid.

**Empfehlungen.** Der Button *+ Empfehlung* im Kontakt legt den neuen Namen sofort an,
vermerkt, von wem er kommt, und setzt die Wiedervorlage auf 48 Stunden.

**Tagesziel.** 3 Kontakte pro Tag. Der Balken zeigt, wo du stehst; die Auswertung zeigt
deine Aktivität der letzten zwei Wochen als Kurve.

## Ersteinrichtung (20 Minuten)

1. **Startdatum setzen** – Tab *8-Wochen-Plan*, Feld „Start (Montag)".
   Voreingestellt ist der nächste Montag.
2. **Prioritäten korrigieren** – Tab *Kontakte*. Alles, was auf „Noch unklar"
   steht, brauchst du irgendwann als echtes Segment. Mach das nicht auf einmal;
   korrigier jeden Kontakt, bevor du ihn anschreibst.
3. **Wochen neu verteilen** – falls du viele Prioritäten geändert hast.
4. **Telefonnummern eintragen** – ohne Nummer öffnet WhatsApp nur den Text zum Weiterleiten,
   mit Nummer direkt den richtigen Chat. Formate wie `0170 1234567` oder `+49 170 1234567`
   funktionieren beide.
5. **Ordner anlegen** – Tab *Ordner* → `kunden-setup.sh` laden → im Terminal:
   ```bash
   bash ~/Downloads/kunden-setup.sh
   ```
   Vorher im Skript den Pfad `VAULT` an deinen Obsidian-Vault anpassen.
6. **Kalender füllen** – Tab *Export* → `.ics` laden → Doppelklick.
7. **Obsidian** – Tab *Export* → `Vertriebsplan.md` in den Vault ziehen.

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
Cockpit auf → Kachel **Heute** → die drei Namen stehen schon da → grüner
WhatsApp-Button → Text kurz anpassen → senden. Status und Wiedervorlage
setzen sich selbst.

**Nach jedem Gespräch (2 Min):**
Kontakt öffnen → Status anklicken → Notizen ergänzen. Nächster Schritt und
Wiedervorlage kommen automatisch.

**Sonntag, 20:00 (20 Min):**
Nächste Welle ansehen, Termine blocken, JSON-Backup ziehen.

## Neue Kontakte

Empfehlungen kommen laufend dazu. Im Kontakt der Button **+ Empfehlung**, sonst in der
Kontaktliste „+ Kontakt" → Name, Beziehung,
Priorität, Woche. Danach im Tab *Ordner* das Skript neu laden und ausführen,
dann existiert auch der Ordner.

## Änderungen am Plan

Alles ist änderbar, ohne dass etwas kaputtgeht:

- **Startdatum verschieben** – alle Wochen und Termine rechnen sich neu
- **Kontakt in andere Woche** – im Detail-Drawer über „Kontaktwoche"
- **Komplett neu verteilen** – Button „Wochen neu verteilen" (nach Priorität)
- **Wochenthemen ändern** – im Quelltext das Array `WOCHEN` bearbeiten
- **Tagesblöcke ändern** – Array `RHYTHMUS`
- **Leitfäden ändern** – Array `GUIDES`
- **Nachrichtentexte ändern** – Objekt `MSG` (je Segment) sowie `MSG_NACH`, `MSG_TERMIN`, `MSG_DANKE`
- **Automatik-Regeln ändern** – Objekt `AUTO` (Status → Folgeschritt + Tage)
- **Sparten/Datenpunkte ändern** – Arrays `SPARTEN` und `DATENPUNKTE`

Alle Arrays stehen ganz oben im `<script>`-Block und sind bewusst so geschrieben,
dass man sie ohne JavaScript-Kenntnisse anpassen kann.

## Drei Systeme, klare Trennung

Nicht dreifach dokumentieren, sonst hält es keine drei Wochen:

- **iCloud** → die echten Dokumente. PDFs, Fotos von Policen, Anträge.
- **Obsidian** → Wissen und Gesprächsnotizen. Verlinkt und durchsuchbar.
- **Cockpit** → der Zustand. Status, Termine, nächste Schritte.
