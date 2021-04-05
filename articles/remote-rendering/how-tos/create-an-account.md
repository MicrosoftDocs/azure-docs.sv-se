---
title: Skapa ett Azure Remote Rendering-konto
description: Beskriver stegen för att skapa ett konto för Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 83bd4a7ae0082d24f7ac617719e628f4db4baeb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98197648"
---
# <a name="create-an-azure-remote-rendering-account"></a>Skapa ett Azure Remote Rendering-konto

Det här kapitlet vägleder dig genom stegen för att skapa ett konto för tjänsten **Azure Remote rendering** . Ett giltigt konto är obligatoriskt för att slutföra alla snabb starter eller självstudier.

## <a name="create-an-account"></a>Skapa ett konto

Följande steg krävs för att skapa ett konto för tjänsten Azure Remote rendering:

1. Gå till [förhands gransknings sidan för Mixad verklighet](https://aka.ms/MixedRealityPrivatePreview)
1. Klicka på knappen "skapa en resurs"
1. Skriv "fjärrrendering" i Sök fältet ("Sök på Marketplace") och tryck på RETUR.
1. I resultat listan klickar du på panelen fjärrrendering
1. Klicka på knappen "skapa" på nästa skärm. Ett formulär öppnas för att skapa ett nytt konto för fjärrrendering:
    1. Ange resurs namnet till namnet på kontot
    1. Uppdatera prenumerationen om det behövs
    1. Ange resurs grupp i valfri resurs grupp
    1. Välj en region från List rutan plats där resursen ska skapas i. Se anmärkningar om [konto regioner](create-an-account.md#account-regions) nedan.
1. När kontot har skapats navigerar du till det och:
    1. På fliken *Översikt* noterar du "konto-ID"
    1. På fliken *inställningar > åtkomst nycklar* noterar du "primär nyckel", det här är kontots hemliga konto nyckel

### <a name="account-regions"></a>Konto regioner
Den plats som anges när kontot skapas när ett konto skapas avgör vilken region som konto resursen är tilldelad till. Detta kan inte ändras efter att det har skapats. Kontot kan dock användas för att ansluta till en fjärrstyrningssession i en [region som stöds](./../reference/regions.md), oavsett kontots plats.

### <a name="retrieve-the-account-information"></a>Hämta konto informationen

Exempel och självstudier kräver att du anger konto-ID och en nyckel. Till exempel i **arrconfig.jspå** en fil som används för PowerShell-exempel skript:

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

Se [listan över tillgängliga regioner](../reference/regions.md) för att fylla i alternativet *region* .

Värdena för **`arrAccountId`** och **`arrAccountKey`** finns i portalen enligt beskrivningen i följande steg:

* Gå till [Azure Portal](https://www.portal.azure.com)
* Hitta ditt **"Fjärråter givnings konto"** – det ska vara i listan **"senaste resurser"** . Du kan också söka efter den i Sök fältet längst upp. I så fall kontrollerar du att den prenumeration du vill använda är markerad i standard prenumerations filtret (filter ikon bredvid Sök fältet):

![Prenumerations filter](./media/azure-subscription-filter.png)

Genom att klicka på ditt konto får du till gång till den här skärmen, som visar  **konto-ID: t** omedelbart:

![ID för Azure-konto](./media/azure-account-id.png)

För nyckeln väljer du **åtkomst nycklar** i panelen till vänster. Nästa sida visar en primär och en sekundär nyckel:

![Åtkomst nycklar för Azure](./media/azure-account-primary-key.png)

Värdet för **`arrAccountKey`** kan vara antingen primär eller sekundär nyckel.

## <a name="link-storage-accounts"></a>Länka lagrings konton

I det här stycket förklaras hur du länkar lagrings konton till ditt konto för fjärrrendering. När ett lagrings konto är länkat behöver du inte skapa en SAS-URI varje gång du vill interagera med data i ditt konto, till exempel när du läser in en modell. I stället kan du använda lagrings kontots namn direkt enligt beskrivningen i [avsnittet läsa in en modell](../concepts/models.md#loading-models).

Stegen i det här stycket måste utföras för varje lagrings konto som ska använda den här åtkomst metoden. Om du inte har skapat lagrings konton än kan du gå igenom respektive steg i avsnittet [konvertera en modell för att rendera snabb start](../quickstarts/convert-model.md#storage-account-creation).

Nu förutsätts det att du har ett lagrings konto. Navigera till lagrings kontot i portalen och gå till fliken **Access Control (IAM)** för det lagrings kontot:

![IAM för lagrings konto](./media/azure-storage-account.png)

Se till att du har ägar behörigheter över det här lagrings kontot för att säkerställa att du kan lägga till roll tilldelningar. Om du inte har åtkomst kommer alternativet **Lägg till en roll tilldelning** att inaktive ras.

Klicka på knappen **Lägg till** i panelen Lägg till en roll tilldelning för att lägga till rollen.

![Lagrings konto IAM Lägg till roll tilldelning](./media/azure-add-role-assignment.png)

* Tilldela rollen **Storage BLOB data Contributor** så som visas på skärm bilden ovan.
* Välj **fjärrstyrt konto**  system tilldelad hanterad identitet från List rutan **tilldela åtkomst till** .
* Välj din prenumeration och fjärråter givnings konto i de sista List rutorna.
* Klicka på Spara för att spara ändringarna.

> [!WARNING]
> Om ditt konto för fjärrrendering inte visas i listan, se det här [fel söknings avsnittet](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

> [!IMPORTANT]
> Azure Role-tilldelningar cachelagras av Azure Storage, så det kan finnas en fördröjning på upp till 30 minuter mellan när du beviljar åtkomst till ditt konto för fjärrrendering och när det kan användas för åtkomst till ditt lagrings konto. Mer information finns i [dokumentationen för rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected) .

## <a name="next-steps"></a>Nästa steg

* [Autentisering](authentication.md)
* [Använda Azure-frontend-API: er för autentisering](frontend-apis.md)
* [PowerShell-exempelskript](../samples/powershell-example-scripts.md)