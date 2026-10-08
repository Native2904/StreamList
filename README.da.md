# StreamList

**Se, redigér og ryd op i skjulte NTFS-datastrømme i Total Commander**

Version 1.2.1 · Filsystem-plugin (WFX) til 32 og 64 bit · Forfatter: Native2904 · Licens: MIT

Kapitlerne bygger videre på hinanden: fra det grundlæggende om alternative datastrømme over arbejdet med enkelte filer til analyse af hele drev med Everything. Kender du allerede StreamList, finder du tastetildelingen, felterne samt indstillinger og protokol samlet i kapitel 5, 10 og 12.

---

## 1. Hvad er en datastrøm?

NTFS gemmer indholdet af en fil i en datastrøm. Ud over denne hoveddatastrøm kan en fil have et vilkårligt antal yderligere, navngivne datastrømme – **alternative datastrømme** (*Alternate Data Streams*, ADS), i det følgende blot kaldt **streams**. Stifinder viser dem ikke, og den viste filstørrelse medregner dem ikke. Ved kopiering eller flytning inden for NTFS følger de alligevel filen.

Oftest støder man på dem som downloadmærket, ofte kaldet *Mark of the Web*: browsere tilføjer en stream `Zone.Identifier` til hver downloadet fil. Den registrerer den sikkerhedszone, filen stammer fra, og som regel også kildeadressen. Den advarsel, Windows viser ved åbning af sådanne filer, bygger på den.

Det kan gøres synligt i kommandoprompten med `dir /r`:

| Størrelse | Navn |
|---:|---|
| 4.858.880 | `alrext.exe` |
| 52 | `alrext.exe:Zone.Identifier:$DATA` |
| 87.552 | `ChmLib.dll` |
| 52 | `ChmLib.dll:Zone.Identifier:$DATA` |

Linjer på formen `fil:navn:$DATA` er streams; `alrext.exe` har altså en stream `Zone.Identifier` på 52 byte.

StreamList viser disse streams i Total Commander som almindelige poster, der kan vises, redigeres, oprettes og slettes.

---

## 2. Installation

Forudsætninger er Total Commander 7.5 eller nyere, Windows 7 eller nyere og **Everything 1.5** fra voidtools med indekserede stream-egenskaber (opsætning i kapitel 8). Åbn arkivet i Total Commander, og bekræft installationen; derefter vises StreamList under **Netværk**.

Kapitel 3 til 7 beskriver arbejdet med enkelte mapper og filer, kapitel 8 analysen af hele drev via Everything.

---

## 3. Første overblik

Posten **StreamList** under Netværk viser systemets NTFS-drev og to lister med et forstørrelsesglas:

| Navn | Streams |
|---|---:|
| 🔍 `! Fri søgning` | |
| 🔍 `! Alle filer med streams` | |
| 🔍 `! Downloads (Zone.Identifier)` | |
| 📁 `C` | ∑ 58.448 |
| 📁 `D` | ∑ 312 |

Listerne behandles i kapitel 8. Først handler det om visningen af almindelige mapper.

---

## 4. Mappevisningen

Inde i StreamList ser en mappe ud som vanligt, og filhandlingerne opfører sig som i et normalt panel: F5 kopierer, F6 flytter, F8 sletter de rigtige filer (kapitel 6). Der er én forskel: **Enter på en fil åbner ikke filen, men dens streams.**

Hvilke filer overhovedet har streams, viser den kolonnevisning, StreamList medbringer ved åbning:

| Navn | Størrelse | Streams | Indhold | Oprindelse |
|---|---:|---:|---|---|
| 📁 `Tools` | `<DIR>` | ∑ 31 | | |
| 📦 `setup.zip` | 2.418.330 | 1 | Zone.Identifier | https://example.org/… |
| 📄 `note.txt` | 1.204 | 2 | Comment, en | |
| 📄 `billede.png` | 88.112 | | | |

- **Streams** angiver antallet af streams for filer. For mapper står her, markeret med `∑`, antallet af filer med streams i hele undertræet; værdien stammer fra Everything-indekset (kapitel 8).
- **Indhold** nævner navnene på streams.
- **Oprindelse** indeholder kildeadressen fra `Zone.Identifier` for downloadede filer.

Tomme felter, som ved `billede.png`, betyder, at der ikke er nogen streams.

Har **mappen selv** streams, vises der desuden posten `[Streams]` øverst. Kapitel 5 forklarer den.

---

## 5. En fils stream-visning

Enter på `note.txt` skifter til stream-visningen af denne fil:

| Navn | Størrelse | Indhold |
|---|---:|---|
| 📄 `Comment.txt` | 7 | Faktura |
| 📄 `en.txt` | 16 | første stream |

Kolonnen **Indhold** viser her den første tekstlinje i hver stream; binære streams vises som en hexadecimal sekvens, f.eks. `4D 5A 90 00 …`.

Endelsen `.txt` er lånt: streams hedder i virkeligheden `Comment` og `en`. StreamList tilføjer den til tekst-streams uden egen endelse, så de kan åbnes med de sædvanlige programmer efter at være kopieret ud. Ved redigering, sletning og omdøbning arbejder StreamList altid med det rigtige navn. Streams med endelse (f.eks. `Zone.Identifier`) og binære streams beholder deres navn; med `TxtExtension=0` slås adfærden fra.

Stream-visningen betjenes med de sædvanlige funktionstaster:

| Tast | Virkning |
|---|---|
| F3 | vis en stream |
| F4 | redigér en stream – når du gemmer, skrives den tilbage i filen |
| F5 | kopiér streams ud; de bliver til normale filer |
| F5 fra det andet panel | kopiér normale filer ind; de bliver til streams |
| F7 | opret en ny, tom stream |
| Shift+F6 | omdøb en stream |
| F6 | flyt streams ud |
| F8 | slet streams |
| Alt+Enter | oplysninger om streamen |

Backspace fører tilbage til mappen. Hvilke beskyttelsesregler der gælder, beskriver det næste kapitel.

### Streams på mapper

Ikke kun filer, også mapper kan bære streams. De er endnu mindre iøjnefaldende end filernes og bruges derfor af og til som gemmested. Da Enter på en mappe fører ind i mappen, viser StreamList dem via en særskilt post:

| Navn | Streams |
|---|---:|
| 📁 `[Streams]` | 2 |
| 📁 `Undermappe` | ∑ 5 |
| 📄 `note.txt` | 1 |

`[Streams]` vises kun, hvis den åbnede mappe selv har streams, og fører til en almindelig stream-visning med alle funktionstaster. To andre veje fører dertil: kommandoen `em_StreamListFolder` (kapitel 7) for den markerede mappe og Alt+Enter på en mappe med streams, som giver valget mellem at vise dens streams eller dens egenskaber.

---

## 6. Beskyttelsesregler

StreamList skelner mellem to niveauer, og funktionstasterne virker altid kun på det niveau, man ser lige nu:

| Niveau | F5 / F6 / F8 / Shift+F6 virker på … |
|---|---|
| Oversigt: mappevisning, fillister (kapitel 8) | de rigtige filer og mapper |
| En fils stream-visning, streamlister (kapitel 8) | streams |

Heraf følger disse principper:

- **Slettede filer og mapper havner i papirkurven.** I filsystem-plugins ville sletning ellers være endelig; papirkurven gør et fejlgreb omgørligt. Selve drevene slettes eller omdøbes aldrig.
- **Kopiering tager streams med.** En kopieret fil beholder sine streams på NTFS-mål.
- **Streams slettes kun dér, hvor de udtrykkeligt står som streams.** F8 på en fil fjerner filen – aldrig blot enkelte af dens streams; omvendt kan F8 i stream-visningen aldrig ramme selve filen.
- **Tidsstempler bevares.** Skrivning til en stream ændrer filens ændringsdato på NTFS; StreamList genskaber den bagefter.
- **Skrivebeskyttede filer** forbliver uændrede; StreamList oplyser årsagen.

Vil man bruge StreamList som et rent værktøj til streams, slår man filhandlingerne fra med `FileOperations=0`. Så ændrer StreamList udelukkende streams og henviser til denne indstilling ved filhandlinger i oversigten.

---

## 7. Direkte adgang med kommando

Til daglig brug er vejen via Netværk besværlig. Fire brugerdefinerede kommandoer i `usercmd.ini` (ved siden af `wincmd.ini`) fører direkte til det ønskede sted:

```ini
[em_StreamList]
cmd=cd
param=\\\StreamList\%P%N

[em_StreamListDir]
cmd=cd
param=\\\StreamList\%P

[em_StreamListFolder]
cmd=cd
param=\\\StreamList\%P%N\[Streams]

[em_StreamListSearch]
cmd=cd
param=\\\StreamList\!find
```

Under *Konfiguration → Indstillinger → Diverse* kan de tildeles genvejstaster; de kan også lægges på knapper.

| Kommando | udløst i et normalt panel på … | Mål |
|---|---|---|
| `em_StreamList` | en fil | denne fils stream-visning |
| `em_StreamListDir` | et vilkårligt sted | den aktuelle mappe set med StreamLists øjne |
| `em_StreamListFolder` | en mappe | denne mappes streams (kapitel 5) |
| `em_StreamListSearch` | et vilkårligt sted | fri søgning (kapitel 9) |

Kommandoerne er beregnet til det normale panel; inde i StreamList er Enter nok.

Pladsholdere som `%P` og `%N` erstatter Total Commander kun i feltet **Parameter** i en kommando eller knap, ikke i feltet **Kommando**. Står alt dér, ankommer stien uændret; StreamList opdager det og gør opmærksom på det.

---

## 8. Analyse af hele drev med Everything

**Everything 1.5** fra voidtools fører et indeks over alle filer på et system og kan også søge i det efter streams. StreamList bruger dette indeks til ∑-summerne og til lister over alle drev, der er klar på få sekunder. Læsning, skrivning og sletning af selve streams udfører StreamList direkte på drevet.

### Engangsforberedelse

For at Everything kan søge i streams, skal to egenskaber indekseres (i Everything nogenlunde under *Funktioner → Indstillinger → Indekser → Egenskaber*):

- **Navne på alternative datastrømme**
- **Antal alternative datastrømme**

Den første indeksering tager noget tid; derefter holder Everything selv indekset opdateret. Mangler egenskaberne, gør StreamList opmærksom på det i stedet for at starte en søgning, der uden indeks ville tage flere minutter.

### Summer pr. mappe

De `∑`-værdier, der er nævnt i kapitel 4, viser, hvor streams er samlet:

| Navn | Streams |
|---|---:|
| 📁 `Users` | ∑ 32.192 |
| 📁 `mingw64` | ∑ 23.044 |
| 📁 `tcmd` | ∑ 961 |
| 📁 `Windows` | ∑ 46 |
| 📁 `Program Files` | |

Høje værdier for udpakkede programmer som `mingw64` er typiske: Ved udpakning overfører Windows et ZIP-arkivs downloadmærke til hver fil, det indeholder.

### Lister i roden

De to lister fra kapitel 3 viser resultaterne af en Everything-søgning på tværs af alle drev som en flad liste.

**`! Alle filer med streams`** indeholder hver fil med mindst én stream; Enter åbner dens stream-visning. Da posterne er rigtige filer, kan de kopieres, flyttes eller slettes som i enhver søgeresultatliste.

**`! Downloads (Zone.Identifier)`** er bygget anderledes: Her står hver post direkte for en fils `Zone.Identifier`.

| Navn | Indhold | Mappe |
|---|---|---|
| 📄 `__init__.py` | …\winlibs.zip | C:\mingw64\lib\python3.9\asyncio |
| 📄 `__init__ [2].py` | …\winlibs.zip | C:\mingw64\lib\python3.9\collections |
| 📦 `setup.zip` | https://example.org/… | C:\Users\…\Downloads |

Kolonnen **Mappe** angiver placeringen; filer med samme navn fra forskellige mapper skelnes med et tillæg som `[2]`.

### Fjernelse af downloadmærker

Da hver post i denne liste er en stream, virker funktionstasterne direkte: F3 viser mærket, **F8 fjerner det** – også for et stort antal markerede poster på én gang, med Total Commanders sædvanlige bekræftelse og fremdriftsvisning.

Virkningen i detaljer:

| | før | efter |
|---|---|---|
| Fil | `alrext.exe` | `alrext.exe` – uændret, samme dato |
| Stream | `alrext.exe:Zone.Identifier` | fjernet |
| Windows | advarer ved åbning | advarer ikke længere |

Mærket har en beskyttende funktion. For pålidelige programmer, man selv har pakket ud, er det overflødigt; for filer af uklar oprindelse er det tilrådeligt at lade det blive.

---

## 9. Egne lister

De to lister er standardlister. I `StreamList.ini` ved siden af plugin'et kan der defineres op til tyve egne søgninger, hver bestående af et navn, et Everything-søgeudtryk og eventuelt et stream-navn:

```ini
[Search1]
Name=! Kommentarer (ntfs_diz)
Query=alternate-data-stream-names:comment
Stream=Comment
```

Posten `Stream=` bestemmer listens type:

| `Stream=` | Listetype | En post er … | Enter / F8 |
|---|---|---|---|
| tom | filliste | en fil | Enter åbner dens streams; F5/F6/F8 virker på filen |
| angivet | streamliste | netop denne stream i filen | F3/F4/F8 virker direkte på streamen |

Så snart der findes en sektion `[Search1]`, træder de egne lister i stedet for standardlisterne.

### Fri søgning

Til skiftende søgeudtryk – f.eks. mærke-streams som `MyTag` – findes posten **„! Fri søgning"** i roden. Når den åbnes, viser den resultaterne af den seneste søgning. Øverst står posten **„! Ny søgning…"**: Enter eller dobbeltklik på den spørger efter en vilkårlig Everything-søgning; de seneste ti søgninger kan vælges. Resultaterne vises som filliste: F5, F6 og F8 virker på filerne, Enter åbner deres streams, og kolonnen *Preview* viser indholdet af deres streams. Resultaterne bevares indtil næste nye søgning. Kommandoen `em_StreamListSearch` (kapitel 7) fører direkte til listen.

---

## 10. Egne kolonnevisninger

Til egne kolonnevisninger stiller StreamList følgende felter til rådighed:

| Felt | viser … |
|---|---|
| `StreamCount` | antal streams; for mapper deres egne streams og ∑-summen af indholdet, f.eks. `1 · ∑ 5` |
| `StreamNames` | navnene på en fils streams |
| `Content` | det mest nyttige for hvert niveau: navne, første linje eller oprindelse |
| `Origin` | downloadadresse (HostUrl) |
| `Referrer` | henvisende side eller arkiv (ReferrerUrl) |
| `Zone` | Internet, Intranet, Lokal … |
| `Preview` | første tekstlinje i en stream; for filer og mapper indholdet af deres streams i kort form (uden Zone.Identifier) – f.eks. til mærke-streams som `MyTag` |
| `Kind` | Tekst, Binær, Zoneinfo, Tom – eller **Program (EXE/DLL)**, hvis en stream indeholder kørbar kode. Det er et kendt gemmested for skadelig software og fortjener et nærmere blik. |
| `Folder` | en fils mappe i Everything-listerne |
| `Size` | filens eller streamens størrelse; erstatter Total Commanders størrelseskolonne i standardvisningen |
| `StreamsTotal` | samlet størrelse af alle en fils streams eller en mappes egne streams |
| `AllocatedSize` | faktisk optaget plads for en stream eller alle streams i en fil eller mappe |
| `EntryModified` | seneste ændring af NTFS-posten – ændres også, når filens ændringsdato bevares |
| `FullStreamName` | fuldt navn på formen `C:\…\fil.txt:Comment` |

I mappevisningen er felterne fra andre **indholds-plugins** også tilgængelige, f.eks. fra ntfs_diz eller xytags, da StreamList oplyser Total Commander om den underliggende fil for hver post.

Bemærkning til brugere af en testversion: Total Commander overtager et plugins standardvisning kun én gang. Forbliver kolonner tomme, så slet den gemte visning; næste gang plugin'et åbnes, opretter StreamList den på ny.

---

## 11. Samarbejde med ntfs_diz

ntfs_diz gemmer filkommentarer som streams, hvert kommentarfelt i sin egen stream med feltets navn (`Comment`, `Comment1` osv.). I StreamList vises disse kommentarer derfor som almindelige streams, der kan læses med F3 og redigeres med F4. Begge plugins kan bruges side om side.

---

## 12. Indstillinger og protokol

Indstillingerne kan nemt ændres i en dialog: **højreklik på StreamList under Netværk → Egenskaber.** Den viser også, om Everything kan nås, og om de to stream-egenskaber er indekseret – den hyppigste årsag, når listerne forbliver tomme. Egne lister (kapitel 9) kan oprettes, redigeres og straks afprøves med „Test" dér.

**?** i titellinjen eller **F1** åbner denne vejledning på det valgte sprog, direkte ved kapitlet for det aktuelt valgte kontrolelement. Vejledningen ligger derfor som Windows-hjælp `StreamList_dan.chm` ved siden af plugin'et; et downloadmærke på denne fil fjerner StreamList selv ved åbning, ellers ville siderne forblive tomme.

Efter OK indlæser StreamList alt på ny; ændringerne bliver synlige, næste gang en liste læses (Ctrl+R eller mappeskift). Det er ikke nødvendigt at genstarte Total Commander.

**Protokol.** StreamList registrerer hver filhandling med dato, klokkeslæt, handling, kilde, mål og resultat i filen `StreamList_operations.txt` ved siden af plugin'et. Knappen „Vis protokol…" i dialogen åbner den som en sorterbar, filtrerbar liste:

| Tid | Handling | Kilde | Resultat |
|---|---|---|---|
| 2026-10-05 14:03:12 | Stream slettet | `C:\…\setup.zip:Zone.Identifier` | OK |
| 2026-10-05 14:05:40 | Flyttet til papirkurven | `C:\Temp\gammel.txt` | OK |
| 2026-10-05 14:06:02 | Stream skrevet | `C:\ADSTest\aa.txt:MyTag` | OK |

Blot at vise (F3) og den midlertidige kopi ved redigering (F4) registreres ikke, men tilbageskrivningen gør. Filen er tabulatorsepareret tekst og kan også åbnes i Lister eller i et regneark.

Dialog og fil er ligeværdige: Alle indstillinger står i `StreamList.ini` ved siden af plugin'et og kan også ændres i hånden. Første gang der gemmes fra dialogen, konverteres filen til UTF-16, så også listenavne med ikke-latinske tegn bevares.

| Sektion | Post | Betydning |
|---|---|---|
| `[Options]` | `KeepTime=1` | bevar filens dato, når streams ændres |
| | `TxtExtension=1` | vis tekst-streams uden endelse som `.txt` (kapitel 5) |
| | `FileOperations=1` | filhandlinger på rigtige filer i oversigten (kapitel 6) |
| `[Protocol]` | `Enabled=1` | registrér filhandlinger |
| | `MaxLines=10000` | højst så mange poster; ældre falder bort |
| `[Settings]` | `Language=auto` | Total Commanders sprog, ellers `eng`, `deu`, `rus`, `ukr`, `dan` |
| `[Everything]` | `Timeout=30000` | vent højst så længe (ms) på Everything |
| | `MaxResults=100000` | højst så mange poster pr. liste |
| | `Exclude=!\$Recycle.Bin\` | føjes til hver søgning; skjuler papirkurven |
| | `FolderQuery=…` | hvad der tælles med i ∑-summerne; tom slår dem fra |
| | `FolderCacheMinutes=5` | så længe genbruges summerne |
| `[Search1]` … `[Search20]` | | egne lister (kapitel 9) |

Om `FolderQuery`: Synkroniseringstjenester som Dropbox giver hver fil sin egen stream og dominerer så summerne. Med `FolderQuery=alternate-data-stream-names:zone.identifier` begrænses summerne til downloadmærker.

---

## 13. Bemærkninger og begrænsninger

- Streams går tabt, så snart en fil kopieres til FAT32 eller exFAT, sendes med e-mail eller uploades til mange cloud-tjenester.
- Om Total Commander tager streams med ved kopiering, styres af indstillingen `CopyStreams` (se Total Commanders hjælp).
- Netværks- og cd-drev vises ikke i roden, men kan nås via `em_StreamListDir`.
- Størrelser og tidsstempler i kolonner leveres som tekst, fordi Total Commander ellers ikke pålideligt viser talværdier i mappelinjer. Størrelser er derfor højrestillede, og tidsstempler skrives som `ÅÅÅÅ-MM-DD tt:mm:ss`; så stemmer sorteringen også.
- ∑-summerne stammer fra Everything-indekset, værdierne for enkelte filer læser StreamList direkte fra drevet. Lige efter ændringer kan de kortvarigt afvige fra hinanden.
- Kører Everything ikke, forbliver ∑-summerne tomme, og listerne viser en besked. StreamList prøver igen efter cirka 30 sekunder; det er ikke nødvendigt at genstarte Total Commander.

---

## 14. Fejlfinding

Til fejlfinding findes en **fejlfindingsversion**. Den logger til filen `StreamList_debug.log` ved siden af plugin'et; den første linje viser, hvilken plugin- og hvilken indstillingsfil Total Commander faktisk indlæser:

```text
=== DEBUG-Build geladen: C:\…\StreamList.wfx64 | INI: C:\…\StreamList.ini (vorhanden) | Log=1 ===
```

Fejlrapporter med denne log og en kort beskrivelse modtages gerne via Total Commander-forummet eller GitHub. Den almindelige version indeholder ingen logningskode.

---

## 15. Oversættelser

`StreamList.lng` indeholder engelsk, tysk, russisk, ukrainsk og dansk. Bidrag på flere sprog modtager forfatteren gerne via Total Commander-forummet eller GitHub.

---

## 16. Versioner

| Version | Ændringer |
|---|---|
| 1.2.1 | Hjælp som CHM på alle fem sprog: F1 og ? springer pålideligt til det rette kapitel, med indholdsfortegnelse og søgning |
| 1.2.0 | Fri søgning: en ny søgning startes kun via posten „! Ny søgning…" – inputdialogen åbner ikke længere utilsigtet, f.eks. når musen føres hen over den |
| 1.1.2 | Mapper viser deres egne streams også i StreamCount, StreamsTotal og AllocatedSize |
| 1.1.1 | Alt+Enter på en mappe med streams: „Vis streams" åbner nu pålideligt stream-visningen |
| 1.1.0 | Fri søgning: vilkårlig Everything-søgning som filliste, med historik |
| 1.0.1 | Kolonnen Preview viser i oversigten indholdet af filers og mappers streams |
| 1.0.0 | Første officielle version: stream-visning for filer og mapper, Everything-lister og ∑-summer, filhandlinger i oversigten, lånt `.txt`-endelse, kolonner sammenlignelige med AlternateStreamView, indstillingsdialog med hjælp, protokol |
| 0.1 – 0.7 | Forhåndsversioner til testere i Total Commander-forummet |

---

<p class="signoff"><em>Lavet med kærlighed – og med <a href="https://somafm.com">SomaFM</a> i ørerne. ♥</em></p>
