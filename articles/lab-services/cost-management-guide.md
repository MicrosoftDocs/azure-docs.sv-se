---
title: Kostnads hanterings guide för Azure Lab Services
description: Förstå de olika sätten att Visa kostnader för labb tjänster.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98ce4d5e82d65d911984dc45615253ddcae33ae1
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589890"
---
# <a name="cost-management-for-azure-lab-services"></a>Kostnads hantering för Azure Lab Services

Kostnads hantering kan delas upp i två olika områden: kostnads uppskattning och kostnads analys.  Kostnads uppskattning inträffar när du konfigurerar labbet för att se till att den inledande strukturen i labbet får plats inom den förväntade budgeten.  Kostnads analys sker vanligt vis i slutet av månaden för att analysera kostnaderna och fastställa de åtgärder som krävs för nästa månad.

## <a name="estimate-the-lab-costs"></a>Uppskatta labb kostnaderna

Varje labb instrument panel har ett **kostnads & fakturerings** avsnitt som ger en grov uppskattning av vad labbet kostar för månaden.  Kostnads uppskattningen sammanfattar användningen av timmen med det maximala antalet användare med den uppskattade kostnaden per timme.  För att få den mest exakta uppskattningen att ställa in labbet, inklusive [schemat](how-to-create-schedules.md), och instrument panelen visar den uppskattade kostnaden.  

Den här uppskattningen kanske inte är alla möjliga kostnader, det finns några resurser som inte ingår.  Mallens förberedelse kostnad är inte faktor för kostnads uppskattningen.  Det kan variera avsevärt i hur lång tid det tar att skapa mallen. Kostnaden för att köra mallen är samma som den övergripande labb kostnaden per timme. Eventuella kostnader för [delade avbildnings gallerier](how-to-use-shared-image-gallery.md) ingår inte i labb instrument panelen eftersom ett galleri kan delas mellan flera labb.  Slutligen kan timmar som uppstått när labb skaparen startar en dator uteslutas från den här beräkningen.

> [!div class="mx-imgBorder"]
> ![Kostnads uppskattning för instrument panelen](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>Analysera föregående månads användning

Kostnads analysen är att granska föregående månads användning för att hjälpa till att fastställa eventuella justeringar för labbet.  Nedbrytning av kostnader i förflutna finns i [prenumerations kostnads analysen](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).  I Azure Portal kan du skriva "prenumerationer" i det övre Sök fältet och sedan välja alternativet prenumerationer.  

> [!div class="mx-imgBorder"]
> ![Prenumerations sökning](./media/cost-management-guide/subscription-search.png)

Välj den prenumeration som ska granskas.

> [!div class="mx-imgBorder"]
> ![Val av prenumeration](./media/cost-management-guide/subscription-select.png)

 Välj "cost Analysis" i den vänstra rutan under **Cost Management**.

> [!div class="mx-imgBorder"]
> ![Kostnads analys av prenumeration](./media/cost-management-guide/subscription-cost-analysis.png)

Den här instrument panelen kommer att möjliggöra djupgående kostnads analys, inklusive möjligheten att exportera till olika filtyper enligt ett schema.  Cost Management har flera funktioner för mer information, se [Cost Management fakturerings översikt](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filtrering av resurs typen: `microsoft.labservices/labaccounts` visar bara kostnaden som är associerad med labb tjänsterna.

## <a name="understand-the-usage"></a>Förstå användningen

Nedan visas ett exempel på kostnads analys.

> [!div class="mx-imgBorder"]
> ![Kostnads analys av prenumeration](./media/cost-management-guide/cost-analysis.png)

Som standard finns det sex kolumner: resurs, resurs typ, plats, resurs grupp namn, taggar, kostnad.  Resurs kolumnen innehåller information om labb kontot, labb namnet och den virtuella datorn.  Raderna med labb konto/labb namn/standard är kostnaden för labbet, som kan visas på den andra och tredje raden.  De använda virtuella datorerna har en kostnad under labb kontots/Labbets namn/standard/VM-namn.  I det här exemplet får du lägga till den första raden med en andra rad, både från och med "aaalab/dockerlab", och den totala kostnaden för labbet "dockerlab" i labb kontot "aaalab".

Om du vill hämta information om det delade bild galleriet ändrar du resurs typen till `Microsoft.Compute/Galleries` , som ger den övergripande kostnaden för avbildnings galleriet.  Gallerien för resurs avbildningar kanske inte visas i kostnaderna beroende på var galleriet lagras.

> [!NOTE]
> Det delade avbildnings galleriet är anslutet till labb kontot.  Det innebär att flera labb kan använda samma bild.

## <a name="separating-costs"></a>Åtskilj kostnader

Vissa universitet har använt labb kontot och resurs gruppen som olika sätt att skilja ut olika klasser.  Varje klass har sitt eget labb konto och en annan resurs grupp. I fönstret kostnads analys lägger du till ett filter baserat på resurs gruppens namn med lämpligt resurs grupp namn för klassen och endast kostnaderna för den klassen visas.  På så sätt kan du göra en tydligare avslutning mellan olika klasser när du visar kostnaderna.  Med den [schemalagda export](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) funktionen i kostnads analysen kan kostnaden för varje klass hämtas i separata filer.

## <a name="managing-costs"></a>Hantera kostnader

Beroende på typen av klass finns det olika sätt att hantera kostnader för att minska antalet virtuella datorer som körs utan student användning av datorn.

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>Maximera kostnads kontrollen med inställningar för automatisk avstängning

Med funktionen för att automatiskt stänga av kostnads styrnings funktioner kan du förhindra att de virtuella datorernas användnings timmar används i labbet. Kombinationen av följande tre funktioner för automatisk avstängning och från koppling fångar upp de flesta fall där användare av misstag lämnar sina virtuella datorer som kör:

> [!div class="mx-imgBorder"]
> ![Kostnads analys av prenumeration](./media/cost-management-guide/auto-shutdown-disconnect.png)

Dessa inställningar kan konfigureras på både labb konto nivån och labb nivån. Om inställningarna är aktiverade på labb konto nivån tillämpas de på alla labb i labb kontot. För alla nya labb konton är de här inställningarna aktiverade som standard. 

#### <a name="details-about-auto-shutdown-settings"></a>Information om inställningar för automatisk avstängning

* Koppla automatiskt bort användare från virtuella datorer som operativ systemet bedömer vara inaktivt (endast Windows).

    > [!NOTE]
    > Den här inställningen är endast tillgänglig för virtuella Windows-datorer.

    När inställningen är aktive rad kopplas användaren bort från alla datorer i labbet när Windows OS bedömer att sessionen är inaktiv (inklusive mallens virtuella datorer). [Windows OS-definitionen för Idle](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) använder två villkor: 

    * Användar frånvaro – inget tangent bord eller mus indata.
    * Brist på resursförbrukning – alla processorer och alla diskar var inaktiva under en viss tid i procent

    Användarna ser ett meddelande som detta i den virtuella datorn innan de kopplas från: 

    > [!div class="mx-imgBorder"]
    > ![Kostnads analys av prenumeration](./media/cost-management-guide/idle-timer-expired.png)
    
    Den virtuella datorn körs fortfarande när användaren är frånkopplad. Om användaren återansluter till den virtuella datorn genom att logga in, kommer Windows eller filer som var öppna eller osparade arbete tidigare till från kopplingen fortfarande att finnas där. I det här läget, eftersom den virtuella datorn körs, räknas den fortfarande som aktiv och kostnaden påförs. 
    
    Om du vill stänga av de inaktiva virtuella datorer som är frånkopplade automatiskt använder du kombinationen av **Koppla från användare när virtuella datorer är inaktiva** och **stänger av virtuella datorer när användarna kopplar från** inställningarna.

    Om du till exempel konfigurerar inställningarna enligt följande:
    
    * Koppla från användare när virtuella datorer är inaktiva – 15 minuter efter inaktivt tillstånd har identifierats.
    * Stäng av virtuella datorer när användare kopplar från – 5 minuter efter att användaren kopplats från.
    
    De virtuella Windows-datorerna stängs automatiskt om 20 minuter efter att användaren slutar använda dem. 
    
    > [!div class="mx-imgBorder"]
    > ![Kostnads analys av prenumeration](./media/cost-management-guide/vm-idle-diagram.png)
* Stäng virtuella datorer automatiskt när användare kopplar från (Windows & Linux).
    
    Den här inställningen stöder både virtuella Windows-och Linux-datorer. När den här inställningen är aktive ras sker automatisk avstängning när:
    
    * För Windows, anslutning till fjärr skrivbord (RDP) är frånkopplad.
    * SSH-anslutningen är frånkopplad för Linux.
    
    > [!NOTE]
    > Endast [vissa distributioner och versioner av Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) stöds.
    
    Du kan ange hur länge de virtuella datorerna ska vänta tills användaren ansluter igen innan den stängs av automatiskt. 
* Stäng av virtuella datorer som har startats automatiskt, men som inte ansluter till dem.
     
    I ett labb kan en användare starta en virtuell dator men inte ansluta till den. Exempel:
    
    * Ett schema i labbet startar alla virtuella datorer för en klassmodul, men vissa studenter visas inte och ansluter inte till sina datorer.  
    * En användare startar en virtuell dator, men glömmer att ansluta. 
    
    Inställningen "Stäng av virtuella datorer när användare inte ansluter" kommer att fånga dessa fall och automatiskt stänga av de virtuella datorerna.  
    
Information om hur du konfigurerar och aktiverar automatisk avstängning av virtuella datorer vid från koppling finns i följande artiklar:

* [Konfigurera automatisk avstängning av virtuella datorer vid från kopplings inställning för ett labb konto](how-to-configure-lab-accounts.md)
* [Aktivera automatisk avstängning av virtuella datorer vid från koppling](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>Kvot jämfört med schemalagd tid

När du förstår [kvot tiden](classroom-labs-concepts.md#quota) vs den [schemalagda tiden](classroom-labs-concepts.md#schedules) får labb ägaren möjlighet att konfigurera labbet så att det bättre passar behoven hos lärare och studenter.  Schemalagd tid är en angiven tid där alla virtuella student datorer har startats och är tillgängliga för anslutning till.  Vanligt vis används ofta i situationen när alla studenter har sin egen virtuella dator och följer anvisningarna i en angiven tid under dagen som klass timmar.  Nack delen är att alla elev-VM: ar startas och att kostnaderna påförs, även om en student inte loggar in på den virtuella datorn.  Kvot tiden allokeras till varje elev som de kan använda för att avgöra om och ofta används för oberoende studerande. De virtuella datorerna startas inte förrän studenten startar den virtuella datorn.  

Ett labb kan använda antingen kvot tid, schemalagd tid eller en kombination av båda. Om en klass inte behöver den schemalagda tiden använder du bara kvot tiden för den mest effektiva användningen av de virtuella datorerna.

### <a name="scheduled-event---stop-only"></a>Schemalagd händelse-stoppa bara

I schemat kan du lägga till en händelse typ som inte är i topp, vilket stoppar alla datorer vid en angiven tidpunkt.  Vissa labb ägare har angett en stopp händelse för varje dag vid midnatt för att minska kostnaderna och kvot användningen när en student glömmer att stänga av den virtuella dator som de använder.  Nack delen med den här typen av händelse är att alla virtuella datorer stängs av även om studenten använder den virtuella datorn.

### <a name="other-costs-related-to-labs"></a>Andra kostnader relaterade till labb 

Det finns kostnader som inte har registrerats i labb tjänsterna, men kan vara knutna till en labb tjänst.  Ett delat avbildnings Galleri kan vara anslutet till labb men visas inte under kostnader för labb tjänster och har kostnader.  För att hjälpa till att hålla totalkostnaderna bör du ta bort eventuella oanvända avbildningar från galleriet eftersom avbildningarna har en ärvd lagrings kostnad.  Labb kan ha anslutningar till andra Azure-resurser av ett virtuellt nätverk (VNet) när ett labb tas bort, du bör ta bort det virtuella nätverket och de andra resurserna.

## <a name="conclusion"></a>Slutsats

Förhoppnings vis ger informationen ovan en bättre förståelse för användnings kostnaderna och hur du använder verktygen för att minska överskotts kostnaderna.
