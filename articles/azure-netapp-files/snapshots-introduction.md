---
title: Så här fungerar Azure NetApp Files-ögonblicksbilder | Microsoft Docs
description: Förklarar hur Azure NetApp Files-ögonblicksbilder fungerar, inklusive metoder för att skapa ögonblicks bilder, sätt att återställa ögonblicks bilder, hur du använder ögonblicks bilder i replikerings inställningar över olika regioner.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: b-juche
ms.openlocfilehash: 4d21f7c4e74a87e409a73b22fc6b316e97e24a4e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122607"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Så här fungerar Azure NetApp Files-ögonblicksbilder

I den här artikeln förklaras hur Azure NetApp Files ögonblicks bilder fungerar. Azure NetApp Files ögonblicks bild teknologi ger stabilitet, skalbarhet och snabbare återställnings möjlighet, utan påverkan på prestanda. Azure NetApp Files ögonblicks bilds teknik utgör grunden för data skydds lösningar, inklusive fil återställningar, volym återställning och kloning och replikering över flera regioner. 

Anvisningar om hur du använder ögonblicks bilder av volymer finns i [Hantera ögonblicks bilder med hjälp av Azure NetApp Files](azure-netapp-files-manage-snapshots.md). Överväganden om ögonblicks bilds hantering i replikering över flera regioner finns i [krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Vilka ögonblicks bilder av volymer är  

En Azure NetApp Files ögonblicks bild är en volym avbildning (Point-in-Time File System). Den kan fungera som en idealisk säkerhets kopiering online. Du kan använda en ögonblicks bild för att [skapa en ny volym](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [återställa en fil](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)eller [återställa en volym](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). I specifika program data som lagras på Azure NetApp Files volymer kan ytterligare steg krävas för att säkerställa programmets konsekvens.  

Ögonblicks bilder med låg omkostnader är möjliga för de unika funktionerna i den underplacerade tekniken för volym virtualisering som är en del av Azure NetApp Files. Precis som en databas använder det här skiktet pekare till de faktiska data blocken på disken. Men till skillnad från en databas skriver den inte över befintliga block. den skriver uppdaterade data till ett nytt block och ändrar pekaren. En Azure NetApp Files ögonblicks bild ändrar bara block pekare, vilket skapar en "frusen", skrivskyddad vy av en volym som gör det möjligt för program att komma åt äldre versioner av filer och katalog versioner utan särskild programmering. Faktiska data block kopieras inte. Ögonblicks bilder är därför effektiva i den tid som behövs för att skapa dem. de är nära genom strömning, oavsett volym storlek. Ögonblicks bilder är också effektiva i lagrings utrymmen. De förbrukar sig själva inget blank steg och endast delta block mellan ögonblicks bilder och aktiva volymer behålls. 

Följande diagram illustrerar begreppen:  

![Diagram som visar viktiga begrepp för ögonblicks bilder](../media/azure-netapp-files/snapshot-concepts.png)

I diagrammen ovan tas ögonblicks bilder i bild 1a. I bild 1b skrivs ändrade data till ett *nytt block* och pekaren uppdateras. Men ögonblicks bilds pekaren pekar fortfarande på det *tidigare skrivna blocket*, vilket ger dig en Live och en historisk vy över data. En annan ögonblicks bild tas i figur 1c. Nu har du till gång till tre generationer av data (real tids data, ögonblicks bild 2 och ögonblicks bild 1, i storleksordning), utan att ta upp det volym utrymme som tre fullständiga kopior kräver. 

En ögonblicks bild tar bara en kopia av volymens metadata (*inode-tabellen*). Det tar bara några sekunder att skapa, oavsett volymens storlek, vilken kapacitet som används eller aktivitetens nivå på volymen. Därför tar en ögonblicks bild av en 100-TiB-volym att ta samma (bredvid noll) tid som en ögonblicks bild av en 100-GiB-volym. När en ögonblicks bild har skapats avspeglas ändringar av datafiler i den aktiva versionen av filerna som vanligt.

Samtidigt förblir data blocken som pekas från en ögonblicks bild till stabila och oföränderliga. På grund av typen "omdirigering av skrivning" för Azure NetApp Files ögonblicks bilder, innebär det att en ögonblicks bild inte medför några prestanda kostnader och inte använder något utrymme. Du kan lagra upp till 255 ögonblicks bilder per volym över tid, som alla är tillgängliga som skrivskyddade och online versioner av data, vilket förbrukar mindre kapacitet som antalet ändrade block mellan varje ögonblicks bild. Ändrade block lagras på den aktiva volymen. Block som pekas på i ögonblicks bilder hålls (som skrivskyddade) i volymen för att vara förbrukade, så att de bara kan användas när alla ögonblicks bilder (pekare) har rensats. Volym användningen kommer därför att öka med tiden, antingen genom nya data block eller (ändrade) data block som lagras i ögonblicks bilder.

 Följande diagram visar en volyms ögonblicks bilder och använt utrymme över tid: 

![Diagram som visar en volyms ögonblicks bilder och använt utrymme över tid](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Eftersom en ögonblicks bild av en volym endast registrerar block ändringarna sedan den senaste ögonblicks bilden, ger den följande viktiga fördelar:

* Ögonblicks bilder är ***lagrings effektiv** _.   
    Ögonblicks bilder förbrukar minimalt lagrings utrymme eftersom det inte kopierar data blocken för hela volymen. Två ögonblicks bilder som tas i följd skiljer sig bara från block som lagts till eller ändrats under tidsintervallet mellan de två. Det här block-incrementa beteendet begränsar den associerade lagrings kapacitets användningen. Många alternativa ögonblicks bilds implementeringar förbrukar lagrings volymer som motsvarar det aktiva fil systemet, vilket ger krav på lagrings kapacitet. Beroende på programmets dagliga _block nivå * ändrings takt förbrukar Azure NetApp Files ögonblicks bilder mer eller mindre kapacitet, men endast på ändrade data. Genomsnittlig förbrukning av en daglig ögonblicks bild sträcker sig från endast 1-5% av använt volym kapacitet för många program volymer, eller upp till 20-30% för volymer som SAP HANA databas volymer. Se till att [övervaka din volym och ögonblicks bilds användning](azure-netapp-files-metrics.md#volumes) för kapacitets förbrukning i bild i förhållande till antalet skapade och underhållna ögonblicks bilder.   

* Ögonblicks bilder är ***snabb för att skapa, replikera, återställa eller klona** _.   
    Det tar bara några sekunder att skapa, replikera, återställa eller klona en ögonblicks bild, oavsett volym storlek och aktivitets nivå. Du kan skapa en ögonblicks bild [av volymen på begäran](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume). Du kan också använda [ögonblicks bilds principer](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) för att ange när Azure NetApp Files ska skapa en ögonblicks bild automatiskt och hur många ögonblicks bilder som ska behållas för en volym.  Program konsekvens kan uppnås genom att dirigera ögonblicks bilder med program lagret, till exempel med hjälp av [AzAcSnap-verktyget](azacsnap-introduction.md) för SAP HANA.

_ Ögonblicks bilder har ingen påverkan på volymen ***prestanda** _.   
    På grund av den här typen av teknik för att dirigera om den här tekniken, sparar eller behåller Azure NetApp Files ögonblicks bilder ingen prestanda påverkan, även med tung data aktivitet. Att ta bort en ögonblicks bild har också liten prestanda påverkan i många fall. 

_ Ögonblicks bilder ger ***skalbar** _ eftersom de kan skapas ofta och många kan behållas.   
    Azure NetApp Files volymer har stöd för upp till 255 ögonblicks bilder. Möjligheten att lagra ett stort antal låg frekventa ögonblicks bilder ökar ofta sannolikheten för att den önskade versionen av data kan återställas.

_ Ögonblicks bilder ger ***användar synlighet** _ och _*_fil återställnings möjlighet_*_.   
Hög prestanda, skalbarhet och stabilitet för Azure NetApp Files ögonblicks bild teknik innebär att den ger en idealisk onlinesäkerhetskopiering för användar driven återställning. Ögonblicks bilder kan göras tillgängliga för fil-, katalog-eller volym återställnings syfte. Med ytterligare lösningar kan du kopiera säkerhets kopior till offline-lagring eller [Replikera över flera regioner](cross-region-replication-introduction.md) för kvarhållning eller katastrof återställning.

## <a name="ways-to-create-snapshots"></a>Sätt att skapa ögonblicks bilder   

Azure NetApp Files ögonblicks bilder är flexibla att använda. Därför finns det flera metoder för att skapa och underhålla ögonblicks bilder:

_ Manuellt (på begäran), med hjälp av:   
    * Verktygen [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST API](/rest/api/netapp/snapshots), [Azure CLI](/cli/azure/netappfiles/snapshot)eller [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)
    * Skript (se [exempel](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Automatiserad, genom att använda:
    * Ögonblicks bilds principer via [Azure Portal](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [REST API](/rest/api/netapp/snapshotpolicies), [Azure CLI](/cli/azure/netappfiles/snapshot/policy)eller [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) -verktyg
    * Programkonsekventa verktyg för ögonblicks bilder, t. ex. [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Hur volymer och ögonblicks bilder replikeras mellan regioner för DR  

Azure NetApp Files stöder [replikering över flera regioner](cross-region-replication-introduction.md) för katastrof återställning (Dr). Azure NetApp Files replikering mellan regioner använder SnapMirror-teknik. Endast ändrade block skickas över nätverket i ett komprimerat, effektivt format. När en replikering mellan regioner initieras mellan volymer överförs hela volym innehållet (det vill säga de faktiska lagrade data blocken) bara en gång. Den här åtgärden kallas för en *bas linje överföring*. Efter den första överföringen överförs endast ändrade block (som fångats i ögonblicks bilder). En asynkron 1:1-replik av käll volymen skapas (inklusive alla ögonblicks bilder).  Det här beteendet följer en fullständig och stegvis dubbelriktad replikering. Den här tillverkarspecifika tekniken minimerar mängden data som krävs för att replikera över regionerna, och därför sparar kostnader för data överföring. Den förkortar också replikerings tiden. Du kan få ett mindre återställnings punkt mål (återställnings punkt mål) eftersom fler ögonblicks bilder kan skapas och överföras oftare med begränsade data överföringar.

Följande diagram visar ögonblicks bilds trafik i scenarier för replikering mellan olika regioner: 

![Diagram som visar ögonblicks bilds trafik i scenarier för replikering mellan regioner](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Sätt att återställa data från ögonblicks bilder  

Azure NetApp Files ögonblicks bilds tekniken förbättrar frekvensen och tillförlitligheten för säkerhets kopieringar. Det medför minimala prestanda kostnader och kan skapas på ett säkert sätt på en aktiv volym. Azure NetApp Files ögonblicks bilder tillåter nära omedelbara, säkra och eventuellt användar hanterade återställningar. I det här avsnittet beskrivs olika sätt att komma åt data eller återställa från Azure NetApp Files ögonblicks bilder.

### <a name="restoring-files-or-directories-from-snapshots"></a>Återställa filer eller kataloger från ögonblicks bilder 

Om [synlighet för ögonblicks bilds Sök vägen](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) inte är dold kan användarna direkt komma åt ögonblicks bilder för att återställa efter oavsiktlig borttagning, skada eller ändring av deras data. Säkerheten för filer och kataloger bevaras i ögonblicks bilden och ögonblicks bilder är skrivskyddade enligt design. Återställningen är därför säker och enkel. 

Följande diagram visar fil-eller katalog åtkomst till en ögonblicks bild: 

![Diagram som visar fil-eller katalog åtkomst till en ögonblicks bild](../media/azure-netapp-files/snapshot-file-directory-access.png)

I diagrammet använder ögonblicks bilden 1 bara delta blocken mellan den aktiva volymen och ögonblicks bilden skapas. Men när du använder ögonblicks bilden via ögonblicks bilden av volymens ögonblicks bild *visas* data som om det är full volym kapaciteten vid tidpunkten för skapandet av ögonblicks bilden. Genom att använda mapparna för ögonblicks bilder kan användare själv återställa data genom att kopiera filer och kataloger från en ögonblicks bild.

På samma sätt går det att använda ögonblicks bilder i mål volymer över flera regioner som skrivskyddade för data återställning i DR-regionen.  

Följande diagram visar ögonblicks bilds åtkomst i scenarier för replikering mellan olika regioner: 

![Diagram som visar Snapshot-åtkomst i replikering mellan regioner](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Se [återställa en fil från en ögonblicks bild med en klient](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) för att återställa enskilda filer eller kataloger från ögonblicks bilder.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Återställa (klona) en ögonblicks bild till en ny volym

Azure NetApp Files ögonblicks bilder kan återställas till en separat, oberoende volym. Den här åtgärden är nära momentan, oavsett volymens storlek och den kapacitet som förbrukas. Den nya volymen är nästan omedelbart tillgänglig för åtkomst, medan de faktiska data blocken för volymen och ögonblicks bilden kopieras över. Beroende på volymens storlek och kapacitet kan processen ta lång tid under vilken den överordnade volymen och ögonblicks bilden inte kan tas bort. Volymen kan dock redan nås efter den första genereringen, medan kopierings processen pågår i bakgrunden. Den här funktionen möjliggör snabb volym skapande för data återställning eller volym kloning för testning och utveckling. Av data kopierings processen kommer förbrukningen av lagrings kapacitet att fördubblas när återställningen är klar och den nya volymen visar den fullständiga aktiva kapaciteten för den ursprungliga ögonblicks bilden. När den här processen har slutförts, kommer volymen att vara oberoende och avassocieras med den ursprungliga volymen, och käll volymer och ögonblicks bilder kan hanteras eller tas bort oberoende av den nya volymen.

Följande diagram visar en ny volym som skapats genom återställning (kloning) en ögonblicks bild:   

![Diagram som visar en ny volym som skapats genom återställning av en ögonblicks bild](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Samma åtgärder kan utföras på replikerade ögonblicks bilder till en haveri beredskaps volym (DR). Alla ögonblicks bilder kan återställas till en ny volym även om replikeringen mellan regioner förblir aktiv eller pågår. Den här funktionen möjliggör icke-störande skapande av test-och utvecklings miljöer i en DR-region, vilket innebär att data kan användas, medan de replikerade volymerna annars endast används för DR. Det här användnings fallet gör det möjligt att testa och utveckla isolerade från produktion, vilket eliminerar potentiell påverkan på produktions miljöer.  

Följande diagram visar volym återställning (kloning) med hjälp av ögonblicks bild av DR-mål volym medan replikeringen mellan regioner sker:  

![Diagram som visar volym återställning med ögonblicks bild av DR-mål volym](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Se [återställa en ögonblicks bild till en ny volym](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) om volym återställnings åtgärder.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Återställa (återställa) en ögonblicks bild på plats

I vissa fall, eftersom den nya volymen kommer att förbruka lagrings kapacitet, kanske det inte behövs eller lämpligt att skapa en ny volym från en ögonblicks bild. För att återställa data från skadade data (t. ex. databaser eller utpressnings angrepp) snabbt, kan det vara mer lämpligt att återställa en ögonblicks bild i själva volymen. Den här åtgärden kan utföras med hjälp av funktionen för återställning av Azure NetApp Files ögonblicks bilder. Med den här funktionen kan du snabbt återställa en volym till det tillstånd den var i när en viss ögonblicks bild togs. I de flesta fall går det mycket snabbare att återställa en volym än att återställa enskilda filer från en ögonblicks bild till det aktiva fil systemet, särskilt i stora volymer med flera TiB-volymer. 

Att återställa en ögonblicks bild av en ögonblicks bild är nära momentant och tar bara några sekunder att slutföra, även för de största volymerna. De aktiva volymens metadata (*inode-tabellen*) ersätts med metadata för ögonblicks bilder från tidpunkten för att skapa ögonblicks bilder, vilket återställer volymen till den aktuella tidpunkten. Inga data block behöver kopieras för att återställningen ska börja gälla. Därför är det mer utrymmes effektivt än att återställa en ögonblicks bild till en ny volym. 

Följande diagram visar en volym som återställs till en tidigare ögonblicks bild:  

![Diagram som visar en volym som återställs till en tidigare ögonblicks bild](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Active filesystem-data som skrevs och ögonblicks bilder som togs efter att den valda ögonblicks bilden togs bort kommer att gå förlorade. Återställnings åtgärden för ögonblicks bilder ersätter alla data på mål volymen med data i den valda ögonblicks bilden. Du bör ta hänsyn till ögonblicks bildens innehåll och datum när du väljer en ögonblicks bild. Det går inte att ångra återställnings åtgärden för ögonblicks bilder.  

Se [återställa en volym med hjälp av ögonblicks bild återställning](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) om hur du använder den här funktionen.

## <a name="how-snapshots-are-deleted"></a>Hur ögonblicks bilder tas bort 

Ögonblicks bilder använder lagrings kapacitet. De lagras vanligt vis inte på obestämd tid. För data skydd, kvarhållning och återställnings möjlighet är ett antal ögonblicks bilder (som skapats vid olika tidpunkter) vanligt vis online under en viss tids period, beroende på kraven för återställnings-, RTO-och kvarhållning i SLA. Äldre ögonblicks bilder behöver dock ofta inte lagras på lagrings tjänsten och kan behöva tas bort för att frigöra utrymme. En ögonblicks bild kan tas bort (inte nödvändigt vis i skapande ordning) av en administratör när som helst. 

> [!IMPORTANT]
> Det går inte att ta bort ögonblicks bilden. 

När en ögonblicks bild tas bort, tas alla pekare från ögonblicks bilden till befintliga data block bort. När ett data block inte har fler pekare som pekar på den (av den aktiva volymen eller andra ögonblicks bilder i volymen), returneras data blocket till volymens lediga utrymme för framtida bruk. Det innebär att ta bort ögonblicks bilder ofta frigör mer kapacitet i en volym än att ta bort data från den aktiva volymen, eftersom data block ofta samlas in i tidigare skapade ögonblicks bilder. 

Följande diagram visar effekterna av lagrings förbrukning för borttagning av ögonblicks bilder för en volym:  

![Diagram som visar lagrings förbruknings effekter för borttagning av ögonblicks bilder](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Se till att [övervaka användningen av volym och ögonblicks bilder](azure-netapp-files-metrics.md#volumes) och förstå hur användningen av program, aktiva volymer och ögonblicks bilder fungerar. 

Se [ta bort ögonblicks bilder](azure-netapp-files-manage-snapshots.md#delete-snapshots) om hur du hanterar borttagning av ögonblicks bilder. Se [Hantera ögonblicks bilds principer](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) för hur du automatiserar den här processen.

## <a name="next-steps"></a>Nästa steg

* [Hantera ögonblicksbilder med hjälp av Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Övervaka volym-och ögonblicks bild mått](azure-netapp-files-metrics.md#volumes)
* [Felsöka policyer för ögonblicksbilder](troubleshoot-snapshot-policies.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files ögonblicks bilder 101 video](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [NetApp Snapshot – NetApp video bibliotek](https://tv.netapp.com/detail/video/2579133646001/snapshot)



