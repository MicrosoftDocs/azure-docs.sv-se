---
title: Kostnads hanterings guide för Azure Lab Services
description: Förstå de olika sätten att Visa kostnader för labb tjänster.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659734"
---
# <a name="cost-management-for-azure-lab-services"></a>Kostnads hantering för Azure Lab Services

För Azure Lab Services kan kostnads hanteringen delas upp i två olika områden: kostnads uppskattning och kostnads analys. Kostnads uppskattning inträffar när du konfigurerar labbet för att se till att den inledande strukturen i labbet får plats inom den förväntade budgeten. Kostnads analys sker vanligt vis i slutet av månaden för att fastställa de åtgärder som krävs för nästa månad.

## <a name="estimate-the-lab-costs"></a>Uppskatta labb kostnaderna

Varje labb instrument panel har ett **kostnads & fakturerings** avsnitt som ger en grov uppskattning av vad labbet kostar för månaden. Kostnads uppskattningen sammanfattar användningen av timmen med det maximala antalet användare med den beräknade kostnaden per timme. För att få den mest exakta beräkningen ställer du in labbet, inklusive [schemat](how-to-create-schedules.md). Den uppskattade kostnaden visas i instrument panelen. 

Den här uppskattningen kanske inte visar alla möjliga kostnader. Några resurser ingår inte:

- Mallens förberedelse kostnad. Det kan variera avsevärt i hur lång tid det tar att skapa mallen. Kostnaden för att köra mallen är samma som den övergripande labb kostnaden per timme. 
- Eventuella kostnader för [delade avbildnings gallerier](how-to-use-shared-image-gallery.md) , eftersom ett galleri kan delas mellan flera labb. 
- Timmar som uppstått när labb skaparen startar en virtuell dator (VM).

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar kostnads uppskattningen för instrument panelen.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Analysera föregående månads användning

Kostnads analysen är att granska föregående månads användning för att hjälpa dig att fastställa eventuella justeringar för labbet. Du kan se fördelningen av tidigare kostnader i [prenumerations kostnads analysen](../cost-management-billing/costs/quick-acm-cost-analysis.md). I Azure Portal kan du ange **prenumerationer** i sökrutan och sedan välja alternativet **prenumerationer** . 

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar sökrutan och alternativet prenumerationer.](./media/cost-management-guide/subscription-search.png)

Välj den prenumeration som du vill granska.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar val av prenumeration.](./media/cost-management-guide/subscription-select.png)

Välj **kostnads analys** i den vänstra rutan under **Cost Management**.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar en prenumerations kostnads analys i ett diagram.](./media/cost-management-guide/subscription-cost-analysis.png)

Den här instrument panelen möjliggör djupgående kostnads analys, inklusive möjligheten att exportera till olika filtyper enligt ett schema. Mer information finns i [Cost Management + fakturerings översikt](../cost-management-billing/cost-management-billing-overview.md).

Du kan filtrera efter resurs typ. Med hjälp av `microsoft.labservices/labaccounts` visas endast den kostnad som är associerad med labb tjänsterna.

## <a name="understand-the-usage"></a>Förstå användningen

Följande skärm bild är ett exempel på en kostnads analys.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar ett exempel på kostnads analys för en prenumeration.](./media/cost-management-guide/cost-analysis.png)

Som standard finns det sex kolumner: **resurs**, **resurs typ**, **plats**, **resurs grupp namn**, **taggar** och **kostnad**. **Resurs** kolumnen innehåller information om labb kontot, labb namnet och den virtuella datorn. De rader som visar labb kontot, labb namnet och standard (andra och tredje raderna) är kostnaden för labbet. De virtuella datorerna som används har en kostnad som du kan se för de rader som visar labb kontot, labb namn, standard namn och VM-namn. 

I det här exemplet kommer du att lägga till den första och andra raden (både från och med **aaalab/dockerlab**) som ger den totala kostnaden för labbet "dockerlab" i labb kontot "aaalab".

Ändra resurs typen till om du vill få en övergripande kostnad för avbildnings galleriet `Microsoft.Compute/Galleries` . Ett delat bild galleri kanske inte visas i kostnaderna, beroende på var galleriet lagras.

> [!NOTE]
> Ett delat avbildnings galleri är anslutet till labb kontot. Det innebär att flera labb kan använda samma bild.

## <a name="separate-the-costs"></a>Separera kostnaderna

Vissa universitet har använt labb kontot och resurs gruppen som olika sätt att avgränsa klasserna. Varje klass har sitt eget labb konto och en annan resurs grupp. 

I fönstret kostnads analys lägger du till ett filter baserat på resurs gruppens namn med lämpligt resurs grupp namn för klassen. Sedan visas bara kostnaderna för den klassen. På så sätt kan du göra en tydligare delinjärhet mellan klasserna när du visar kostnaderna. Du kan använda den [schemalagda export](../cost-management-billing/costs/tutorial-export-acm-data.md) funktionen i kostnads analysen för att ladda ned kostnaderna för varje klass i separata filer.

## <a name="manage-costs"></a>Hantera kostnader

Beroende på typen av klass finns det olika sätt att hantera kostnader för att minska antalet instanser av virtuella datorer som körs utan student användning.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Inställningar för automatisk avstängning för kostnads kontroll

Med funktionen för automatisk avstängning kan du förhindra användnings timmar för virtuella datorer i labbet. Följande inställningar fångar de flesta fall där användare oavsiktligt lämnar sina virtuella datorer igång:

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar de tre inställningarna för automatisk avstängning.](./media/cost-management-guide/auto-shutdown-disconnect.png)

Du kan konfigurera dessa inställningar på både labb konto nivå och labb nivå. Om du aktiverar dem på labb konto nivån används de för alla labb i labb kontot. För alla nya labb konton är de här inställningarna aktiverade som standard. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>Koppla automatiskt bort användare från virtuella datorer som operativ systemet bedömer som inaktivt

> [!NOTE]
> Den här inställningen är endast tillgänglig för virtuella Windows-datorer.

När inställningen **Koppla från användare när virtuella datorer är inaktiv** är aktive rad kopplas användaren från alla datorer i labbet när Windows-operativsystemet bedömer att sessionen är inaktiv (inklusive mallens virtuella datorer). [Windows OS-definitionen för Idle](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) använder två villkor: 

* Användar frånvaro: inget tangent bord eller mus indata.
* Brist på resursförbrukning: alla processorer och alla diskar var inaktiva under en viss procent andel av tiden.

Användarna ser ett meddelande som detta i den virtuella datorn innan de kopplas från: 

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar ett varnings meddelande om att en session har varit inaktiv under tids gränsen och kommer att kopplas från.](./media/cost-management-guide/idle-timer-expired.png)
 
Den virtuella datorn körs fortfarande när användaren är frånkopplad. Om användaren återansluter till den virtuella datorn genom att logga in, är Windows eller filer som var öppna eller fungerar som inte sparades före från kopplingen kvar. I det här läget, eftersom den virtuella datorn körs, räknas den fortfarande som aktiv och kostnaden påförs. 
 
Om du automatiskt vill stänga av inaktiva virtuella Windows-datorer som är frånkopplade använder du en kombination av **Koppla från användare när virtuella datorer är inaktiva** och **stänger av virtuella datorer när användarna kopplar från** inställningarna.

Om du till exempel konfigurerar inställningarna enligt följande:
 
* **Koppla från användare när virtuella datorer är inaktiva**: 15 minuter efter att tillståndet inaktive ras.
* **Stäng av virtuella datorer när användare kopplar från**: 5 minuter efter att användaren kopplats från.
 
De virtuella Windows-datorerna stängs av automatiskt om 20 minuter efter att användaren slutar använda dem. 
 
> [!div class="mx-imgBorder"]
> ![Diagram som illustrerar den kombination av inställningar som skapas vid automatisk avstängning av virtuella datorer.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Stäng virtuella datorer automatiskt när användare kopplar från
 
Inställningen **Stäng av virtuella datorer när användare kopplar från** , stöder både virtuella Windows-och Linux-datorer. När den här inställningen är aktive ras sker automatisk avstängning när:
 
* För Windows frånkopplas en anslutning till fjärr skrivbord (RDP).
* För Linux är en SSH-anslutning frånkopplad.
 
> [!NOTE]
> Endast [vissa distributioner och versioner av Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions) stöds.
 
Du kan ange hur länge de virtuella datorerna ska vänta tills användaren ansluter igen innan den stängs av automatiskt. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Stäng av virtuella datorer som har startats automatiskt, men som inte ansluter till dem
 
I ett labb kan en användare starta en virtuell dator men inte ansluta till den. Exempel:
 
* Ett schema i labbet startar alla virtuella datorer för en klassmodul, men vissa studenter visas inte och ansluter inte till sina datorer. 
* En användare startar en virtuell dator men glömmer att ansluta. 
 
Inställningen **Stäng av virtuella datorer när användare inte ansluter** kommer att fånga dessa fall och automatiskt stänga av de virtuella datorerna. 
 
Information om hur du konfigurerar och aktiverar automatisk avstängning av virtuella datorer vid från koppling finns i följande artiklar:

* [Konfigurera automatisk avstängning av virtuella datorer för ett labb konto](how-to-configure-lab-accounts.md)
* [Konfigurera automatisk avstängning av virtuella datorer för ett labb](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Schemalagd tid jämfört med kvot tid

Att förstå [schemalagd tid](classroom-labs-concepts.md#schedules) och [kvot tid](classroom-labs-concepts.md#quota) hjälper dig att konfigurera ett labb för bättre anpassning av behoven hos lärare och studenter. 

Schemalagd tid är en angiven tid där alla virtuella student datorer har startats och är tillgängliga för anslutning. Den schemalagda tiden används ofta när alla studenter har sina egna virtuella datorer och följer anvisningarna på den första dagen (t. ex. klass timmar). Nack delen är att alla elev-VM: ar startas och att kostnaderna påförs, även om en student inte loggar in på en virtuell dator. 

Kvot tiden allokeras till varje elev och används ofta för oberoende studerande. De virtuella datorerna startas inte förrän studenten startar den virtuella datorn. 

Ett labb kan använda antingen kvot tid eller schemalagd tid, eller en kombination av båda. Om en klass inte behöver schemalagd tid använder du bara kvot tiden för den mest effektiva användningen av de virtuella datorerna.

### <a name="scheduled-event-stop-only"></a>Schemalagd händelse: stoppa bara

I schemat kan du lägga till en händelse typ som inte är av typen stopp som stoppar alla datorer vid en angiven tidpunkt. Vissa labb ägare har angett en stopp händelse för varje dag vid midnatt för att minska kostnaderna och kvot användningen när en student glömmer att stänga av den virtuella dator som de använder. Nack delen med den här typen av händelse är att alla virtuella datorer ska stängas av, även om en student använder en virtuell dator.

### <a name="other-costs-related-to-labs"></a>Andra kostnader relaterade till labb 

Vissa kostnader är inte i labb tjänster, men kan vara knutna till en labb tjänst. Du kan ansluta ett delat avbildnings galleri till ett labb, men det visas inte under kostnader för labb tjänster och har kostnader. För att hålla totalkostnaderna nere bör du ta bort eventuella oanvända avbildningar från galleriet eftersom avbildningarna har en inbyggd lagrings kostnad. 

Labb kan ha anslutningar till andra Azure-resurser via ett virtuellt nätverk. När ett labb tas bort bör du ta bort det virtuella nätverket och de andra resurserna.

## <a name="conclusion"></a>Slutsats

Förhoppnings vis har informationen i den här artikeln fått en bättre förståelse för de verktyg som kan hjälpa dig att minska användnings kostnaderna.