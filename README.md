# StreamList

**Versteckte NTFS-Datenströme in Total Commander sehen, bearbeiten und aufräumen**

Version 0.7.1 · Dateisystem-Plugin (WFX) für 32 und 64 Bit · Autor: Native2904 · Lizenz: MIT

Die Kapitel bauen aufeinander auf: von den Grundlagen alternativer Datenströme über die Arbeit mit einzelnen Dateien bis zur laufwerksweiten Auswertung mit Everything. Wer StreamList bereits kennt, findet Tastenbelegung, Felder sowie Einstellungen und Protokoll in den Kapiteln 5, 10 und 12 gesammelt.

---

## 1. Was ist ein Datenstrom?

NTFS speichert den Inhalt einer Datei in einem Datenstrom. Neben diesem Hauptdatenstrom kann eine Datei beliebig viele weitere, benannte Datenströme besitzen – **alternative Datenströme** (*Alternate Data Streams*, kurz ADS), im Folgenden einfach **Streams**. Der Explorer zeigt sie nicht an, und die angezeigte Dateigröße berücksichtigt sie nicht. Beim Kopieren oder Verschieben innerhalb von NTFS bleiben sie dennoch mit der Datei verbunden.

Am häufigsten begegnet man ihnen in Form der Download-Kennzeichnung, oft *Mark of the Web* genannt: Browser legen an jede heruntergeladene Datei einen Stream `Zone.Identifier` an. Er vermerkt die Sicherheitszone, aus der die Datei stammt, und meist auch die Herkunftsadresse. Auf ihm beruht der Hinweis, den Windows beim Öffnen solcher Dateien anzeigt.

Sichtbar machen lässt sich das in der Eingabeaufforderung mit `dir /r`:

| Größe | Name |
|---:|---|
| 4.858.880 | `alrext.exe` |
| 52 | `alrext.exe:Zone.Identifier:$DATA` |
| 87.552 | `ChmLib.dll` |
| 52 | `ChmLib.dll:Zone.Identifier:$DATA` |

Die Zeilen in der Form `Datei:Name:$DATA` sind Streams; `alrext.exe` besitzt demnach einen 52 Byte großen Stream `Zone.Identifier`.

StreamList stellt diese Streams in Total Commander als gewöhnliche Einträge dar, die sich ansehen, bearbeiten, anlegen und löschen lassen.

---

## 2. Installation

Voraussetzungen sind Total Commander ab Version 7.5, Windows 7 oder neuer und **Everything 1.5** von voidtools mit indizierten Stream-Eigenschaften (Einrichtung in Kapitel 8). Das Archiv wird in Total Commander geöffnet und die Installation bestätigt; StreamList erscheint anschließend in der **Netzwerkumgebung**.

Die Kapitel 3 bis 7 beschreiben die Arbeit mit einzelnen Ordnern und Dateien, Kapitel 8 die laufwerksweite Auswertung über Everything.

---

## 3. Erster Überblick

Der Eintrag **StreamList** in der Netzwerkumgebung zeigt die NTFS-Laufwerke des Systems und zwei Listen mit Lupensymbol:

| Name | Streams |
|---|---:|
| 🔍 `! Alle Dateien mit Streams` | |
| 🔍 `! Downloads (Zone.Identifier)` | |
| 📁 `C` | ∑ 58.448 |
| 📁 `D` | ∑ 312 |

Die Listen behandelt Kapitel 8. Zunächst geht es um die Darstellung gewöhnlicher Ordner.

---

## 4. Die Ordneransicht

Innerhalb von StreamList entspricht ein Ordner der gewohnten Darstellung, und auch die Dateioperationen verhalten sich wie im normalen Panel: F5 kopiert, F6 verschiebt, F8 löscht die echten Dateien (Kapitel 6). Eine Abweichung gibt es: **Enter auf einer Datei öffnet nicht die Datei, sondern ihre Streams.**

Welche Dateien überhaupt Streams besitzen, zeigt die Spaltenansicht, die StreamList beim Betreten mitbringt:

| Name | Größe | Streams | Inhalt | Herkunft |
|---|---:|---:|---|---|
| 📁 `Tools` | `<DIR>` | ∑ 31 | | |
| 📦 `setup.zip` | 2.418.330 | 1 | Zone.Identifier | https://example.org/… |
| 📄 `notiz.txt` | 1.204 | 2 | Comment, eins | |
| 📄 `bild.png` | 88.112 | | | |

- **Streams** gibt bei Dateien die Anzahl ihrer Streams an. Bei Ordnern steht dort, mit `∑` gekennzeichnet, die Zahl der Dateien mit Streams im gesamten Teilbaum; sie stammt aus dem Everything-Index (Kapitel 8).
- **Inhalt** nennt die Namen der Streams.
- **Herkunft** enthält bei heruntergeladenen Dateien die Quelladresse aus dem `Zone.Identifier`.

Leere Felder, wie bei `bild.png`, bedeuten, dass keine Streams vorhanden sind.

---

## 5. Die Stream-Ansicht einer Datei

Enter auf `notiz.txt` wechselt in die Stream-Ansicht dieser Datei:

| Name | Größe | Inhalt |
|---|---:|---|
| 📄 `Comment.txt` | 7 | Rechnung |
| 📄 `eins.txt` | 16 | erster Stream |

Die Spalte **Inhalt** enthält hier die erste Textzeile des jeweiligen Streams; binäre Streams werden als Hexadezimalfolge wiedergegeben, etwa `4D 5A 90 00 …`.

Die Endung `.txt` ist geliehen: Die Streams heißen tatsächlich `Comment` und `eins`. StreamList ergänzt sie bei Text-Streams ohne eigene Endung, damit sie sich nach dem Herauskopieren mit den üblichen Programmen öffnen lassen. Beim Bearbeiten, Löschen und Umbenennen arbeitet StreamList stets mit dem echten Namen. Streams mit Endung (etwa `Zone.Identifier`) und binäre Streams behalten ihren Namen; mit `TxtExtension=0` lässt sich das Verhalten abschalten.

Bedient wird die Stream-Ansicht mit den üblichen Funktionstasten:

| Taste | Wirkung |
|---|---|
| F3 | Stream ansehen |
| F4 | Stream bearbeiten – beim Speichern wird er in die Datei zurückgeschrieben |
| F5 | Streams herauskopieren; sie werden zu normalen Dateien |
| F5 aus dem anderen Panel | normale Dateien hineinkopieren; sie werden zu Streams |
| F7 | neuen, leeren Stream anlegen |
| Umsch+F6 | Stream umbenennen |
| F6 | Streams herausverschieben |
| F8 | Streams löschen |
| Alt+Enter | Infos zum Stream |

Die Rücktaste führt zurück in den Ordner. Welche Schutzregeln dabei gelten, beschreibt das folgende Kapitel.

---

## 6. Schutzregeln

StreamList unterscheidet zwei Ebenen, und die Funktionstasten wirken jeweils nur auf die Ebene, die man gerade sieht:

| Ebene | F5 / F6 / F8 / Umsch+F6 wirken auf … |
|---|---|
| Übersicht: Ordneransicht, Dateilisten (Kapitel 8) | die echten Dateien und Ordner |
| Stream-Ansicht einer Datei, Streamlisten (Kapitel 8) | die Streams |

Daraus ergeben sich folgende Grundsätze:

- **Gelöschte Dateien und Ordner landen im Papierkorb.** In Dateisystem-Plugins wäre Löschen sonst endgültig; der Papierkorb macht einen Fehlgriff rückgängig. Laufwerke selbst werden nie gelöscht oder umbenannt.
- **Kopieren nimmt die Streams mit.** Eine kopierte Datei behält auf NTFS-Zielen ihre Streams.
- **Streams werden nur dort gelöscht, wo sie ausdrücklich als Streams aufgeführt sind.** Ein F8 auf eine Datei entfernt die Datei – nie nur einzelne ihrer Streams; umgekehrt kann ein F8 in der Stream-Ansicht nie die Datei selbst treffen.
- **Zeitstempel bleiben erhalten.** Schreibzugriffe auf einen Stream ändern unter NTFS das Änderungsdatum der Datei; StreamList stellt es anschließend wieder her.
- **Schreibgeschützte Dateien** bleiben unverändert; StreamList nennt den Grund.

Wer StreamList als reines Werkzeug für Streams nutzen möchte, schaltet die Dateioperationen mit `FileOperations=0` ab. Dann verändert StreamList ausschließlich Streams und weist bei Dateioperationen in der Übersicht auf diese Einstellung hin.

---

## 7. Direkteinstieg per Befehl

Für den täglichen Gebrauch ist der Weg über die Netzwerkumgebung umständlich. Zwei benutzerdefinierte Befehle in der `usercmd.ini` (neben der `wincmd.ini`) führen direkt an die gewünschte Stelle:

```ini
[em_StreamList]
cmd=cd
param=\\\StreamList\%P%N

[em_StreamListDir]
cmd=cd
param=\\\StreamList\%P
```

Unter *Konfiguration → Einstellungen → Diverses* lassen sich ihnen Tastenkürzel zuweisen; ebenso können sie auf Buttons gelegt werden.

| Befehl | ausgelöst im normalen Panel auf … | Ziel |
|---|---|---|
| `em_StreamList` | einer Datei | Stream-Ansicht dieser Datei |
| `em_StreamListDir` | beliebiger Stelle | aktueller Ordner in der Darstellung von StreamList |

Gedacht sind beide Befehle für das normale Panel; innerhalb von StreamList genügt Enter.

---

## 8. Laufwerksweite Auswertung mit Everything

**Everything 1.5** von voidtools führt einen Index über alle Dateien eines Systems und kann ihn auch nach Streams durchsuchen. StreamList nutzt diesen Index für die ∑-Summen und für laufwerksweite Listen, die in wenigen Sekunden vorliegen. Das Lesen, Schreiben und Löschen der Streams selbst übernimmt StreamList unmittelbar auf dem Datenträger.

### Einmalige Vorbereitung

Damit Everything Streams durchsuchen kann, müssen zwei Eigenschaften indiziert werden (in Everything sinngemäß unter *Extras → Optionen → Indizes → Eigenschaften*):

- **Namen alternativer Datenströme**
- **Anzahl alternativer Datenströme**

Der erste Indexlauf nimmt einige Zeit in Anspruch; danach hält Everything den Index selbständig aktuell. Fehlen die Eigenschaften, weist StreamList darauf hin, statt eine Suche zu beginnen, die ohne Index mehrere Minuten dauern würde.

### Summen je Ordner

Die in Kapitel 4 erwähnten `∑`-Werte zeigen, wo sich Streams konzentrieren:

| Name | Streams |
|---|---:|
| 📁 `Users` | ∑ 32.192 |
| 📁 `mingw64` | ∑ 23.044 |
| 📁 `tcmd` | ∑ 961 |
| 📁 `Windows` | ∑ 46 |
| 📁 `Program Files` | |

Hohe Werte bei entpackten Programmen wie `mingw64` sind typisch: Windows überträgt die Download-Kennzeichnung eines ZIP-Archivs beim Entpacken auf jede enthaltene Datei.

### Listen im Hauptverzeichnis

Die beiden Listen aus Kapitel 3 stellen die Treffer einer Everything-Suche über alle Laufwerke als flache Liste dar.

**`! Alle Dateien mit Streams`** enthält jede Datei mit mindestens einem Stream; Enter öffnet ihre Stream-Ansicht. Da die Einträge echte Dateien sind, lassen sie sich wie in jeder Suchergebnisliste kopieren, verschieben oder löschen.

**`! Downloads (Zone.Identifier)`** ist anders aufgebaut: Jeder Eintrag steht hier unmittelbar für den `Zone.Identifier` einer Datei.

| Name | Inhalt | Ordner |
|---|---|---|
| 📄 `__init__.py` | …\winlibs.zip | C:\mingw64\lib\python3.9\asyncio |
| 📄 `__init__ [2].py` | …\winlibs.zip | C:\mingw64\lib\python3.9\collections |
| 📦 `setup.zip` | https://example.org/… | C:\Users\…\Downloads |

Die Spalte **Ordner** gibt den Speicherort an; gleichnamige Dateien aus verschiedenen Ordnern werden durch einen Zusatz wie `[2]` unterschieden.

### Download-Kennzeichnungen entfernen

Da jeder Eintrag dieser Liste ein Stream ist, wirken die Funktionstasten unmittelbar: F3 zeigt die Kennzeichnung an, **F8 entfernt sie** – auch für eine große Zahl markierter Einträge in einem Durchgang, mit der üblichen Rückfrage und Fortschrittsanzeige von Total Commander.

Die Auswirkung im Einzelnen:

| | vorher | nachher |
|---|---|---|
| Datei | `alrext.exe` | `alrext.exe` – unverändert, gleiches Datum |
| Stream | `alrext.exe:Zone.Identifier` | entfernt |
| Windows | warnt beim Öffnen | warnt nicht mehr |

Die Kennzeichnung erfüllt eine Schutzfunktion. Bei vertrauenswürdigen, selbst entpackten Programmen ist sie entbehrlich; bei Dateien unklarer Herkunft empfiehlt es sich, sie zu belassen.

---

## 9. Eigene Listen

Die beiden Listen sind Voreinstellungen. In der `StreamList.ini` neben dem Plugin lassen sich bis zu zwanzig eigene Suchen definieren, jeweils bestehend aus einem Namen, einem Everything-Suchausdruck und optional einem Streamnamen:

```ini
[Search1]
Name=! Kommentare (ntfs_diz)
Query=alternate-data-stream-names:comment
Stream=Comment
```

Der Eintrag `Stream=` bestimmt die Art der Liste:

| `Stream=` | Art der Liste | Ein Eintrag ist … | Enter / F8 |
|---|---|---|---|
| leer | Dateiliste | eine Datei | Enter öffnet ihre Streams, F8 ist gesperrt |
| gesetzt | Streamliste | genau dieser Stream der Datei | F3/F4/F8 wirken direkt auf den Stream |

Sobald ein Abschnitt `[Search1]` vorhanden ist, treten die eigenen Listen an die Stelle der Voreinstellungen.

---

## 10. Eigene Spaltenansichten

Für eigene Spaltenansichten stellt StreamList folgende Felder bereit:

| Feld | zeigt … |
|---|---|
| `StreamCount` | Anzahl der Streams, bei Ordnern die ∑-Summe |
| `StreamNames` | Namen der Streams einer Datei |
| `Content` | je nach Ebene das Nützlichste: Namen, erste Zeile oder Herkunft |
| `Origin` | Download-Adresse (HostUrl) |
| `Referrer` | verweisende Seite oder Archiv (ReferrerUrl) |
| `Zone` | Internet, Intranet, Lokal … |
| `Preview` | erste Textzeile eines Streams |
| `Kind` | Text, Binär, Zoneninfo, Leer – oder **Programm (EXE/DLL)**, wenn ein Stream ausführbaren Code enthält. Das ist ein bekanntes Versteck für Schadsoftware und verdient einen genaueren Blick. |
| `Folder` | Ordner einer Datei in den Everything-Listen |
| `Size` | Größe der Datei bzw. des Streams; ersetzt in der Standardansicht die Größenspalte von Total Commander |
| `StreamsTotal` | Summe der Größen aller Streams einer Datei |
| `AllocatedSize` | tatsächlich belegter Platz eines Streams bzw. aller Streams einer Datei |
| `EntryModified` | letzte Änderung des NTFS-Eintrags – ändert sich auch dann, wenn das Änderungsdatum der Datei erhalten bleibt |
| `FullStreamName` | vollständiger Name in der Form `C:\…\datei.txt:Comment` |

In der Ordneransicht stehen außerdem die Felder anderer **Inhalts-Plugins** zur Verfügung, etwa von ntfs_diz oder xytags, da StreamList Total Commander zu jedem Eintrag die zugrunde liegende Datei mitteilt.

Hinweis für Nutzer früherer Versionen: Total Commander übernimmt die Standardansicht eines Plugins nur einmal. Wer die neue Größenspalte (ab 0.5.0) sehen möchte oder noch das alte Anzahl-Feld `Streams` (vor 0.4.1) verwendet, löscht die gespeicherte Ansicht; beim nächsten Betreten legt StreamList sie neu an.

---

## 11. Zusammenarbeit mit ntfs_diz

ntfs_diz legt Dateikommentare als Streams ab, jedes Kommentarfeld in einem eigenen Stream mit dem Feldnamen (`Comment`, `Comment1` usw.). In StreamList erscheinen diese Kommentare daher als gewöhnliche Streams, die sich mit F3 lesen und mit F4 bearbeiten lassen. Beide Plugins können parallel eingesetzt werden.

---

## 12. Einstellungen und Protokoll

Die Einstellungen lassen sich bequem über einen Dialog ändern: **Rechtsklick auf StreamList in der Netzwerkumgebung → Eigenschaften.** Er zeigt außerdem, ob Everything erreichbar ist und ob die beiden Stream-Eigenschaften indiziert sind – die häufigste Ursache, wenn die Listen leer bleiben. Eigene Listen (Kapitel 9) lassen sich dort anlegen, bearbeiten und mit „Testen“ sofort ausprobieren.

Das **?** in der Titelleiste oder **F1** öffnet diese Anleitung in der eingestellten Sprache, direkt beim Kapitel zum gerade gewählten Bedienelement.

Nach OK liest StreamList alles neu ein; sichtbar werden die Änderungen beim nächsten Einlesen einer Liste (Strg+R oder Ordnerwechsel). Ein Neustart von Total Commander ist nicht nötig.

**Protokoll.** StreamList hält jede Dateioperation mit Datum, Uhrzeit, Aktion, Quelle, Ziel und Ergebnis in der Datei `StreamList_operations.txt` neben dem Plugin fest. Die Schaltfläche „Protokoll anzeigen…“ im Dialog öffnet sie als sortierbare, filterbare Liste:

| Zeit | Aktion | Quelle | Ergebnis |
|---|---|---|---|
| 2026-10-05 14:03:12 | Stream gelöscht | `C:\…\setup.zip:Zone.Identifier` | OK |
| 2026-10-05 14:05:40 | In den Papierkorb | `C:\Temp\alt.txt` | OK |
| 2026-10-05 14:06:02 | Stream geschrieben | `C:\ADSTest\aa.txt:MyTag` | OK |

Das bloße Ansehen (F3) und das Zwischenspeichern beim Bearbeiten (F4) werden nicht festgehalten, wohl aber das Zurückschreiben. Die Datei ist tabulatorgetrennter Text und lässt sich auch im Lister oder in einer Tabellenkalkulation öffnen.

Dialog und Datei sind gleichwertig: Sämtliche Einstellungen stehen in der `StreamList.ini` neben dem Plugin und können auch von Hand geändert werden. Beim ersten Speichern aus dem Dialog wird die Datei in UTF-16 umgewandelt, damit auch Listennamen in nicht-lateinischer Schrift erhalten bleiben.

| Abschnitt | Eintrag | Bedeutung |
|---|---|---|
| `[Options]` | `KeepTime=1` | Datum der Datei beim Ändern von Streams erhalten |
| | `TxtExtension=1` | Text-Streams ohne Endung als `.txt` anzeigen (Kapitel 5) |
| | `FileOperations=1` | Dateioperationen auf echte Dateien in der Übersicht (Kapitel 6) |
| `[Protocol]` | `Enabled=1` | Dateioperationen protokollieren |
| | `MaxLines=10000` | höchstens so viele Einträge; ältere fallen weg |
| `[Settings]` | `Language=auto` | Sprache von Total Commander, sonst `eng`, `deu`, `rus`, `ukr`, `dan` |
| `[Everything]` | `Timeout=30000` | so lange (ms) höchstens auf Everything warten |
| | `MaxResults=100000` | höchstens so viele Einträge pro Liste |
| | `Exclude=!\$Recycle.Bin\` | an jede Suche angehängt; blendet den Papierkorb aus |
| | `FolderQuery=…` | was für die ∑-Summen gezählt wird; leer schaltet sie ab |
| | `FolderCacheMinutes=5` | so lange werden die Summen wiederverwendet |
| `[Search1]` … `[Search20]` | | eigene Listen (Kapitel 9) |

Zu `FolderQuery`: Synchronisationsdienste wie Dropbox versehen jede Datei mit einem eigenen Stream und dominieren dann die Summen. Mit `FolderQuery=alternate-data-stream-names:zone.identifier` beschränken sich die Summen auf Download-Kennzeichnungen.

---

## 13. Hinweise und Grenzen

- Streams gehen verloren, sobald eine Datei auf FAT32 oder exFAT kopiert, per E-Mail versandt oder bei vielen Cloud-Diensten hochgeladen wird.
- Ob Total Commander Streams beim Kopieren mitnimmt, regelt die Option `CopyStreams` (siehe Hilfe von Total Commander).
- Netz- und CD-Laufwerke werden im Hauptverzeichnis nicht aufgeführt, sind aber über `em_StreamListDir` erreichbar.
- Streams, die an Ordnern selbst hängen, werden derzeit nicht angezeigt.
- Größen und Zeitstempel in Spalten werden als Text geliefert, weil Total Commander Zahlenwerte bei Ordnerzeilen sonst nicht zuverlässig anzeigt. Größen sind deshalb rechtsbündig aufgefüllt und Zeitstempel im Format `JJJJ-MM-TT hh:mm:ss` geschrieben; so stimmt auch die Sortierung.
- Die ∑-Summen stammen aus dem Everything-Index, die Werte einzelner Dateien liest StreamList direkt vom Datenträger. Unmittelbar nach Änderungen können beide kurzzeitig voneinander abweichen.
- Ist Everything nicht gestartet, bleiben die ∑-Summen leer und die Listen zeigen einen Hinweis. StreamList versucht es nach etwa 30 Sekunden erneut; ein Neustart von Total Commander ist nicht nötig.

---

## 14. Fehlersuche

Zur Fehlersuche steht eine **Debug-Version** bereit. Sie protokolliert in die Datei `StreamList_debug.log` neben dem Plugin; die erste Zeile weist aus, welche Plugin- und welche Einstellungsdatei Total Commander tatsächlich lädt:

```text
=== DEBUG-Build geladen: C:\…\StreamList.wfx64 | INI: C:\…\StreamList.ini (vorhanden) | Log=1 ===
```

Fehlerberichte mit diesem Protokoll und einer kurzen Beschreibung sind über das Total-Commander-Forum oder GitHub willkommen. Die reguläre Version enthält keinen Protokollierungscode.

---

## 15. Übersetzungen

`StreamList.lng` enthält Englisch, Deutsch, Russisch, Ukrainisch und Dänisch. Beiträge in weiteren Sprachen nimmt der Autor gern über das Total-Commander-Forum oder GitHub entgegen.

---

## 16. Versionen

| Version | Änderungen |
|---|---|
| 0.7.1 | Hilfe per F1 oder ? direkt beim passenden Kapitel |
| 0.7.0 | Einstellungsdialog mit Everything-Status und Listen-Editor; Protokoll aller Dateioperationen |
| 0.6.0 | Übersicht mit normalen Dateioperationen (Löschen über den Papierkorb, abschaltbar); Enter führt in die Streams; ausführbare Streams werden als Programm gekennzeichnet |
| 0.5.1 | Text-Streams ohne Endung erscheinen als `.txt` (abschaltbar) |
| 0.5.0 | neue Felder Size, StreamsTotal, AllocatedSize, EntryModified und FullStreamName; korrekte Stream-Größen auch in Streamlisten |
| 0.4.4 | Everything 1.5 als Voraussetzung; nicht mehr benötigter Code entfernt; schnellerer Neuversuch, wenn Everything später startet |
| 0.4.3 | Debug- und Release-Version getrennt; Debug-Version meldet geladene Dateien |
| 0.4.0 | ∑-Summen für Ordner und Laufwerke; Feld `StreamCount` |
| 0.3.0 | Everything-Listen für die ganze Platte; Streamlisten mit direktem F3/F4/F8; Spalte Ordner |
| 0.2.0 | eigene Spalten und Standardansicht; Inhalts-Plugins anderer Autoren; Symbol |
| 0.1.1 | Hotkey funktioniert auch innerhalb des Plugins |
| 0.1.0 | erste Version: Streams auflisten, ansehen, bearbeiten, anlegen, umbenennen, löschen |
