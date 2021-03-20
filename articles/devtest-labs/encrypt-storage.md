---
title: Kryptera ett Azure Storage-konto som används av ett labb i Azure DevTest Labs
description: Lär dig hur du konfigurerar kryptering av en Azure-lagring som används av ett labb i Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92149318"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Kryptera Azure Storage som används av ett labb i Azure DevTest Labs
Alla labb som skapats i Azure DevTest Labs skapas med ett associerat Azure Storage-konto. Lagrings kontot används i följande syfte: 

- Lagring av [formel](devtest-lab-manage-formulas.md) dokument som kan användas för att skapa virtuella datorer.
- Att lagra artefakt resultat som innehåller distributions-och tilläggs loggar som genereras från att tillämpa artefakter. 
- [Laddar upp virtuella hård diskar (VHD) för att skapa anpassade avbildningar i labbet.](devtest-lab-create-template.md)
- Cachelagra ofta använda [artefakter](add-artifact-vm.md) och [Azure Resource Manager mallar](devtest-lab-create-environment-from-arm.md) för snabbare hämtning när du skapar en virtuell dator/miljö.

> [!NOTE]
> Informationen ovan är avgörande för att labbet ska kunna användas. Den lagras i Labbets livs cykel (och labb resurser) om det inte tas bort uttryckligen. Om du tar bort dessa resurser manuellt kan det leda till fel när du skapar virtuella labb datorer och/eller formler blir skadade för framtida bruk. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Hitta lagrings kontot och visa dess innehåll

1. På Start sidan för labbet väljer du **resurs gruppen** på sidan **Översikt** . Du bör se sidan **resurs grupp** för resurs gruppen som innehåller labbet. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Välj resurs grupp på sidan Översikt":::
1. Välj Azure Storage-kontot för labbet. Namn konventionen för labb lagrings kontot är: `a<labNameWithoutInvalidCharacters><4-digit number>` . Om Lab-namnet t. ex. är det `contosolab` kan lagrings konto namnet vara `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Välj lagrings konto i resurs gruppen i labbet":::
3. På sidan **lagrings konto** väljer du **Storage Explorer (för hands version)** på den vänstra menyn och väljer sedan **BLOB-behållare** för att hitta relevant labb relaterat innehåll. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Lagringsutforskaren (förhandsversion)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Kryptera ditt labb lagrings konto
Azure Storage krypterar dina data automatiskt när de sparas i molnet. Azure Storage kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Mer information finns i [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md).

Data i labb lagrings kontot krypteras med en **Microsoft-hanterad nyckel**. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar för labbets lagrings konto kan du ange en **kundhanterad nyckel** med Azure Key Vault som ska användas för att kryptera/dekryptera data i Blob Storage och i Azure Files. Mer information om Kundhanterade nycklar finns i [använda Kundhanterade nycklar med Azure Key Vault för att hantera Azure Storage kryptering](../storage/common/customer-managed-keys-overview.md).

Information om hur du konfigurerar Kundhanterade nycklar för Azure Storage kryptering finns i följande artiklar: 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Hantera Azure Blob Storage-livs cykeln
Som nämnts är informationen som lagras i labb lagrings kontot avgörande för att labbet ska kunna köras utan fel. Om du inte uttryckligen tar bort den kommer dessa data fortfarande att finnas kvar i Labbets lagrings konto under Labbets livs cykel eller livs längd för vissa virtuella labb datorer, beroende på vilken typ av data du har.

### <a name="uploaded-vhds"></a>Överförda virtuella hård diskar
Dessa virtuella hård diskar används för att skapa anpassade avbildningar. Om du tar bort dem går det inte längre att skapa anpassade avbildningar från de här virtuella hård diskarna.

### <a name="artifacts-cache"></a>Artefakt cache
Dessa cacheminnen kommer att återskapas varje gång artefakter tillämpas. De kommer att uppdateras med det senaste innehållet från respektive refererade databaser. Så om du tar bort den här informationen för att spara säkerhetsrelaterade utgifter, är befrielsen tillfällig.

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager mall-cache
Dessa cacheminnen återskapas varje gång Azure Resource Manager-baserade mallfiler är anslutna och kan anpassas i labbet. De kommer att uppdateras med det senaste innehållet från respektive refererade databaser. Så om du tar bort den här informationen för att spara säkerhetsrelaterade utgifter, är befrielsen tillfällig.

### <a name="formulas"></a>Formler
Dessa dokument används för att stödja alternativet att både skapa formler från befintliga virtuella datorer och att skapa virtuella datorer från formler. Att ta bort dessa formel dokument kan leda till fel när du utför följande åtgärder:

- Skapa en formel från en befintlig virtuell labb dator
- Skapa eller uppdatera formler 
- Skapa en virtuell dator från en formel.

### <a name="artifact-results"></a>Artefakt resultat
När artefakter används kan storleken på respektive artefakt resultat öka över tiden beroende på antalet och typen av artefakter som körs på virtuella labb datorer. Så som labb ägare kanske du vill kontrol lera livs cykeln för dessa dokument. Mer information finns i [Hantera Azure Blob Storage-livscykeln](../storage/blobs/storage-lifecycle-management-concepts.md).

> [!IMPORTANT]
> Vi rekommenderar att du gör det här steget för att minska kostnaderna som är associerade med Azure Storage-kontot. 

Följande regel används till exempel för att ange en utgångs regel för 90 dagar specifikt för artefakt resultat. Det garanterar att äldre artefakt resultat återvinns från lagrings kontot på en vanlig takt.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
Information om hur du konfigurerar Kundhanterade nycklar för Azure Storage kryptering finns i följande artiklar: 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)