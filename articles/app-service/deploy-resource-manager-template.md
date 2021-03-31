---
title: Distribuera appar med mallar
description: Hitta vägledning om hur du skapar Azure Resource Manager mallar för att etablera och distribuera App Service appar.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1146b5979d81b91c6c6894aa54b2e0ca50c896c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961626"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Vägledning om hur du distribuerar webbappar med hjälp av Azure Resource Manager-mallar

Den här artikeln innehåller rekommendationer för att skapa Azure Resource Manager mallar för att distribuera Azure App Service lösningar. Dessa rekommendationer kan hjälpa dig att undvika vanliga problem.

## <a name="define-dependencies"></a>Definiera beroenden

Att definiera beroenden för Web Apps kräver en förståelse för hur resurserna i en webbapp interagerar. Om du anger beroenden i en felaktig ordning kan du orsaka distributions fel eller skapa ett tävlings villkor som stannar på distributionen.

> [!WARNING]
> Om du inkluderar ett MSDeploy webbplats tillägg i mallen måste du ange konfigurations resurser som beroende av MSDeploy-resursen. Konfigurations ändringar gör att webbplatsen startas om asynkront. Genom att göra konfigurations resurserna beroende av MSDeploy ser du till att MSDeploy slutförs innan platsen startas om. Utan dessa beroenden kan platsen startas om under distributions processen för MSDeploy. En exempel-mall finns i [Wordpress-mall med webb distributions beroende](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Följande bild visar beroende ordningen för olika App Service resurser:

![Webb program beroenden](media/web-sites-rm-template-guidance/web-dependencies.png)

Du distribuerar resurser i följande ordning:

**Nivå 1**
* App Service plan.
* Andra relaterade resurser, t. ex. databaser eller lagrings konton.

**Nivå 2**
* Webbapp – är beroende av App Service plan.
* Azure Application insikter som är mål för Server gruppen – är beroende av App Service plan.

**Nivå 3**
* Käll kontroll--är beroende av webbappen.
* MSDeploy webbplats tillägg – är beroende av webbappen.
* Azure Application Insights-instans som är riktad mot webbappen – är beroende av webbappen.

**Nivå 4**
* App Service certifikat – är beroende av käll kontroll eller MSDeploy om det finns något. Annars beror det på webbappen.
* Konfigurations inställningar (anslutnings strängar, web.config värden, appinställningar)--beror på käll kontroll eller MSDeploy om det finns någon. Annars beror det på webbappen.

**Nivå 5**
* Bindningar för värdnamn – beroende på certifikatet om det finns. Annars beror det på en resurs på en högre nivå.
* Webbplats tillägg – är beroende av konfigurations inställningar om det finns. Annars beror det på en resurs på en högre nivå.

Vanligt vis innehåller din lösning bara några av dessa resurser och nivåer. Mappa lägre resurser till nästa högre nivå för saknade nivåer.

I följande exempel visas en del av en mall. Värdet för konfigurationen av anslutnings strängen beror på MSDeploy-tillägget. MSDeploy-tillägget är beroende av webbappen och databasen. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Ett klart att köra-exempel som använder koden ovan finns i [mall: skapa en enkel Umbraco-webbapp](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Hitta information om MSDeploy-fel

Om din Resource Manager-mall använder MSDeploy kan det vara svårt att förstå distributions fel meddelandena. Om du vill ha mer information efter en misslyckad distribution kan du prova följande steg:

1. Gå till platsens [kudu-konsol](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Bläddra till mappen på D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Leta efter appManagerStatus.xml och appManagerLog.xml filer. Den första filen loggar status. Den andra filen loggar information om felet. Om felet inte är klart för dig kan du inkludera det när du ber om hjälp i [forumet](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Välj ett unikt webb program namn

Namnet på din webbapp måste vara globalt unikt. Du kan använda en namngivnings konvention som sannolikt är unik, eller så kan du använda [funktionen uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) för att hjälpa till med att generera ett unikt namn.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Distribuera webb program certifikat från Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om din mall innehåller en [Microsoft. Web/certificates-](/azure/templates/microsoft.web/certificates) resurs för TLS/SSL-bindning och certifikatet lagras i en Key Vault, måste du kontrol lera att App Service identiteten har åtkomst till certifikatet.

I Global Azure har App Service tjänstens huvud namn ID: t för **abfa0a7c-a6b6-4736-8310-5855508787cd**. Om du vill bevilja åtkomst till Key Vault för App Service tjänstens huvud namn använder du:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

I Azure Government har App Service tjänstens huvud namn ID: t för **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Använd detta ID i föregående exempel.

I Key Vault väljer du **certifikat** och **genererar/importera** för att ladda upp certifikatet.

![Importera certifikatet](media/web-sites-rm-template-guidance/import-certificate.png)

Ange namnet på certifikatet för i mallen `keyVaultSecretName` .

En exempel-mall finns i [distribuera ett webb program certifikat från Key Vault hemlighet och använda det för att skapa en SSL-bindning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Nästa steg

* En själv studie kurs om hur du distribuerar webbappar med en mall finns i [etablera och distribuera mikrotjänster förutsägbart i Azure](deploy-complex-application-predictably.md).
* Mer information om JSON-syntax och egenskaper för resurs typer i mallar finns [Azure Resource Manager mall referens](/azure/templates/).