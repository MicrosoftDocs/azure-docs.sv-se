---
title: Spela in anpassade röst exempel – tal tjänst
titleSuffix: Azure Cognitive Services
description: Gör en anpassad röst för produktion kvalitet genom att förbereda ett robust skript, anlita bra röst personal och registrera dig professionellt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 966b11e2c9a0f7ffc5e6ec9238080b9076d37af6
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572433"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Spela in röst exempel för att skapa en anpassad röst

Att skapa en anpassad röst med hög kvalitet från grunden är inte ett vardagligt företag. Den centrala komponenten i en anpassad röst är en stor samling ljud exempel av mänskligt tal. Det är viktigt att dessa ljud inspelningar är av hög kvalitet. Välj en röst personal som har erfarenhet av de här typerna av inspelningar, och få dem registrerade av en behörig inspelnings tekniker med hjälp av professionell utrustning.

Innan du kan göra dessa inspelningar måste du ha ett-skript: de ord som talas av din röst-personal för att skapa ljud exemplen. För bästa resultat måste ditt skript ha god fonetisk omfattning och tillräcklig mängd för att träna den anpassade röst modellen.

Många små men viktiga uppgifter är att skapa en professionell röst inspelning. Den här guiden är en översikt över en process som hjälper dig att få bättre, konsekventa resultat.

> [!NOTE]
> Om du vill träna en neurala röst måste du ange en röst personal-profil med den ljud medgivande fil som du har fått från röst personal som kan bekräfta att hans/hennes tal-data används för att träna en anpassad röst modell. När du förbereder ditt inspelnings skript måste du se till att ta med nedanstående mening. 

> "I [tillstånd ditt för-och efter namn] är du medveten om att inspelningar av min röst kommer att användas av [stat namnet på företaget] för att skapa och använda en syntetisk version av rösten."
Den här meningen används för att kontrol lera om inlärnings data utförs av samma person som gör ditt medgivande. Läs mer om [röst personal-verifiering](https://aka.ms/CNV-data-privacy) här.

> Anpassad neurala-röst är tillgänglig med begränsad åtkomst. Se till att du förstår de [ansvariga AI-kraven](https://aka.ms/gating-overview) och [Använd åtkomsten här](https://aka.ms/customneural). 

> [!TIP]
> Överväg att engagera Microsoft för att hjälpa till att utveckla din anpassade röst för högsta kvalitet. Microsoft har omfattande erfarenhet av att skapa hög kvalitets röster för sina egna produkter, inklusive Cortana och Office.

## <a name="voice-recording-roles"></a>Röst inspelnings roller

Det finns fyra grundläggande roller i ett anpassat röst inspelnings projekt:

Roll|Syfte
-|-
Röst personal        |Den här personens röst kommer att utgöra grunden för den anpassade rösten.
Inspelnings tekniker  |Överser de tekniska aspekterna av inspelningen och arbetar med inspelnings utrustningen.
Director            |Förbereder skriptet och bussar av röst personal prestanda.
Redigerare              |Slutför ljudfilerna och förbereder dem för uppladdning till den anpassade röst portalen.

En enskild person kan fylla mer än en roll. Den här guiden förutsätter att du i första hand fyller i Director-rollen och anställer både en röst personal och en inspelnings tekniker. Om du vill göra inspelningarna själv, innehåller den här artikeln viss information om inspelnings ingenjörs rollen. Redigerings rollen behövs inte förrän efter sessionen, så kan utföras av directorn eller inspelnings teknikern.

## <a name="choose-your-voice-talent"></a>Välj din röst personal

Aktörer som har erfarenhet av VoiceOver eller röst Character-arbete gör rätt anpassade röst personal. Du kan också ofta hitta lämpliga personal mellan annonser och diskussions grupper.

Välj röst personal vars naturliga röst du vill ha. Det är möjligt att skapa unika "Character"-röster, men det är mycket svårare för de flesta personal att utföra dem konsekvent och ansträngningen kan orsaka röst stammar.

> [!TIP]
> Undvik i allmänhet att använda identifierbara röster för att skapa en anpassad röst, såvida inte ditt mål är att skapa en kändis röst. Mindre kända röster är vanligt vis mindre störande för användare.

Den enda viktigaste faktorn för att välja röst personal är konsekvens. Dina inspelningar bör alla ljud som de har gjort på samma dag i samma rum. Du kan närma dig detta genom bra registrerings metoder och teknik.

Din röst personal är den andra halvan av ekvationen. De måste kunna tala med konsekvent hastighet, volym nivå, färgdjup och ton. Rensa Diktering är ett måste. Personal måste också kunna begränsa variationer, känslomässig påverkan och tal mannerisms.

Inspelning av anpassade röst exempel kan vara mer fatiguing än andra typer av röst arbete. De flesta röst personal kan spela in i två eller tre timmar per dag. Begränsa sessioner till tre eller fyra veckor, med en dag från mellan om möjligt.

Arbeta med din röst personal för att utveckla en "persona" som definierar den övergripande ljud-och känslomässig-tonen för den anpassade rösten. I processen får du reda på vilka "neutral"-ljud som för personen. Med den anpassade röst funktionen för neurala kan du träna en modell som pratar med känslor. Definiera "tal format" och be din röst personal att läsa skriptet på ett sätt som mottas av de format som du vill använda.  

En persona kan till exempel ha en naturlig intakts personlighet. Så "deras"-röst kan innehålla en anteckning om optimerad, även om de pratar neutralt. En sådan personlig egenskap bör dock vara diskret och konsekvent. Lyssna på avläsningar från befintliga röster för att få en uppfattning om vad du vill ha.

> [!TIP]
> Vanligt vis vill du veta vilka röst inspelningar du gör. Din röst-personal bör vara lämpar till ett arbets-till-anställnings-kontrakt för projektet.

## <a name="create-a-script"></a>Skapa ett skript

Start punkten för en anpassad röst inspelnings session är skriptet, som innehåller yttranden som ska läsas av din röst personal. (Termen "yttranden" omfattar både fullständiga meningar och kortare fraser.)

Yttranden i skriptet kan komma från var som helst: fiktion, icke-fiktion, avskrifter av tanke tal, nyhets rapporter och annat som är tillgängligt i utskriften. Om du vill vara säker på att rösten fungerar bra på vissa typer av ord (till exempel medicinsk terminologi eller programmerings jargong), kanske du vill lägga till meningar från teknisk dokumentation eller tekniska dokument. En kort beskrivning av potentiella juridiska problem finns i avsnittet ["juridiska bestämmelser"](#legalities) . Du kan också skriva egen text.

Din yttranden behöver inte komma från samma källa, eller samma typ av källa. De behöver inte ens ha något att göra med varandra. Men om du kommer att använda set-fraser (till exempel "du har loggat in") i ditt tal program, måste du ta med dem i skriptet. Detta ger din anpassade röst en bättre chans att pronouncing dessa fraser. Om du bestämmer dig för att använda en inspelning i stället för syntetiskt tal finns det redan i samma röst.

När konsekvensen är viktig vid valet av röst personal, är sorten Hallmark för ett lyckat skript. Skriptet bör innehålla många olika ord och meningar med olika menings längder, strukturer och stämningar. Varje ljud på språket bör visas flera gånger och i flera kontexter (kallas *fonetisk täckning*).

Dessutom bör texten innehålla alla sätt som ett visst ljud kan representeras i skriftligen och placera varje ljud på olika platser i meningarna. Både deklarativ meningar och frågor bör tas med och läsas med lämpliga intonation.

Det är svårt att skriva ett skript som ger *precis tillräckligt med* data för att Custom Speech portalen ska kunna skapa en bra röst. I praktiken är det enklaste sättet att göra ett skript som uppnår robust fonetisk täckning att inkludera ett stort antal exempel. Standard rösterna som Microsoft tillhandahåller har byggts av tusentals yttranden. Du bör vara beredd att registrera några få till flera tusen yttranden som minst för att bygga en anpassad röst i produktion kvalitet.

Kontrol lera skriptet noggrant efter fel. Om möjligt bör någon annan kontrol lera den. När du kör skriptet med din personal kommer du förmodligen att fånga några fler misstag.

### <a name="script-format"></a>Skript format

Du kan skriva skriptet i Microsoft Word. Skriptet är att använda under inspelningen, så du kan ställa in det på ett sätt som är enkelt att arbeta med. Skapa text filen som krävs av den anpassade röst portalen separat.

Ett Basic-skript format innehåller tre kolumner:

* Numret för uttryck, från 1. Numrering gör det enkelt för alla i Studio att referera till en viss uttryck ("Vi försöker numrera 356 igen"). Du kan använda funktionen styckenumrering för ord för att numrera raderna i tabellen automatiskt.
* En tom kolumn där du skriver ta-nummer eller-tid för varje uttryck som hjälper dig att hitta den i den färdiga inspelningen.
* Texten för själva uttryck.

![Exempelskript](media/custom-voice/script.png)

> [!NOTE]
> De flesta Studios-poster i korta segment är kända som *tar*. Vart och ett omfattar vanligt vis 10-till-24-yttranden. Det räcker med att Observera att ta-talet är tillräckligt för att hitta en uttryck senare. Om du spelar in i en studio som föredrar att göra längre inspelningar, vill du anteckna tids koden i stället. Studio kommer att ha en synlig tids visning.

Lämna tillräckligt med utrymme efter att varje rad har skrivit anteckningar. Se till att ingen uttryck är uppdelad mellan sidor. Numrera sidorna och skriv ut skriptet på en sida av papperet.

Skriv ut tre kopior av skriptet: ett för personal, ett för teknikern och en för Director (du). Använd ett gem i stället för häftklamrar: en erfaren röst artist separerar sidorna för att undvika brus när sidorna är aktiverade.

### <a name="legalities"></a>Juridiska

Under upphovs rätts lagstiftning kan en aktörs läsning av upphovsrättsskyddad text vara en prestanda för vilken författaren av arbetet bör kompenseras. Den här prestandan går inte att känna igen i den slutliga produkten, den anpassade rösten. Även om den juridiska användningen av ett upphovsrättsskyddat verk för detta ändamål inte är väl etablerad. Microsoft kan inte ge juridisk rådgivning om det här problemet. Kontakta din egen rådgivning.

Lyckligt vis är det möjligt att undvika dessa problem helt och hållet. Det finns många text källor som du kan använda utan behörighet eller licens.

|Text källa|Beskrivning|
|-|-|
|[CMU Arctic-sökkorpus](http://festvox.org/cmu_arctic/)|Om 1100 meningar som valts från frånvaro av upphovs rätts arbete som används i tal syntes projekt. En utmärkt start punkt.|
|Fungerar inte längre<br>under Copyright|Vanligt vis publicerade Works före 1923. För engelska erbjuder [Project Gutenberg](https://www.gutenberg.org/) tusentals sådana arbeten. Du kanske vill fokusera på nyare arbeten, eftersom språket är närmare för modern engelska.|
|Myndigheter &nbsp; fungerar|Verk som skapats av USA myndigheter är inte upphovsrättsskyddade i USA, även om myndigheter kan anspråka upphovs rätt i andra länder/regioner.|
|Offentlig domän|Fungerar som upphovs rätt uttryckligen har frigjorts eller som har reserver ATS för den offentliga domänen. Det kan inte vara möjligt att avstå från upphovs rätt helt i vissa jurisdiktioner.|
|Auktoriserade och licensierade arbeten|Works distribueras under en licens som Creative Commons eller GNU License License License (GFDL). Wikipedia använder GFDL. Vissa licenser kan dock medföra begränsningar för prestanda för det licensierade innehållet som kan påverka skapandet av en anpassad röst modell, så Läs licensen noggrant.|

## <a name="recording-your-script"></a>Spela in ditt skript

Registrera ditt skript i en proffsig inspelning Studio som specialiserar sig på röst arbete. De har en registrerings hytt, rätt utrustning och rätt personer att använda den. Den betalar inte för skimp vid registrering.

Diskutera ditt projekt med Studios inspelnings tekniker och lyssna på deras råd. Inspelningen bör ha liten eller ingen dynamisk intervall komprimering (högst 4:1). Det är viktigt att ljudet har konsekvent volym och ett högt signal-till-brus-förhållande, samtidigt som oönskade ljud är fria.

### <a name="do-it-yourself"></a>Gör det själv

Om du vill göra inspelningen själv, i stället för att gå in i en inspelning Studio, är det här en kort introduktion. Tack vare den lett till både start-och poddsändning, är det enklare än någonsin att hitta välsamma inspelnings råd och resurser online.

Din "inspelnings hytt" bör vara ett litet rum utan märkbart eko eller "rums ton". Den bör vara så tyst och soundproof som möjligt. Drapes på väggarna kan användas för att minska eko och neutralisera eller "död" av rummets ljud.

Använd en högkvalitativ Studio kylare ("Mic" för kort) som är avsedd för inspelning av rösten. Sennheiser, AKG och till och med nyare zoomnings MICS kan ge bättre resultat. Du kan köpa en mikrofon eller hyra en från ett lokalt ljud-och ett moln företag. Sök efter ett med ett USB-gränssnitt. Den här typen av MIC kombinerar bekvämt mikrofon element, preamp och analog-till-digital-omvandlare till ett paket, vilket fören klar Anslut.

Du kan också använda en analog mikrofon. Många hyres hus erbjuder "årgång"-mikrofoner välrenommerade för sina röst bokstäver. Observera att den analoga maskin varan använder balanserade XLR-anslutningar i stället för den 1/4-tums plugg som används i konsument utrustning. Om du väljer analog behöver du också ett preamp och ett dator ljud gränssnitt med dessa anslutningar.

Installera mikrofonen på ett stativ eller sådär och installera ett pop-filter framför mikrofonen för att undvika brus från "plosive"-konsonanter som "p" och "b". Vissa mikrofoner levereras med en uppskjutnings montering som isolerar dem från vibrationer i stativ, vilket är användbart.

Röst-personal måste vara i ett konsekvent avstånd från mikrofonen. Använd band på golvet för att markera var de ska stå. Om personal föredrar att sitta kan du vara särskilt noga med att övervaka mikrofon avståndet och undvika att stolen rör sig.

Använd ett stativ för att hålla skriptet. Undvik Angling i stället så att det kan reflektera ljudet mot mikrofonen.

Den person som driver inspelnings utrustningen – teknikern – bör vara i ett separat rum från personal, med något sätt att prata med personal i registrerings hytten (en *TalkBack-krets).*

Inspelningen bör innehålla så lite brus som möjligt, med ett mål av en 80-dB signal-till-brus-kvot eller bättre.

Lyssna nära en inspelning av tystnad i "Monte" och ta reda på var bruset kommer från och eliminera orsaken. Vanliga buller källor är luft Ventilationer, fluorescerande ljus laster, trafik på närliggande vägar och utrustnings fläktar (även bärbara datorer kan ha fläktar). Mikrofoner och kablar kan hämta Elbrus från närbelägen AC-kabel, vanligt vis en Hum eller en i regel. En i början kan också orsakas av en *grund slinga*, som orsakas av att utrustning är ansluten till mer än en elektrisk krets.

> [!TIP]
> I vissa fall kan du använda en equalizer eller ett program för brus minskning av program vara för att hjälpa till att ta bort brus från dina inspelningar, även om det alltid är bäst att stoppa det vid källan.

Ange nivåer så att det mesta av det tillgängliga dynamiska intervallet av digital inspelning används utan överkörning. Det innebär att du ställer in ljudet högt, men inte så högt som det blir förvrängt. Ett exempel på en våg våg med en inspelning visas i följande bild:

![En välspelad våg form](media/custom-voice/good-recording.png)

Här används merparten av intervallet (height), men den högsta toppar i signalen når inte upp eller ned i fönstret. Du kan också se att ljud dämparen i inspelningen närmar sig en tunn vågrät linje som anger ett lågt brus golv. Den här inspelningen har godtagbart dynamiskt intervall och signal-till-brus-förhållande.

Spela in direkt på datorn via ett ljud gränssnitt med hög kvalitet eller en USB-port, beroende på vilken MIC du använder. För analogt, Behåll ljud kedjan enkel: MIC, preamp, ljud gränssnitt, dator. Du kan licensiera både [Avid Pro Tools](https://www.avid.com/en/pro-tools) och [Adobe Audition](https://www.adobe.com/products/audition.html) per månad till en rimlig kostnad. Om budgeten är mycket tätt kan du prova med den kostnads fria [Audacity](https://www.audacityteam.org/).

Spela in med 44,1 kHz 16-bitars Monophonic (CD-kvalitet) eller bättre. Nuvarande stadium är 48 kHz 24-bitars, om din utrustning stöder det. Du kommer att stänga av ljudet till 16 kHz 16-bitars innan du skickar det till den anpassade röst portalen. Det betalar fortfarande att en ursprunglig inspelning av hög kvalitet i händelse redigeringarna krävs.

Vi rekommenderar att olika personer fungerar i rollerna chef, tekniker och personal. Försök inte helt själv. En person kan vara både Director och teknikern.

### <a name="before-the-session"></a>Före sessionen

Undvik att slösa i studio-tid genom att köra skriptet med din röst personal innan du spelar in sessionen. När röst personal är bekant med texten kan de klargöra uttal av alla okända ord.

> [!NOTE]
> De flesta inspelnings Studios ger elektronisk visning av skript i registrerings hytten. I det här fallet skriver du dina körnings anteckningar direkt i skriptets dokument. Du vill fortfarande att en pappers kopia ska göra anteckningar under sessionen, även om. De flesta tekniker vill även ha en hård kopia. Och du vill ha en tredje utskrift som en säkerhets kopia för personal, om datorn inte är igång.

Din röst-personal kan be dig om vilket ord du vill framhäva i en uttryck (det "fungerande ordet"). Berätta för dem att du vill ha en naturlig läsning utan särskild uppmärksamhet. Betoning kan läggas till när tal är syntetiskt. den får inte vara en del av den ursprungliga inspelningen.

Instruera personal att uttala ord tydligt. Varje ord i skriptet bör uttalas som skrivet. Ljud får inte utelämnas eller slurreds tillsammans, som vanligt i vardagligt tal, *om de inte har skrivits på det sättet i skriptet*.

|Skriven text|Oönskat vardagligt uttal|
|-|-|
|lämna aldrig igång|lämna aldrig igång|
|Det finns fyra lampor|Det finns fyra lampor|
|Hur är väder idag|Hur är "väder" idag|
|Säg hej till min lilla vän|Säg hej till min Lil-vän|

Personal bör *inte* lägga till distinkta pauser mellan ord. Meningen bör fortfarande flöda naturligt, även om den är lite formell. Den här fina skillnaden kan vara bra att komma åt.

### <a name="the-recording-session"></a>Inspelnings sessionen

Skapa en referens inspelning eller *matcha fil* för en typisk uttryck i början av sessionen. Be personal att upprepa den här raden varje sida eller så. Jämför den nya inspelningen med referensen varje gång. Den här metoden hjälper personal att vara konsekvent i volym, tempo, färgdjup och intonation. Under tiden kan teknikern använda matchnings filen som referens för nivåer och övergripande konsekvens på ljudet.

Matchnings filen är särskilt viktig när du återupptar inspelningen efter en rast eller en annan dag. Du vill spela upp det några gånger för personal och låta dem upprepas varje gång tills de matchar sig.

Personal att ta en djup inandning och pausa en stund innan varje uttryck. Registrera några sekunders tystnad mellan yttranden. Orden bör uttalas på samma sätt varje gång de visas, vilket överväger kontexten. Till exempel är "Record" som ett verb uttalat på ett annat sätt än "Record" som ett substantiv.

Registrera en lämplig fem sekunders tystnad innan den första inspelningen fångar "rums tonen". Den här metoden hjälper den anpassade röst portalen att kompensera för eventuella återstående brus i inspelningarna.

> [!TIP]
> Allt du behöver fånga är röst personal, så att du kan göra en Monophonic-inspelning (en enda kanal) av enbart sina rader. Men om du spelar in i stereo kan du använda den andra kanalen för att registrera Chatter i kontroll rummet för att samla in diskussion om vissa rader eller ta. Ta bort den här spårningen från den version som överförs till den anpassade röst portalen.

Lyssna nära, med hörlurar, till röstens personal-prestanda. Du letar efter bra men naturlig diktering, rätt uttal och avsaknad av oönskade ljud. Ovilliga inte att be din personal att registrera en uttryck som inte uppfyller dessa standarder.

> [!TIP]
> Om du använder ett stort antal yttranden kanske en enskild uttryck inte har en märkbar inverkan på den resulterande anpassade rösten. Det kan vara mer tillrådligt att bara anteckna eventuella yttranden med problem, undanta dem från din data uppsättning och se hur din anpassade röst blir slut. Du kan alltid gå tillbaka till Studio och registrera de missade exemplen senare.

Anteckna ta-eller-tid-koden i skriptet för varje uttryck. Be teknikern att markera varje uttryck i inspelningens metadata eller i stack-och stack-bladet.

Ta vanliga avbrott och ge en dryck för att hjälpa din röst personal att hålla rösten i en fungerande form.

### <a name="after-the-session"></a>Efter sessionen

Modern inspelning Studios körs på datorer. I slutet av sessionen får du en eller flera ljudfiler, inte ett band. Filerna är förmodligen WAV-eller AIFF-format i CD-kvalitet (44,1 kHz 16 bitar) eller bättre. 48 kHz 24-bitars är vanligt och önskvärt. Högre samplings frekvenser, till exempel 96 kHz, behövs vanligt vis inte.

Den anpassade röst portalen kräver att varje tillhandahållen uttryck är i en egen fil. Varje ljudfil som levereras av Studio innehåller flera yttranden. Den primära aktiviteten efter produktion är att dela upp inspelningarna och förbereda dem för överföring. Inspelnings teknikern kan ha placerat markörer i filen (eller angett ett separat Cue-blad) för att ange var varje uttryck startar.

Använd dina anteckningar för att hitta exakt det du vill ha och Använd sedan ett verktyg för ljud redigering, till exempel [Avid Pro Tools](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html)eller den kostnads fria [Audacity](https://www.audacityteam.org/), för att kopiera varje uttryck till en ny fil.

Lämna bara cirka 0,2 sekunders tystnad i början och slutet av varje klipp, förutom det första. Filen bör börja med en fullständig fem sekunders tystnad. Använd inte ett ljud redigerings program för att "nollställa" delar av filen i bakgrunden. Om du inkluderar "rums tonen" kommer de anpassade röst algoritmerna att kompensera för eventuella kvarvarande bakgrunds brus.

Lyssna på varje fil noggrant. I det här skedet kan du redigera ut små oönskade ljud som du missade under inspelningen, t. ex. ett lätt Lip-smack innan en rad, men var noga med att inte ta bort några faktiska tal. Om du inte kan reparera en fil tar du bort den från din data uppsättning och noterar att du har gjort det.

Konvertera varje fil till 16 bitar och en samplings frekvens på 16 kHz innan du sparar och, om du har spelat in Studio-Chatter, tar du bort den andra kanalen. Spara varje fil i WAV-format och namnge filerna med uttryck-numret från skriptet.

Slutligen skapar du *avskriften* som associerar varje wav-fil med en text version av motsvarande uttryck. Att [skapa anpassade röster](./how-to-custom-voice-create-voice.md) innehåller information om det begärda formatet. Du kan kopiera texten direkt från skriptet. Skapa sedan en zip-fil med WAV-filerna och text avskriften.

Arkivera de ursprungliga inspelningarna på ett säkert ställe om du behöver dem senare. Behåll även ditt skript och dina kommentarer.

## <a name="next-steps"></a>Nästa steg

Du är redo att ladda upp dina inspelningar och skapa din anpassade röst.

> [!div class="nextstepaction"]
> [Skapa anpassade röst teckensnitt](./how-to-custom-voice-create-voice.md)
