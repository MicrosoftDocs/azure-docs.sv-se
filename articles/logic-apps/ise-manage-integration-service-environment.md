---
title: Hantera integrerings tjänst miljöer i Azure Logic Apps
description: Kontrol lera nätverks hälsa och hantera Logi Kap par, anslutningar, anpassade anslutningar och integrations konton i integrerings tjänst miljön (ISE) för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: 4df9543e5e747de640562b7e5be224e257e0cfd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92676115"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Hantera din Integration Service Environment (ISE) i Azure Logic Apps

Den här artikeln visar hur du utför hanterings uppgifter för [integrerings tjänst miljön (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), till exempel:

* Hantera resurser som Logi Kap par, anslutningar, integrations konton och anslutningar i din ISE.

* Kontrol lera nätverkets hälso tillstånd för ISE.

* Lägg till kapacitet, starta om ISE eller ta bort din ISE genom att följa stegen i det här avsnittet. Information om hur du lägger till dessa artefakter i ISE finns i [lägga till artefakter i integrerings tjänst miljön](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Visa din ISE

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I portalens sökruta anger du "integrerings tjänst miljöer" och väljer sedan **integrerings tjänst miljöer**.

   ![Hitta integrerings tjänst miljöer](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. I resultat listan väljer du integrerings tjänst miljön.

   ![Välj integrerings tjänst miljö](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Fortsätt till nästa avsnitt för att hitta Logi Kap par, anslutningar, kopplingar eller integrations konton i din ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Kontrol lera nätverks hälsa

På menyn ISE väljer du **nätverks hälsa** under **Inställningar**. I det här fönstret visas hälso status för dina undernät och utgående beroenden för andra tjänster.

![Kontrol lera nätverks hälsa](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> Om din ISE-nätverk blir felfria kan den interna App Service-miljön (ASE) som används av din ISE också bli felaktig. Om ASE är i fel tillstånd i mer än sju dagar pausas ASE. Kontrol lera konfigurationen av det virtuella nätverket för att lösa det här läget. Lös eventuella problem som du hittar och starta sedan om ISE. I annat fall tas den uppskjutna ASE bort, och ISE blir oanvändbara om du har 90 dagar. Därför bör du se till att du låter din ISE vara felfri för att tillåta nödvändig trafik.
> 
> Mer information finns i de här ämnena:
>
> * [Översikt över Azure App Service diagnostik](../app-service/overview-diagnostics.md)
> * [Meddelande loggning för Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Hantera dina Logic Apps

Du kan visa och hantera de Logic Apps som finns i din ISE.

1. I menyn ISE väljer du Logi Kap par under  **Inställningar**.

   ![Visa Logic Apps](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Om du vill ta bort Logi Kap par som du inte längre behöver i din ISE väljer du dessa Logic Apps och väljer sedan **ta bort**. Bekräfta att du vill ta bort genom att välja **Ja**.

> [!NOTE]
> Om du tar bort och återskapar en underordnad Logic-app måste du spara om den överordnade Logic-appen. Den omskapade underordnade appen kommer att ha olika metadata.
> Om du inte sparar om den överordnade Logic-appen när du har återskapat dess underordnade Logic-program, kommer anropen till den underordnade Logic-appen att Miss varnas med fel "obehörig". Det här beteendet gäller för över-underordnade Logic Apps, till exempel de som använder artefakter i integrations konton eller anropar Azure Functions.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Hantera API-anslutningar

Du kan visa och hantera de anslutningar som har skapats av Logic Apps som körs i din ISE.

1. I menyn ISE väljer du **API-anslutningar** under **Inställningar**.

   ![Visa API-anslutningar](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Om du vill ta bort anslutningar som du inte längre behöver i din ISE väljer du dessa anslutningar och väljer sedan **ta bort**. Bekräfta att du vill ta bort genom att välja **Ja**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Hantera ISE-anslutningar

Du kan visa och hantera API-kopplingar som distribueras till din ISE.

1. På menyn ISE väljer du **hanterade anslutningar** under **Inställningar**.

   ![Visa hanterade anslutningar](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Om du vill ta bort anslutningar som du inte vill ska vara tillgängliga i din ISE väljer du de kopplingarna och väljer sedan **ta bort**. Bekräfta att du vill ta bort genom att välja **Ja**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Hantera anpassade anslutningar

Du kan visa och hantera de anpassade anslutnings program som du har distribuerat till din ISE.

1. På menyn ISE väljer du **anpassade anslutningar** under **Inställningar**.

   ![Hitta anpassade anslutningsappar](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Om du vill ta bort anpassade anslutningar som du inte längre behöver i din ISE väljer du de kopplingarna och väljer sedan **ta bort**. Bekräfta att du vill ta bort genom att välja **Ja**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Hantera integrationskonton

1. På menyn ISE väljer du **integrations konton** under **Inställningar**.

   ![Hitta integrations konton](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Om du vill ta bort integrations konton från din ISE när de inte längre behövs väljer du dessa integrations konton och väljer sedan **ta bort**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Lägg till ISE-kapacitet

Premium ISE-bas enheten har fast kapacitet, så om du behöver mer data flöde kan du lägga till fler skalnings enheter, antingen under skapandet eller efteråt. Developer SKU: n inkluderar inte möjligheten att lägga till skalnings enheter.

1. Gå till din ISE i [Azure Portal](https://portal.azure.com).

1. Om du vill granska användnings-och prestanda mått för din ISE väljer du **Översikt** på din ISE-meny.

   ![Visa användning för ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. Under **Inställningar** väljer du **skala ut**. I fönstret **Konfigurera** väljer du bland följande alternativ:

   * [**Manuell skalning**](#manual-scale): skala baserat på antalet bearbetnings enheter som du vill använda.
   * [**Anpassad autoskalning**](#custom-autoscale): skala baserat på prestanda mått genom att välja bland olika kriterier och ange tröskelvärdena för att uppfylla villkoren.

   ![Skärm bild som visar sidan "skala ut" med "manuell skalning" vald.](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Manuell skalning

1. När du har valt **manuell skalning**, för **ytterligare kapacitet** väljer du det antal skalnings enheter som du vill använda.

   ![Välj den skalnings typ som du vill använda](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. När du är klar väljer du **Spara**.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Anpassad autoskalning

1. När du har valt **anpassad autoskalning** anger du ett namn för inställningen i **autoskalning inställnings namn** och du kan också välja den Azure-resurs grupp där inställningen tillhör.

   ![Ange namnet på inställningen för autoskalning och Välj resurs grupp](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. För **standard** villkoret väljer du antingen **skala baserat på ett mått** eller **skala till ett angivet instans antal**.

   * Om du väljer instans-baserad anger du antalet för bearbetnings enheterna, vilket är ett värde mellan 0 och 10.

   * Följ dessa steg om du väljer Metric-baserad:

     1. I avsnittet **regler** väljer du **Lägg till en regel**.

     1. I fönstret **skalnings regel** ställer du in kriterier och åtgärder som ska vidtas när regeln utlöses.

     1. Ange följande värden för **instans gränser**:

        * **Minimum**: minsta antal bearbetnings enheter som ska användas
        * **Max**: det högsta antalet bearbetnings enheter som ska användas
        * **Standard**: om några problem inträffar när du läser resurs måtten och den aktuella kapaciteten är lägre än standard kapaciteten skalas automatisk skalning till standard antalet bearbetnings enheter. Men om den aktuella kapaciteten överstiger standard kapaciteten skalas inte autoskalning i.

1. Om du vill lägga till ett annat villkor väljer du **Lägg till skalnings villkor**.

1. Spara ändringarna när du är klar med inställningarna för autoskalning.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Starta om ISE

Om du ändrar inställningarna för DNS-servern eller DNS-servern måste du starta om ISE så att ISE kan hämta ändringarna. Om du startar om en Premium-SKU ISE uppstår avbrott på grund av redundans och komponenter som startar om en i taget under återvinningen. Men en utvecklare av websku ISE upplever avbrott eftersom det inte finns någon redundans. Mer information finns i [ISE SKU: er](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. Gå till din ISE i [Azure Portal](https://portal.azure.com).

1. På menyn ISE väljer du **Översikt**. I översikts verktygsfältet **startar du om**.

   ![Starta om integrerings tjänst miljön](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Ta bort ISE

Innan du tar bort en ISE som du inte längre behöver eller en Azure-resurs grupp som innehåller en ISE kontrollerar du att du inte har några principer eller lås på den Azure-resurs grupp som innehåller resurserna eller på ditt virtuella Azure-nätverk eftersom dessa objekt kan blockera borttagning.

När du har tagit bort din ISE kan du behöva vänta upp till 9 timmar innan du försöker ta bort ditt virtuella Azure-nätverk eller undernät.

## <a name="next-steps"></a>Nästa steg

* [Lägga till resurser i integreringstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
