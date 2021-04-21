---
title: Azure Functions distributionsfack
description: Lär dig att skapa och använda distributionsfack med Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 5f003b68283217f7877dc650ae4f07ddc5a31012
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789347"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions distributionsfack

Azure Functions distributionsfack gör att funktionsappen kan köra olika instanser som kallas "platser". Platser är olika miljöer som exponeras via en offentligt tillgänglig slutpunkt. En appinstans mappas alltid till produktionsplatsen och du kan växla instanser som tilldelats till en plats på begäran. Funktionsappar som körs under Apps Service-planen kan ha flera platser, medan endast ett fack tillåts under förbrukningsplanen.

Följande visar hur funktioner påverkas av växlingsfack:

- Omdirigering av trafik är sömlöst. inga begäranden tas bort på grund av ett byte.
- Om en funktion körs under en växling fortsätter körningen och nästa utlösare dirigeras till den växlade appinstansen.

## <a name="why-use-slots"></a>Varför ska jag använda platser?

Det finns ett antal fördelar med att använda distributionsfack. I följande scenarier beskrivs vanliga användningsområden för platser:

- **Olika miljöer för olika syften: Om** du använder olika platser kan du särskilja appinstanser innan du växlar till produktion eller mellanlagringsplats.
- **Förewarming:** Genom att distribuera till en plats i stället för direkt till produktion kan appen värma upp innan den går live. Dessutom minskar svarstiden för HTTP-utlösta arbetsbelastningar med fack. Instanserna värmes upp före distributionen, vilket minskar kallstarten för nyligen distribuerade funktioner.
- **Enkla återställningar:** Efter ett byte med produktion har facket med en tidigare mellanfasad app nu den tidigare produktionsappen. Om ändringarna som växlades till produktionsplatsen inte är som förväntat kan du omedelbart ångra växlingen för att få tillbaka din "senast kända bra instans".

## <a name="swap-operations"></a>Växlingsåtgärder

Under en växling anses en plats vara källan och den andra målet. Källfacket har den instans av programmet som tillämpas på målplatsen. Följande steg säkerställer att målfacket inte drabbas av driftstopp under en växling:

1. **Tillämpa inställningar:** Inställningar från målfacket tillämpas på alla instanser av källfacket. Till exempel tillämpas produktionsinställningarna på mellanlagringsinstansen. De tillämpade inställningarna omfattar följande kategorier:
    - [Platsspecifika appinställningar](#manage-settings) och anslutningssträngar (om tillämpligt)
    - [Inställningar för kontinuerlig](../app-service/deploy-continuous-deployment.md) distribution (om det är aktiverat)
    - [App Service autentiseringsinställningar](../app-service/overview-authentication-authorization.md) (om det är aktiverat)

1. **Vänta på omstarter och tillgänglighet:** Växlingen väntar på att varje instans i källfacket ska slutföra omstarten och vara tillgänglig för begäranden. Om någon instans inte startar om återställer växlingsåtgärden alla ändringar till källfacket och stoppar åtgärden.

1. **Uppdateringsroutning:** Om alla instanser på källplatsen har värmets upp slutför de två facken växlingen genom att växla routningsregler. Efter det här steget har målplatsen (till exempel produktionsplatsen) appen som tidigare har värmts upp i källfacket.

1. **Upprepa åtgärd:** Nu när källfacket har förväxlingsappen som tidigare fanns på målplatsen slutför du samma åtgärd genom att tillämpa alla inställningar och starta om instanserna för källfacket.

Tänk på följande punkter:

- Vid vilken tidpunkt som helst av växlingsåtgärden sker initieringen av de växlade apparna på källplatsen. Målplatsen förblir online medan källfacket förbereds, oavsett om växlingen lyckas eller misslyckas.

- Om du vill växla en mellanlagringsplats med produktionsplatsen kontrollerar du att produktionsplatsen *alltid är* målplatsen. På så sätt påverkar inte växlingsåtgärden din produktionsapp.

- Inställningar som rör händelsekällor och bindningar måste konfigureras som inställningar [för distributionsfack](#manage-settings) *innan du startar ett byte.* Genom att markera dem som "sticky" (fäst) i förväg ser du till att händelser och utdata dirigeras till rätt instans.

## <a name="manage-settings"></a>Hantera inställningar

Vissa konfigurationsinställningar är fackspecifika. I följande lista visas vilka inställningar som ändras när du växlar fack och vilka som förblir desamma.

**Fackspecifika inställningar:**

* Publicera slutpunkter
* Egna domännamn
* Icke-offentliga certifikat och TLS/SSL-inställningar
* Skalningsinställningar
* WebJobs-schemaläggare
* IP-begränsningar
* Alltid på
* Diagnostikinställningar
* Resursdelning för korsande ursprung (CORS)

**Icke-fackspecifika inställningar:**

* Allmänna inställningar, till exempel ramverksversion, 32/64-bitars, webbsocketar
* Appinställningar (kan konfigureras för att hålla sig till ett fack)
* Anslutningssträngar (kan konfigureras för att hålla sig till en plats)
* Hanterarmappningar
* Offentliga certifikat
* WebJobs-innehåll
* Hybridanslutningar *
* Integrering av virtuella nätverk *
* Tjänstslutpunkter *
* Azure Content Delivery Network *

Funktioner som markerats med en asterisk (*) planeras att tas bort. 

> [!NOTE]
> Vissa appinställningar som gäller för ej använda inställningar byts inte heller ut. Eftersom diagnostikinställningar till exempel inte växlas kan relaterade appinställningar som och inte heller växlas, även om de inte visas `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` som fackinställningar.
>

### <a name="create-a-deployment-setting"></a>Skapa en distributionsinställning

Du kan markera inställningar som en distributionsinställning, vilket gör den "fäst". En fästinställning växlar inte med appinstansen.

Om du skapar en distributionsinställning i ett fack måste du skapa samma inställning med ett unikt värde i alla andra fack som är inblandade i ett byte. På så sätt förblir inställningsnamnen konsekventa mellan facken, även om värdet för en inställning inte ändras. Den här namnkonsekvensen säkerställer att koden inte försöker komma åt en inställning som har definierats i en plats men inte en annan.

Använd följande steg för att skapa en distributionsinställning:

1. Gå till **Distributionsfack** i funktionsappen och välj sedan facknamnet.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Hitta platser i Azure Portal." border="true":::

1. Välj **Konfiguration** och välj sedan det inställningsnamn som du vill använda för det aktuella facket.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Konfigurera programinställningen för ett fack i Azure Portal." border="true":::

1. Välj **Distributionsfackinställning** och välj sedan **OK.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Konfigurera inställningen för distributionsfack." border="true":::

1. När inställningsavsnittet försvinner väljer du **Spara** för att behålla ändringarna

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Spara inställningen för distributionsfack." border="true":::

## <a name="deployment"></a>Distribution

Fack är tomma när du skapar ett fack. Du kan använda någon av de [distributionstekniker som stöds](./functions-deployment-technologies.md) för att distribuera ditt program till ett fack.

## <a name="scaling"></a>Skalning

Alla fack skalas till samma antal arbetare som produktionsplatsen.

- För förbrukningsplaner skalas facket när funktionsappen skalas.
- För App Service-planer skalas appen till ett fast antal arbetare. Platser körs på samma antal arbetare som appplanen.

## <a name="add-a-slot"></a>Lägga till ett fack

Du kan lägga till ett fack via [CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create) eller via portalen. Följande steg visar hur du skapar en ny plats i portalen:

1. Gå till funktionsappen.

1. Välj **Distributionsfack** och välj sedan **+ Lägg till fack.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Lägg Azure Functions distributionsfack." border="true":::

1. Skriv namnet på facket och välj Lägg **till**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Namnge Azure Functions distributionsfacket." border="true":::

## <a name="swap-slots"></a>Växla fack

Du kan växla fack via [CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap) eller via portalen. Följande steg visar hur du växlar platser i portalen:

1. Gå till funktionsappen.
1. Välj **Distributionsfack** och välj sedan **Växla**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Skärmbild som visar sidan Distributionsfack med åtgärden Lägg till plats markerad." border="true":::

1. Kontrollera konfigurationsinställningarna för växlingen och välj **Växla**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Växla distributionsfacket." border="true":::

Åtgärden kan ta en stund medan växlingsåtgärden körs.

## <a name="roll-back-a-swap"></a>Återställa en växling

Om en växling resulterar i ett fel eller om du bara vill "ångra" en växling kan du återställa till det ursprungliga tillståndet. Om du vill återgå till förväxlingstillståndet gör du en annan växling för att ångra växlingen.

## <a name="remove-a-slot"></a>Ta bort ett fack

Du kan ta bort ett fack via [CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete) eller via portalen. Följande steg visar hur du tar bort ett fack i portalen:

1. Gå till **Distributionsfack** i funktionsappen och välj sedan facknamnet.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Hitta platser i Azure Portal." border="true":::

1. Välj **Ta bort**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Skärmbild som visar sidan Översikt med åtgärden Ta bort markerad." border="true":::

1. Skriv namnet på det distributionsfack som du vill ta bort och välj sedan Ta **bort.**

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Ta bort distributionsfacket i Azure Portal." border="true":::

1. Stäng bekräftelsefönstret för borttagning.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Bekräftelse av borttagning av distributionsfack." border="true":::

## <a name="automate-slot-management"></a>Automatisera platshantering

Med [hjälp av Azure CLI](/cli/azure/functionapp/deployment/slot)kan du automatisera följande åtgärder för ett fack:

- [Skapa](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create)
- [Ta bort](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete)
- [Lista](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_list)
- [Swap](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap)
- [automatisk växling](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_auto_swap)

## <a name="change-app-service-plan"></a>Ändra App Service plan

Med en funktionsapp som körs under en App Service plan kan du ändra den underliggande App Service plan för ett fack.

> [!NOTE]
> Du kan inte ändra en platss App Service plan under förbrukningsplanen.

Använd följande steg för att ändra ett facks App Service plan:

1. Gå till **Distributionsfack** i funktionsappen och välj sedan facknamnet.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Hitta platser i Azure Portal." border="true":::

1. Under **App Service plan** väljer du **Ändra App Service plan**.

1. Välj den plan som du vill uppgradera till eller skapa en ny plan.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Ändra App Service plan i Azure Portal." border="true":::

1. Välj **OK**.

## <a name="limitations"></a>Begränsningar

Azure Functions distributionsfack har följande begränsningar:

- Antalet platser som är tillgängliga för en app beror på planen. Förbrukningsplanen tillåts bara ett distributionsfack. Ytterligare platser är tillgängliga för appar som körs under App Service plan.
- Om du växlar ett fack återställs nycklar för appar som har en `AzureWebJobsSecretStorageType` appinställning som är lika med `files` .
- Fack är inte tillgängliga för Linux-förbrukning plan.

## <a name="support-levels"></a>Supportnivåer

Det finns två nivåer av stöd för distributionsfack:

- **Allmän tillgänglighet (GA):** Fullständigt stöd och godkänt för produktionsanvändning.
- **Förhandsversion:** Stöds inte ännu, men förväntas nå GA-status i framtiden.

| OPERATIVSYSTEM/värdplan           | Supportnivå     |
| ------------------------- | -------------------- |
| Windows-förbrukning       | Allmän tillgänglighet |
| Windows Premium           | Allmän tillgänglighet  |
| Windows Dedicated         | Allmän tillgänglighet |
| Linux-förbrukning         | Förhandsgranskning          |
| Linux Premium             | Allmän tillgänglighet  |
| Linux Dedicated           | Allmän tillgänglighet |

## <a name="next-steps"></a>Nästa steg

- [Distributionstekniker i Azure Functions](./functions-deployment-technologies.md)
