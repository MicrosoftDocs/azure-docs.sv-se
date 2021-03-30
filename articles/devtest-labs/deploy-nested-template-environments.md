---
title: Distribuera miljöer med kapslade mallar i Azure DevTest Labs
description: Lär dig hur du distribuerar kapslade Azure Resource Manager mallar för att tillhandahålla miljöer med Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 39002e286fafd4f813333a14ed86256a517897e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481348"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Distribuera kapslade Azure Resource Manager mallar för test miljöer
Med en kapslad distribution kan du köra andra Azure Resource Manager mallar inifrån en huvud resurs hanterings mall. Det gör att du kan dela upp distributionen i en uppsättning riktade och språkspecifika mallar. Det ger fördelar avseende testning, åter användning och läsbarhet. Artikeln [med länkade mallar när du distribuerar Azure-resurser](../azure-resource-manager/templates/linked-templates.md) ger en bättre översikt över den här lösningen med flera kod exempel. Den här artikeln innehåller ett exempel som är speciellt för Azure DevTest Labs. 

## <a name="key-parameters"></a>Nyckel parametrar
Även om du kan skapa en egen Resource Manager-mall från början, rekommenderar vi att du använder [Azures resurs grupps projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) i Visual Studio, vilket gör det enkelt att utveckla och felsöka mallar. När du lägger till en kapslad distributions resurs för att azuredeploy.jspå, lägger Visual Studio till flera objekt för att göra mallen mer flexibel. Dessa objekt inkluderar undermappen med den sekundära mallen och parameter filen, variabel namn i filen för huvud mal len och två parametrar för lagrings platsen för de nya filerna. **_ArtifactsLocation** och **_artifactsLocationSasToken** är de nyckel parametrar som används av DevTest Labs. 

Om du inte är bekant med hur DevTest Labs fungerar med miljöer kan du läsa [skapa miljöer med flera virtuella datorer och PaaS resurser med Azure Resource Manager mallar](devtest-lab-create-environment-from-arm.md). Mallarna lagras i databasen som är länkad till labbet i DevTest Labs. När du skapar en ny miljö med dessa mallar flyttas filerna till en Azure Storage behållare i labbet. För att kunna identifiera och kopiera de kapslade filerna identifierar DevTest Labs _artifactsLocation och _artifactsLocationSasToken parametrar och kopierar undermapparna till lagrings behållaren. Sedan infogas platsen och SaS-token (signaturen för delad åtkomst) i parametrar automatiskt. 

## <a name="nested-deployment-example"></a>Exempel på kapslad distribution
Här är ett enkelt exempel på en kapslad distribution:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

Mappen i lagrings platsen som innehåller den här mallen har en undermapp `nestedtemplates` med filerna **NestOne.jspå** och **NestOne.parameters.jspå**. I **azuredeploy.jspå** skapas URI för mallen med hjälp av platsen för artefakter, kapslad mall, fil namn för kapslad mall. På samma sätt skapas URI för parametrarna med platsen för artefakter, den kapslade mallen och parameter filen för den kapslade mallen. 

Här är en bild av samma projekt struktur i Visual Studio: 

![Projekt struktur i Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Du kan lägga till fler mappar i den primära mappen men inte djupare än en nivå. 

## <a name="next-steps"></a>Nästa steg
I följande artiklar finns information om miljöer: 

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Anslut en miljö till ditt labbs virtuella nätverk i Azure DevTest Labs](connect-environment-lab-virtual-network.md)