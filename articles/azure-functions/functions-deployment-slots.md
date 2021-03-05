---
title: Azure Functions distributions platser
description: Lär dig att skapa och använda distributions fack med Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 2dbf7c31e6b0b40fa9dc2d59e86c0ecc731657e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172417"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions distributions platser

Med Azure Functions distributions platser kan din Function-app köra olika instanser som kallas "platser". Platser är olika miljöer som exponeras via en offentligt tillgänglig slut punkt. En app-instans är alltid mappad till produktions platsen och du kan byta instanser som har tilldelats till en plats på begäran. Function-appar som körs under App Service-planen kan ha flera platser, medan endast en plats tillåts under förbruknings planen.

Följande visar hur funktioner påverkas av växlings platser:

- Trafik omdirigering är sömlös; inga förfrågningar ignoreras på grund av en växling.
- Om en funktion körs under en växling fortsätter körningen och nästa utlösare dirigeras till den utbytta App-instansen.

> [!NOTE]
> Platser är för närvarande inte tillgängliga för Linux-förbruknings planen.

## <a name="why-use-slots"></a>Varför ska jag använda platser?

Det finns ett antal fördelar med att använda distributions platser. I följande scenarier beskrivs vanliga användnings områden för fack:

- **Olika miljöer i olika syfte**: med olika platser får du möjlighet att särskilja App-instanser innan du växlar till produktion eller mellanlagringsplats.
- Att **förvärma**: distribution till en plats i stället för direkt till produktion gör att appen kan värmas innan Live. Dessutom minskar svars tiden för HTTP-utlösta arbets belastningar med hjälp av platser. Instanser värms upp före distributionen, vilket minskar kall starten för nyligen distribuerade funktioner.
- **Enkel återgång**: efter en byte med produktion har platsen med en tidigare mellanlagrad app nu den tidigare produktions appen. Om ändringarna som utbyts till produktions platsen inte är som du förväntar dig, kan du omedelbart omvända växlingen för att få din "senast fungerande instans" tillbaka.

## <a name="swap-operations"></a>Växlings åtgärder

Under en växling anses en plats vara källan och den andra målet. Käll platsen har instansen av programmet som används på mål platsen. Följande steg säkerställer att mål platsen inte upplever drift stopp under en växling:

1. **Tillämpa inställningar:** Inställningar från mål platsen tillämpas på alla instanser av käll platsen. Produktions inställningarna tillämpas till exempel på mellanlagringsplatsen. De tillämpade inställningarna omfattar följande kategorier:
    - [Platsspecifika appinställningar och](#manage-settings) anslutnings strängar (om tillämpligt)
    - Inställningar för [kontinuerlig distribution](../app-service/deploy-continuous-deployment.md) (om aktive rad)
    - [App Service autentiseringsinställningar](../app-service/overview-authentication-authorization.md) (om den är aktive rad)

1. **Vänta tills omstarter och tillgänglighet:** Växlingen väntar på att varje instans på käll platsen ska kunna slutföra sin omstart och är tillgänglig för förfrågningar. Om någon instans inte kan starta om återställer växlings åtgärden alla ändringar till käll platsen och stoppar åtgärden.

1. **Uppdaterings dirigering:** Om alla instanser på käll platsen har förvärmts korrekt, slutför de två platserna växlingen genom att växla regler för routning. Efter det här steget har mål platsen (till exempel produktions platsen) den app som tidigare har förvärmts på käll platsen.

1. **Upprepa åtgärd:** Nu när käll platsen har för hands växlings appen tidigare på mål platsen, slutför du samma åtgärd genom att tillämpa alla inställningar och starta om instanserna för käll platsen.

Tänk på följande punkter:

- Vid alla tidpunkter på växlings åtgärden sker initieringen av de växlade apparna på käll platsen. Mål platsen är online när käll platsen är för beredd, om växlingen lyckas eller inte.

- Om du vill byta mellan en mellanlagringsplats med produktions platsen kontrollerar du att produktions platsen *alltid* är mål platsen. På så sätt kommer växlings åtgärden inte att påverka din produktions program.

- Inställningar som rör händelse källor och bindningar måste konfigureras som [distributions plats inställningar](#manage-settings) *innan du påbörjar en växling*. Genom att markera dem som "trög" i förväg säkerställer du att händelser och utdata dirigeras till rätt instans.

## <a name="manage-settings"></a>Hantera inställningar

Vissa konfigurations inställningar är platsspecifika. Nedan visas information om vilka inställningar som ändras när du byter platser och som är oförändrade.

Platsspecifika **Inställningar**:

* Publicerings slut punkter
* Egna domännamn
* Icke-offentliga certifikat och TLS/SSL-inställningar
* Skalnings inställningar
* Jobb scheman för WebJobs
* IP-begränsningar
* Alltid på
* Diagnostikinställningar
* Resursdelning för korsande ursprung (CORS)

**Inställningar som inte är** platsspecifika:

* Allmänna inställningar, till exempel Ramverks version, 32/64-bitars, Web Sockets
* Appinställningar (kan konfigureras för att fästa på en plats)
* Anslutnings strängar (kan konfigureras att fästa mot en plats)
* Mappningar för hanterare
* Offentliga certifikat
* WebJobs-innehåll
* Hybrid anslutningar *
* Integrering av virtuella nätverk *
* Tjänst slut punkter *
* Azure Content Delivery Network *

Funktioner som har marker ATS med en asterisk (*) planeras att inte växlas. 

> [!NOTE]
> Vissa inställningar för appar som tillämpas på inställningar som inte har växlats växlas inte heller. Till exempel, eftersom diagnostikinställningar inte växlas, växlas inte relaterade appinställningar, till exempel `WEBSITE_HTTPLOGGING_RETENTION_DAYS` och `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` , även om de inte visas som plats inställningar.
>

### <a name="create-a-deployment-setting"></a>Skapa en distributions inställning

Du kan markera inställningar som en distributions inställning, vilket gör den "trög". En inställning för tröghet växlar inte med App-instansen.

Om du skapar en distributions inställning på en plats, se till att skapa samma inställning med ett unikt värde på någon annan plats som är involverad i en växling. På så sätt, medan värdet för en inställning inte ändras, förblir inställnings namnen konsekventa mellan platser. Detta namn konsekvens säkerställer att koden inte försöker komma åt en inställning som har definierats på en plats, men inte en annan.

Använd följande steg för att skapa en distributions inställning:

1. Gå till **distributions fack** i Function-appen och välj plats namnet.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Hitta platser i Azure Portal." border="true":::

1. Välj **konfiguration** och välj sedan det inställnings namn som du vill använda för den aktuella platsen.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Konfigurera program inställningen för en plats i Azure Portal." border="true":::

1. Välj **distributions plats inställning** och välj sedan **OK**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Konfigurera distributions plats inställningen." border="true":::

1. När du har angett avsnittet försvinner väljer du **Spara** för att behålla ändringarna

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Spara distributions plats inställningen." border="true":::

## <a name="deployment"></a>Distribution

Platser är tomma när du skapar en plats. Du kan använda någon av de [distributions tekniker som stöds](./functions-deployment-technologies.md) för att distribuera programmet till en plats.

## <a name="scaling"></a>Skalning

Alla platser skalas till samma antal anställda som produktions platsen.

- För förbruknings planer skalas-facket som Function-appen skalar.
- För App Service planer skalas appen till ett fast antal arbetare. Platser körs på samma antal arbets tagare som app-planen.

## <a name="add-a-slot"></a>Lägga till ett fack

Du kan lägga till en plats via [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create) eller via portalen. Följande steg visar hur du skapar en ny plats i portalen:

1. Navigera till din Function-app.

1. Välj **distributions platser** och välj sedan **+ Lägg till plats**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Lägg till Azure Functions distributions plats." border="true":::

1. Skriv namnet på facket och välj **Lägg till**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Ge Azure Functions distributions facket." border="true":::

## <a name="swap-slots"></a>Växla platser

Du kan växla mellan platser via [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap) eller via portalen. Följande steg visar hur du byter platser i portalen:

1. Navigera till Function-appen.
1. Välj **distributions platser** och välj sedan **Växla**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Skärm bild som visar sidan &quot;distributions fack&quot; där åtgärden Lägg till plats har valts." border="true":::

1. Verifiera konfigurations inställningarna för din växling och välj **Växla**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Växla distributions facket." border="true":::

Åtgärden kan ta en stund medan växlings åtgärden körs.

## <a name="roll-back-a-swap"></a>Återställa en växling

Om en växling resulterar i ett fel eller om du bara vill "ångra" en växling kan du återställa den ursprungliga statusen. Om du vill återgå till förväxlat tillstånd, gör du en annan växling för att omvända växlingen.

## <a name="remove-a-slot"></a>Ta bort en plats

Du kan ta bort en plats via [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete) eller via portalen. Följande steg visar hur du tar bort en plats i portalen:

1. Gå till **distributions fack** i Function-appen och välj plats namnet.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Hitta platser i Azure Portal." border="true":::

1. Välj **Ta bort**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Skärm bild som visar sidan &quot;Översikt&quot; där åtgärden ta bort har marker ATS." border="true":::

1. Skriv namnet på den distributions plats som du vill ta bort och välj sedan **ta bort**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Ta bort distributions platsen i Azure Portal." border="true":::

1. Stäng bekräftelse fönstret för borttagning.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Bekräfta borttagning av distributions plats." border="true":::

## <a name="automate-slot-management"></a>Automatisera plats hantering

Med [Azure CLI](/cli/azure/functionapp/deployment/slot)kan du automatisera följande åtgärder för en plats:

- [fram](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create)
- [delete](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete)
- [lista](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-list)
- [skärm](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap)
- [automatisk växling](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Ändra App Service plan

Med en Function-app som körs under en App Service plan kan du ändra den underliggande App Service plan för en plats.

> [!NOTE]
> Du kan inte ändra en platss App Service plan under förbruknings planen.

Använd följande steg för att ändra en platss App Service plan:

1. Gå till **distributions fack** i Function-appen och välj plats namnet.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Hitta platser i Azure Portal." border="true":::

1. Under **App Service plan** väljer du **ändra App Service plan**.

1. Välj den plan som du vill uppgradera till eller skapa en ny plan.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Ändra App Service plan i Azure Portal." border="true":::

1. Välj **OK**.

## <a name="limitations"></a>Begränsningar

Azure Functions distributions fack har följande begränsningar:

- Antalet tillgängliga fack för en app beror på planen. Förbruknings planen tillåts bara en distributions plats. Det finns ytterligare platser för appar som körs under App Service plan.
- När en plats byts ut återställs nycklar för appar som har en `AzureWebJobsSecretStorageType` app-inställning som är lika med `files` .
- Det finns inga tillgängliga platser för Linux-förbruknings planen.

## <a name="support-levels"></a>Support nivåer

Det finns två nivåer av stöd för distributions platser:

- **Allmän tillgänglighet (ga)**: fullständigt stöd för och godkänd användning av produktion.
- För **hands version**: stöds inte ännu, men förväntas komma att uppnå GA-status i framtiden.

| Operativ system/värd plan           | Support nivå     |
| ------------------------- | -------------------- |
| Windows-förbrukning       | Allmän tillgänglighet |
| Windows Premium           | Allmän tillgänglighet  |
| Windows-dedikerad         | Allmän tillgänglighet |
| Linux-förbrukning         | Stöd saknas          |
| Linux Premium             | Allmän tillgänglighet  |
| Linux-dedikerad           | Allmän tillgänglighet |

## <a name="next-steps"></a>Nästa steg

- [Distributions tekniker i Azure Functions](./functions-deployment-technologies.md)
