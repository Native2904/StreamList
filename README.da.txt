StreamList 0.2.0
NTFS alternative datastrømme til Total Commander (filsystem-plugin, 32/64 bit)
Forfatter: Native2904 (TC-forum / GitHub)   Licens: MIT


HVAD ER ALTERNATIVE DATASTRØMME?
--------------------------------
På NTFS kan en fil ud over sit normale indhold bære yderligere, skjulte
"underfiler". Stifinder viser dem ikke, og filstørrelsen medregner dem ikke,
men de følger filen, så længe den ligger på NTFS.

Du har næsten helt sikkert nogle: browsere tilføjer en stream med navnet
"Zone.Identifier" til hver download. Den indeholder zonen (Internet) og ofte
adressen, filen kom fra - det er derfra, Windows-advarslen "Denne fil kom fra
en anden computer" stammer.

Prøv selv (kommandoprompt):
  echo Hovedindhold > test.txt
  echo Hej fra streamen > test.txt:hemmelig
  dir /r


KRAV
----
Total Commander 7.5 eller nyere, Windows 7 eller nyere, NTFS-drev.


INSTALLATION
------------
Åbn arkivet i Total Commander og bekræft installationen.
StreamList vises derefter i Netværk.


BRUG
----
Netværk -> StreamList viser dine NTFS-drev. Derunder navigerer du i normale
mapper, men filer vises som mapper: Enter på en fil åbner listen over dens
streams.

Direkte adgang fra et normalt panel - tilføj i usercmd.ini (ved siden af
wincmd.ini):

  [em_StreamList]
  cmd=cd
  param=\\\StreamList\%P%N

Tildel derefter en genvejstast under Konfiguration -> Indstillinger -> Diverse.
Brug genvejstasten i et normalt panel; inde i StreamList trykker du blot Enter.

Inde i en fil (stream-visning):
  F3          vis en stream
  F4          redigér en stream - når du gemmer, skrives den tilbage i filen
  F5          kopiér streams ud (de bliver til normale filer)
  F5 ind      kopiér normale filer ind i stream-visningen: de bliver streams
  F7          opret en ny, tom stream
  Shift+F6    omdøb en stream
  F6          flyt streams ud
  F8          slet streams
  Alt+Enter   stream-info; på en fil i mappevisningen: Stifinder-egenskaber


KOLONNER
--------
Ved åbning viser StreamList: Størrelse | Dato | Streams | Indhold | Oprindelse
  Mappevisning:    antal streams, deres navne, download-adresse
  Stream-visning:  første tekstlinje i hver stream (binære som hex)

Felter til egne kolonnesæt ([=streamlist.<felt>]):
  Streams, StreamNames, Origin, Referrer, Zone, Content, Preview, Kind

Indholds-plugins fra andre forfattere (f.eks. ntfs_diz, xytags) virker også
i mappevisningen, fordi StreamList oplyser den rigtige fil bag hver post.


SIKKERHED
---------
- StreamList sletter, omdøber eller opretter aldrig rigtige filer eller
  mapper.
- Streams kan kun slettes eller flyttes inde fra deres fils stream-visning.
  F8 på en fil eller mappe i mappevisningen viser kun en besked.
- Filens dato/tid bevares, når dens streams ændres (KeepTime).
- Skrivebeskyttede filer: streams kan ikke skrives; StreamList viser en
  besked.


KOMPATIBEL MED NTFS_DIZ
-----------------------
ntfs_diz gemmer hvert kommentarfelt som en stream med feltets navn
(Comment, Comment1, ...). StreamList viser disse streams, og de kan læses og
redigeres - begge plugins kan bruges side om side.


INDSTILLINGER (StreamList.ini ved siden af plugin'et)
-----------------------------------------------------
  [Options]  KeepTime=1     1 = bevar filens dato, når streams ændres
  [Settings] Language=auto  auto = TC-sprog, ellers eng/deu/rus/ukr/dan
  [Debug]    Log=0          1 = skriv StreamList_debug.log ved plugin'et


GODT AT VIDE
------------
- Streams går tabt, når en fil kopieres til FAT32/exFAT, sendes med e-mail
  eller uploades til mange cloud-tjenester.
- Hvis Total Commander ikke kopierer streams, se indstillingen CopyStreams i
  wincmd.ini (TC-hjælpen).
- Netværks- og cd-drev vises ikke i roden, men kan åbnes med genvejstasten
  eller cd \\\StreamList\X:\...
- Streams på mapper vises endnu ikke.
- Genvejstasten virker også inde i StreamList, men så viser TC plugin-navnet
  to gange i stien. Brug hellere Enter inde i plugin'et.


OVERSÆTTELSER
-------------
StreamList.lng indeholder engelsk, tysk, russisk, ukrainsk og dansk.
Nye sprog er velkomne - send dem via TC-forummet eller GitHub.


HISTORIK
--------
0.2.0  Egne kolonner og standardvisning, indholds-plugins fra andre
       forfattere i mappevisningen, ikon, beskeder i stedet for generelle fejl
0.1.1  Genvejstasten virker også inde i plugin'et
0.1.0  Første version: vis, redigér, tilføj, omdøb og slet streams
