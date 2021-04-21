---
title: Distribuera appar med mallar
description: Få vägledning om hur du skapar Azure Resource Manager för att etablera och distribuera App Service appar.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: ed6edeadfb1c6f73cc10771d4a5328e7bddb3642
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835172"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Vägledning om hur du distribuerar webbappar med hjälp Azure Resource Manager mallar

Den här artikeln innehåller rekommendationer för att skapa Azure Resource Manager mallar för att distribuera Azure App Service lösningar. Dessa rekommendationer kan hjälpa dig att undvika vanliga problem.

## <a name="define-dependencies"></a>Definiera beroenden

Att definiera beroenden för webbappar kräver en förståelse för hur resurserna i en webbapp interagerar. Om du anger beroenden i fel ordning kan du orsaka distributionsfel eller skapa ett rastillstånd som gör att distributionen stoppas.

> [!WARNING]
> Om du inkluderar ett MSDeploy-platstillägg i mallen måste du ange alla konfigurationsresurser som beroende av MSDeploy-resursen. Konfigurationsändringar gör att platsen startas om asynkront. Genom att göra konfigurationsresurserna beroende av MSDeploy ser du till att MSDeploy slutförs innan platsen startas om. Utan dessa beroenden kan platsen startas om under distributionsprocessen för MSDeploy. En exempelmall finns i [WordPress-mall med Web Deploy Dependency](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Följande bild visar beroendeordningen för olika App Service resurser:

![Beroenden för webbapp](media/web-sites-rm-template-guidance/web-dependencies.png)

Du distribuerar resurser i följande ordning:

**Nivå 1**
* App Service plan.
* Andra relaterade resurser, till exempel databaser eller lagringskonton.

**Nivå 2**
* Webbapp – beror på App Service plan.
* Azure Application Insights-instansen som är inriktad på servergruppen – beror på App Service plan.

**Nivå 3**
* Källkontroll – beror på webbappen.
* MSDeploy-webbplatstillägget – beror på webbappen.
* Azure Application Insights-instans som riktar sig mot webbappen – beror på webbappen.

**Nivå 4**
* App Service certifikat – beror på källkontroll eller MSDeploy om någon av dem finns. Annars beror det på webbappen.
* Konfigurationsinställningar (anslutningssträngar, web.config värden, appinställningar) – beror på källkontroll eller MSDeploy om någon av dessa finns. Annars beror det på webbappen.

**Nivå 5**
* Bindningar för värdnamn – beror på certifikatet om det finns. Annars beror det på en resurs på högre nivå.
* Platstillägg – beror på konfigurationsinställningarna om de finns. Annars beror det på en resurs på högre nivå.

Vanligtvis innehåller din lösning bara vissa av dessa resurser och nivåer. För nivåer som saknas mappar du lägre resurser till nästa högre nivå.

I följande exempel visas en del av en mall. Värdet för konfigurationen av anslutningssträngen beror på MSDeploy-tillägget. MSDeploy-tillägget beror på webbappen och databasen. 

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

Ett körklart exempel som använder koden ovan finns i [Mall: Skapa en enkel Umbraco-webbapp.](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)

## <a name="find-information-about-msdeploy-errors"></a>Hitta information om MSDeploy-fel

Om din Resource Manager mallen använder MSDeploy kan det vara svårt att förstå felmeddelandena för distributionen. Om du vill ha mer information efter en misslyckad distribution kan du prova följande steg:

1. Gå till webbplatsens [Kudu-konsol.](https://github.com/projectkudu/kudu/wiki/Kudu-console)
2. Bläddra till mappen på D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Leta efter de appManagerStatus.xml och appManagerLog.xml filerna. Den första filen loggar statusen. Den andra filen loggar information om felet. Om felet inte är tydligt för dig kan du inkludera det när du ber om hjälp i [forumet](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Välj ett unikt webbappnamn

Namnet på din webbapp måste vara globalt unikt. Du kan använda en namngivningskonvention som troligen är unik, eller så kan du använda [funktionen uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) för att generera ett unikt namn.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Distribuera webbappscertifikat från Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om mallen innehåller en [Microsoft.Web/certificates-resurs](/azure/templates/microsoft.web/certificates) för TLS/SSL-bindning och certifikatet lagras i en Key Vault måste du se till att App Service-identiteten har åtkomst till certifikatet.

I globala Azure har App Service-tjänstens huvudnamn **ID:t abfa0a7c-a6b6-4736-8310-585508787cd**. Om du vill bevilja åtkomst Key Vault till App Service tjänstens huvudnamn använder du:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

I Azure Government har App Service-tjänstens huvudnamn **ID:t 6a02c803-dafd-4136-b4c3-5a6f318b4714**. Använd det ID:t i föregående exempel.

I ditt Key Vault väljer du **Certifikat och** **Generera/importera för** att ladda upp certifikatet.

![Importera certifikatet](media/web-sites-rm-template-guidance/import-certificate.png)

I mallen anger du namnet på certifikatet för `keyVaultSecretName` .

En exempelmall finns i Distribuera ett webbappcertifikat från en Key Vault och använda den [för att skapa SSL-bindning.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)

## <a name="next-steps"></a>Nästa steg

* En självstudiekurs om hur du distribuerar webbappar med en mall finns i Etablera och [distribuera mikrotjänster förutsägbart i Azure.](deploy-complex-application-predictably.md)
* Mer information om JSON-syntax och egenskaper för resurstyper i mallar finns i [Azure Resource Manager mallreferens](/azure/templates/).
