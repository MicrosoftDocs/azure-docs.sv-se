---
title: 'Snabb start: dela utanför din organisation – Azure-Dataresurs'
description: Snabb start – dela data med kunder och partner med Azure Data Resource och Resource Manager-mall
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/17/2020
ms.openlocfilehash: 62c800e8da3ab4f99b0933e286debcb05c5c3e22
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88575699"
---
# <a name="tutorial-share-data-using-azure-data-share-and-resource-manager-templates"></a>Självstudie: dela data med Azure Data Resource och Resource Manager-mallar

Lär dig hur du konfigurerar en ny Azure-Dataresurs från ett Azure Storage-konto genom att använda Azure Resource Manager mall och börja dela data med kunder och partner utanför Azure-organisationen. En lista över data lager som stöds finns i [data lager som stöds i Azure Data Share](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/).

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

Följande resurser definieras i mallen:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft. Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft. Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft. DataShare/konton](/rest/api/datashare/accounts/create)
* [Microsoft. DataShare/konton/resurser](/rest/api/datashare/shares/create)
* [Microsoft. DataShare/konton/resurser/data uppsättningar](/rest/api/datashare/datasets/create)
* [Microsoft. DataShare/konton/resurser/inbjudningar](/rest/api/datashare/invitations/create)
* [Microsoft. DataShare/Accounts/Shares/synchronizationSettings](/rest/api/datashare/synchronizationsettings/create)

Mallen utför följande uppgifter:

* Skapa ett lagrings konto och en behållare som används som data källa för data resursen.
* Skapa ett data resurs konto och en data resurs.
* Skapa en roll tilldelning som ger Storage BLOB data Reader-rollen till käll data resursens resurs. Se [roller och krav för Azure Data Share](./concepts-roles-permissions.md).
* Lägg till en data uppsättning i datum resursen.
* Lägg till mottagare i data resursen.
* Aktivera ett ögonblicks bild schema för data resursen.

Den här mallen skapas i inlärnings syfte. I praktiken har du vanligt vis vissa data i ett befintligt lagrings konto. Du måste skapa roll tilldelningen innan du kör en mall eller ett skript för att skapa data uppsättningen. Ibland kan du få följande fel meddelande när du distribuerar mallen:

```error message
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Det beror på att distributionen försöker skapa data uppsättningen innan RBAC-tilldelningen har slutförts. Trots fel meddelandet lyckades distributionen.  Du kan fortfarande gå igenom [granskade distribuerade resurser](#review-deployed-resources).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna mallen.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Välj eller ange följande värden:

    * **Prenumeration**: Välj en Azure-prenumeration som används för att skapa data resursen och de andra resurserna.
    * **Resurs grupp**: Välj **Skapa ny** för att skapa en ny resurs grupp eller Välj en befintlig resurs grupp.
    * **Plats**: Välj en plats för resursgruppen.
    * **Projekt namn**: Ange ett projekt namn.  Projekt namnet används för att generera resurs namn.  Se variabel definitionerna i föregående mall.
    * **plats**: Välj en plats för resurserna.  Du kan använda samma plats för resurs gruppen.
    * **E-postadress till inbjudan**: Ange mottagarens e-postadress till data resursen.  E-postalias fungerar inte.

    Använd standardvärdet för resten av inställningarna.
1. Välj **Jag accepterar de villkor som anges ovan**och välj **köp**.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna det data resurs konto som du har skapat.
1. Välj **skicka resurser**på den vänstra menyn.  Du ser det lagrings konto som anges i listan.
1. Välj lagrings konto.  Under **Detaljer**visas synkroniseringsinställningarna som du konfigurerade i mallen.

    ![Synkroniseringsinställningar för Azure Data resurs lagrings konto](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Välj **inbjudningar** överst. Du ska se den e-postadress som du angav när du distribuerar mallen. **Statusen** är **väntande**.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du lära dig hur du skapar en Azure-Dataresurs och bjuder in mottagare. Om du vill veta mer om hur en data konsument kan godkänna och ta emot en data resurs fortsätter du till kursen för att [godkänna och ta emot data](subscribe-to-data-share.md) .
