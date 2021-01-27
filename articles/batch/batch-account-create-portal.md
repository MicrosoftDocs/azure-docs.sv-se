---
title: Skapa ett konto i Azure Portal
description: Lär dig hur du skapar ett Azure Batch-konto på Azure-portalen för att köra storskaliga parallella arbetsbelastningar i molnet
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 943fd145dbd4964e0d43d91f726ea5a79fda59de
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896016"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Skapa ett Batch-konto med Azure Portal

I det här avsnittet visas hur du skapar ett [Azure Batch konto](accounts.md) i [Azure Portal](https://portal.azure.com)och väljer de konto egenskaper som passar ditt beräknings scenario. Du lär dig också var du hittar viktiga konto egenskaper som åtkomst nycklar och konto-URL: er.

För bakgrund om batch-konton och scenarier, se [batch-tjänstens arbets flöde och resurser](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På sidan start väljer du **skapa en resurs**.

1. I rutan Sök anger du **Batch-tjänst**. Välj **Batch-tjänst** från resultaten och välj sedan **skapa**.

1. Ange följande information.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Skärm bild av fönstret nytt batch-konto.":::

    a. **Prenumeration**: Prenumerationen som Batch-kontot skapas i. Om du bara har en prenumeration väljs den som standard.

    b. **Resursgrupp**: Välj en befintlig resursgrupp för ditt nya Batch-konto. Du kan också skapa en ny resursgrupp.

    c. **Kontonamn**: Det namn du väljer måste vara unikt i den Azure-region där kontot skapas (se **Plats** nedan). Kontonamnet får bara innehålla gemener eller siffror och måste vara mellan 3 och 24 tecken långt.

    d. **Plats**: Azure-regionen som Batch-kontot skapas i. Endast de regioner som stöds av din prenumeration och resursgrupp visas som alternativ.

    e. **Lagrings konto**: ett valfritt [Azure Storage-konto](accounts.md#azure-storage-accounts) som du associerar med ditt batch-konto. Du kan välja ett befintligt lagrings konto eller skapa ett nytt. Ett v2-lagringskonto för generell användning rekommenderas för bästa prestanda.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Skärm bild av alternativen när du skapar ett lagrings konto.":::

1. Om du vill kan du välja **Avancerat** för att ange **identitets typ**, **offentlig nätverks åtkomst** eller **pool tilldelnings läge**. I de flesta fall är standard alternativen fina.

1. Välj **Granska + skapa** och välj **skapa** för att skapa kontot.

## <a name="view-batch-account-properties"></a>Visa egenskaper för ett Batch-konto

När kontot har skapats kan du välja kontot för att komma åt dess inställningar och egenskaper. Du kan komma åt alla kontoinställningar och kontoegenskaper från den vänstra menyn.

> [!NOTE]
> Namnet på batch-kontot är dess ID och kan inte ändras. Om du behöver ändra namnet på ett batch-konto måste du ta bort kontot och skapa ett nytt med det avsedda namnet.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Skärm bild av sidan för batch-kontot i Azure Portal.":::

När du utvecklar ett program med [Batch-API:er](batch-apis-tools.md#azure-accounts-for-batch-development) behöver du en konto-URL och en nyckel för att få åtkomst till dina Batch-resurser. (Batch har även stöd för Azure Active Directory autentisering.) Om du vill visa åtkomst information för batch-kontot väljer du **nycklar**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Skärm bild av batch-kontots nycklar i Azure Portal.":::

Om du vill visa namnet på och nycklarna för lagringskontot som är associerat med Batch-kontot väljer du **Lagringskonto**.

Om du vill visa de [resurs kvoter](batch-quota-limit.md) som gäller för batch-kontot väljer du **kvoter**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Ytterligare konfiguration för läget Användarprenumeration

Om du väljer att skapa ett Batch-konto i läget Användarprenumeration ska du utföra följande åtgärder innan du skapar kontot.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Tillåt att Azure Batch får åtkomst till prenumerationen (engångsåtgärd)

När du skapar ditt första Batch-konto i användarprenumerationsläge behöver du registrera prenumerationen med Batch. (Om du redan har gjort det kan du gå vidare till nästa avsnitt.)

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **alla tjänster**  >  **prenumerationer** och välj den prenumeration som du vill använda för batch-kontot.

1. På sidan **Prenumeration** väljer du **resursproviders** och sök efter **Microsoft.Batch**. Kontrollera att resursprovidern **Microsoft.Batch** har registrerats i prenumerationen. Om det inte är det väljer du länken **Registrera** högst upp på skärmen.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Skärm bild som visar Microsoft.BatCH-resurs leverantören.":::

1. Gå tillbaka till sidan **prenumeration** och välj sedan **åtkomst kontroll (IAM)**  >  **roll tilldelningar**  >  **Lägg** till  >  **Lägg till roll tilldelning**.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Skärm bild av sidan roll tilldelningar för en prenumeration.":::

1. På sidan **Lägg till roll tilldelning** väljer du rollen **deltagare** eller **ägare** och söker efter batch-API: et. Sök efter **Microsoft Azure Batch** eller **MicrosoftAzureBatch** för att hitta API: et. (**ddbf3205-c6bd-46ae-8127-60eb93363864** är program-ID: t för batch-API: et.)

1. När du har hittat Batch-API:t markerar du det och klickar på **Spara**.

### <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

I användar prenumerations läge krävs en [Azure Key Vault](../key-vault/general/overview.md) . Key Vault måste vara i samma prenumeration och region som det batch-konto som ska skapas.

1. Välj **skapa en resurs** på Start sidan för [Azure Portal](https://portal.azure.com).
1. I rutan Sök anger du **Key Vault**. Välj **Key Vault** från resultaten och välj sedan **skapa**.
1. På sidan **skapa nyckel valv** anger du ett namn för Key Vault och skapar en ny resurs grupp i samma region som du vill använda för ditt batch-konto. Lämna standardvärdena för resten av inställningarna och väljer sedan **Skapa**.

När du skapar batch-kontot i användar prenumerations läge anger du **användar prenumeration** som poolens fördelnings läge, väljer den Key Vault du skapade och markerar kryss rutan för att bevilja Azure Batch åtkomst till Key Vault.

Om du vill bevilja åtkomst till Key Vault manuellt går du till avsnittet **åtkomst principer** i Key Vault och väljer **Lägg till åtkomst princip**. Välj länken bredvid **Välj huvud namn** och sök efter **Microsoft Azure Batch** (program-ID **ddbf3205-c6bd-46ae-8127-60eb93363864**). Välj detta huvud konto och konfigurera sedan de **hemliga behörigheterna** med hjälp av den nedrullningsbara menyn. Azure Batch måste ges minst behörigheterna **Get**, **list**, **set** och **Delete** .

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Skärm bild av val av hemliga behörigheter för Azure Batch":::

Välj **Lägg till** och kontrol lera att kryss rutorna **Azure Virtual Machines för distribution** och **Azure Resource Manager för distribution av mallar** är markerade för den länkade **Key Vault** resursen. Välj **Spara** för att genomföra ändringarna.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Skärm bild av skärmen åtkomst princip.":::

### <a name="configure-subscription-quotas"></a>Konfigurera prenumerationskvoter

För batch-konton för användar prenumerationer måste [kärn kvoter](batch-quota-limit.md) anges manuellt. Standard Batch kärn kvoter gäller inte för konton i användar prenumerations läge och [kvoterna i din prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md) för regionala beräknings kärnor, beräknings kärnor per serie och andra resurser används och tillämpas.

1. I [Azure-portalen](https://portal.azure.com) väljer du ditt Batch-konto för läget för användarprenumeration för att visa dess inställningar och egenskaper.
1. På den vänstra menyn väljer du **Kvoter** för att visa och konfigurera de kärnkvoter som är associerade med ditt Batch-konto.

## <a name="other-batch-account-management-options"></a>Andra alternativ för Batch-kontohantering

Förutom att använda Azure Portal kan du skapa och hantera Batch-konton med verktyg som följande:

- [PowerShell-cmdlets för Batch](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](quick-run-dotnet.md) eller [Python](quick-run-python.md). De här snabb starterna vägleder dig genom ett exempel program som använder batch-tjänsten för att köra en arbets belastning på flera datornoder, med Azure Storage för mellanlagring och hämtning av arbets belastnings filer.