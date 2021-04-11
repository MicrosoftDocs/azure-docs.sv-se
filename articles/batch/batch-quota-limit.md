---
title: Kvoter och begränsningar för tjänsten
description: Lär dig mer om standard Azure Batch kvoter, gränser och begränsningar samt hur du begär kvot ökningar
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: seodec18
ms.openlocfilehash: 6e17a90cc573205bcb964a0428e0b7320323b8a6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553557"
---
# <a name="batch-service-quotas-and-limits"></a>Kvoter och begränsningar för Batch-tjänsten

Precis som med andra Azure-tjänster finns det gränser för vissa resurser som är kopplade till batch-tjänsten. Många av de här gränserna är standard kvoter som används av Azure på prenumerations-eller konto nivå.

Behåll dessa kvoter i åtanke när du utformar och skalar upp dina batch-arbetsbelastningar. Om din pool till exempel inte når mål antalet datornoder som du har angett kan du ha nått kärn kvot gränsen för ditt batch-konto.

Du kan köra flera batch-arbetsbelastningar i ett enda batch-konto eller distribuera dina arbets belastningar mellan batch-konton som finns i samma prenumeration, men i olika Azure-regioner.

Om du planerar att köra produktions arbets belastningar i batch kan du behöva öka en eller flera av kvoterna ovanför standardvärdet. Om du vill öka en kvot kan du [begära en kvot ökning](#increase-a-quota) utan kostnad.

## <a name="resource-quotas"></a>Resurskvoter

En kvot är en gräns, inte en kapacitets garanti. Kontakta Azure-supporten om du har storskaliga kapacitets behov.

Observera också att kvoter inte är garanterade värden. Kvoter kan variera beroende på ändringar från batch-tjänsten eller en användar förfrågan för att ändra ett kvot värde.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Kärn kvoter

### <a name="cores-quotas-in-batch-service-mode"></a>Kärn kvoter i batch service-läge

Det finns kärn kvoter för varje VM-serie som stöds av batch. De här kärn kvoterna visas på sidan **kvoter** i Azure Portal. Kvot gränser för VM-serien kan uppdateras med en support förfrågan enligt beskrivningen nedan. För dedikerade noder tvingar batch en kärn kvot gräns för varje VM-serie, samt en total kärn kvot gräns för hela batch-kontot. För noder med låg prioritet tvingar batch endast en total kärn kvot för batch-kontot utan någon skillnad mellan olika VM-serier.

### <a name="cores-quotas-in-user-subscription-mode"></a>Kärn kvoter i användar prenumerations läge

Om du har skapat ett [Batch-konto](accounts.md) med pool-allokeringsinställningar inställt på **användar prenumeration** skapas virtuella batch-datorer och andra resurser direkt i prenumerationen när en pool skapas eller ändras. Kvoten Azure Batch Core gäller inte och kvoterna i din prenumeration för regionala beräknings kärnor, beräknings kärnor per serie och andra resurser används och tillämpas.

Läs mer om de här kvoterna i [Azure-prenumerationer och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Storleks begränsningar för pooler

Storleks gränser för pooler anges av batch-tjänsten. Till skillnad från [resurs kvoter](#resource-quotas)kan de här värdena inte ändras. Endast pooler med kommunikation mellan noder och anpassade avbildningar har begränsningar som skiljer sig från standard kvoten.

| **Resurs** | **Övre gräns** |
| --- | --- |
| **Compute-noder i en [pool för kommunikation mellan noder](batch-mpi.md)**  ||
| Allocation mode för batch service pool | 100 |
| Pool för batch-Fakturapool | 80 |
| **Compute-noder som [skapats med en hanterad avbildnings resurs](batch-custom-images.md)**<sup>1</sup> ||
| Dedikerade noder | 2000 |
| Lågprioriterade virtuella noder | 1000 |

<sup>1</sup> för pooler som inte är kommunikation mellan noder aktive rad.

## <a name="other-limits"></a>Andra gränser

Dessa ytterligare gränser anges av batch-tjänsten. Till skillnad från [resurs kvoter](#resource-quotas)kan de här värdena inte ändras.

| **Resurs** | **Övre gräns** |
| --- | --- |
| [Samtidiga aktiviteter](batch-parallel-node-tasks.md) per Compute-nod | 4 x antal Node-kärnor |
| [Program](batch-application-packages.md) per batch-konto | 20 |
| Programpaket per program | 40 |
| Programpaket per pool | 10 |
| Maximal varaktighet för aktivitet | 180 dagar<sup>1</sup> |
| [Monteringar](virtual-file-mount.md) per Compute-nod | 10 |
| Certifikat per pool | 12 |

<sup>1</sup> den maximala livs längden för en aktivitet, från när den läggs till i jobbet till när den är klar, är 180 dagar. Slutförda uppgifter sparas i sju dagar; data för uppgifter som inte har slutförts inom den maximala livs längden är inte tillgängliga.

## <a name="view-batch-quotas"></a>Visa batch-kvoter

Så här visar du kvoter för batch-kontot i [Azure Portal](https://portal.azure.com):

1. Välj **batch-konton** och välj sedan det batch-konto som du är intresse rad av.
1. Välj **kvoter** på menyn för batch-kontot.
1. Visa de kvoter som för närvarande tillämpas på batch-kontot.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Skärm bild som visar kvoter för batch-konto i Azure Portal.":::

## <a name="increase-a-quota"></a>Öka en kvot

Du kan begära en kvot ökning för batch-kontot eller prenumerationen med hjälp av [Azure Portal](https://portal.azure.com) eller genom att använda [azures kvot REST API](#azure-quota-rest-api).

Vilken typ av kvot ökning som är beror på poolens fördelnings läge för batch-kontot. Om du vill begära en kvot ökning måste du ta med den VM-serien som du vill öka kvoten för. När kvot ökningen tillämpas tillämpas den på alla virtuella datorer.

När du har skickat in ditt support ärende kontaktar Azure-supporten. Kvot begär Anden kan slutföras inom några minuter eller upp till två arbets dagar.

### <a name="azure-portal"></a>Azure Portal

1. På sidan **kvoter** väljer du **begär kvot ökning**. Alternativt kan du välja **Hjälp + Support** panelen på portalens instrument panel (eller från frågetecknet (**?**) i det övre högra hörnet i portalen) och sedan välja **ny supportbegäran.**

1. Gör så här i **Grundläggande**:

    1. För **typ av problem** väljer du **begränsningar för tjänsten och prenumerationen (kvoter)**.
    1. Välj din prenumeration.
    1. För **typ av kvot** väljer du **batch**.
    1. Fortsätt genom att välja **Nästa**.

1. I **Information**:

    1. I avsnittet **Ange information** anger du plats, kvot typ och batch-konto (om tillämpligt) och sedan de kvoter som ska ökas.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Skärm bild av skärmen med kvot information när du begär en kvot ökning.":::

       Kvot typer är:

       - **Per batch-konto**  
         Använd det här alternativet för att begära kvot ökningar som är specifikt för ett enda batch-konto, inklusive dedikerade och låg prioritets kärnor samt antalet jobb och pooler.

         Om du väljer det här alternativet anger du det batch-konto som denna begäran ska gälla för och väljer sedan de kvoter som du vill uppdatera. Ange den nya gräns som du begär för varje resurs.

         Kvoten med låg prioritet är ett enda värde i alla VM-serier. Om du behöver begränsade SKU: er måste du välja **låg prioritets kärnor** och inkludera VM-familjer som ska begäras.

       - **Alla konton i den här regionen**  
         Använd det här alternativet för att begära kvot ökningar som gäller för alla batch-konton i en region, till exempel antalet batch-konton per region per prenumeration.

    1. I **support metod** väljer du en **allvarlighets grad** för din [affärs påverkan](https://aka.ms/supportseverity) och önskad kontakt metod och support språk.

    1. I **kontakt information** kontrollerar du och anger de nödvändiga kontakt uppgifterna.

1. Välj **Granska + skapa** och välj sedan **skapa** för att skicka in support förfrågan.

### <a name="azure-quota-rest-api"></a>Azure-kvot REST API

Du kan använda Azure-kvoten REST API för att begära en kvot ökning på prenumerations nivån eller på grupp konto nivån.

Mer information och exempel finns i [begär en kvot ökning med hjälp av Azure-Support REST API](/rest/api/support/quota-payload#azure-batch).

## <a name="related-quotas-for-vm-pools"></a>Relaterade kvoter för VM-pooler

[Batch-pooler i den virtuella dator konfigurationen som distribueras i ett virtuellt Azure-nätverk](batch-virtual-network.md) allokerar automatiskt ytterligare Azure nätverks resurser. Dessa resurser skapas i den prenumeration som innehåller det virtuella nätverk som angavs när du skapade batch-poolen.

Följande resurser skapas för varje 100-pool i ett virtuellt nätverk:

- En [nätverks säkerhets grupp](../virtual-network/network-security-groups-overview.md#network-security-groups)
- En [offentlig IP-adress](../virtual-network/public-ip-addresses.md)
- En [belastningsutjämnare](../load-balancer/load-balancer-overview.md)

Dessa resurser begränsas av prenumerationens [resurskvoter](../azure-resource-manager/management/azure-subscription-service-limits.md). Om du planerar distributioner av stora pooler i ett virtuellt nätverk kan du behöva begära en kvot ökning för en eller flera av dessa resurser.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig mer om [Azure-prenumerationer, tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).