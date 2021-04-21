---
title: Vad ändring till volymhård kvot innebär för din Azure NetApp Files tjänst | Microsoft Docs
description: Beskriver ändringen av att använda volymhård kvot, hur du planerar för ändringen och hur du övervakar och hanterar kapaciteter.
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
ms.openlocfilehash: 5e7f71f91e5778b4f096bb760bfe5a0a89b5cbcb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764287"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Vad ändring till volymhård kvot innebär för din Azure NetApp Files tjänst

Från början av tjänsten har Azure NetApp Files använt en etablering av kapacitetspooler och en automatisk tillväxtmekanism. Azure NetApp Files volymer är tunt etablerade på en underlager, kundetablering av kapacitetspool med en vald nivå och storlek. Volymstorlekar (kvoter) används för att tillhandahålla prestanda och kapacitet, och kvoter kan justeras när som helst. Detta innebär att volymkvoten för närvarande är en prestandas lever som används för att styra bandbredden till volymen. För närvarande växer underlagerkapacitetspooler automatiskt när kapaciteten fylls.   

> [!IMPORTANT] 
> Det Azure NetApp Files beteendet för etablering av volym- och kapacitetspooler ändras *till en manuell* och *kontrollbar* mekanism. **Från och med 30 april 2021 (uppdaterad), kommer volymstorlekar (kvot) att hantera bandbreddsprestanda, samt etablerad kapacitet, och underliggande kapacitetspooler kommer inte längre att växa automatiskt.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Orsaker till att kvoten för volymhård volym har ändrats

Många kunder har angett tre huvudsakliga utmaningar med det *inledande* beteendet:
* VM-klienter skulle se den tunt etablerade (100 TiB)-kapaciteten för en viss volym när de använder verktyg för os-utrymme eller kapacitetsövervakning, vilket ger felaktig kapacitetssynlighet på klient- eller programsidan.
* Programägare skulle inte ha någon kontroll över det etablerade kapacitetspoolutrymmet (och tillhörande kostnad) på grund av beteendet för automatisk tillväxt av kapacitetspoolen. Den här situationen är besvärlig i miljöer där "run-away-processer" snabbt kan fyllas upp och öka den etablerade kapaciteten och kostnaden.
* Kunder vill se och upprätthålla en direkt korrelation mellan volymstorlek (kvot) och prestanda. Med det aktuella beteendet för (implicit) överprenumerering av en volym (kapacitetsvis) och automatisk pooltillväxt har kunderna ingen direkt korrelation förrän volymkvoten har ställts in eller återställts aktivt. 

Många kunder har begärt direkt kontroll över den etablerade kapaciteten. De vill styra och balansera lagringskapacitet och användning. De vill också kontrollera kostnaderna tillsammans med synligheten på programsidan och på klientsidan för tillgängliga, använda och etablerade kapaciteter och prestanda för sina programvolymer. 

## <a name="what-is-the-volume-hard-quota-change"></a>Vad är ändringen av kvoten för volymhård   

När kvoten för volymen är hård kommer Azure NetApp Files volymer inte längre att vara tunt etablerade vid (max) 100 TiB. Volymerna etableras med den faktiska konfigurerade storleken (kvoten). Dessutom kommer de kapacitetspooler som ligger under att växa automatiskt när förbrukningen når full kapacitet. Den här ändringen återspeglar beteendet som Hanterade Azure-diskar, som också etableras som de är, utan automatisk kapacitetsökning.

Du kan till exempel Azure NetApp Files en volym som konfigurerats med en TiB-storlek (kvot) på en kapacitetspool på 4 TiB Ultra-tjänstnivå. Ett program skriver kontinuerligt data till volymen.

Det *inledande* beteendet:  
* Förväntad bandbredd: 128 MiB/s
* Total användbar kapacitet (och synlig klient): 100 TiB   
    Du kommer inte att kunna skriva mer data på volymen utöver den här storleken.
* Kapacitetspool: Växer automatiskt med 1 TiB-steg när den är full.
* Ändring av volymkvot: Ändrar endast volymens prestanda (bandbredd). Klientens synliga eller användbara kapacitet ändras inte.

Det *ändrade* beteendet:  
* Förväntad bandbredd: 128 MiB/s
* Total användbar kapacitet (och synlig klient): 1 TiB Du kommer inte att kunna skriva mer data på volymen utöver den här storleken.
* Kapacitetspool: Förblir 4 TiB i storlek och växer inte automatiskt. 
* Ändring av volymkvot: Ändrar prestanda (bandbredd) och klientens synliga eller användbara kapacitet för volymen.

Du måste proaktivt övervaka användningen av Azure NetApp Files och kapacitetspooler. Du behöver ändra volymen och poolanvändningen för nära till fullständig förbrukning. Azure NetApp Files kommer att fortsätta att tillåta åtgärder [för storleksändring av volymer och kapacitetspooler i farten.](azure-netapp-files-resize-capacity-pools-or-volumes.md)

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Så här operationaliserar du kvotändringen för volymen

Det här avsnittet innehåller råd om hur du operationaliserar ändringen av den volymhårda kvoten för en smidig övergång. Det ger också insikter för hantering av för närvarande etablerade volymer och kapacitetspooler, pågående övervakning, aviseringar och alternativ för kapacitetshantering.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>För närvarande etablerade volymer och kapacitetspooler

På grund av den stora kvotändringen bör du ändra din driftsmodell. Etablerade volymer och kapacitetspooler kräver kontinuerlig kapacitetshantering.  Eftersom det ändrade beteendet kommer att ske direkt rekommenderar Azure NetApp Files-teamet en serie korrigerande åtgärder för befintliga, tidigare etablerade volymer och kapacitetspooler, enligt beskrivningen i det här avsnittet.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Rekommendationer om korrigerande eller förebyggande åtgärder en gång  

Om kvoten för volymen är hård resulterar det i ändringar i etablerade och tillgängliga kapaciteter för tidigare etablerade volymer och pooler. Därför kan vissa kapacitetsallokeringsutmaningar uppstå. För att undvika kortvariga situationer utanför utrymme för kunder rekommenderar Azure NetApp Files rekommenderar följande korrigerande/förebyggande åtgärder en gång: 

* **Etablerade volymstorlekar:**   
    Ändra storlek på varje etablerad volym så att den har lämplig buffert baserat på ändringsfrekvens och aviseringar eller ändra storlek på turnaround-tid (till exempel 20 % baserat på vanliga arbetsbelastningsöverväganden), med högst 100 TiB (vilket är [volymstorleksgränsen).](azure-netapp-files-resource-limits.md#resource-limits) Den här nya volymstorleken, inklusive buffertkapacitet, bör baseras på följande faktorer:
    * **Etablerad** volymkapacitet, om den använda kapaciteten är mindre än den etablerade volymkvoten.
    * **Använd** volymkapacitet om den kapacitet som används är större än den etablerade volymkvoten.  
    Det tillkommer inga extra avgifter för kapacitetsökning på volymnivå om den underlagerbaserade kapacitetspoolen inte behöver växa. Som en effekt av den här ändringen  kan du se en ökning av bandbreddsgränsen för volymen (om den automatiska [QoS-kapacitetspooltypen](azure-netapp-files-understand-storage-hierarchy.md#qos_types) används).

* **Storlekar på etablerade kapacitetspooler:**   
    Om summan av volymstorlekarna blir större än storleken på värdkapacitetspoolen efter volymstorleksjusteringen måste kapacitetspoolen ökas till en storlek som är lika med eller större än summan av volymerna, med högst 500 TiB (vilket är storleksgränsen för [kapacitetspoolen).](azure-netapp-files-resource-limits.md#resource-limits) Ytterligare kapacitetspoolkapacitet debiteras som vanligt för ACR.

Du bör samarbeta med Azure NetApp Files specialister för att verifiera din miljö om du behöver hjälp med att konfigurera övervakning eller aviseringar enligt beskrivningen i avsnitten nedan.

### <a name="ongoing-capacity-management"></a>Pågående kapacitetshantering  

När du har utfört de korrigerande åtgärderna en gång bör du samla pågående processer för att övervaka och hantera kapacitet. Följande avsnitt innehåller förslag och alternativ om kapacitetsövervakning och hantering.

### <a name="monitor-capacity-utilization"></a>Övervaka kapacitetsutnyttjande

Du kan övervaka kapacitetsutnyttjandet på olika nivåer. 

#### <a name="vm-level-monitoring"></a>Övervakning på VM-nivå 

Den högsta övervakningsnivån (närmast programmet) är inifrån den virtuella programdatorn. Du kommer att se en ändring i beteendet i kapacitetsrapporteringen inifrån VM-klientoperativsystemet.

I följande två scenarier bör du överväga en Azure NetApp Files volym som konfigurerats med en Storlek på 1 TiB (kvot) på en kapacitetspool på Ultra-tjänstnivå på 4 TiB. 

##### <a name="windows"></a>Windows

Windows-klienter kan kontrollera den använda och tillgängliga kapaciteten för en volym med hjälp av egenskaperna för den nätverksmappade enheten. Du kan använda alternativet **Egenskaper**  ->  **för**  ->  **Explorer-enhet.**  

I följande exempel visas volymkapacitetsrapporteringen i Windows *före det* ändrade beteendet:

![Skärmbilder som visar exempel på lagringskapacitet för en volym innan beteendet ändras.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Du kan också använda `dir` kommandot i kommandotolken enligt nedan:

![Skärmbild som visar hur du använder ett kommando för att visa lagringskapacitet för en volym innan beteendet ändras.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

I följande exempel visas volymkapacitetsrapporteringen i Windows *efter det* ändrade beteendet:

![Skärmbilder som visar exempel på lagringskapacitet för en volym när beteendet har ändrats.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

I följande exempel visas `dir` kommandoutdata:  

![Skärmbild som visar hur du använder ett kommando för att visa lagringskapacitet för en volym efter att beteendet har ändrats.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux-klienter kan kontrollera den använda och tillgängliga kapaciteten för en volym med hjälp av [ `df` kommandot](https://linux.die.net/man/1/df). Alternativet visar storlek, använt utrymme och tillgängligt utrymme i mänskligt läsbart format med hjälp av `-h` enhetsstorlekarna M, G och T.

I följande exempel visas volymkapacitetsrapportering i Linux *före det* ändrade beteendet:  

![Skärmbild som visar hur du använder Linux för att visa lagringskapacitet för en volym innan beteendet ändras.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

I följande exempel visas volymkapacitetsrapportering i Linux *efter det* ändrade beteendet:  

![Skärmbild som visar hur du använder Linux för att visa lagringskapacitet för en volym efter att beteendet har ändrats.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Konfigurera aviseringar med ANFCapacityManager

Du kan använda verktyget ANFCapacityManager Logic Apps som stöds av communityn för att övervaka Azure NetApp Files kapacitet och få skräddarsydda aviseringar. Verktyget ANFCapacityManager finns på [GitHub-sidan ANFCapacityManager.](https://github.com/ANFTechTeam/ANFCapacityManager)

ANFCapacityManager är en Azure-logikapp som hanterar kapacitetsbaserade aviseringsregler. Det ökar automatiskt volymstorlekarna för att Azure NetApp Files dina volymer börjar få slut på utrymme. Det är enkelt att distribuera och innehåller Aviseringshantering funktioner:

* När en Azure NetApp Files en kapacitetspool eller volym skapas skapar ANFCapacityManager en måttaviseringsregel baserat på det angivna tröskelvärdet för procent som förbrukas.
* När storleken Azure NetApp Files en kapacitetspool eller volym ändrar ANFCapacityManager måttaviseringsregeln baserat på tröskelvärdet för den angivna procentandelen kapacitet som förbrukas. Om aviseringsregeln inte finns skapas den.
* När en Azure NetApp Files en kapacitetspool eller volym tas bort tas motsvarande måttaviseringsregel bort.

Du kan konfigurera följande viktiga aviseringsinställningar:  

* **Kapacitetspool % fullt tröskelvärde –** Den här inställningen avgör det förbrukade tröskelvärdet som utlöser en avisering för kapacitetspooler. Värdet 90 skulle leda till att en avisering utlöses när kapacitetspoolen når 90 % förbrukad.
* **Volym % fullständigt tröskelvärde –** Den här inställningen anger det förbrukade tröskelvärdet som utlöser en avisering för volymer. Värdet 80 skulle göra att en avisering utlöses när volymen når 80 % förbrukad.
* **Befintlig åtgärdsgrupp för kapacitetsmeddelanden** – Den här inställningen är den åtgärdsgrupp som utlöses för kapacitetsbaserade aviseringar. Den här inställningen bör skapas i förväg av dig. Åtgärdsgruppen kan skicka e-post, SMS eller andra format.

Följande bild visar aviseringskonfigurationen:  

![Bild som visar aviseringskonfiguration med hjälp av ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

När du har installerat ANFCapacityManager kan du förvänta dig följande beteende: När en Azure NetApp Files-kapacitetspool eller volym skapas, ändras eller tas bort, skapar, ändrar eller tar logikappen automatiskt bort en kapacitetsbaserad måttaviseringsregel med namnet `ANF_Pool_poolname` eller `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Hantera kapacitet

Förutom övervakning och aviseringar bör du även använda en metod för hantering av programkapacitet för att hantera Azure NetApp Files (ökad) kapacitetsförbrukning. När en Azure NetApp Files volym eller kapacitetspool fylls kan extra kapacitet tillhandahållas i farten [utan avbrott i programmet.](azure-netapp-files-resize-capacity-pools-or-volumes.md) I det här avsnittet beskrivs olika manuella och automatiserade sätt att öka volym- och kapacitetspoolens etablerade utrymme efter behov.
 
#### <a name="manual"></a>Manuell 

Du kan använda portalen eller CLI för att manuellt öka volym- eller kapacitetspoolstorlekarna. 

##### <a name="portal"></a>Portalen 

Du kan [ändra storleken på en volym efter](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) behov. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.

1. På bladet Hantera NetApp-konto klickar du på **Volymer.**  
2. Högerklicka på namnet på den volym som du vill ändra storlek på eller klicka på ikonen i slutet av volymens rad `…` för att visa snabbmenyn. 
3. Använd alternativen på snabbmenyn för att ändra storlek på eller ta bort volymen.   

   ![Skärmbild som visar snabbmenyalternativ för en volym.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Skärmbild som visar fönstret Uppdatera volymkvot.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

I vissa fall har värdkapacitetspoolen inte tillräckligt med kapacitet för att ändra storlek på volymerna. Du kan dock ändra [storleken på kapacitetspoolen](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) i steg om 1 TiB eller mindre. Kapacitetspoolens storlek får inte vara mindre än 4 TiB. *När du ändrar storlek på kapacitetspoolen ändras Azure NetApp Files kapacitet.*

1. På bladet Hantera NetApp-konto klickar du på den kapacitetspool som du vill ändra storlek på.
2. Högerklicka på namnet på kapacitetspoolen eller klicka på ikonen i slutet av `…` kapacitetspoolens rad för att visa snabbmenyn.
3. Använd alternativen på snabbmenyn för att ändra storlek på eller ta bort kapacitetspoolen.    

   ![Skärmbild som visar snabbmenyalternativ för en kapacitetspool.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Skärmbild som visar fönstret Ändra storlek på pool.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI eller PowerShell

Du kan använda [cli Azure NetApp Files verktygen,](azure-netapp-files-sdk-cli.md#cli-tools)inklusive Azure CLI och Azure PowerShell, för att manuellt ändra volym- eller kapacitetspoolens storlek.  Följande två kommandon kan användas för att hantera Azure NetApp Files och poolresurser:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Om du Azure NetApp Files hantera resurser med Azure CLI kan du öppna Azure Portal och välja länken Azure **Cloud Shell** längst upp på menyraden: 

[![Skärmbild som visar hur du kommer åt Cloud Shell länk. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Den här åtgärden öppnar Azure Cloud Shell:

[![Skärmbild som visar Cloud Shell fönster. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

I följande exempel används kommandon för [att visa](/cli/azure/netappfiles/volume#az_netappfiles_volume_show) [och uppdatera](/cli/azure/netappfiles/volume#az_netappfiles_volume_update) storleken på en volym:
 
[![Skärmbild som visar hur du använder PowerShell för att visa volymstorlek. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Skärmbild som visar hur du använder PowerShell för att uppdatera volymstorleken. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

I följande exempel används kommandon för att [visa](/cli/azure/netappfiles/pool#az_netappfiles_pool_show) och [uppdatera](/cli/azure/netappfiles/pool#az_netappfiles_pool_update) storleken på en kapacitetspool:

[![Skärmbild som visar hur du använder PowerShell för att visa storleken på kapacitetspoolen. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Skärmbild som visar hur du använder PowerShell för att uppdatera storleken på kapacitetspoolen. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatiserad  

Du kan skapa en automatiserad process för att hantera det ändrade beteendet.

##### <a name="rest-api"></a>REST-API   

Den REST API för Azure NetApp Files-tjänsten definierar HTTP-åtgärder mot resurser som NetApp-kontot, kapacitetspoolen, volymerna och ögonblicksbilderna. Specifikationen REST API för Azure NetApp Files publiceras via [Azure NetApp Files Resource Manager GitHub-sidan](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]. Du hittar [exempelkod för användning med REST-API:er](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) i GitHub.

Se [Utveckla för Azure NetApp Files med REST API](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>REST API med hjälp av PowerShell  

Den REST API för Azure NetApp Files-tjänsten definierar HTTP-åtgärder mot resurser som NetApp-kontot, kapacitetspoolen, volymerna och ögonblicksbilderna. Specifikationen [REST API för Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) publiceras via GitHub.

Se [Utveckla för Azure NetApp Files med REST API powershell.](develop-rest-api-powershell.md)

##### <a name="capacity-management-using-anfcapacitymanager"></a>Kapacitetshantering med ANFCapacityManager

ANFCapacityManager är en Azure-logikapp som hanterar kapacitetsbaserade aviseringsregler. Det ökar automatiskt volymstorlekarna för att Azure NetApp Files få slut på utrymme. Förutom att skicka aviseringar kan det aktivera automatisk ökning av volym- och kapacitetspoolstorlekar för att förhindra att Azure NetApp Files får slut på utrymme: 

* Om en Azure NetApp Files volym når det angivna tröskelvärdet för procent som förbrukats, ökar volymkvoten (storleken) med den procentandel som anges mellan 10 och 100.  
* Om en ökning av volymstorleken överskrider kapaciteten för den innehållande kapacitetspoolen ökas även storleken på kapacitetspoolen för att hantera den nya volymstorleken.

Du kan konfigurera följande nyckelinställning för kapacitetshantering:  

* **Öka automatiskt i procent –** Procent av den befintliga volymens storlek för att automatiskt öka en volym om den når det angivna fullständiga **tröskelvärdet för %**. Värdet 0 (noll) inaktiverar funktionen AutoGrow. Ett värde mellan 10 och 100 rekommenderas.

    ![Skärmbild som visar fönstret Set Volume Auto Growth Percent (Ange automatisk ökning i procent för volym).](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Vanliga frågor 

I det här avsnittet får du svar på några frågor om den hårdkvotsändringen för volymen. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Räknas ögonblicksbildutrymmet mot den användbara eller etablerade kapaciteten för en volym?

Ja, den förbrukade ögonblicksbildkapaciteten räknas mot det etablerade utrymmet i volymen. Om volymen körs fullt bör du överväga två reparationsalternativ:

* Ändra storlek på volymen enligt beskrivningen i den här artikeln.
* Ta bort äldre ögonblicksbilder för att frigöra utrymme på värdvolymen.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Innebär den här ändringen att beteendet för automatisk volymtillväxt försvinner från Azure NetApp Files?

En vanlig missuppfattning är att Azure NetApp Files *skulle växa* automatiskt när de fylls upp. Volymer etablerades tunt med en storlek på 100 TiB, oavsett den faktiska  kvoten, medan den underlagerbaserade kapacitetspoolen automatiskt skulle växa med 1 TiB-steg. Den här ändringen adresserar volymstorleken (synlig och *användbar)* till den inställda kvoten och *kapacitetspoolerna* utökas inte längre automatiskt. Den här ändringen resulterar i normalt önskat korrekt utrymme på klientsidan och kapacitetsrapportering. Den undviker "skenande" kapacitetsförbrukning.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Har den här ändringen någon effekt på volymer som replikeras med replikering mellan regioner (förhandsversion)? 

Den hårda volymkvoten tillämpas inte på replikeringens målvolymer.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Har den här ändringen någon effekt på mått som för närvarande är tillgängliga i Azure Monitor?

Portalmått och statistik Azure Monitor korrekt återspeglar den nya allokerings- och användningsmodellen.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Har den här ändringen någon effekt på resursgränserna för Azure NetApp Files?

Det finns ingen ändring i resursgränserna för Azure NetApp Files utanför kvotändringarna som beskrivs i den här artikeln.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Finns det ett exempel på ett ANFCapacityManager-arbetsflöde?  

Ja. Se [GitHub-sidan Volume AutoGrow Workflow Example](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Stöds ANFCapacityManager Microsoft?  

[Logikappen ANFCapacityManager tillhandahålls som den är och stöds inte av NetApp eller Microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). Du uppmanas att ändra så att det passar din specifika miljö eller dina krav. Du bör testa funktionen innan du distribuerar den till affärskritiska miljöer eller produktionsmiljöer.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Hur kan jag rapportera en bugg eller skicka en funktionsbegäran för ANFCapacityManger?
Du kan skicka in buggar och funktionsbegäranden genom **att klicka på Nytt** problem på [GitHub-sidan ANFCapacityManager.](https://github.com/ANFTechTeam/ANFCapacityManager/issues)

## <a name="next-steps"></a>Nästa steg
* [Ändra storlek på en kapacitetspool eller en volym](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Mått för Azure NetApp Files](azure-netapp-files-metrics.md)
