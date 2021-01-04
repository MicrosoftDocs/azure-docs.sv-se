---
title: Hantera tillgängligheten för virtuella datorer
description: Lär dig hur du använder flera virtuella datorer för att säkerställa hög tillgänglighet för dina program i Azure
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cynthn
ms.openlocfilehash: 0ae4a311bc4f5084ff930b97d68482d64671a782
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695748"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Hantera tillgängligheten för virtuella Linux-datorer

Lär dig hur du konfigurerar och hanterar flera virtuella datorer för att säkerställa hög tillgänglighet för ditt Linux-program i Azure. 


## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Förstå omstarter av virtuella datorer – underhåll och driftavbrott
Det finns tre scenarier som kan leda till att den virtuella datorn i Azure påverkas: oplanerat maskin varu underhåll, oväntad stillestånds tid och planerat underhåll.

* **Oplanerat maskinvaruunderhåll** inträffar när Azure-plattformen förutser att maskinvaran eller plattformskomponenter som är associerade med en fysisk dator är på väg att få problem. När plattformen förutser ett problem skickar den en händelse om oplanerat maskinvaruunderhåll för att minska påverkan på virtuella datorer som finns på maskinvaran i fråga. Azure använder [Direktmigrering](./maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2flinux%2ftoc.json) teknik för att migrera Virtual Machines från maskin varu fel till en felfri fysisk dator. Direktmigrering är en åtgärd för att skydda virtuella datorer, som endast pausar den virtuella datorn en kort stund. Minne, öppna filer och nätverksanslutningar bevaras, men prestanda kan försämras före och/eller efter händelsen. I de fall då det inte går att använda direktmigrering uppstår ett oväntat driftavbrott på den virtuella datorn (se nedan).


* **En oväntad stillestånds** tid är när maskin varan eller den fysiska infrastrukturen för den virtuella datorn Miss lyckas oväntat. Detta kan omfatta lokala nätverks haverier, lokala diskfel eller andra rack nivå problem. När den identifieras migrerar Azure-plattformen automatiskt (läka) den virtuella datorn till en felfri fysisk dator i samma data Center. Återställningsprocessen medför driftavbrott (omstart) på virtuella datorer och i vissa fall förlust av den temporära enheten. Anslutna operativsystems- och datadiskar bevaras alltid.

  Virtuella datorer kan också uppleva stillestånds tid i den osannolika händelsen av ett avbrott eller en katastrof som påverkar ett helt data Center, eller till och med en hel region. I dessa scenarier tillhandahåller Azure skydds alternativ, inklusive  [tillgänglighets zoner](../availability-zones/az-overview.md) och [kopplade regioner](regions.md#region-pairs).

* **Planerade underhålls händelser** är periodiska uppdateringar som görs av Microsoft till den underliggande Azure-plattformen för att förbättra den övergripande tillförlitligheten, prestandan och säkerheten för den plattforms infrastruktur som dina virtuella datorer körs på. De flesta av dessa uppdateringar utförs utan någon inverkan på Virtual Machines eller Cloud Services (se [underhåll som inte kräver omstart](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Azure-plattformen försöker alltid att utföra underhåll utan att påverka virtuella datorer, men i sällsynta fall kräver dessa uppdateringar en omstart av den virtuella datorn för att de nödvändiga uppdateringarna av den underliggande infrastrukturen ska kunna installeras. I detta fall kan du utföra planerat underhåll i Azure med underhålls- och omdistributionsåtgärden genom att initiera underhållet för de virtuella datorerna vid en lämplig tidpunkt. Mer information finns i [Planned Maintenance for Virtual Machines](maintenance-and-updates.md) (Planerat underhåll för virtuella datorer).


För att undvika påverkan av den här typen av avbrott rekommenderar vi att du gör följande för att säkerställa hög tillgänglighet för dina virtuella datorer:

* Använd Tillgänglighetszoner för att skydda från data Center problem
* Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans
* Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning
* Använd schemalagda händelser för att proaktivt svara på händelser som påverkar virtuella datorer
* Konfigurera varje programnivå i separata tillgänglighetsuppsättningar
* Kombinera en belastningsutjämnare med tillgänglighets zoner eller uppsättningar

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Använda tillgänglighets zoner för att skydda från data center nivå problem

[Tillgänglighets zoner](../availability-zones/az-overview.md) utökar kontroll nivån som du måste ha för att upprätthålla tillgängligheten för program och data på dina virtuella datorer. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att garantera återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zoner – redundanta tjänster replikerar dina program och data över Tillgänglighetszoner för att skydda från enskilda platser.

En tillgänglighets zon i en Azure-region är en kombination av en **fel domän** och en **uppdaterings domän**. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer. Genom att skapa lösningar för att använda replikerade virtuella datorer i zoner kan du skydda dina program och data från förlust av ett Data Center. Om en zon komprometteras, är replikerade appar och data omedelbart tillgängliga i en annan zon.

![Tillgänglighetszoner](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

Lär dig mer om att distribuera en virtuell [Windows](./windows/create-powershell-availability-zone.md) -eller [Linux](./linux/create-cli-availability-zone.md) -dator i en tillgänglighets zon.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans
Tillgänglighets uppsättningar är en annan data Center konfiguration som ger VM-redundans och tillgänglighet. Den här konfigurationen i ett Data Center garanterar att minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse och uppfyller 99,95% Azure SLA. Mer information finns i [Serviceavtal för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> En virtuell dator med en enda instans i en tillgänglighets uppsättning bör använda Premium SSD eller Ultra disk för alla operativ system diskar och data diskar för att kvalificera sig för service avtalet för anslutning till virtuell dator med minst 99,9%. 
> 
> En virtuell dator med en enda instans med en Standard SSD har ett service avtal på minst 99,5%, medan en enskild instans av en virtuell dator med en Standard HDD har ett service avtal som är minst 95%.  Se [SLA för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/)

Varje virtuell dator i din tillgänglighets uppsättning tilldelas en **uppdaterings domän** och en **feldomän** av den underliggande Azure-plattformen. För en viss tillgänglighetsuppsättning tilldelas som standard fem uppdateringsdomäner, som inte kan konfigureras av användare, (Resource Manager-distributioner kan utökas till 20 uppdateringsdomäner) för att ange grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas om samtidigt. Om fler än fem virtuella datorer har konfigurerats i en enskild tillgänglighetsuppsättning placeras den sjätte virtuella datorn i samma uppdateringsdomän som den första virtuella datorn. Den sjunde placeras i samma uppdateringsdomän som den andra virtuella datorn och så vidare. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om. En omstartad uppdateringsdomän får 30 minuter på sig för återställning innan underhållet initieras i en annan uppdateringsdomän.

Feldomäner definierar den grupp av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard är de virtuella datorer som konfigureras i din tillgänglighetsuppsättning indelade i tre feldomäner för Resource Manager-distributioner (två feldomäner för klassisk distribution). Att placera de virtuella datorerna i en tillgänglighetsuppsättning skyddar inte ditt program mot operativsystemfel eller programspecifika fel, men det begränsar påverkan av potentiella fel på fysisk maskinvara, problem med nätverket och strömavbrott.

<!--Image reference-->
   ![Skiss på en konfiguration med uppdateringsdomäner och feldomäner](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning
Om du för närvarande använder virtuella datorer med ohanterade diskar rekommenderar vi starkt att du konverterar från ohanterade diskar till hanterade diskar för [Linux](./linux/convert-unmanaged-to-managed-disks.md) och [Windows](./windows/convert-unmanaged-to-managed-disks.md).

[Hanterade diskar](./managed-disks-overview.md) ger bättre tillförlitlighet för tillgänglighetsuppsättningar genom att säkerställa att diskarna på virtuella datorer i en tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika felkritiska systemdelar. Detta görs genom att automatiskt placera diskarna i olika lagrings fel domäner (lagrings kluster) och justera dem med den virtuella dator fel domänen. Om en lagrings fel domän Miss lyckas på grund av maskin-eller program varu fel, Miss lyckas bara den virtuella dator instansen med diskar på lagrings fel domänen.
![Hanterade diskar fd](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Antalet feldomäner för hanterade tillgänglighetsuppsättningar varierar beroende på region – antingen två eller tre per region. Du kan se fel domänen för varje region genom att köra följande skript.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Under vissa omständigheter kan två virtuella datorer i samma tillgänglighets uppsättning dela en fel domän. Du kan bekräfta en delad fel domän genom att gå till din tillgänglighets uppsättning och kontrol lera kolumnen **fel domän** . En delad feldomän kan orsakas av att följande sekvens slutförs när du distribuerade de virtuella datorerna:
> 1. Distribuera den första virtuella datorn.
> 1. Stoppa/frigör den första virtuella datorn.
> 1. Distribuera den andra virtuella datorn.
>
> Under dessa omständigheter kan operativ system disken för den andra virtuella datorn skapas på samma fel domän som den första virtuella datorn, så de två virtuella datorerna kommer att finnas på samma feldomän. För att undvika det här problemet rekommenderar vi att du inte stoppar/frigör virtuella datorer mellan distributioner.

Om du planerar att använda virtuella datorer med ohanterade diskar följer du rekommendationerna nedan för lagrings konton där virtuella hård diskar (VHD) för virtuella datorer lagras som [Page blobbar](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Förvara alla diskar (operativsystem och data) som är associerade med en virtuell dator i samma lagringskonto**
2. **Granska [gränserna](../storage/blobs/scalability-targets-premium-page-blobs.md) för antalet ohanterade diskar i ett Azure Storage konto** innan du lägger till fler virtuella hård diskar till ett lagrings konto
3. **Använd ett separat lagrings konto för varje virtuell dator i en tillgänglighets uppsättning.** Dela inte Storage-konton med flera virtuella datorer i samma tillgänglighetsuppsättning. Det är acceptabelt för virtuella datorer över olika tillgänglighets uppsättningar för att dela lagrings konton om de rekommenderade säkerhets metoderna följer ![ ohanterade diskar fd](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Använd schemalagda händelser för att proaktivt svara på händelser som påverkar virtuella datorer

När du prenumererar på [schemalagda händelser](./linux/scheduled-events.md), meddelas din virtuella dator om kommande underhålls händelser som kan påverka den virtuella datorn. När schemalagda händelser aktive ras får den virtuella datorn en minimal tid innan underhålls aktiviteten utförs. Till exempel placeras värdar för OS-uppdateringar som kan påverka den virtuella datorn som händelser som anger påverkan, samt en tidpunkt då underhållet utförs om ingen åtgärd vidtas. Schema händelser köas också när Azure upptäcker ett överhäng ande maskin varu haveri som kan påverka den virtuella datorn, vilket gör att du kan bestämma när du vill utföra återställningen. Kunder kan använda händelsen för att utföra uppgifter före underhållet, till exempel spara status, redundansväxla till den sekundära och så vidare. När du har slutfört din logik för att hantera underhålls händelsen smidigt kan du godkänna den väntande schemalagda händelsen så att plattformen kan fortsätta med underhållet.


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Kombinera en belastningsutjämnare med tillgänglighets zoner eller uppsättningar
Kombinera [Azure Load Balancer](../load-balancer/load-balancer-overview.md) med en tillgänglighets zon eller Ställ in för att få de flesta program återhämtning. Azure Load Balancer distribuerar trafiken mellan flera virtuella datorer. Azure Load Balancer ingår i standardnivån för Virtual Machines. Azure Load Balancer ingår inte i alla nivåer för Virtual Machines. Mer information om belastnings utjämning för virtuella datorer finns i **belastnings utjämning för virtuella datorer** för [Linux](linux/tutorial-load-balancer.md) eller [Windows](windows/tutorial-load-balancer.md).

Om lastbalanseraren inte konfigureras för att jämna ut trafiken mellan flera virtuella datorer kommer varje planerat underhåll att påverka endast den virtuella dator som hanterar trafik och orsaka ett avbrott på programnivån. Om du placerar flera virtuella datorer av samma nivå under samma lastbalanserare och tillgänglighetsuppsättning kommer trafiken att hanteras kontinuerligt av minst en instans.

En själv studie kurs om belastnings utjämning över tillgänglighets zoner finns i [Självstudier: belastningsutjämna virtuella datorer i tillgänglighets zoner med en standard Load Balancer med hjälp av Azure Portal](../load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal.md).


## <a name="next-steps"></a>Nästa steg
Mer information om belastnings utjämning för virtuella datorer finns i [belastnings utjämning för virtuella datorer](../load-balancer/load-balancer-overview.md).
