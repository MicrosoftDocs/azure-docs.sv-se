---
title: Hämta kostnads analys och Ställ in budgetar för Azure Batch
description: Lär dig hur du kan få en kostnads analys, ange en budget och minska kostnaderna för underliggande beräknings resurser och program varu licenser som används för att köra batch-arbetsbelastningar.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091335"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Hämta kostnads analys och Ställ in budgetar för Azure Batch

Det här avsnittet hjälper dig att förstå kostnader som kan vara kopplade till Azure Batch, hur du ställer in en budget för en batch-pool eller ett konto och hur du kan minska kostnaderna för batch-arbetsbelastningar.

## <a name="understand-costs-associated-with-batch-resources"></a>Förstå kostnader som är kopplade till batch-resurser

Det kostar inget att använda Azure Batch sig själv, men det kan finnas avgifter för underliggande beräknings resurser och program varu licenser som används för att köra batch-arbetsbelastningar. Kostnader kan uppstå från virtuella datorer i en pool, data överföring från den virtuella datorn eller indata eller utdata som lagras i molnet.

### <a name="virtual-machines"></a>Virtuella datorer

Virtuella datorer är den viktigaste resursen som används för batchbearbetning. Kostnaden för att använda virtuella datorer för batch beräknas utifrån typ, kvantitet och användnings tid. Fakturerings alternativ för virtuella datorer omfattar [betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning eller [reservation](../cost-management-billing/reservations/save-compute-costs-reservations.md) (betala i förväg). Båda betalnings alternativen har olika fördelar beroende på din beräknings belastning och kommer att påverka din faktura på ett annat sätt.

Varje virtuell dator i en pool som skapats med [konfigurationen av virtuell dator](nodes-and-pools.md#virtual-machine-configuration) har en tillhör ande OS-disk som använder Azure-hanterade diskar. Azure-hanterade diskar har ytterligare kostnader och andra disk prestanda nivåer har också olika kostnader.

### <a name="storage"></a>Storage

När program distribueras till batch-noder (VM: ar) med hjälp av [programpaket](batch-application-packages.md)debiteras du för de Azure Storage-resurser som dina program paket använder. Du debiteras också för lagring av indata-eller utdatafiler, till exempel resursfiler och andra loggdata.

I allmänhet är kostnaden för lagrings data som är kopplade till batch mycket lägre än kostnaden för beräknings resurser.

### <a name="networking-resources"></a>Nätverks resurser

Batch-pooler använder nätverks resurser, varav vissa har tillhör ande kostnader. För konfigurations pooler för virtuella datorer används i synnerhet standard belastnings utjämning, vilket kräver statiska IP-adresser. De belastnings utjämningar som används av batch är synliga för [konton](accounts.md#batch-accounts) som kon figurer ATS i användar prenumerations läge, men inte i batch service-läge.

Standard belastnings utjämning debiteras för alla data som skickas till och från virtuella batch-pooler. Välj batch-API: er som hämtar data från pool-noder (till exempel hämta aktivitet/Node-fil), uppgiftens programpaket, resurs-/utdatafiler och behållar avbildningar debiteras också.

### <a name="additional-services"></a>Ytterligare tjänster

Beroende på vilka tjänster du använder med din batch-lösning kan du debiteras ytterligare avgifter. Se [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att fastställa kostnaden för varje ytterligare tjänst. Tjänster som ofta används med batch som kan ha associerade kostnader är:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Virtuella datorer med grafik program

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Visa kostnads analys och skapa en budget för en pool

I Azure Portal kan du skapa budgetar och utgifts varningar för dina batch-pooler eller batch-konton. Budgetar och aviseringar är användbara för att meddela intressenter om eventuella risker för överförbrukning, även om det är möjligt att det uppstår en fördröjning i utgifts aviseringar och för att något ska överskrida en budget.

> [!NOTE]
> Poolen i det här exemplet använder **konfiguration av virtuell dator**, vilket rekommenderas för de flesta pooler och har avgifter baserat på Virtual Machines pris struktur. Pooler som använder **Cloud Services konfiguration** debiteras baserat på Cloud Services prissättnings struktur.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Visa kostnads analys för en batch-pool

1. I Azure Portal skriver du in eller väljer **Cost Management + fakturering** .
1. Välj din prenumeration i avsnittet **fakturerings omfång** .
1. Under **Cost Management** väljer du **Kostnadsanalys**.
1. Välj **Lägg till filter**. I den första List rutan väljer du **resurs**
1. I den andra List rutan väljer du batch-poolen. När du väljer poolen visas kostnads analysen för poolen, ungefär som i exemplet som visas här.
    ![Skärm bild som visar kostnads analys för en pool i Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

Den resulterande kostnads analysen visar kostnaden för poolen och de resurser som bidrar till den här kostnaden. I det här exemplet är de virtuella datorer som används i poolen den mest dyra resursen.

### <a name="create-a-budget-for-a-batch-pool"></a>Skapa en budget för en batch-pool

1. Välj **Budget: ingen** på sidan **kostnads analys** .
1. Välj **Skapa ny budget >**.
1. Använd det resulterande fönstret för att konfigurera en budget som är specifik för din pool. Mer information finns i [Självstudier: skapa och hantera Azure-budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimera kostnader som är kopplade till Azure Batch

Beroende på ditt scenario kanske du vill minska kostnaderna så mycket som möjligt. Överväg att använda en eller flera av dessa strategier för att maximera effektiviteten hos dina arbets belastningar och minska eventuella kostnader.

### <a name="use-low-priority-virtual-machines"></a>Använd virtuella datorer med låg prioritet

[Virtuella datorer med låg prioritet](batch-low-pri-vms.md) minskar kostnaden för batch-arbetsbelastningar genom att dra nytta av överskotts data behandling i Azure. När du anger virtuella datorer med låg prioritet i dina pooler använder batch detta överskott för att köra arbets belastningen. Det kan vara avsevärda besparingar när du använder virtuella datorer med låg prioritet i stället för dedikerade virtuella datorer.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Välj en standard typ av operativ system disk för virtuell dator

Azure erbjuder flera [typer av virtuella dator diskar](../virtual-machines/disks-types.md). De flesta VM-serier har storlekar som stöder både Premium-och standard-lagring. När ens VM-storlek har valts för en pool konfigurerar batch att Premium SSD OS-diskar. När den virtuella datorns icke-s-storlek väljs används den billigare, standard hård disk typen. Till exempel används Premium SSD OS-diskar för `Standard_D2s_v3` och standard diskar för HDD-operativsystem används för `Standard_D2_v3` .

Premium SSD OS-diskar är dyrare, men har högre prestanda. Virtuella datorer med Premium diskar kan starta något snabbare än virtuella datorer med standard diskar för HDD-operativsystem. Med batch används ofta operativ system disken mycket, eftersom program-och aktivitets filerna finns på den virtuella datorns temporära SSD-disk. Därför kan du ofta välja den virtuella dator storleken "non-s" för att undvika att betala den ökade kostnaden för Premium SSD som är etablerad när storleken på en virtuell dator har angetts.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Köp reservationer för virtuella dator instanser

Om du avser att använda batch under en lång tids period kan du minska kostnaderna för virtuella datorer genom att använda [Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) för dina arbets belastningar. En reservations taxa är betydligt lägre än en taxa enligt principen betala per användning. Virtuella dator instanser som används utan reservationer debiteras enligt priset för betala per användning. När du köper en reservation tillämpas reservations rabatten.

### <a name="use-automatic-scaling"></a>Använd automatisk skalning

Med [automatisk skalning](batch-automatic-scaling.md) skalas antalet virtuella datorer dynamiskt i batch-poolen baserat på kraven för det aktuella jobbet. Genom att skala poolen baserat på ett jobbs livs längd garanterar automatisk skalning att virtuella datorer skalas upp och används endast när det finns ett jobb att utföra. När jobbet har slutförts, eller när det inte finns några jobb, skalas de virtuella datorerna automatiskt ned för att spara beräknings resurser. Med skalning kan du sänka den totala kostnaden för din batch-lösning genom att bara använda de resurser du behöver.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Cost Management + fakturering](../cost-management-billing/cost-management-billing-overview.md)
- Lär dig mer om att [använda virtuella datorer med låg prioritet med batch](batch-low-pri-vms.md).
