---
title: ta med fil
description: ta med fil
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 57d14ae6e6da7cfa883f1aed74cce390c0185d98
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491701"
---
Hastigheten och framgångs takten för en bestämd RoboCopy-körning är beroende av flera faktorer:

* IOPS på käll-och mål lagrings platsen
* tillgänglig nätverks bandbredd mellan källa och mål
* möjlighet att snabbt bearbeta filer och mappar i ett namn område
* antalet ändringar mellan RoboCopy körs


### <a name="iops-and-bandwidth-considerations"></a>Överväganden vid IOPS och bandbredd

I den här kategorin måste du ta hänsyn till funktioner i **käll lagringen**, **mål lagringen** och **nätverket** som ansluter dem. Högsta möjliga data flöde bestäms av de långsammaste komponenterna i dessa tre komponenter. Kontrol lera att din nätverks infrastruktur har kon figurer ATS för att stödja optimal överföringshastighet för bästa möjliga möjligheter.

> [!CAUTION]
> När du kopierar så snabbt som möjligt är det ofta bäst att ta hänsyn till användningen av ditt lokala nätverk och NAS-enheten för andra, ofta affärs kritiska uppgifter.

Det kanske inte är önskvärt att kopiera så snabbt som möjligt om det finns en risk att migreringen kan monopolisera tillgängliga resurser.

* Tänk på att när det är bäst i din miljö att köra migreringar: under dag, på timmar eller under helger.
* Överväg även nätverks-QoS på en Windows-Server för att reglera RoboCopy-hastigheten.
* Undvik onödigt arbete för Migreringsverktyg.

RobCopy kan infoga fördröjningar mellan paket genom att ange `/IPG:n` växeln där `n` mäts i millisekunder mellan Robocopy-paket. Med den här växeln kan du undvika monopolization av resurser på både IO-begränsade enheter och överbelastade nätverks länkar.

`/IPG:n` kan inte användas för exakt nätverks begränsning till en viss Mbit/s. Använd Windows Server Network QoS i stället. RoboCopy är helt beroende av SMB-protokollet för alla nätverks behov. Att använda SMB är orsaken till att RoboCopy inte kan påverka själva nätverks flödet, men det kan sakta ned användningen. 

En liknande idé gäller den IOPS som observerats i NAS. Kluster storleken på NAS-volymen, paket storlekarna och en matris med andra faktorer påverkar den observerade IOPS. Det enklaste sättet att kontrol lera belastningen på NAS är ofta det enklaste sättet att införa en fördröjning mellan paket. Testa flera värden, till exempel från cirka 20 millisekunder (n = 20) till multiplar av det numret. När du har introducerat en fördröjning kan du utvärdera om dina andra appar nu kan fungera som förväntat. Med den här optimerings strategin kan du hitta den optimala RoboCopy-hastigheten i din miljö.

### <a name="processing-speed"></a>Bearbetnings hastighet

RoboCopy kommer att gå igenom namn området som det pekar på och utvärdera varje fil och mapp för kopiering. Varje fil kommer att utvärderas under en inledande kopia och under fångst kopior. Till exempel upprepas körning av RoboCopy-/MIR mot samma käll-och mål lagrings platser. Dessa upprepade körningar är användbara för att minimera stillestånds tiden för användare och appar, och för att förbättra den totala antalet lyckade filer som migreras.

Vi använder ofta standard bandbredd som den mest begränsande faktorn i en migrering – och kan vara sann. Men möjligheten att räkna upp ett namn område kan påverka den totala tiden för att kopiera ännu mer för större namn områden med mindre filer. Tänk på att kopiering av 1 TiB av små filer tar avsevärt längre tid än att kopiera 1 TiB av färre men större filer. Förutsatt att alla andra variabler är oförändrade.

Orsaken till den här skillnaden är den bearbetnings kraft som behövs för att gå igenom ett namn område. RoboCopy stöder flertrådade kopior via `/MT:n` parametern där n står för antalet processor trådar. När du konfigurerar en dator särskilt för RoboCopy bör du tänka på antalet processor kärnor och deras relation till antalet trådar som de tillhandahåller. De vanligaste är två trådar per kärna. Datorns kärn-och tråd antal är en viktig data punkt för att avgöra vilka värden för flera trådar `/MT:n` som du bör ange. Överväg också hur många RoboCopy-jobb du planerar att köra parallellt på en specifik dator.

Fler trådar kopierar vårt 1-TiB exempel på små filer avsevärt snabbare än färre trådar. På samma tid kanske den extra resurs investeringen på vår 1-TiB av större filer inte ger proportionella fördelar. Ett högsta antal trådar kommer att försöka kopiera fler av de stora filerna över nätverket samtidigt. Den här extra nätverks aktiviteten ökar sannolikheten för att få begränsad genom strömning eller Storage IOPS.

### <a name="avoid-unnecessary-work"></a>Undvik onödigt arbete

Undvik stora skalnings ändringar i ditt namn område. Du kan till exempel flytta filer mellan kataloger, ändra egenskaper med stor skala eller ändra behörigheter (NTFS ACL). Vissa ändringar i ACL kan ha hög påverkan eftersom de ofta har en sammanhängande ändrings effekt på filer som är lägre i mapphierarkin. Konsekvenser kan vara:

* utökat jobb körnings tid för RoboCopy eftersom varje fil och mapp som påverkas av en ACL-ändring behöver uppdateras
* att återanvända data som flyttats tidigare kan behöva kopieras igen. Till exempel måste mer data kopieras när mapp strukturer ändras efter att filerna redan har kopierats tidigare. Ett RoboCopy-jobb kan inte "spela upp" en namn områdes ändring. Nästa jobb måste rensa filerna som tidigare transporter ATS till den gamla mappstrukturen och ladda upp filerna i den nya mappstrukturen igen.

En annan viktig aspekt är att använda RoboCopy-verktyget effektivt. Med det rekommenderade RoboCopy-skriptet skapar du och sparar en loggfil för fel. Kopierings fel kan inträffa – det är normalt. De här felen gör det ofta nödvändigt att köra flera avrundning av ett kopierings verktyg som RoboCopy. En inledande körning, t. ex. från en NAS till data eller en server till en Azure-filresurs. Och en eller flera extra körningar med/MIR-växeln för att hämta och försöka igen filer som inte kopierades.

Du bör vara beredd på att köra flera avrundade RoboCopy mot en specifik namn område. Efterföljande körningar slutförs snabbare eftersom de har mindre att kopiera men är begränsade, allt efter hastigheten för bearbetning av namn området. När du kör flera avrundar kan du påskynda varje avrundning genom att inte låta RoboCopy prova orimligt hårt för att kopiera allting i en specifik körning. Dessa RoboCopy-växlar kan göra stor skillnad:

* `/R:n` n = hur ofta du försöker kopiera en misslyckad fil och 
* `/W:n` n = hur många sekunder väntar mellan återförsök

`/R:5 /W:5` är en rimlig inställning som du kan anpassa efter dina önskemål. I det här exemplet kommer en misslyckad fil att provas fem gånger, med en vänte tid på fem sekunder mellan återförsök. Om det fortfarande inte går att kopiera filen försöker nästa RoboCopy-jobb att försöka igen. Ofta filer som misslyckades eftersom de används eller på grund av timeout-problem kan ibland kopieras på det här sättet.
   
