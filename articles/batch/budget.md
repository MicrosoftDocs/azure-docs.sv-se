---
title: Kostnadsanalys och budget
description: Lär dig hur du kan få en kostnads analys, ange en budget och minska kostnaderna för underliggande beräknings resurser och program varu licenser som används för att köra batch-arbetsbelastningar.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679328"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Kostnads analys och budgetar för Azure Batch

Det kostar inget att använda Azure Batch sig själv, men det kan finnas avgifter för underliggande beräknings resurser och program varu licenser som används för att köra batch-arbetsbelastningar. Kostnader kan uppstå från virtuella datorer i en pool, data överföring från den virtuella datorn eller indata eller utdata som lagras i molnet. Det här avsnittet hjälper dig att förstå var kostnader kommer från, hur du ställer in en budget för en batch-pool eller ett konto och hur du kan minska kostnaderna för batch-arbetsbelastningar.

## <a name="batch-resources"></a>Batch-resurser

Virtuella datorer är den viktigaste resursen som används för batchbearbetning. Kostnaden för att använda virtuella datorer för batch beräknas utifrån typ, kvantitet och användnings tid. Fakturerings alternativ för virtuella datorer omfattar [betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning eller [reservation](../cost-management-billing/reservations/save-compute-costs-reservations.md) (betala i förväg). Båda betalnings alternativen har olika fördelar beroende på din beräknings belastning och kommer att påverka din faktura på ett annat sätt.

När program distribueras till batch-noder (VM: ar) med hjälp av [programpaket](batch-application-packages.md)debiteras du för de Azure Storage-resurser som dina program paket använder. Du debiteras också för lagring av indata-eller utdatafiler, till exempel resursfiler och andra loggdata. I allmänhet är kostnaden för lagrings data som är kopplade till batch mycket lägre än kostnaden för beräknings resurser. Varje virtuell dator i en pool som skapats med [konfigurationen av virtuell dator](nodes-and-pools.md#virtual-machine-configuration) har en tillhör ande OS-disk som använder Azure-hanterade diskar. Azure-hanterade diskar har ytterligare kostnader och andra disk prestanda nivåer har också olika kostnader.

Batch-pooler använder nätverks resurser. I synnerhet används standard belastnings utjämning för **VirtualMachineConfiguration** -pooler, vilket kräver statiska IP-adresser. De belastnings utjämningar som används av batch är synliga för **användar prenumerations** konton, men är inte synliga för **batch service-** konton. Standard belastnings utjämning debiteras för alla data som skickas till och från virtuella batch-pooler. Välj batch-API: er som hämtar data från pool-noder (till exempel hämta aktivitet/Node-fil), uppgiftens programpaket, resurs/utdatafiler och behållar avbildningar debiteras.

### <a name="additional-services"></a>Ytterligare tjänster

Tjänster som inte omfattar virtuella datorer och lagrings enheter kan vara till för kostnaden för ditt batch-konto.

Andra tjänster som används ofta med batch kan innehålla:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Virtuella datorer med grafik program

Beroende på vilka tjänster du använder med din batch-lösning kan du debiteras ytterligare avgifter. Se [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att fastställa kostnaden för varje ytterligare tjänst.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Kostnads analys och budget för en pool

I Azure Portal kan du skapa budgetar och utgifts varningar för dina batch-pooler eller batch-konton. Budgetar och aviseringar är användbara för att meddela intressenter om eventuella risker för överförbrukning, även om det är möjligt att det uppstår en fördröjning i utgifts aviseringar och för att något ska överskrida en budget.

I det här exemplet ska vi visa en kostnads analys av en enskild batch-pool.

1. I Azure Portal skriver du in eller väljer **Cost Management + fakturering** .
1. Välj din prenumeration i avsnittet **fakturerings omfång** .
1. Under **Cost Management** väljer du **Kostnadsanalys**.
1. Välj **Lägg till filter**. I den första List rutan väljer du **resurs**
1. I den andra List rutan väljer du batch-poolen. När du väljer poolen visas kostnads analysen för poolen, ungefär som i exemplet som visas här.
    ![Skärm bild som visar kostnads analys för en pool i Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

Den resulterande kostnads analysen visar kostnaden för poolen och de resurser som bidrar till den här kostnaden. I det här exemplet är de virtuella datorer som används i poolen den mest dyra resursen.

Om du vill skapa en budget för poolen väljer du **Budget: ingen** och väljer sedan **skapa ny budget >**. Använd nu fönstret för att [Konfigurera en budget](../cost-management-billing/costs/tutorial-acm-create-budgets.md) som är specifik för din pool.

> [!NOTE]
> Azure Batch bygger på Azure Cloud Services-och Azure Virtual Machines-teknik. När du väljer **Cloud Services konfiguration** debiteras du baserat på Cloud Services prissättnings struktur. När du väljer **konfiguration för virtuell dator** debiteras du baserat på Virtual Machines pris strukturen. Exemplet på den här sidan använder konfigurationen för den **virtuella datorn**, vilket rekommenderas för de flesta batch-pooler.

## <a name="minimize-cost"></a>Minimera kostnader

Det kan vara dyrt att använda flera virtuella datorer och Azure-tjänster för längre tids perioder. Överväg att använda dessa strategier för att maximera effektiviteten i dina arbets belastningar och minska kostnaderna.

### <a name="low-priority-virtual-machines"></a>Virtuella datorer med låg prioritet

[Virtuella datorer med låg prioritet](batch-low-pri-vms.md) minskar kostnaden för batch-arbetsbelastningar genom att dra nytta av överskotts data behandling i Azure. När du anger virtuella datorer med låg prioritet i dina pooler använder batch detta överskott för att köra arbets belastningen. Det kan vara avsevärda besparingar när du använder virtuella datorer med låg prioritet i stället för dedikerade virtuella datorer.

### <a name="virtual-machine-os-disk-type"></a>Typ av operativ system disk för virtuell dator

Azure erbjuder flera [typer av virtuella dator diskar](../virtual-machines/disks-types.md). De flesta VM-serier har storlekar som stöder både Premium-och standard-lagring. När ens VM-storlek har valts för en pool konfigurerar batch att Premium SSD OS-diskar. När den virtuella datorns icke-s-storlek väljs används den billigare, standard hård disk typen. Till exempel används Premium SSD OS-diskar för `Standard_D2s_v3` och standard diskar för HDD-operativsystem används för `Standard_D2_v3` .

Premium SSD OS-diskar är dyrare, men har högre prestanda. Virtuella datorer med Premium diskar kan starta något snabbare än virtuella datorer med standard diskar för HDD-operativsystem. Med batch används ofta operativ system disken mycket, eftersom program-och aktivitets filerna finns på den virtuella datorns temporära SSD-disk. Därför kan du ofta välja den virtuella dator storleken "non-s" för att undvika att betala den ökade kostnaden för Premium SSD som är etablerad när storleken på en virtuell dator har angetts.

### <a name="reserved-virtual-machine-instances"></a>Reserverade instanser av virtuella datorer

Om du avser att använda batch under en lång tids period kan du minska kostnaderna för virtuella datorer genom att använda [Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) för dina arbets belastningar. En reservations taxa är betydligt lägre än en taxa enligt principen betala per användning. Virtuella dator instanser som används utan reservationer debiteras enligt priset för betala per användning. När du köper en reservation tillämpas reservations rabatten.

### <a name="automatic-scaling"></a>Automatisk skalning

Med [automatisk skalning](batch-automatic-scaling.md) skalas antalet virtuella datorer dynamiskt i batch-poolen baserat på kraven för det aktuella jobbet. Genom att skala poolen baserat på ett jobbs livs längd garanterar automatisk skalning att virtuella datorer skalas upp och används endast när det finns ett jobb att utföra. När jobbet har slutförts, eller när det inte finns några jobb, skalas de virtuella datorerna automatiskt ned för att spara beräknings resurser. Med skalning kan du sänka den totala kostnaden för din batch-lösning genom att bara använda de resurser du behöver.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-API: er och verktyg](batch-apis-tools.md) som är tillgängliga för att skapa och övervaka batch-lösningar.  
- Lär dig mer om att [använda virtuella datorer med låg prioritet med batch](batch-low-pri-vms.md).
