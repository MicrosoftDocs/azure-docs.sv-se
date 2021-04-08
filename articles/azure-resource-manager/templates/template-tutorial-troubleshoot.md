---
title: Felsöka distributioner
description: Lär dig hur du övervakar och felsöker Azure Resource Manager mall (ARM-mall) distributioner. Visar aktivitets loggar och distributions historik.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 31c4e6383b5eaea2bb66dc1baafa0fbff4918a7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589125"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Självstudie: Felsöka distributioner av ARM-mallar

Lär dig hur du felsöker distributions fel för Azure Resource Manager mall (ARM-mall). I den här självstudien konfigurerar du två fel i en mall och lär dig hur du använder aktivitetsloggar och distributionshistoriken för att lösa problemen.

Det finns två typer av fel som rör malldistribution:

- **Valideringsfel** uppstår från scenarier som kan fastställas före distributionen. De innehåller syntaxfel i mallen eller försöker distribuera resurser som skulle överskrida prenumerationskvoterna.
- **Distributionsfel** uppstår från förhållanden som inträffar under distributionsprocessen. De omfattar försök att få åtkomst till en resurs som distribueras parallellt.

Båda typerna av fel returnerar en felkod som du använder för att felsöka distributionen. Båda typerna av fel visas i aktivitetsloggen. Dock visas valideringsfel inte i distributionshistoriken eftersom distributionen aldrig startades.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> - Skapa en problemmall
> - Felsöka valideringsfel
> - Felsöka vanliga distributionsfel
> - Rensa resurser

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa stegen i den här artikeln behöver du:

- Visual Studio Code med Resource Manager Tools-tillägg. Se [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="create-a-problematic-template"></a>Skapa en problemmall

Öppna en mall som heter [skapa ett standard lagrings konto](https://azure.microsoft.com/resources/templates/101-storage-account-create/) från [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/)och konfigurera två problem med mallar.

1. Från Visual Studio **Code väljer du**  >  **Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen.
4. Ändra `apiVersion` raden till följande rad:

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - `apiVersion1` är ett ogiltigt element namn. Det är ett valideringsfel.
    - API-versionen är `"2018-07-01"` .  Det är ett distributionsfel.

5. Välj **Arkiv**  >  **Spara som** för att spara filen som _azuredeploy.jspå_ den lokala datorn.

## <a name="troubleshoot-the-validation-error"></a>Felsöka valideringsfelet

Se avsnittet [Distribuera mallen](template-tutorial-create-multiple-instances.md#deploy-the-template) för distribution av mallen.

Du bör få ett fel i gränssnittet som liknar:

```azurepowershell
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Fel meddelandet anger att problemet är med `apiVersion1` .

Använd Visual Studio Code för att åtgärda problemet genom att ändra `apiVersion1` till `apiVersion` och sedan spara mallen.

## <a name="troubleshoot-the-deployment-error"></a>Felsöka distributionsfelet

Se avsnittet [Distribuera mallen](template-tutorial-create-multiple-instances.md#deploy-the-template) för distribution av mallen.

Du bör få ett fel i gränssnittet som liknar:

```azurepowershell
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Distributionsfelet kan hittas i Azure-portalen med följande procedur:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna resursgruppen genom att välja **Resursgrupper** och sedan resursgruppens namn. Du bör se **1 Failed** (misslyckades) under **Distribution**.

    ![Skärm bild som markerar den misslyckade distributionen.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Välj **Felinformation**.

    ![Skärm bild som markerar länken fel information.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Felmeddelandet är samma som det som visades tidigare:

    ![Skärm bild som visar fel informationen.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Du kan även hitta felet i aktivitetsloggarna:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **övervaka**  >  **aktivitets logg**.
3. Använd filtren för att hitta loggen.

    ![Självstudie om att felsöka Resource Manager](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Använd Visual Studio Code för att åtgärda problemet och omdistribuera sedan mallen.

En lista över vanliga fel finns i avsnittet om att [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du felsöker distributions fel för ARM-mallar.  Mer information finns i avsnittet om att [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).
