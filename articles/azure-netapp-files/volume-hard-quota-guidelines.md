---
title: Det innebär att du ändrar volymens hård kvot för din Azure NetApp Files-tjänst | Microsoft Docs
description: Beskriver ändringen av volymens hård kvot, hur du planerar ändringen och hur du övervakar och hanterar kapaciteter.
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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 52e855cb4ab42ed78a055ecdc31cffa886dc9bf2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731997"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Det innebär att du kan ändra volymens hård kvot för din Azure NetApp Files-tjänst

Från början av tjänsten har Azure NetApp Files använt en pool för kapacitets etablering och automatisk tillväxt. Azure NetApp Files volymer är tunt etablerade på en underställd pool med kundetablerad kapacitet för en vald nivå och storlek. Volym storlekar (kvoter) används för att tillhandahålla prestanda och kapacitet, och kvoterna kan justeras direkt när som helst. Detta innebär att volym kvoten för närvarande är en prestanda inställning som används för att styra bandbredden för volymen. För närvarande växer automatiskt kapacitets pooler när kapaciteten fylls.   

> [!IMPORTANT] 
> Azure NetApp Files beteendet för volym-och kapacitets etablerings etableringen kommer att ändras till en *manuell* och *kontrollerbar* mekanism. **Från och med den 30 april 2021 (uppdaterat) kommer volym storlekar (kvot) att hantera bandbredds prestanda, samt en etablerad kapacitet, och de underliggande kapacitets pooler kommer inte längre att växa automatiskt.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Orsaker till volymens hård kvot ändring

Många kunder har angett tre huvudsakliga utmaningar med det *första* beteendet:
* VM-klienter ser den tunt allokerade (100 TiB) kapaciteten för en bestämd volym när du använder verktyg för operativ system utrymme eller kapacitets övervakning, vilket ger felaktig kapacitets visning på klient eller program sida.
* Program ägare har ingen kontroll över allokerat kapacitets utrymme för pool (och associerad kostnad) på grund av beteendet för automatisk storleks ökning i kapacitets gruppen. Den här situationen är besvärlig i miljöer där "körnings processer" kan snabbt fylla upp och öka den tillhandahållna kapaciteten och kostnaden.
* Kunder vill se och underhålla en direkt korrelation mellan volymens storlek (kvot) och prestanda. Med det aktuella beteendet (implicit) oversubscribing en volym (kapacitets vis) och automatisk storleks ökning i pooler, kan kunderna inte ha någon direkt korrelation, tills volym kvoten har ställts in eller återställs aktivt. 

Många kunder har begärt direkt kontroll över etablerad kapacitet. De vill kontrol lera och balansera lagrings kapaciteten och användningen. De vill också kontrol lera kostnaderna tillsammans med program sidans och klient sidans synlighet på tillgänglig, Använd och etablerad kapacitet och prestanda för deras program volymer. 

## <a name="what-is-the-volume-hard-quota-change"></a>Vad är ändring av volymens hård kvot   

Med volym ändringen av hård kvoten kommer Azure NetApp Files volymer inte längre vara tunt etablerade vid (max) 100 TiB. Volymerna kommer att tillhandahållas med den faktiska konfigurerade storleken (kvoten). Dessutom kommer Poolernas kapacitets pooler inte längre att växa automatiskt när det når full kapacitets förbrukning. Den här ändringen visar beteendet som Azure Managed disks, som också tillhandahålls i befintligt skick, utan automatisk kapacitets ökning.

Överväg till exempel en Azure NetApp Files volym som har kon figurer ATS på 1-TiB storlek (kvot) på en 4-TiB hög service nivå kapacitets grupp. Ett program skriver kontinuerligt data till volymen.

Det *första* beteendet:  
* Förväntad bandbredd: 128 MiB/s
* Total användbar (och synlig klient) kapacitet: 100 TiB   
    Du kommer inte att kunna skriva mer data på volymen utöver den här storleken.
* Kapacitets pool: ökar automatiskt med 1 TiB steg när den är full.
* Ändring av volym kvot: endast ändringar av volymens prestanda (bandbredd). Den ändrar inte synlig eller användbar kapacitet för klienten.

Det *ändrade* beteendet:  
* Förväntad bandbredd: 128 MiB/s
* Total användbar (och synlig klient) kapacitet: 1 TiB du kan inte skriva mer data på volymen utöver den här storleken.
* Kapacitets pool: behåller 4 TiB storlek och växer inte automatiskt. 
* Ändring av volym kvot: ändrar prestanda (bandbredd) och synlig eller användbar kapacitet för volymen.

Du måste övervaka användningen av Azure NetApp Files volymer och kapacitets grupper proaktivt. Du måste ändra belastningen på volymen och poolen för att kunna använda den. Azure NetApp Files kommer att fortsätta att tillåta [åtgärder för att ändra storlek på den lokala volymen och kapacitets poolen](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Så här operationalisera du volymens hård kvot ändring

Det här avsnittet innehåller anvisningar om hur du operationalisera förändringen av volymens hård kvot för en smidig över gång. Det ger också insikter om hantering av just nu etablerade volymer och kapacitets grupper, pågående övervakning och alternativ för avisering och kapacitets hantering.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>För närvarande etablerade volymer och kapacitets grupper

På grund av volymens hård kvot ändring bör du ändra din operativ modell. De etablerade volymerna och kapacitets poolerna kräver kontinuerlig kapacitets hantering.  Eftersom det ändrade beteendet sker direkt, rekommenderar Azure NetApp Files-teamet en serie åtgärder som korrigeras för befintliga, tidigare etablerade volymer och kapacitets grupper, enligt beskrivningen i det här avsnittet.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Åtgärds rekommendationer vid enstaka tidpunkter eller förebyggande åtgärder  

Volymens hårda kvot ändring leder till ändringar i etablerade och tillgängliga volymer för tidigare etablerade volymer och pooler. Därför kan vissa utmaningar med kapacitets beläggningen inträffa. För att undvika kortsiktiga bristande situationer för kunder, rekommenderar Azure NetApp Files-teamet följande åtgärder som åtgärdas/förebyggande av en tidpunkt: 

* **Etablerade volym storlekar**:   
    Ändra storlek på varje allokerad volym så att den har rätt buffert baserat på ändrings takt och avisering eller storleks ändrings tid (till exempel 20% baserat på typiska arbets belastnings överväganden), med högst 100 TiB (vilket är [volymens storleks gräns](azure-netapp-files-resource-limits.md#resource-limits)). Den nya volym storleken, inklusive buffertens kapacitet, bör baseras på följande faktorer:
    * **Etablerad** volym kapacitet, om den använda kapaciteten är mindre än den etablerade volym kvoten.
    * **Använd** volym kapacitet, om den använda kapaciteten är större än den etablerade volym kvoten.  
    Det kostar inget extra att öka kapaciteten på volym nivå om kapacitets poolen för underlägget inte behöver odlas. Som en effekt av den här ändringen kan du se en *ökning* av bandbredds gränsen för volymen (om den [automatiska adresspoolen för QoS-kapacitet](azure-netapp-files-understand-storage-hierarchy.md#qos_types) används).

* **Etablerade kapacitets bassängs storlekar**:   
    Efter justeringen av volym storlekarna måste kapacitets gruppen ökas till en storlek som är lika med eller större än summan av volymerna, om summan av volym storlekar blir större än storleken på volymerna, med högst 500 TiB (vilket är [storleks gränsen för kapacitets pool](azure-netapp-files-resource-limits.md#resource-limits)). Ytterligare kapacitet för kapacitets bassänger är beroende av ACR-avgiften som normalt.

Du bör arbeta med dina Azure NetApp Files specialister för att verifiera din miljö, om du behöver hjälp med att konfigurera övervakning eller aviseringar enligt beskrivningen i avsnitten nedan.

### <a name="ongoing-capacity-management"></a>Kontinuerlig kapacitets hantering  

När du har genomfört korrigerande åtgärder bör du samla in pågående processer för att övervaka och hantera kapacitet. Följande avsnitt innehåller förslag och alternativ för kapacitets övervakning och hantering.

### <a name="monitor-capacity-utilization"></a>Övervaka kapacitets användning

Du kan övervaka kapacitets användningen på olika nivåer. 

#### <a name="vm-level-monitoring"></a>Övervakning på VM-nivå 

Den högsta övervaknings nivån (närmast programmet) är inifrån den virtuella program datorn. Du kommer att se hur det fungerar i kapacitets rapporteringen från den virtuella datorns klient operativ system.

I följande två scenarier bör du överväga en Azure NetApp Files volym som har kon figurer ATS med 1-TiB storlek (kvot) på en 4-TiB-pool med kapacitet på service nivå. 

##### <a name="windows"></a>Windows

Windows-klienter kan kontrol lera den använda och tillgängliga kapaciteten för en volym med hjälp av egenskaperna för den mappade nätverks enheten. Du kan använda alternativet för  ->  **enhets**  ->  **Egenskaper** i Utforskaren.  

I följande exempel visas volym kapacitets rapportering i Windows *innan* det ändrade beteendet:

![Skärm bilder som visar exempel lagrings kapacitet för en volym innan beteendet ändras.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Du kan också använda `dir` kommandot i kommando tolken som du ser nedan:

![Skärm bild som visar hur du använder ett kommando för att Visa lagrings kapaciteten för en volym innan beteendet ändras.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

I följande exempel visas volym kapacitets rapportering i Windows *efter* det ändrade beteendet:

![Skärm bilder som visar exempel lagrings kapacitet för en volym efter ändring av beteendet.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

I följande exempel visas `dir` kommandoutdata:  

![Skärm bild som visar hur du använder ett kommando för att Visa lagrings kapaciteten för en volym efter ändring av beteendet.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux-klienter kan kontrol lera den använda och tillgängliga kapaciteten för en volym med hjälp av [ `df` kommandot](https://linux.die.net/man/1/df). `-h`Alternativet visar storleken, det använda utrymmet och det tillgängliga utrymmet i människo läsbart format, med enhets storlekarna M, G och T.

I följande exempel visas volym kapacitets rapportering i Linux *innan* det ändrade beteendet:  

![Skärm bild som visar hur du använder Linux för att Visa lagrings kapaciteten för en volym innan beteendet ändras.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

I följande exempel visas volym kapacitets rapportering i Linux *efter* det ändrade beteendet:  

![Skärm bild som visar hur du använder Linux för att Visa lagrings kapaciteten för en volym efter ändring av beteendet.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Konfigurera aviseringar med ANFCapacityManager

Du kan använda ANFCapacityManager-verktyget som Logic Apps stöds av community för att övervaka Azure NetApp Files kapacitet och få skräddarsydda aviseringar. Verktyget ANFCapacityManager finns på [sidan ANFCapacityManager GitHub](https://github.com/ANFTechTeam/ANFCapacityManager).

ANFCapacityManager är en Azure Logic app som hanterar kapacitets aviserings regler. Den ökar volym storlekarna automatiskt för att förhindra att Azure NetApp Files-volymer börjar ta slut på utrymme. Det är enkelt att distribuera och ger följande Aviseringshantering funktioner:

* När en Azure NetApp Files-kapacitet eller volym skapas, skapar ANFCapacityManager en regel för mått varningar baserat på det angivna tröskelvärdet för procent förbrukat.
* När en Azure NetApp Files kapacitets-eller volym storlek ändras ändrar ANFCapacityManager regel varnings regeln baserat på det angivna tröskelvärdet för procent förbrukade procent. Om aviserings regeln inte finns skapas den.
* När en pool med Azure NetApp Files kapacitet eller volym tas bort, tas motsvarande mått varnings regel bort.

Du kan konfigurera följande inställningar för nyckel aviseringar:  

* **Poolen% full Threshold** – den här inställningen avgör den förbrukade tröskeln som utlöser en avisering för kapacitets grupper. Värdet 90 skulle orsaka att en avisering utlöses när kapacitetsutnyttjandet når 90% för bruk ATS.
* **Volym% fullständig tröskel** – med den här inställningen bestäms det förbrukade tröskelvärdet som utlöser en avisering för volymer. Värdet 80 skulle orsaka att en avisering utlöses när volymen når 80% för bruk ATS.
* **Befintlig åtgärds grupp för kapacitets aviseringar** – den här inställningen är den åtgärds grupp som ska utlösas för kapacitets-baserade aviseringar. Den här inställningen bör skapas i förväg av dig. Åtgärds gruppen kan skicka e-post, SMS eller andra format.

Följande bild visar aviserings konfigurationen:  

![Bild som visar aviserings konfiguration med hjälp av ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

När du har installerat ANFCapacityManager kan du förvänta följande beteende: när en pool för Azure NetApp Files kapacitet eller volym skapas, ändras eller tas bort, kommer Logic app automatiskt att skapa, ändra eller ta bort en kapacitets beroende mått varnings regel med namnet `ANF_Pool_poolname` eller `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Hantera kapacitet

Förutom övervakning och avisering bör du även inkludera en användning av program kapacitets hantering för att hantera Azure NetApp Files (ökad) kapacitets förbrukning. När en Azure NetApp Files volym eller kapacitets uppsättning fyller upp [kan extra kapacitet tillhandahållas direkt utan program avbrott](azure-netapp-files-resize-capacity-pools-or-volumes.md). I det här avsnittet beskrivs olika manuella och automatiserade sätt att öka mängden allokerat utrymme för volymer och kapacitets pooler vid behov.
 
#### <a name="manual"></a>Manuell 

Du kan använda portalen eller CLI för att öka storleken på volymer eller kapacitets pooler manuellt. 

##### <a name="portal"></a>Portal 

Du kan [ändra storlek på en volym om det](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) behövs. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.

1. Från bladet hantera NetApp-konton klickar du på **volymer**.  
2. Högerklicka på namnet på den volym som du vill ändra storlek på eller klicka på `…` ikonen i slutet av volymens rad för att Visa snabb menyn. 
3. Använd snabb meny alternativen för att ändra storlek på eller ta bort volymen.   

   ![Skärm bild som visar snabb meny alternativ för en volym.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Skärm bild som visar fönstret uppdatera volym kvot.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

I vissa fall har poolen för värd kapacitet inte tillräckligt med kapacitet för att ändra storlek på volymerna. Du kan dock [ändra storleken](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) på TIB ökningar eller minskningar i en pool. Storleken på kapacitets gruppen får inte vara mindre än 4 TiB. *Om du ändrar storlek på kapacitetsutnyttjandet ändras den köpta Azure NetApp Files kapaciteten.*

1. Från bladet hantera NetApp-konton klickar du på den kapacitets uppsättning som du vill ändra storlek på.
2. Högerklicka på namnet på mediepoolen eller klicka på `…` ikonen i slutet av Capacity-poolens rad för att Visa snabb menyn.
3. Använd snabb meny alternativen för att ändra storlek på eller ta bort kapacitets gruppen.    

   ![Skärm bild som visar snabb meny alternativ för en kapacitets grupp.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Skärm bild som visar fönstret Ändra storlek på pool.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI eller PowerShell

Du kan använda [Azure NETAPP Files CLI-verktyg](azure-netapp-files-sdk-cli.md#cli-tools), inklusive Azure CLI och Azure PowerShell, för att manuellt ändra storlek på volymen eller kapacitets gruppen.  Följande två kommandon kan användas för att hantera Azure NetApp Files volym-och pool resurser:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Om du vill hantera Azure NetApp Files-resurser med Azure CLI kan du öppna Azure Portal och välja Azure **Cloud Shell** -länken överst på Meny raden: 

[![Skärm bild som visar hur du kommer åt Cloud Shell länk. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Den här åtgärden öppnar Azure Cloud Shell:

[![Skärm bild som visar Cloud Shell fönstret. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

I följande exempel används kommandon för att [Visa](/cli/azure/netappfiles/volume#az-netappfiles-volume-show) och [Uppdatera](/cli/azure/netappfiles/volume#az-netappfiles-volume-update) storleken på en volym:
 
[![Skärm bild som visar hur volym storleken visas med PowerShell. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Skärm bild som visar hur du uppdaterar volym storleken med PowerShell. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

I följande exempel används kommandona för att [Visa](/cli/azure/netappfiles/pool#az-netappfiles-pool-show) och [Uppdatera](/cli/azure/netappfiles/pool#az-netappfiles-pool-update) storleken på en kapacitets grupp:

[![Skärm bild som visar hur du använder PowerShell för att visa storleken på kapacitets gruppen. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Skärm bild som visar hur du använder PowerShell för att uppdatera kapacitets Poolens storlek. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatiserad  

Du kan bygga en automatiserad process för att hantera det ändrade beteendet.

##### <a name="rest-api"></a>REST-API   

REST API för Azure NetApp Files-tjänsten definierar HTTP-åtgärder mot resurser, till exempel NetApp-konto, kapacitets uppsättning, volymer och ögonblicks bilder. REST APIs specifikationen för Azure NetApp Files publiceras via [Azure NetApp Files Resource Manager-GitHub sida](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]. Du kan hitta [exempel kod för användning med REST API: er](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) i GitHub.

Se [utveckla för Azure NetApp Files med REST API](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>REST API med hjälp av PowerShell  

REST API för Azure NetApp Files-tjänsten definierar HTTP-åtgärder mot resurser, till exempel NetApp-konto, kapacitets uppsättning, volymer och ögonblicks bilder. [Rest Apis specifikationen för Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) publiceras via GitHub.

Se [utveckla för Azure NetApp Files med REST API med PowerShell](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Kapacitets hantering med ANFCapacityManager

ANFCapacityManager är en Azure Logic app som hanterar kapacitets aviserings regler. Den ökar volym storlekarna automatiskt för att förhindra att Azure NetApp Files-volymer börjar ta slut på utrymme. Förutom att skicka aviseringar kan du aktivera den automatiska ökningen av volym-och kapacitets poolens storlekar för att förhindra att Azure NetApp Files-volymer får slut på utrymme: 

* Om en Azure NetApp Files volym når det angivna tröskelvärdet för förbrukad procent, ökas volym kvoten (storlek) med den procents ATS som angetts mellan 10-100.  
* Om du ökar volym storleken överskrider kapaciteten för den överordnade kapacitets poolen, ökar kapacitets poolinställningarna också för att hantera den nya volym storleken.

Du kan konfigurera följande inställning för nyckel kapacitets hantering:  

* **Utöka automatiskt procent ökning** – procent av den befintliga volym storleken för att automatiskt utöka en volym om den når det angivna **% fulla tröskelvärdet**. Om värdet är 0 (noll) inaktive ras funktionen för automatisk utökning. Ett värde mellan 10 och 100 rekommenderas.

    ![Skärm bild som visar fönstret Ange volymens automatiska tillväxt procent.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Vanliga frågor 

I det här avsnittet besvaras några frågor om volymens hård kvot ändring. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Räknas ögonblicks bilds området mot den användbara eller etablerade kapaciteten för en volym?

Ja, den förbrukade ögonblicks bild kapaciteten räknas till det allokerade utrymmet på volymen. Om volymen körs fullt ut bör du överväga två reparations alternativ:

* Ändra storlek på volymen enligt beskrivningen i den här artikeln.
* Ta bort äldre ögonblicks bilder för att frigöra utrymme på värd volymen.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Innebär den här ändringen att beteendet för automatisk volym ökning försvinner från Azure NetApp Files?

En vanlig felbegrepps åtgärd är att Azure NetApp Files *volymer* utökas automatiskt när de fylls. Volymerna upprättades tunt med en storlek på 100 TiB, oavsett den faktiska mängd kvoten, medan *kapacitets* gruppen för underpooler skulle växa automatiskt med 1-TIB-steg. Den här ändringen kommer att hantera den (synliga och användbara) *volym* storleken för den inställda kvoten och *kapacitets grupper* kommer inte längre att växa automatiskt. Den här ändringen resulterar i att du vanligt vis vill ha rätt utrymme och kapacitets rapportering på klient sidan. Den undviker kapacitets förbrukningen "Kap".

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Påverkar den här ändringen de volymer som replikeras med replikering mellan regioner (för hands version)? 

Hård volym kvoten tillämpas inte på mål volymerna för replikeringen.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Påverkar den här ändringen de mått som för närvarande är tillgängliga i Azure Monitor?

Portal mått och Azure Monitor statistik kommer att avspegla den nya tilldelnings-och användnings modellen korrekt.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Påverkar den här ändringen resurs gränserna för Azure NetApp Files?

Det finns inga förändringar i resurs gränserna för Azure NetApp Files utöver de kvot ändringar som beskrivs i den här artikeln.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Finns det ett exempel på ANFCapacityManager-arbetsflöde?  

Ja. Se GitHub-sidan för automatisk [volym arbets flödes exempel](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Stöds ANFCapacityManager Microsoft?  

[ANFCapacityManager Logic-appen tillhandahålls i befintligt skick och stöds inte av NetApp eller Microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Du uppmanas att ändra så att det passar din miljö eller dina behov. Du bör testa funktionerna innan du distribuerar dem till alla affärs kritiska eller produktions miljöer.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Hur kan jag rapportera ett fel eller skicka en funktions förfrågan för ANFCapacityManger?
Du kan skicka buggar och funktions förfrågningar genom att klicka på **nytt problem** på [sidan ANFCapacityManager GitHub](https://github.com/ANFTechTeam/ANFCapacityManager/issues).

## <a name="next-steps"></a>Nästa steg
* [Ändra storlek på en kapacitetspool eller en volym](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Mått för Azure NetApp Files](azure-netapp-files-metrics.md)
