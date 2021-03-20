---
title: Identifiera resurs egenskaper
description: Beskriver hur du söker efter resurs egenskaper.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327756"
---
# <a name="discover-resource-properties"></a>Identifiera resurs egenskaper

Innan du skapar Resource Manager-mallar måste du förstå vilka resurs typer som är tillgängliga och vilka värden som ska användas i mallen. I den här artikeln visas några exempel på hur du kan hitta de egenskaper som ska ingå i mallen.

## <a name="find-resource-provider-namespaces"></a>Hitta resurs leverantörens namn områden

Resurser i en ARM-mall definieras med ett namn område för en resurs leverantör och en resurs typ. Till exempel är Microsoft. Storage/storageAccounts det fullständiga namnet på lagrings kontots resurs typ. Microsoft. Storage är namn området. Om du inte redan känner till namn områdena för de resurs typer som du vill använda, se [Resource providers för Azure-tjänster](../management/azure-services-resource-providers.md).

![Mappning av namnrymd för resurs hanterarens resurs leverantör](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>Exportera mallar

Det enklaste sättet att hämta mallens egenskaper för dina befintliga resurser är att exportera mallen. Mer information finns i [en och flera-resurs-export till en mall i Azure Portal](./export-template-portal.md).

## <a name="use-resource-manager-tools-extension"></a>Använd tillägg för Resource Manager-verktyg

Med Visual Studio Code och Azure Resource Manager Tools-tillägget kan du se exakt vilka egenskaper som behövs för varje resurs typ. De innehåller IntelliSense och kodfragment som fören klar hur du definierar en resurs i mallen. Mer information finns i [snabb start: skapa Azure Resource Manager mallar med Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource).

Följande skärm bild visar en lagrings konto resurs som läggs till i en mall:

![Program tillägg för Resource Manager-verktyg](./media/view-resources/resource-manager-tools-extension-snippets.png)

Tillägget innehåller också en lista med alternativ för konfigurations egenskaperna.

![Konfigurations bara värden för Resource Manager-verktyg](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>Använda mallreferens

Referensen till Azure Resource Manager mal len är den mest omfattande resursen för mall-schemat. Du kan hitta API-versioner, mallformat och egenskaps information.

1. Bläddra till [Azure Resource Manager mal len referens](/azure/templates/).
1. Välj **Storage** i det vänstra navigerings fältet och välj sedan **alla resurser**. Sidan alla resurser sammanfattar resurs typerna och versionerna.

    ![referens resurs versioner för mall](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    Om du känner till resurs typen kan du gå direkt till den här sidan med följande URL-format: `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` .

1. Välj den senaste versionen. Vi rekommenderar att du använder den senaste API-versionen.

    I avsnittet **mall format** visas alla egenskaper för lagrings kontot. **SKU** finns i listan:

    ![mall referens lagrings konto format](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    Rulla ned för att se **SKU-objekt** i avsnittet **egenskaps värden** . I artikeln visas tillåtna värden för SKU-namn:

    ![mall referens för lagrings kontots SKU-värden](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    I slutet av sidan innehåller avsnittet **snabb starts mallar** några Azure snabb starts mallar som innehåller resurs typen:

    ![mall referens lagrings konto snabb starts mallar](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

Mal lin Reference är länkad från var och en av Azure-tjänstens dokumentations webbplatser.  Till exempel [Key Vault dokumentations webbplats](../../key-vault/general/overview.md):

![Referens Key Vault för Resource Manager-mall](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>Använd Resursläsaren

Resursläsaren är inbäddat i Azure Portal. Innan du använder den här metoden behöver du ett lagrings konto. Om du inte har någon kan du skapa en genom att klicka på följande knapp:

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I rutan Sök anger du **resurs läsaren** och väljer sedan **Resursläsaren**.

    ![Skärm bild som visar sökning efter Resursläsaren i Azure Portal.](./media/view-resources/azure-portal-resource-explorer.png)

1. Från vänster expanderar du **prenumerationer** och expanderar sedan din Azure-prenumeration. Du kan hitta lagrings kontot under antingen **providers** eller **ResourceGroups**.

    ![Azure Portal Resursläsaren](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **Providers**: Expandera **providers**  ->  **Microsoft. Storage**  ->  **storageAccounts** och välj sedan ditt lagrings konto.
    - **ResourceGroups**: Välj den resurs grupp som innehåller lagrings kontot, Välj **resurser** och välj sedan lagrings kontot.

    Till höger ser du SKU-konfigurationen för det befintliga lagrings kontot som liknar:

    ![Azure Portal Resursläsaren lagrings kontots SKU](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Använd Resources.azure.com

Resources.azure.com är en offentlig webbplats som alla med en Azure-prenumeration kan komma åt. Den är i för hands version.  Överväg att använda [Resursläsaren](#use-resource-explorer) i stället. Det här verktyget innehåller följande funktioner:

- Upptäck Azure Resource Management-API: erna.
- Hämta API-dokumentation och schema information.
- Gör API-anrop direkt i dina egna prenumerationer.

Du behöver ett lagrings konto för att demonstrera hur du hämtar schema information med hjälp av det här verktyget. Om du inte har någon kan du skapa en genom att klicka på följande knapp:

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Bläddra till [Resources.Azure.com](https://resources.azure.com/). Det tar en stund för verktyget att populär det vänstra fönstret.
1. Välj **prenumerationer**.

    ![resource.azure.com API-mappning](./media/view-resources/resources-azure-com-api-mapping.png)

    Noden till vänster matchar API-anropet till höger. Du kan göra API-anropet genom att välja knappen **Hämta** .
1. Från vänster expanderar du **prenumerationer** och expanderar sedan din Azure-prenumeration. Du kan hitta lagrings kontot under antingen **providers** eller **ResourceGroups**.

    - **Providers**: Expandera **providers**  ->  **Microsoft. Storage**  ->  **storageAccounts** och bläddra sedan till lagrings kontot.
    - **ResourceGroups**: Välj den resurs grupp som innehåller lagrings kontot och välj sedan **resurser**.

    Till höger ser du SKU-konfigurationen för det befintliga lagrings kontot som liknar:

    ![Azure Portal Resursläsaren lagrings kontots SKU](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hittar mallens schema information. Mer information om hur du skapar Resource Manager-mallar finns i [förstå strukturen och syntaxen för ARM-mallar](./template-syntax.md).
