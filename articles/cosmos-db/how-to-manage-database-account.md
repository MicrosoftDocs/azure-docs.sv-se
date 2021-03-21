---
title: Lär dig hur du hanterar databaskonton i Azure Cosmos DB
description: Lär dig hur du hanterar Azure Cosmos DB-resurser med hjälp av mallarna Azure Portal, PowerShell, CLI och Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.openlocfilehash: d542e2b4e5db86fd3354514790e718f0694a09a5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489760"
---
# <a name="manage-an-azure-cosmos-account"></a>Hantera ett Azure Cosmos-konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver hur du hanterar olika uppgifter i ett Azure Cosmos-konto med hjälp av Azure-portalen, Azure PowerShell, Azure CLI och Azure Resource Manager-mallar.

## <a name="create-an-account"></a>Skapa ett konto

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Information om hur du [skapar ett Azure Cosmos DB konto med Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Information om hur du [skapar ett Azure Cosmos DB konto med PowerShell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager-mall

Information om hur du [skapar Azure Cosmos DB konto med Azure Resource Manager mallar](./manage-with-templates.md)

## <a name="addremove-regions-from-your-database-account"></a>Lägga till/ta bort regioner från ditt databaskonto

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till ditt Azure Cosmos-konto och öppna menyn **replikera data globalt** .

1. Om du vill lägga till regioner väljer du sexhörningarna på kartan med den **+** etikett som motsvarar din önskade region (er). Om du vill lägga till en region väljer du alternativet **+ Lägg till region** och väljer en region i den nedrullningsbara menyn.

1. Om du vill ta bort regioner avmarkerar du en eller flera regioner från kartan genom att välja de blå sexhörningarna med kryssmarkeringar. Eller välj ”papperskorgsikonen” (🗑) intill regionen på höger sida.

1. Spara ändringarna genom att välja **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Lägga till eller ta bort regionsmenyn":::

I skrivläge för en enstaka region kan du inte ta bort skrivregionen. Du måste redundansväxla till en annan region innan du kan ta bort den aktuella skrivregionen.

I skrivläge för flera regioner kan du lägga till eller ta bort vilka regioner som helst förutsatt att du har minst en region.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

Se [lägga till eller ta bort regioner med Azure CLI](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Se [lägga till eller ta bort regioner med PowerShell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Konfigurera flera skrivregioner

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure Portal

Öppna fliken **replikera data globalt** och välj **Aktivera** för att aktivera flera regioner. När du har aktiverat skrivningar i flera regioner blir alla Läs regioner som du för närvarande har på kontot Läs-och skriv regioner.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Azure Cosmos-konto konfigurerar skärm bild för flera regioner":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Se [aktivera flera-Write-regioner med Azure CLI](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Se [aktivera flera-Write-regioner med PowerShell](manage-with-powershell.md#multi-region-writes)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Resource Manager-mall

Ett konto kan migreras från en enda Skriv region till flera Skriv regioner genom att distribuera Resource Manager-mallen som används för att skapa kontot och inställningen `enableMultipleWriteLocations: true` . Följande Azure Resource Manager mall är en minimal mall som används för att distribuera ett Azure Cosmos-konto för SQL-API med två regioner och flera Skriv platser är aktiverade.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Aktivera automatisk redundans för ditt Azure Cosmos-konto

Med alternativet automatisk redundans kan Azure Cosmos DB redundansväxla till den region som har den högsta prioriteten för redundans utan användar åtgärd om en region blir otillgänglig. När automatisk redundans har Aktiver ATS kan du ändra region prioriteten. Kontot måste ha två eller flera regioner för att aktivera automatisk redundans.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Öppna fönstret **replikera data globalt** från ditt Azure Cosmos-konto.

2. Längst upp i fönsterrutan väljer du **Automatisk redundans**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menyn Replikera data globalt":::

3. I fönsterrutan **Automatisk redundans** ser du till att **Aktivera automatisk redundans** är inställt på **PÅ**. 

4. Välj **Spara**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menyn Automatisk redundans i portalen":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Läs [Aktivera automatisk redundans med Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Läs [Aktivera automatisk redundans med PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ange redundansprioritet för ditt Azure Cosmos-konto

När ett Cosmos-konto har kon figurer ATS för automatisk redundans kan växlings prioriteten för regioner ändras.

> [!IMPORTANT]
> Du kan inte ändra Skriv regionen (växlings prioriteten är noll) när kontot har kon figurer ATS för automatisk redundans. Om du vill ändra Skriv regionen måste du inaktivera automatisk redundans och göra en manuell redundansväxling.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Öppna fönstret **replikera data globalt** från ditt Azure Cosmos-konto.

2. Längst upp i fönsterrutan väljer du **Automatisk redundans**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menyn Replikera data globalt":::

3. I fönsterrutan **Automatisk redundans** ser du till att **Aktivera automatisk redundans** är inställt på **PÅ**.

4. Du ändrar redundansprioritet genom att dra läsregionerna via de tre punkterna till vänster om raden som visas när du hovrar över dem.

5. Välj **Spara**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menyn Automatisk redundans i portalen":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Se [ange prioritet för redundans med Azure CLI](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Se [ange prioritet för redundans med PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Utför manuell redundansväxling för ett Azure Cosmos-konto

> [!IMPORTANT]
> Azure Cosmos-kontot måste konfigureras för manuell redundansväxling för att åtgärden ska lyckas.

Processen för att utföra en manuell redundansväxling innebär att ändra kontots Skriv region (redundans prioritet = 0) till en annan region som kon figurer ATS för kontot.

> [!NOTE]
> Konton med flera Skriv regioner kan inte växlas över manuellt. För program som använder Azure Cosmos SDK identifierar SDK när en region blir otillgänglig och dirigerar sedan automatiskt till nästa närmaste region.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Gå till ditt Azure Cosmos-konto och öppna menyn **replikera data globalt** .

2. Längst upp på menyn väljer du **Manuell redundans**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menyn Replikera data globalt":::

3. På menyn **Manuell redundans** väljer du din nya skrivregion. Markera kryssrutan för att bekräfta att du förstår att det här alternativet ändrar din skrivregion.

4. Utlös redundansväxlingen genom att välja **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Menyn Manuell redundans i portalen":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Se [Utlös manuell redundans med Azure CLI](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Se [Utlös manuell redundans med PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du hanterar Azure Cosmos-kontot samt databas och behållare finns i följande artiklar:

* [Hantera Azure Cosmos DB med Azure PowerShell](manage-with-powershell.md)
* [Hantera Azure Cosmos DB med Azure CLI](manage-with-cli.md)