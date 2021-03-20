---
title: Lägga till resurser i integreringstjänstmiljöer
description: Lägg till logi Kap par, integrations konton, anpassade anslutningar och hanterade anslutningar i integrerings tjänst miljön (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 147247c663311cfb3e05a986c6fb2bffbb41158b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92675213"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Lägg till resurser i integrerings tjänst miljön (ISE) i Azure Logic Apps

När du har skapat en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lägger du till resurser som Logi Kap par, integrations konton och anslutningar så att de kan komma åt resurserna i ditt virtuella Azure-nätverk. Till exempel visas inte hanterade ISE-kopplingar som blir tillgängliga efter att du har skapat din ISE automatiskt i Logic App Designer. Innan du kan använda dessa ISE-kopplingar måste du manuellt [lägga till och distribuera dessa anslutningar till ISE](#add-ise-connectors-environment) så att de visas i Logic App Designer.

> [!IMPORTANT]
> För att Logic Apps och integrations konton ska fungera tillsammans i en ISE måste båda använda *samma ISE* som plats.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Den ISE som du skapade för att köra dina Logic Apps. Om du inte har någon ISE måste du [först skapa en ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Om du vill skapa, lägga till eller uppdatera resurser som distribueras till en ISE måste du tilldela rollen ägare eller deltagare på denna ISE, eller så har du behörigheter som ärvts via Azure-prenumerationen eller Azure-resurs gruppen som är associerad med ISE. För personer som inte har ägare, deltagare eller ärvda behörigheter kan de tilldelas rollen som Integration Service Environment deltagare eller rollen Integration Service Environment utvecklare. Mer information finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md)?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Skapa Logic Apps

Följ dessa steg om du vill skapa Logi Kap par som körs i integrerings tjänst miljön (ISE):

1. Leta upp och öppna din ISE, om den inte redan är öppen. Från menyn ISE, under **Inställningar**, väljer du **Logic Apps**  >  **Lägg till**.

   ![Lägg till ny Logic app i ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Ange information om den Logic-app som du vill skapa, till exempel:

   ![Skärm bild som visar fönstret "Logic app" "skapa" med exempel information angiven.](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Namnet på den Logic app som ska skapas |
   | **Prenumeration** | Ja | Namnet på den Azure-prenumeration som ska användas |
   | **Resursgrupp** | Ja | Namnet på Azure-resurs gruppen (ny eller befintlig) som ska användas |
   | **Plats** | Ja | Under **integrerings tjänst miljöer** väljer du den ISE som ska användas, om det inte redan har valts. <p><p> **Viktigt**: om du vill använda dina Logi Kap par med ett integrations konto måste båda använda samma ISE. |
   ||||

1. När du är färdig väljer du **Skapa**.

1. Fortsätt [att skapa din Logic app på vanligt sätt](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   För skillnader i hur utlösare och åtgärder fungerar och hur de märks när du använder en ISE jämfört med Logic Apps tjänsten för flera innehavare, se [isolerade kontra flera innehavare i ISE-översikten](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Information om hur du hanterar Logic Apps och API-anslutningar i din ISE finns i [Hantera integrerings tjänst miljön](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Skapa integrations konton

Utifrån den [ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) som valts vid skapandet, innehåller din ISE en viss integrerings konto användning utan extra kostnad. Logi Kap par som finns i en integrerings tjänst miljö (ISE) kan endast referera till integrations konton som finns i samma ISE. För att ett integrations konto ska fungera med logi Kap par i en ISE måste både integrations kontot och Logic Apps använda *samma miljö* som platsen. Mer information om integrations konton och ISEs finns i [integrations konton med ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Följ dessa steg om du vill skapa ett integrations konto som använder en ISE:

1. Leta upp och öppna din ISE, om den inte redan är öppen. Från menyn ISE, under **Inställningar**, väljer du **integrerings konton**  >  **Lägg till**.

   ![Lägg till nytt integrations konto i ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Ange information om den Logic-app som du vill skapa, till exempel:

   ![Välj integrerings tjänst miljö](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Namnet på det integrations konto som du vill skapa |
   | **Prenumeration** | Ja | Namnet på den Azure-prenumeration som du vill använda |
   | **Resursgrupp** | Ja | Namnet på Azure-resurs gruppen (ny eller befintlig) som ska användas |
   | **Prisnivå** | Ja | Den pris nivå som ska användas för integrations kontot |
   | **Plats** | Ja | Under **integrerings tjänst miljöer** väljer du samma ISE som dina Logi Kap par använder, om de inte redan har valts. <p><p> **Viktigt**: om du vill använda ditt integrations konto med Logic Apps måste båda använda samma ISE. |
   ||||

1. När du är färdig väljer du **Skapa**.

1. [Länka din Logic app till ditt integrations konto på vanligt sätt](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Fortsätt genom att lägga till resurser till ditt integrations konto, till exempel [handels partner](../logic-apps/logic-apps-enterprise-integration-partners.md) och [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Information om hur du hanterar integrations konton i din ISE finns i [Hantera integrerings tjänst miljön](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Lägg till ISE-anslutningar

När du har skapat din ISE visas inte hanterade ISE-kopplingar automatiskt i anslutnings väljaren i Logic App Designer. Innan du kan använda dessa ISE-kopplingar måste du manuellt lägga till och distribuera dessa anslutningar till ISE så att de visas i Logic App Designer.

> [!IMPORTANT]
> Hanterade ISE-anslutningar stöder för närvarande inte [taggar](../azure-resource-manager/management/tag-support.md). Om du ställer in en princip som kräver taggning kan försök att lägga till ISE-anslutningar Miss förorsakat av ett fel som liknar detta exempel:
> 
> ```json
> {
>    "error": { 
>       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
>       "message": "The tags are not supported in the managed API 'azureblob'."
>    }
> }
> ```
> 
> För att lägga till ISE-anslutningar måste du antingen inaktivera eller ta bort principen. 

1. På menyn ISE väljer du **hanterade anslutningar** under **Inställningar**. Välj **Lägg till** i verktygsfältet.

   ![Visa hanterade anslutningar](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Öppna listan **Sök koppling** i fönstret **Lägg till en ny hanterad koppling** . Välj den ISE-anslutning som du vill använda men som inte har distribuerats i ISE. Välj **Skapa**.

   ![Välj den ISE-anslutning som du vill distribuera i din ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Endast ISE-kopplingar som är berättigade men som ännu inte har distribuerats till din ISE visas tillgängliga för dig att välja. Anslutningar som redan har distribuerats i din ISE visas inte tillgängliga för markering.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Skapa anpassade anslutningar

Om du vill använda anpassade anslutningar i ISE skapar du de anpassade anslutningarna direkt inuti din ISE.

1. Leta upp och öppna din ISE, om den inte redan är öppen. I menyn ISE väljer du  **anpassade anslutningar**  >  **Lägg till** under Inställningar.

   ![Skapa anpassad anslutningsapp](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Ange namnet, Azure-prenumerationen och Azure-resurs gruppen (ny eller befintlig) som ska användas för din anpassade anslutning.

1. I listan **plats** i avsnittet **integrerings tjänst miljöer** väljer du samma ISE som din Logi Kap par använder och väljer **skapa**, till exempel:

   ![Skärm bild som visar fönstret "skapa Logic Apps anpassad koppling" med exempel information valt.](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Välj din nya anpassade anslutning och välj sedan **Redigera**, till exempel:

   ![Välj och redigera anpassad anslutning](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Fortsätt genom att skapa anslutningen på vanligt sätt från en [openapi-definition](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) eller [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Information om hur du hanterar anpassade anslutningar i din ISE finns i [Hantera integrerings tjänst miljön](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera integreringstjänstmiljöer](../logic-apps/ise-manage-integration-service-environment.md)
