---
title: Azure Media Player ändringsloggen
description: Azure Media Player ändringsloggen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: c63a8efc4bbcf6e5a124d439e6f9f91397e2fa53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370584"
---
# <a name="changelog"></a>Ändringslogg

## <a name="236-official-update-september-21-2020"></a>2.3.6 (officiell uppdatering september 21 2020)

### <a name="features-236"></a>Funktioner 2.3.6

Stöd för att lägga till ljudstöd för azureHtml5JS-teknik (tank streck) har stöd för sen start av direkt avskrifts stöd språk ändring i direkt skriftering

### <a name="bug-fixes-236"></a>Fel korrigeringar 2.3.6

När du använder "playsinline" i HLS-uppspelningar på Apple-enheter, kan du klicka på "LIVE"-knappen för att starta om bilden av en AMP-bild ibland orsakar ett undantag. knappen volym saknas när du skulle spela upp HLS FairPlay [Accessibility] knapp beskrivningar som inte har definierats för knappar när tangent bordet används [Hjälpmedel] ljus styrkas förhållandet är mindre än 1.3:1 för förlopps indikatorn [Hjälpmedel] tangent bords fokus är ibland inte tillbaka till knappen video kvalitet [Hjälpmedel]-kontrollerna syns inte i videon skärm, vilket hindrar skärm läsaren från att hitta dem

### <a name="changes-236"></a>Ändringar 2.3.6

Returnera meningsfulla fel leverans fel för att anropa program

## <a name="235-official-update-june-1-2020"></a>2.3.5 (officiell uppdatering juni 1 2020)

### <a name="bug-fixes-235"></a>Fel korrigeringar 2.3.5

- Tågen ESC Key Listener i alternativ fönstret är kopplad till dokumentet
- Tågen Förhindra att Player-ANVÄNDARGRÄNSSNITTET visas om kontroll fältet eller alternativ-menyn har fokus
- Kontroll fältet visar felaktig vägg Clock-tid när visnings inställningarna för väggens klock slag är aktiverat

### <a name="changes-235"></a>Ändringar 2.3.5

- Fel meddelande har lagts till för felkod 0x00400005 och dokumenteras

## <a name="234-official-update-march-4-2020"></a>2.3.4 (officiell uppdatering mars 4 2020)

### <a name="bug-fixes-234"></a>Fel korrigeringar i 2.3.4

- Det går inte att ange PlayReady-overrideLicenseAcquistionUrl
- Det gick inte att spela upp innehåll med discontinuities
- Tågen Värdet för ID-attribut för skärm läsar avisering måste vara unikt
- Tågen När du navigerar i dialog rutan inställningar för textning flyttar du bort från dialog rutan

### <a name="changes-234"></a>Ändringar i 2.3.4

- Logga innehålls längd efter en lyckad nedladdning för att analysera dekrypterings fel 2.3.3 (officiell uppdatering november 12 2019)

### <a name="features-234"></a>Funktioner i 2.3.4

- Stöd har lagts till för att Visa väggens Klock tid för en video som ett överlägg och i kontroll fältet

### <a name="bug-fixes-234"></a>Fel korrigeringar i 2.3.4

- Ljud spårs växeln fungerar men utdata har fel på IE11-och Windows7-objektet stöder inte egenskapen eller metoden Enabled
- Ljud spårs växeln Miss lyckas när bufferten har lästs in helt
- Ljud spårs växeln Miss lyckas när användaren pausar video och växlar mellan ljud spår snabbt
- Tågen Knapp beskrivningar har inte definierats för video kontroll under Videos pelaren
- Volym knappar saknas på HTML5 beroende på när ' loadstart ' tas emot
- Tågen Det går inte att ange alternativ texten för affisch bilden
- Tågen Program fokus försvinner när du har valt Slutför i dialog rutan inställningar för under texter
- Tågen Felaktiga ARIA-attribut har definierats för video under segment Preview

### <a name="changes-234"></a>Ändringar i 2.3.4

- En tom beskrivnings etikett har tagits bort från HLS på iOS och macOS Safari
- Minska antalet 412s för IMSC1-beskrivningar
- Varning om utdata i-konsolen för 10 på varandra följande tomma IMSC1-textning för att hjälpa till att använda Live-felsökning

## <a name="232-official-update-october-9-2019"></a>2.3.2 (officiell uppdatering oktober 9 2019)

### <a name="features-232"></a>Funktioner 2.3.2

– PlayReady-stöd har lagts till för streck uppspelning för krom Edge-webbläsare

### <a name="bug-fixes-232"></a>Fel korrigeringar 2.3.2

- Den aktuella uppspelnings hastigheten visas inte visuellt i uppspelnings hastigheten om användaren inte anger den manuellt
- Tågen Panelen Inställningar komprimeras inte med ESC-tangenten
- Tågen Kortkommando kortkommandot ' ' fungerar inte när skärm läsaren är på

### <a name="changes-232"></a>Ändringar 2.3.2

- För webbläsare som inte stöder E-AC3 ljud-codec är E-AC3 ljud spår dolda på ljud spårs menyn
- För webbläsare som stöder E-AC3 ljud-codec är ett E-AC3 ljud spår valt som standard
- För webbläsare som inte stöder ljud-codec-växling är ljud spår med en annan codec från det valda spåret dolda på ljud spårs menyn

## <a name="231-official-update-august-12-2019"></a>2.3.1 (officiell uppdatering augusti 12 2019)

### <a name="features-231"></a>Funktioner 2.3.1

- Signalerar en händelse när EMSG rutor tas emot i streck uppspelning – stöd för att Visa EG-3 ljud spår i ljud menyn i webbläsare som stöder EG-3 och för att växla ljud spår från AAC till EC3 och vice versa endast på den Chromimum-baserade Edge-webbläsaren

### <a name="bug-fixes-231"></a>Fel korrigeringar 2.3.1

- Ljud spårs menyn är skadad efter borttagning av EC-3-spår
- Den aktuella tiden kan vara bra än videons varaktighet
- Det fungerar inte att ställa in uppspelnings hastigheten via initialSpeed
- Ibland efter en sökning verkar spelaren vara fastnad
- På kant-och Internet Explorer på en touch-skärm, efter att ha zoomat in på en sida, så får du inte korrekt segment i videon genom att trycka eller hovra över SeekBar.
- Tågen Aria-etiketten för Play/Pause är inte beskrivande för Video Player Map live segment kunde inte hittas fel meddelande för blixt till rätt amp-fel
- Tågen ARIA-roller som används för Play/Pause måste följa giltiga värden (. VJS-text-Track-display)
- Tågen Vissa ARIA-roller måste ingå i vissa föräldrar
- Tågen Ingen knapp beskrivning har definierats för knappen Spela upp/pausa i Videos pelaren IMSC1-textning kan försvinna efter sökning i den aktuella video-/ljudbufferten

### <a name="changes-231"></a>Ändringar 2.3.1

- Vid hämtningen av en segmentDecryptError och spelaren redan finns på Live Edge uppdaterar spelaren nu manifestet i stället för att försöka med nästa segment
- Ytterligare loggning för diagnostik har lagts till
- Uppdaterad dokumentation för att inkludera FairPlay-stöd för iOS Safari
- Ett exempel har lagts till för alternativet "srclang" i IMSC1
- Utfyllnad, textPadding, boxShadow åsidosättningar har lagts till för text spår.
- En felkod (0x0020025B) har lagts till för att skilja på att nedladdningen av segment misslyckades på grund av inget Internet snarare än att bara hämta 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (officiella versionen april 30 2019)

### <a name="features-230"></a>Funktioner 2.3.0

- Stöd har lagts till för IMSC1-textning för streck
- Stöd har lagts till för endast video till gångar för streck
- API-presentationTimeOffsetInSec har lagts till

### <a name="bug-fixes-230"></a>Fel korrigeringar 2.3.0

- En heuristiks profil för AMP-LowLatency störs av iOS-videouppspelningen "mute" och "unmute" för vissa språk har fel översättningar
- Aria-valuenow-värdet för skjutreglaget för förlopps indikatorn är ibland felaktigt
- Aria för text spårets visning är felaktigt

### <a name="changes-230"></a>Ändringar 2.3.0

- Loggar innehåller nu storleken på hämtade medie fragment
- Tog bort support för IE 9 och IE 10
- Uppdaterat CEA708-exempel för att Visa Vänsterjustera text texter
- Inkludera MediaError. Message i loggar för uppspelnings fel

## <a name="224-official-update-february-22-2019"></a>2.2.4 (officiell uppdatering februari 22 2019) ##

### <a name="bug-fixes-224"></a>Fel korrigeringar 2.2.4 ###

- [Fel korrigering] AMP Tågen Tog bort en nåbar flik för att hitta en nåbar skärm när fel skärmen visas
- [Fel korrigering] AMP Åtgärdat kortkommandot "för IE11 och Edge
- [Fel korrigering] AMP Åtgärdat ett undantag för CEA708-beskrivningar
- [Fel korrigering] AMP Problem med en video frysning för Edge-webbläsaren

### <a name="changes-224"></a>Ändringar 2.2.4 ###

- Ändrade AMP När ett fragment-dekrypterings fel inträffar försöker spelaren igen aktuella och olika fragment för att återställa uppspelningen
- Ändrade AMP Gjorde AMP mer tolerant för överlappande video eller ljudfragment

## <a name="223-official-update-january-9-2019"></a>2.2.3 (officiell uppdatering januari 9 2019) ##

### <a name="features-223"></a>Funktioner 2.2.3 ###

- Zoomfunktionen HLS Ljud spårs menyn har lagts till för Safari HLS-uppspelning

### <a name="bug-fixes-223"></a>Fel korrigeringar 2.2.3 ###

- [Fel korrigering] AMP Tågen Under direkt sändnings uppspelning kan du inte välja knappen Live genom att använda tangent bordet
- [Fel korrigering] AMP Fast falsk positiva fel 0x0400003 fel på grund av misslyckad MSE-test
- [Fel korrigering] AMP Ett problem har åtgärd ATS där videon skulle låsas när en Live-dataström startades

### <a name="changes-223"></a>Ändringar 2.2.3 ###

- Ändrade AMP Mer information har lagts till i loggen för att möjliggöra bättre diagnostik
- Ändrade AMP Om fler än en bit hastighet finns på samma skärmupplösning, är alla bit hastigheter tillgängliga för val

## <a name="222-official-update"></a>2.2.2 (officiell uppdatering) ##

### <a name="bug-fixes-222"></a>Fel korrigeringar 2.2.2 ###

- [Fel korrigering] AMP När spelaren påträffar ett tillfälligt nätverks avbrott, stoppas uppspelningen omedelbart
- [Fel korrigering] AMP Tågen Fel dialog rutan är inte tillgänglig för tangent bordet
- [Fel korrigering] AMP Oändlig rotations ruta visas vid uppspelning av ljud endast till gång i stället för fel som inte stöds

### <a name="changes-222"></a>Ändringar i 2.2.2 ###

- Ändrade [AMP] lokaliserade lokaliserade strängar för annons gränssnitt

## <a name="221-official-update"></a>2.2.1 (officiell uppdatering) ##

### <a name="features-221"></a>Funktioner 2.2.1 ###

- Zoomfunktionen [CMAF] Stöd har lagts till för HLS-CMAF

### <a name="bug-fixes"></a>Felkorrigeringar ###

- [Fel korrigering] [AMP] avtömda timers i omprövnings logik ger uppspelnings fel
- [Fel korrigering] AMP [Firefox] avslutad händelse utlöses inte på Firefox och Chrome när Live-programmet stoppas
- [Fel korrigering] AMP Kontroller som visas efter setsource, även när kontroller har angetts till falskt i alternativ för spelare

### <a name="changes"></a>Ändringar ###

- Ändrade [Direkt textning] API-namnet för CEA-textning har ändrats från 608 till 708. Mer information finns i [Inställningar för CEA708-textning](/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (officiell utgåva) ##

### <a name="features-220"></a>Funktioner 2.2.0 ###

- Zoomfunktionen [Azurehtml5JS] Utvecklingsverktyget [LiveCaptions] Stöd för CEA 708-textning i Azurehtml5JS och blixt teknisk information för tydliga och AES-innehåll.

### <a name="bug-fixes-220"></a>Fel korrigeringar 2.2.0 ###

- [Fel korrigering] Identifiering av Flash-versioner fungerar inte i Chrome/Edge

### <a name="changes-220"></a>Ändringar 2.2.0 ###

- Ändrade AMP Heuristik Ändrade heuristisk profil namn från snabb start till LowLatency
- Ändrade Utvecklingsverktyget Ändra i Flash Player för versions identifiering för att aktivera uppspelning av AES-innehåll med den nya Adobe Flash-uppdateringen.

## <a name="219-official-hotfix"></a>2.1.9 (officiell snabb korrigering) ##

### <a name="bug-fixes-219"></a>Fel korrigeringar 2.1.9 ###

- [Fel korrigering] Realtidsinformation Undantag som inträffar när Live Streams övergår till video på begäran/Live-Arkiv

### <a name="changes-219"></a>Ändringar 2.1.9 ###

- Ändrade Utvecklingsverktyget AES Modifierad teknik för Flash-teknik för att inte använda sharedbytearrays för AES-dekryptering eftersom Adobe har blockerat användningen från och med Flash 30. Observera att uppspelningen bara fungerar när Adobe distribuerar en ny version av Flash på grund av ett fel i V30. Mer information finns i [kända problem](azure-media-player-known-issues.md)

## <a name="218-official-update"></a>2.1.8 (officiell uppdatering) ##

### <a name="bug-fixes-218"></a>Fel korrigeringar 2.1.8 ###

- [Fel korrigering] Rotations rutan ibland visar inte post-sökning och för-uppspelning
- [Fel korrigering] Spelaren startar inte tyst när alternativet avstängd är aktiverat
- [Fel korrigering] Skjutreglaget för volym visas när kontroller är inställda på falskt
- [Fel korrigering] Uppspelningen upprepas ibland när användaren hoppar till Live Edge
- [Fel korrigering] Firefox Player returnerar ibland JavaScript-undantag vid inläsning
- [Fel korrigering] Tågen Knappen Spela upp/pausa/volym förlorar fokus på konturen när den markeras med tangent bords kontroller
- [Fel korrigering] Det fasta minnes läckaget i spelaren tas bort
- [Fel korrigering] Anrop till src () efter det att spelarna har slutat återställa källan
- [Fel korrigering] Realtidsinformation AMP är i ett konstant inläsnings tillstånd när användaren klickar på Live-knappen när sändningen har slutförts
- [Fel korrigering] Chrome Spelare låser sig och spelas inte upp när webbläsaren minimeras till bakgrund.

### <a name="changes-218"></a>Ändringar 2.1.8 ###

- Ändrade Uppdaterad 0x0600001-fel för att visa när AES-innehåll spelas upp med Flash 30 eftersom det inte stöds för tillfället. Mer information finns i [kända problem](azure-media-player-known-issues.md)
- Ändrade Ytterligare återförsök har lagts till för Live-scenarier när manifest begär 404 eller returnerar tomma manifest.

## <a name="217-official-update"></a>2.1.7 (officiell uppdatering) ##

### <a name="features-217"></a>Funktioner 2.1.7 ###

- Zoomfunktionen [AzureHtml5JS] Konfigurations alternativet har lagts till för att tömma inaktuella data i medie källans buffert

### <a name="bug-fixes-217"></a>Fel korrigeringar 2.1.7 ###

- [Fel korrigering] Tågen [Skärm läsare] Det tomma sidhuvudet som angavs när title har inte angetts har tagits bort
- [Fel korrigering] [UWA] Det utlöses undantag vid uppspelning i Universal Windows-appen
- [Fel korrigering] [OSX] setActiveTextTrack () fungerar inte i Safari på OSx
- [Fel korrigering] Realtidsinformation Om du klickar på den levande kanten efter att du har avmarkerat och initierat om spelaren ger upphov till undantag
- [Fel korrigering] Skal Aktuell tid trunkerad för vissa till gångar
- [Fel korrigering] [DRM]-korrigering ingår som stöd för uppspelning i webbläsare som stöder flera CENC DRM

### <a name="changes-217"></a>Ändringar 2.1.7 ###

- Ändrade Stickprov Tågen Språk tag gen har lagts till i alla exempel

## <a name="216-official-update"></a>2.1.6 (officiell uppdatering) ##

### <a name="bug-fixes-216"></a>Fel korrigeringar 2.1.6 ###

- [Fel korrigering] AMP visar felaktig varaktighet för en speciell till gång
- [Fel korrigering] [FairPlay-HLS] Fairplay-fel som inte sprids till UI
- [Fel korrigering] Anpassade heuristiska egenskaper ignoreras i AMP-2.1.5.

### <a name="changes-216"></a>Ändringar 2.1.6 ###

- Ändrade [FairPlayDRM] Tids gränsen för både certifikatbegäran och licens förfrågan för FairPlay har tagits bort för att hålla paritet med PlayReady-och Widevine-implementeringar
- Ändrade Förbättringar av diverse heuristik för att bekämpa suddigt innehåll

### <a name="features-216"></a>Funktioner 2.1.6 ###

- Zoomfunktionen Stöd för formatet mpd-Time-cmaf har lagts till

## <a name="215-official-hotfix"></a>2.1.5 (officiell snabb korrigering) ##

### <a name="bug-fixes-215"></a>Fel korrigeringar 2.1.5 ###

- [Fel korrigering] Texter VTT-format återges inte korrekt av spelaren
- [Fel korrigering] Tågen Live-knappen har ingen Aria-etikett

## <a name="214-official-update"></a>2.1.4 (officiell uppdatering) ##

### <a name="bug-fixes-214"></a>Fel korrigeringar 2.1.4 ###

- [Fel korrigering] Tågen Aktiv Användare kan inte fokusera på anpassade knappar som har lagts till höger i hel skärms knappen i kontroll fältet
- [Fel korrigering] [IE11] [Volym fält] När du tabbar till volymens popup-fönster får hela skärmen Blink i IE11 i hel skärms läge
- [Fel korrigering] [Skal | Tömning] utrymme som visas mellan kontroll fältet och list rutan för volym fältet
- [Fel korrigering] AMP Texter Gamla inbäddade spår rensas inte när källan ändras på en befintlig spelare
- [Fel korrigering] Tågen Skärm läsaren Skärm läsaren läser volym kontrollen felaktigt
- [Fel korrigering] [Blixt] Play-händelsen utlöses ibland inte från Flash Tech
- [Fel korrigering] AMP Aktiv Play/Pause kräver två klick när spelaren har fokus och är i full skärms läge
- [Fel korrigering] AMP Skal Felaktig varaktighet visas i förlopps indikatorn för en angiven till gång
- [Fel korrigering] Annonser [AD-Butler] Den stora parsern hanterar inte stora filer som inte har någon förlopps händelse
- [Fel korrigering] SDN [AMP] Ett problem har åtgärd ATS för stöd för Hive-SDN-plugin
- [Fel korrigering] Tågen Skärm läsaren läser "midnatt på knappen" när användaren har fokus på volym knappen

### <a name="changes-214"></a>Ändringar 2.1.4 ###

- Ändrade Tågen [Hjälpmedels teknik] Knapparna har nu Aria-Live-egenskap för att förbättra upplevelsen med hjälpmedels teknik
- Ändrade Tågen [Volym knapp | Skärm läsaren] förbättrad tillgänglighet för volym knappar genom att ändra funktionerna för tabbar och skjutreglaget. De här ändringarna gör det enklare för tangent bords användare att ändra spelarens volym
- Ändrade Tids gräns för snabb meny för inaktivitet har ökats från 3 till 5 sekunder
- Ändrade Tågen Luminans Förbättrat kontrast förhållande för Luminiscens i list Rute inställningar

## <a name="213-official-update"></a>2.1.3 (officiell uppdatering) ##

### <a name="bug-fixes-213"></a>Fel korrigeringar 2.1.3 ###

- [Fel korrigering] [Plugin-program | Rubrik överlägg] rubrik överlägg-plugin-program genererar JS-undantag med AMP v2. X +
- [Fel korrigering] Käll uppsättnings händelsen skickas till JavaScript-konsolen även när loggningen är avstängd
- [Fel korrigering] Skal Tips för Player-tid återges utanför kontexten för spelaren vid hovring över slut varaktighets fältet
- [Fel korrigering] Tågen [Skärm läsare] Skärm läsaren läser "region landmärke" eller "Video Player regions landmärke" när visnings programmet har fokus på spelaren
- [Fel korrigering] AMP Det går inte att inaktivera Player-disposition via CSS
- [Fel korrigering] Tågen Det går inte att fokusera på hela spelaren när användaren är i hel skärms läge
- [Fel korrigering] Skal Realtidsinformation Skalet svarar inte på lokaliserad DIREKTSÄND text på japanska
- [Fel korrigering] Skal Varaktighet och aktuell tid som kapas när Stream > 60 min-[fel korrigering] [iPhone | Live] Player visar text för aktuell tid/varaktighet i kontroll fältet
- [Fel korrigering] AMP API: er för heuristik för att anropa spelare ger JavaScript-undantag
- [Fel korrigering] [Native HTML5 | iOS] Videotag-egenskapen "playsinline" sprids inte till Player
- [Fel korrigering] [iOS | iframe] Det går inte att ange hel skärms läge på iPhone om spelaren har lästs in i en iframe
- [Fel korrigering] AMP Heuristik AMP fungerar alltid med hybrid profil oavsett alternativ för spelare
- [Fel korrigering] [AMP | Win 8.1] utlöses när den körs i Win 8.1-appen med en webbvy

### <a name="changes-213"></a>Ändringar 2.1.3 ###

- Ändrade AMP Information om CDN-slutpunkt har lagts till i FragmentDownloadComplete-händelsen
- Ändrade AMP Realtidsinformation Förbättrad och optimerad Live streaming-latens

## <a name="212-official-hotfix"></a>2.1.2 (officiell snabb korrigering) ##

### <a name="bug-fixes-212"></a>Fel korrigeringar 2.1.2 ####

- [Fel korrigering] Tågen [Windows skärm läsaren] Skärm läsaren läser "Progress midnatt" när användaren har kontext för förlopps indikatorn och aktuell tid är 0:00
- [Fel korrigering] [Skal] logo typ storleken är hårdkodad i JavaScript-kod
- Tågen Snabb tangenter Snabb tangenter är inte aktiverade när användaren klickar på spelaren.

### <a name="changes-212"></a>Ändringar i 2.1.2 ####

- Ändrade Logging Logg Manifestets URL när Player inte kan läsa in manifestet

### <a name="features-212"></a>Funktioner 2.1.2 ###

- Ändrade Historik Optimering Förbättrad inläsning av spelare och start tider

## <a name="211-official-update"></a>2.1.1 (officiell uppdatering) ##

### <a name="bug-fixes-211"></a>Fel korrigeringar 2.1.1 ####

- [Fel korrigering] Stoppa Ange oändlig rotations ruta för spela upp automatiskt till falskt i Safari för iOS
- [Fel korrigering] Söker en tid som är större än varaktigheten för innehålls varaktigheten ger oändlig rotations ruta
- [Fel korrigering] Snabb tangenter kräver flera tangent bords flikar för att Media Player ska fungera
- [Fel korrigering] Video låser sig några sekunder efter att du ändrat storlek på spelaren i vissa till gångar
- [Fel korrigering] Oändlig rotations ruta (när sökningen har slutförts) när användaren gör flera sökningar snabbt
- [Fel korrigering] Kontroll fältet döljs inte vid inaktivitet
- [Fel korrigering] Att öppna en webapp som är värd för AMP kan orsaka att webb sidan läses in två gånger
- [Fel korrigering] Oändligt vid uppspelning av innehåll till vissa till gångar via Flash Tech
- [Fel korrigering] Menyn fler alternativ visas inte med plugin-program från tredje part
- [Fel korrigering] [Skal | Provrör] [Live] två Live-ikoner visas när spelaren är på en Live Edge i ett program
- [Fel korrigering] Skal Logo typen kan inte inaktive ras
- [Fel korrigering] [DD + innehåll] En kontinuerlig rotations ruta visas för till gångar som innehåller Dolby Digital ljud spår
- [Fel korrigering] Senaste AMP låser sig när du växlar ljud språks spår under liveström
- [Fel korrigering] fast bakgrunds avvisare för rotations rutan
- [Fel korrigering] Oändlig rotations ruta i AES Flash-token statiska exempel fel korrigeringar

### <a name="changes-211"></a>Ändringar, 2.1.1 ####

- Ändrade Felkod har lagts till för Widevine https-krav: från och med Chrome V58, måste Widevine-innehåll läsas in/spelas upp via `https://` protokollet annars går det inte att spela upp.
- Ändrade Lade till Aria-etiketten för inläsnings rotation så att den tekniska inläsningen kan göra att video inläsningen visas när innehållet läses in  

## <a name="210-official-release"></a>2.1.0 (officiell utgåva) ##

### <a name="features-210"></a>Funktioner 2.1.0 ###

- Zoomfunktionen [AzureHtml5JS] VOD för AD-stöd för före mitten-post-kast
- Zoomfunktionen Â [AzureHtml5JS] Live AD-stöd för före mitten-post-rullar
- Zoomfunktionen Nytt skal alternativ har lagts till – AMP-flush
- Zoomfunktionen Förbättrade Aria-etiketter har lagts till för bättre integrering med skärm läsare/hjälpmedels teknik
- Zoomfunktionen Skal Skalet visar nu alla ikoner och knappar tydligt i hög kontrast läge

### <a name="bug-fixes-210"></a>Fel korrigeringar 2.1.0 ###

- [Fel korrigering] Antal tillgänglighets-och UI-korrigeringar
- [Fel korrigering] AMP läses inte in på rätt sätt i IE9

### <a name="changes-210"></a>Ändringar 2.1.0 ###

- Ändrade Omstrukturerade DOM-element i Player för att hantera annonser
- Ändrade Växlat från CSS till SCSS för hud utveckling
- Ändrade Stickprov Tillagt exempel för VOD-annonser
- Ändrade Stickprov Exemplet har lagts till för uppspelnings hastighet
- Ändrade Stickprov Exemplet har lagts till för Flush Skin

## <a name="200-beta-release"></a>2.0.0 (beta version) ##

- [Ändra] Uppdaterad till VJS5
- zoomfunktionen Nya vätske-API har lagts till för hög tillgänglighets svars tid
- Zoomfunktionen Uppspelnings hastighet
- Ändrade Växlat från CSS till SCSS för skal

## <a name="183-official-hotfix-update"></a>1.8.3 (officiell snabb korrigerings uppdatering) ##

### <a name="bug-fixes-183"></a>Fel korrigeringar 1.8.3 ###

- [Fel korrigering] [AzureHtml5JS] Vissa till gångar med negativ DTS spelas inte upp i Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (officiell snabb korrigerings uppdatering) ##

### <a name="bug-fixes-182"></a>Fel korrigeringar 1.8.2 ###

- [Fel korrigering] [AzureHtml5JS] Högre ljud bit hastigheter spelas inte tillbaka via AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (officiell uppdatering) ##

### <a name="bug-fixes-181"></a>Fel korrigeringar 1.8.1 ###

- [Fel korrigering] Stoppa Under texter/under texter visas inte i den inbyggda spelaren
- [Fel korrigering] AMP CDN-säkerhetskopierade URL: er som lagts till med autentiseringstoken som inte spelas upp
- [Fel korrigering] Fairplay FairPlay-felkod saknade Tech ID (BITS [31-28] av ErrorCode) se felkoder för mer information
- [Fel korrigering] Safari PlayReady PlayReady-innehåll i Safari ger oändlig rotations ruta

### <a name="changes-181"></a>Ändringar 1.8.1 ###

- Ändrade HTML5 Ändra interna HTML5 tekniska utförliga loggar för att innehålla händelser från VideoTag

## <a name="180-official-update"></a>1.8.0 (officiell uppdatering) ##

### <a name="features-180"></a>Funktioner 1.8.0 ###

- Egenskaper Rights Stöd för FairPlay har lagts till (se [skyddat innehåll](azure-media-player-protected-content.md) för mer information)

### <a name="bug-fixes-180"></a>Fel korrigeringar 1.8.0 ###

- [Fel korrigering] AMP Användar sökning utlöser inte en wait-händelse när nätverket är begränsat
- [Fel korrigering] [Blixt] Att välja kvalitet i Flash Tech Throw-undantag
- [Fel korrigering] AMP Dynamiskt val av kvalitet visas i snabb menyn
- [Fel korrigering] Skal Det är svårt att välja det sista meny alternativet för snabb menyer

### <a name="changes-180"></a>Ändringar 1.8.0 ###

- Ändrade Uppdaterad spelare till aktuella Chrome EME-krav
- Ändrade Standard techOrder ändrades till att rymma nya Tech-html5FairPlayHLS (se [skyddat innehåll](azure-media-player-protected-content.md) för mer information)
- Ändrade [AzureHtml5JS] Aktive rad MPEG-Dash uppspelning i Safari
- Ändrade Stickprov Flera DRM-exempel har ändrats för att hantera FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (officiell snabb korrigerings uppdatering) ##

### <a name="bug-fixes-174"></a>Fel korrigeringar 1.7.4 ###

- [Fel korrigering] Chrome Blå kontur visas runt Sök handtaget när användaren har ett sammanhang i spelaren
- [Fel korrigering] IE9 JavaScript-undantag utlöstes när spelaren lästes in i IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (officiell snabb korrigerings uppdatering) ##

### <a name="bug-fixes-173"></a>Fel korrigeringar 1.7.3 ###

- [Fel korrigering] [AzureHtml5JS] Spelarens tids gräns i begränsade nätverk

### <a name="changes-173"></a>Ändringar 1.7.3 ###

- Ändrade Aktivera webbkryptering på gräns för dekryptering av AES-innehåll
- Ändrade Optimerar AMP-heuristiken för att redovisa cachelagrade segment
- Ändrade [AzureHtml5JS] Optimera heuristiken genom att minska svars tiden för bandbredds uppskattning

## <a name="172-official-hotfix-update"></a>1.7.2 (officiell snabb korrigerings uppdatering) ##

### <a name="features-172"></a>Funktioner 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- Zoomfunktionen [AzureHtml5JS | Firefox] Aktivera Widevine-uppspelning med EME för Firefox 47 +
- Zoomfunktionen Lägg till händelse för Player som kasseras
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Fel korrigeringar 1.7.2 ###

- [Fel korrigering] Kodade Akamai CDN URL-frågeparametrar har inte avkodats korrekt
- [Fel korrigering] Ett undantags fel inträffade på manifestPlayableWindowLength ()
- [Fel korrigering] Visnings programmet kan inte alltid klicka på spela upp på videon när videon har slutat att titta på
- [Fel korrigering] Svars storlek som inte överensstämmer med snabba fönster storleks ändringar
- [Fel korrigering] [Edge | IE] storleks ändringen börjar inte gälla på sid inläsning för width = x, height = Auto
- [Fel korrigering] [Android | Chrome] Chrome be om behörighet för uppspelning av DRM-innehåll när innehållet inte är krypterat
- [Fel korrigering] Tågen Marginal Tangent bords kontroller väljer inte snabb meny alternativ korrekt
- [Fel korrigering] Tågen Kant linje saknas i hög kontrast läge
- [Fel korrigering] [Blixt] Händelse lyssnaren för mus uppåt tas inte bort efter att Player dispose orsakar undantag
- [Fel korrigering] [Blixt] Problem med att parsa manifest-URL med kodade blank steg
- [Fel korrigering] Stoppa Typ fel vid utvärdering av Tech. featuresVolumeControl

### <a name="changes-172"></a>Ändringar 1.7.2 ###

- Ändrade Rights Flytta DRM-kontroller efter att källan har angetts till kontrol lera när innehållet är krypterat
- Ändrade AES Tog bort den odefinierade bröd texten av typen/klartext från begäran om nyckel leverans
- Ändrade Tågen Windows skärm läsaren läser nu "Media Player" när kontexten är på spelare i stället för egenskaper

## <a name="171-official-hotfix-update"></a>1.7.1 (officiell snabb korrigerings uppdatering) ##

### <a name="features-171"></a>Funktioner 1.7.1 ###

- Zoomfunktionen Alternativet har lagts till för Hybrid heuristisk profil (den här profilen anges som standard)

### <a name="bug-fixes-171"></a>Fel korrigeringar 1.7.1 ###

- [Fel korrigering] Den svarande designen fungerar inte enligt HTML5 standard (width = 100%, height = Auto)
- [Fel korrigering] Procent värden för bredd och höjd fungerar inte som förväntat i v-1.7.0

## <a name="170-official-update"></a>1.7.0 (officiell uppdatering) ##

### <a name="features-170"></a>Funktioner 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- Zoomfunktionen [AzureHtml5JS] [Blixt] CurrentMediaTime () har lagts till för att hämta kodarens medie tid för den aktuella tiden i sekunder
- Zoomfunktionen [Blixt] Implementerade API: er för att hämta telemetri med videoBufferData () och audioBufferData ()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- Zoomfunktionen [Blixt] Downloadbitratechanged-händelsen har lagts till
- Zoomfunktionen Inläsnings tiden har förbättrats jämfört med äldre versioner av Player
- Zoomfunktionen Fel loggas till JavaScript-konsolen

### <a name="bug-fixes-170"></a>Fel korrigeringar 1.7.0 ###

- [Fel korrigering] Kodad affisch-URL med parametrar för frågesträng som inte visas i spelaren
- [Fel korrigering] Ett undantag utlöstes när ingen teknik har lästs in och API-amp. Player. affisch () kallas
- [Fel korrigering] Ett undantag utlöstes när Functions försöker få åtkomst till Player efter att den tagits bort
- [Fel korrigering] Tågen Disposition saknas för fokus på förlopps indikator Sök huvud
- [Fel korrigering] Tågen Snabb menyer har en skugga i hög kontrast läge
- [Fel korrigering] [iOS] inbyggd spelare WebVTT-textning för uppspelning fungerar inte
- [Fel korrigering] [AzureHtml5JS] Fel 0x0100002 bör visas vid uppspelning av HTTP Stream på HTTPS-platsen som i stället ger oändlig rotations ruta till följd av blandat innehåll
- [Fel korrigering] [AzureHtml5JS] Saknat slut segment som orsakar fel vid kontroll av hälso kontroll visar ett uppfattat oändligt buffert tillstånd
- [Fel korrigering] [AzureHtml5JS] Felaktigt ljud spårs namn i menyn när useManifestForLabel = falskt och tre bokstäver är språk koder
- [Fel korrigering] [AzureHtml5JS | Chrome] uppfattat oändligt buffertutrymme i slutet av innehåll som orsakats av flytt ALS precision i varaktighet med Java Script i Chrome
- [Fel korrigering] [Blixt] Tillfälligt fel som inte är allvarligt visas när Flash Player skapades
- [Fel korrigering] [Blixt] Uppspelningen fungerar inte när video-och ljud strömmar använder olika tids skalor på grund av avrundning av "fragment-URL (...) Det gick inte att generera FLVTags "
- [Fel korrigering] [Blixt] Problem vid parsning av manifest-URL: er med kodade blank steg
- [Fel korrigering] [Blixt] Kontroll för att avgöra om Flash Player-version >= 11,4 som orsakar ett fel i uppspelning i stället för att falla tillbaka till nästa Tech i techOrder
- [Fel korrigering] [Blixt] AES Problem som accepterar AES-token med under streck
- [Fel korrigering] [Silverlight-mig | OSX] "//" förreparerar ett manifest i stället för protokollet (HTTP eller HTTPS) identifieras som en oändlig rotations ruta för lokal fil

### <a name="changes-170"></a>Ändringar 1.7.0 ###

- Ändrade [Blixt] Sammanfogade SWF-skript ("MSAdaptiveStreamingPlugin-osmf 2.0. swf" och "StrobeMediaPlayback. 2.0. swf") till en enda SWF-fil som kallas "StrobeMediaPlayback. 2.0. swf"
- Ändrade [Blixt] Uppdatering av felkodens spridning för att få mer exakta felkoder (t. ex. 404s resulterar nu i 0x30200194 i stället för Generic Error 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (officiell snabb korrigerings uppdatering) ##

### <a name="bug-fixes-163"></a>Fel korrigeringar 1.6.3 ###

- [Fel korrigering] Java Script runtime-undantag när händelse hanteraren för snabb tangenter körs när spelaren har avverkställts
- [Fel korrigering] Android [AzureHtml5JS] Ingen uppspelning på mobil enhet med mobilt nätverk
- [Fel korrigering] Uppdaterade falska för att köras som Web Worker för att frigöra användar gränssnitt

## <a name="162-official-hotfix-update"></a>1.6.2 (officiell snabb korrigerings uppdatering) ##

### <a name="features-162"></a>Funktioner 1.6.2 ###

- Zoomfunktionen Ytterligare språk har lagts till för lokalisering (se dokumentationen för mer information)

### <a name="bug-fixes-162"></a>Fel korrigeringar 1.6.2 ###

- [Fel korrigering] [IE9-10] Klicka på områden runt spelarens minimerade webbläsare på grund av IE9/IE10-fel som minimeras i window. oskärpa ()
- [Fel korrigering] [Blixt] Accepterar inte AES-token med under streck

## <a name="161-official-hotfix-update"></a>1.6.1 (officiell snabb korrigerings uppdatering) ##

### <a name="bug-fixes-161"></a>Fel korrigeringar 1.6.1 ###

- [Fel korrigering] [För blixt | Edge, IE] [Silverlight | IE] kan inte fokusera på andra GRÄNSSNITTs element för indata eller andra i IE/Edge
- [Fel korrigering] AES-uppspelningen fungerar inte när falska har definierats
- [Fel korrigering] Android [AzureHtml5JS | Chrome] kontinuerlig rotations ruta spelar inte tillbaka innehåll i hälso kontrolls slinga
- [Fel korrigering] [IE9] Console. log () stöds inte av IE 9 orsakade undantag

## <a name="160-official-update"></a>1.6.0 (officiell uppdatering) ##

### <a name="features-160"></a>Funktioner 1.6.0 ###

- [Funktion] 33% storleks minskning av azuremediaplayer.min.js
- Zoomfunktionen [AzureHtml5JS | Edge] [avtestat] stöd för DD + ljud strömmar i Edge (ingen codec-växel efter det första valet). Appen måste välja rätt ljud ström för tillfället.
- Zoomfunktionen Snabb tangents kontroller (se dokument för mer information)
- Zoomfunktionen Förlopps indikator för förlopps fördröjning för korrekt sökning
- Zoomfunktionen Tillåt asynkron identifiering av plugin-program om setupDone-metoden finns i plugin-programmet

### <a name="bug-fixes-160"></a>Fel korrigeringar 1.6.0 ###

- [Fel korrigering] Minnes loggen töms inte på getMemoryLog (true)
- [Fel korrigering] Markerings ruta för bit hastighet återställs vid mus flyttning, vilket orsakar problem med att välja lägre bit hastigheter genom mus kontrollen
- [Fel korrigering] Mac Office i appen kraschar när DRM-kontroll utförs
- [Fel korrigering] CSS-klasser är lätt att skriva över av misstag
- [Fel korrigering] Chrome Uppdaterings identifiering från användar agentens sträng webbläsare är Edge
- [Fel korrigering] [AzureHtml5JS] Knappen under texter visas inte i verktygsfältet i Edge (Win10) eller Chrome (Mac)
- [Fel korrigering] Android [AzureHtml5JS | Chrome] InvalidStateError-undantag på endOfStream ()-anropet på korta videor
- [Fel korrigering] Firefox Borttagning av DRM-varning som orsakas av Firefox vid kontroll av webb läsar funktioner
- [Fel korrigering] HTML5 Under text/under texter visas inte med progressiv MP4-innehåll
- [Fel korrigering] [Blixt] Meddelanden med matchande tidsstämplar loggades i omvänd ordning
- [Fel korrigering] Tågen [Chrome | Firefox]-fliken och väljer kontroller automatiskt Välj första meny alternativet
- [Fel korrigering] Tågen Flik för att styra volym knappen

### <a name="changes-160"></a>Ändringar 1.6.0 ###

- Ändrade Använd AES-avkryptering vid val av kvalitets nivå
- Ändrade Uppdatera URL-Rewriter för att använda HLS v4 innan HLS v3 för flera ljud strömmar
- Ändrade Ange nativeControlsForTouch till false som standard (måste vara falskt för att fungera korrekt)

## <a name="150-official-update"></a>1.5.0 (officiell uppdatering) ##

### <a name="features-150"></a>Funktioner 1.5.0 ###

- Zoomfunktionen Förbättringar för allmän webb säkerhet (förebyggande av insprutning, XSS osv.)
- Zoomfunktionen SDN plugin-program för integrering av sourceset event och Options. Sdn
- Zoomfunktionen Robust hantering av 5XX-och 4XX-fel under uppspelning

### <a name="bug-fixes-150"></a>Fel korrigeringar 1.5.0 ###

- [Fel korrigering] Uppdatera CSS-minimering för att använda kod koder för HTML-enheter för knappar i stället för Unicode
- [Fel korrigering] [AzureHtml5JS] Med multi-DRM väljer du alltid det första elementets token från protectionInfo som orsakar att andra DRM fungerar
- [Fel korrigering] [AzureHtml5JS] Att söka aldrig slutförs vid sökning i ett utrymme med segment som saknas.
- [Fel korrigering] [AzureHtml5JS | Edge] Aktivera fasta EME i Edge-uppdatering för PlayReady-uppspelning
- [Fel korrigering] [AzureHtml5JS | Firefox] uppdatera EME-kontroll för att tillåta Firefox V42 + (med MSE) att återgå till Silverlight för skyddat innehåll
- [Fel korrigering] [Blixt] Uppdaterings fel. meddelande från nummer till detaljerad sträng

### <a name="changes-150"></a>Ändringar 1.5.0 ###

- Ändrade Affischer fungerar för närvarande bara som absoluta URL: er.

## <a name="140-official-update"></a>1.4.0 (officiell uppdatering) ##

### <a name="features-140"></a>Funktioner 1.4.0 ###

- Zoomfunktionen [AzureHtml5JS | Chrome] enkel Widevine DRM-support
- Zoomfunktionen [AzureHtml5JS] Robust hantering av 404/412-fel under uppspelning

### <a name="bug-fixes-140"></a>Fel korrigeringar 1.4.0 ###

- [Fel korrigering] [Blixt] Förbättring av parameter validering

## <a name="130-official-update"></a>1.3.0 (officiell uppdatering) ##

### <a name="features-130"></a>Funktioner 1.3.0 ###

- Zoomfunktionen [AzureHtml5JS] [Blixt] Ljud växling av samma codec-innehåll med flera ljud

### <a name="bug-fixes-130"></a>Fel korrigeringar 1.3.0 ###

- [Fel korrigering] [AzureHtml5JS | Chrome] oändlig oändlig rotations ruta
- [Fel korrigering] [AzureHtml5JS | IE] [Windows Phone] undantag som gör att Windows Phone har uppspelnings problem
- [Fel korrigering] [Blixt] Spela upp automatiskt har angetts till false misslyckades för ytterligare instanser
- [Fel korrigering] Problem med att ändra GRÄNSSNITTs menyn

## <a name="120-official-update"></a>1.2.0 (officiell uppdatering) ##

### <a name="features-120"></a>Funktioner 1.2.0 ###

- Zoomfunktionen [AzureHtml5JS | Firefox]-stöd när MSE är aktive rad
- Zoomfunktionen Kräver inte längre appen för att tillhandahålla sökvägar för Tech-binärfiler (SWF, XAP). Sökvägen är relativ i förhållande till Azure Media Player-skriptet.

### <a name="bug-fixes-120"></a>Fel korrigeringar 1.2.0 ###

- [Fel korrigering] [AzureHtml5JS | Chrome] spelare försenar Live Edge när spelaren är i bakgrunden
- [Fel korrigering] [AzureHtml5JS | Edge] full skärm fungerar inte
- [Fel korrigering] [AzureHtml5JS] Loggning aktiverades inte korrekt när du angav i alternativ
- [Fel korrigering] Utvecklingsverktyget Både "buffer" och buffer-ikonen visas under vänte händelse
- [Fel korrigering] Tillåt uppspelning om du vill fortsätta om begäran om inledande bandbredd Miss lyckas
- [Fel korrigering] Det går inte att läsa in Player vid initiering med odefinierade alternativ
- [Fel korrigering] Ett vdata-undantag uppstår vid försök att ta bort spelaren när den redan har tagits bort
- [Fel korrigering] Kvalitets fälts ikoner har mappats felaktigt

## <a name="111-official-hotfix-update"></a>1.1.1 (officiell snabb korrigerings uppdatering) ##

### <a name="bug-fixes-111"></a>Fel korrigeringar 1.1.1 ###

- [Fel korrigering] Problem med en äldre IE-skärm
- [Fel korrigering] Plugin-program skrivs inte längre

## <a name="110-official-update"></a>1.1.0 (officiell uppdatering) ##

### <a name="features-110"></a>Funktioner 1.1.0 ###

- Zoomfunktionen Uppdatera lokaliserings strängar för gränssnitt

### <a name="bug-fixes-110"></a>Fel korrigeringar 1.1.0 ###

- [Fel korrigering] Den stora uppspelnings knappen har inte tillräckligt med kontrast
- [Fel korrigering] Indikator för visuell flik fokus
- [Fel korrigering] Menyn Välj bit hastighet som nu använder korrekt lösnings information
- [Fel korrigering] Menyn fler alternativ är nu dynamiskt storlek
- [Fel korrigering] Olika UI-problem

## <a name="100-official-release"></a>1.0.0 (officiell utgåva) ##

### <a name="features-100"></a>Funktioner 1.0.0 ###

- Zoomfunktionen Grundläggande hjälpmedels testning för flikkontroll, fokus kontroll, skärm läsare, användar gränssnitt med hög kontrast
- Zoomfunktionen Uppdaterat användar gränssnitt
- Zoomfunktionen Dev-loggning
- Zoomfunktionen API för dynamisk inställning av under texter/under texter spår
- Zoomfunktionen Grundläggande lokaliserings funktioner
- Zoomfunktionen Fel kod konsolidering i Techs
- Zoomfunktionen Ny felkod för när plugin-program (t. ex. Flash eller Silverlight) inte är installerade
- Zoomfunktionen [AzureHtml5JS] Implementerade grundläggande diagnostiska händelser

### <a name="bug-fixes-100"></a>Fel korrigeringar 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Fel korrigering] [AzureHtml5JS] Direkt uppspelnings frysning av MPD-uppdateringar när det finns små inprecisioner i tidsstämpeln
- [Fel korrigering] [AzureHtml5JS] Minimerade flera problem med direkt uppspelning
- [Fel korrigering] [AzureHtml5JS] Töm buffertar när heuristik för fönster storlek är på och går till en skärm med högre upplösning
- [Fel korrigering] [AzureHtml5JS] Chrome visar nu händelsen avslutades korrekt. En länkad till ett tidigare känt problem med *Chrome skickar inte slut händelsen korrekt när du använder AzureHtml5JS. Det finns ett problem i den underliggande webbläsaren.*
- [Fel korrigering] [AzureHtml5JS] Den här Tech-inaktiverade Safari för att åtgärda *uppspelnings problem med OSX Yosemite med AzureHtml5JS Tech. Det finns problem med att implementera MSE. Temporär minskning: tvinga fram blixt, Silverlight som teknisk ordning för dessa användar agenter*
- [Fel korrigering] [Blixt] loadstart utlöses efter att ett fel uppstod

## <a name="020-beta"></a>0.2.0 (beta) ##

### <a name="features-020"></a>Funktioner 0.2.0 ###

- Zoomfunktionen Testet har slutförts för PlayReady och AES för on-demand och Live-se kompatibilitet mat ris
- Zoomfunktionen Hantera discontinuities
- Zoomfunktionen Stöd för tidsstämplar som är större än 2 ^ 53
- Zoomfunktionen URL-frågeparameter finns kvar i manifest förfrågan
- Zoomfunktionen [Avtestat] Stöd för `QuickStart` och `HighQuality` heuristiks profiler
- Zoomfunktionen [Avtestat] Exponerar video Ströms information för bit hastigheter, bredd och höjd på AzureHtml5JS och blixt
- Zoomfunktionen [Avtestat] Välj bit hastighet på AzureHtml5JS och blixt (se API-dokumentation)

### <a name="bug-fixes-020"></a>Fel korrigeringar 0.2.0 ###

- [Fel korrigering] stor uppspelnings knapp visas nu på WP 8.1
- [Fel korrigering] åtgärdat flera direktsända uppspelnings problem
- [Fel korrigering] ljud på knappen fungerar nu i användar gränssnittet
- [Fel korrigering] Uppdaterad UI inläsnings upplevelse för läget för automatisk uppspelning
- [Fel korrigering] Problem vid inläsning av AMD-inläsare och definiera metod
- [Fel korrigering] WP 8,1 Cordova app inläsnings problem
- [Fel korrigering] Skyddat innehåll frågar plattform/Tech-skydds typ som stöds för att välja lämplig Tech för uppspelning.  Korrigeringar av tidigare känt problem med _PlayReady-innehåll på Chrome (Desktop)/Safari 8 (på OSX Yosemite) återgår för närvarande inte till Silverlight Player_
- [Fel korrigering] ett undantags fel har inträffat i WinServer 2012 R2 på grund av att Media Foundation inte installerats på datorn som standard.  Försök att använda HTML-video märknings-API: er, som inte har implementerats, och därmed utlöser ett fel. Aktuell minskning är att fånga felet och returnera falskt i stället för att returnera felet.
- [Fel korrigering] Hämta alltid init-segmentet efter sökning eller HTTP-fel för att förhindra problem under uppspelningen
- [Fel korrigering] Stäng av spåra simulerade förlopp och timeupdates när ett fel har uppstått.
- [Fel korrigering] ta bort meny för snabb klickning
- [Fel korrigering] [AzureHtml5JS] fel meddelandet visas inte när ogiltig token har angetts för PlayReady-innehåll
- [Fel korrigering] [AzureHtml5JS] hel skärms läge när Live uppspelning inte tog hänsyn till fönster storleks heuristik i konto
- [Fel korrigering] [Blixt] Ta bort strobe Media Player meddelanden som visas så att endast Azure Media Player meddelanden visas
- [Fel korrigering] [Silverlights] Det går inte att hämta "sökte"-händelsen när vi söker efter varaktighet eller mindre än 0

## <a name="010-beta-release"></a>0.1.0 (beta version) ##

Första för hands version

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)
