---
title: Hanterad app med hanterad identitet
description: Konfigurera hanterat program med hanterad identitet för länkning till befintliga resurser, hantering av Azure-resurser och tillhandahållande av drift identitet för aktivitets loggen.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 277faa2d47df9fddd1762d90d9aa2fb5bf00d4df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82508145"
---
# <a name="azure-managed-application-with-managed-identity"></a>Azure-hanterat program med hanterad identitet

> [!NOTE]
> Stöd för hanterade identiteter för hanterade program finns för närvarande i för hands version. Använd 2018-09-01-Preview API-versionen för att använda hanterad identitet.

Lär dig hur du konfigurerar ett hanterat program så att det innehåller en hanterad identitet. Hanterad identitet kan användas för att ge kunden möjlighet att ge hanterade program åtkomst till ytterligare befintliga resurser. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter i Azure Active Directory (AAD) finns i [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Ditt program kan beviljas två typer av identiteter:

- En **systemtilldelad identitet** är kopplad till ditt program och tas bort om din app tas bort. En app kan bara ha en tilldelad identitet.
- En **användardefinierad identitet** är en fristående Azure-resurs som kan tilldelas till din app. En app kan ha flera användare tilldelade identiteter.

## <a name="how-to-use-managed-identity"></a>Använda hanterad identitet

Hanterad identitet möjliggör många scenarier för hanterade program. Några vanliga scenarier som kan lösas är:

- Distribuera ett hanterat program som är länkat till befintliga Azure-resurser. Ett exempel är att distribuera en virtuell Azure-dator (VM) i det hanterade programmet som är kopplat till ett [befintligt nätverks gränssnitt](../../virtual-network/virtual-network-network-interface-vm.md).
- Bevilja det hanterade programmet och Publisher åtkomst till Azure-resurser utanför den **hanterade resurs gruppen**.
- Tillhandahålla en operationell identitet för hanterade program för aktivitets logg och andra tjänster i Azure.

## <a name="adding-managed-identity"></a>Lägger till hanterad identitet

Att skapa ett hanterat program med en hanterad identitet kräver att ytterligare en egenskap anges på Azure-resursen. I följande exempel visas en exempel på en **identitets** egenskap:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Det finns två vanliga sätt att skapa ett hanterat program med **identitet**: [CreateUIDefinition.jspå](./create-uidefinition-overview.md) och [Azure Resource Manager mallar](../templates/template-syntax.md). För enkla scenarier med enkel skapande bör CreateUIDefinition användas för att aktivera hanterad identitet, eftersom det ger en rikare upplevelse. Men när du hanterar avancerade eller komplexa system som kräver automatiserade eller flera hanterade program distributioner kan du använda mallarna.

### <a name="using-createuidefinition"></a>Använda CreateUIDefinition

Ett hanterat program kan konfigureras med hanterad identitet via [CreateUIDefinition.jspå](./create-uidefinition-overview.md). I [avsnittet utdata](./create-uidefinition-overview.md#outputs) `managedIdentity` kan du använda nyckeln för att åsidosätta egenskapen Identity för mallen för hanterade program. Nedan kommer att aktivera **systemtilldelad** identitet i det hanterade programmet. Mer komplexa identitets objekt kan skapas med hjälp av CreateUIDefinition-element för att be konsumenterna om indata. Dessa indata kan användas för att skapa hanterade program med **användardefinierad identitet**.

```json
"outputs": {
    "managedIdentity": { "Type": "SystemAssigned" }
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>När du ska använda CreateUIDefinition för hanterad identitet

Nedan följer några rekommendationer när du ska använda CreateUIDefinition för att aktivera hanterad identitet för hanterade program.

- Skapandet av det hanterade programmet går via Azure Portal eller Marketplace.
- Den hanterade identiteten kräver komplexa konsument ingångar.
- Den hanterade identiteten krävs när det hanterade programmet skapas.

#### <a name="managed-identity-createuidefinition-control"></a>Hanterad Identity CreateUIDefinition-kontroll

CreateUIDefinition stöder en inbyggd [hanterad identitets kontroll](./microsoft-managedidentity-identityselector.md).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.0.1-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "appName",
            "type": "Microsoft.Common.TextBox",
            "label": "Managed application Name",
            "toolTip": "Managed application instance name",
            "visible": true
          },
          {
            "name": "appIdentity",
            "type": "Microsoft.ManagedIdentity.IdentitySelector",
            "label": "Managed Identity Configuration",
            "toolTip": {
              "systemAssignedIdentity": "Enable system assigned identity to grant the managed application access to additional existing resources.",
              "userAssignedIdentity": "Add user assigned identities to grant the managed application access to additional existing resources."
            },
            "defaultValue": {
              "systemAssignedIdentity": "Off"
            },
            "options": {
              "hideSystemAssignedIdentity": false,
              "hideUserAssignedIdentity": false,
              "readOnlySystemAssignedIdentity": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "applicationResourceName": "[steps('applicationSettings').appName]",
      "location": "[location()]",
      "managedIdentity": "[steps('applicationSettings').appIdentity]"
    }
  }
}
```

![Hanterad identitet CreateUIDefinition](./media/publish-managed-identity/msi-cuid.png)

### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

> [!NOTE]
> Marketplace-hanterade programmallar skapas automatiskt för kunder som går genom Azure Portal skapa upplevelse.
> I dessa scenarier `managedIdentity` måste utmatnings nyckeln på CreateUIDefinition användas för att aktivera identitet.

Den hanterade identiteten kan också aktive ras via Azure Resource Manager mallar. Nedan kommer att aktivera **systemtilldelad** identitet i det hanterade programmet. Mer komplexa identitets objekt kan skapas med hjälp av Azure Resource Manager mallparametrar för att tillhandahålla indata. Dessa indata kan användas för att skapa hanterade program med **användardefinierad identitet**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>När du ska använda Azure Resource Manager mallar för hanterad identitet

Nedan följer några rekommendationer när du ska använda Azure Resource Manager mallar för att aktivera hanterad identitet för hanterade program.

- Hanterade program kan distribueras program mässigt baserat på en mall.
- Anpassade roll tilldelningar för den hanterade identiteten krävs för att etablera det hanterade programmet.
- Det hanterade programmet behöver inte skapa Azure Portal och Marketplace.

#### <a name="systemassigned-template"></a>SystemAssigned-mall

En grundläggande Azure Resource Manager-mall som distribuerar ett hanterat program med **systemtilldelad** identitet.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned-mall

En grundläggande Azure Resource Manager-mall som distribuerar ett hanterat program med en **tilldelad identitet**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Bevilja åtkomst till Azure-resurser

När ett hanterat program beviljas en identitet kan den beviljas åtkomst till befintliga Azure-resurser. Den här processen kan göras via gränssnittet åtkomst kontroll (IAM) i Azure Portal. Namnet på det hanterade programmet eller den **användardefinierade identiteten** kan sökas i för att lägga till en roll tilldelning.

![Lägg till roll tilldelning för hanterat program](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Länka befintliga Azure-resurser

> [!NOTE]
> En **användardefinierad identitet** måste [konfigureras](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) innan det hanterade programmet distribueras. Dessutom stöds endast länkad resurs distribution av hanterade program för **Marketplace** -typen.

Hanterad identitet kan också användas för att distribuera ett hanterat program som kräver åtkomst till befintliga resurser under distributionen. När det hanterade programmet tillhandahålls av kunden kan **användardefinierade identiteter** läggas till för att ge ytterligare auktorisering till **mainTemplate** -distributionen.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Redigera CreateUIDefinition med en länkad resurs

När du länkar distributionen av det hanterade programmet till befintliga resurser måste du ange både den befintliga Azure-resursen och en **tilldelad identitet** med lämplig roll tilldelning för den resursen.

 En exempel-CreateUIDefinition som kräver två indata: ett nätverks gränssnitt resurs-ID och en tilldelad identitets resurs-ID.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Den här CreateUIDefinition.jsskapar en användar upplevelse med två fält. Det första fältet låter användaren ange i Azure-resurs-ID: t för den resurs som länkas till distributionen av hanterade program. Det andra är för en konsument att ange **användar tilldelad identitet** Azure-resurs-ID som har åtkomst till den länkade Azure-resursen. Den genererade upplevelsen skulle se ut så här:

![Exempel på CreateUIDefinition med två indata: ett nätverks gränssnitt resurs-ID och en tilldelad identitets resurs-ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Redigera mainTemplate med en länkad resurs

Förutom att uppdatera CreateUIDefinition, behöver huvud mal len också uppdateras för att godkänna det skickade länkade resurs-ID: t. Huvud mal len kan uppdateras för att godkänna de nya utdata genom att lägga till en ny parameter. Eftersom `managedIdentity` utdata åsidosätter värdet i den genererade mallen för hanterade program skickas det inte till huvudmallen och ska inte ingå i avsnittet parametrar.

Ett exempel på en huvudmall som anger nätverks profilen till ett befintligt nätverks gränssnitt som tillhandahålls av CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Konsumera det hanterade programmet med en länkad resurs

När det hanterade programpaketet har skapats kan det hanterade programmet förbrukas via Azure Portal. Innan det kan förbrukas finns det flera nödvändiga steg.

- En instans av den nödvändiga länkade Azure-resursen måste skapas.
- Den **användare som tilldelats identiteten** måste [skapas och tilldelas roll tilldelningar](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) till den länkade resursen.
- Det befintliga länkade resurs-ID: t och det **användardefinierade identitets** -ID: t anges till CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Åtkomst till den hanterade identitets-token

Token för det hanterade programmet kan nu nås via `listTokens` API: t från utgivarens klient organisation. En exempel förfrågan kan se ut så här:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Begär ande text parametrar:

Parameter | Krävs | Beskrivning
---|---|---
authorizationAudience | *Nej* | Mål resursens app-ID-URI. Det är också `aud` anspråk (Audience) för Utfärdad token. Standardvärdet är " https://management.azure.com/ "
userAssignedIdentities | *Nej* | Listan över användarspecifika hanterade identiteter för att hämta en token för. Om inget anges `listTokens` returnerar token för den systemtilldelade hanterade identiteten.


Ett exempel svar kan se ut så här:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

Svaret kommer att innehålla en matris med tokens under `value` egenskapen:

Parameter | Beskrivning
---|---
access_token | Den begärda åtkomsttoken.
expires_in | Antalet sekunder som åtkomst-token är giltig.
expires_on | TimeSpan när åtkomsttoken upphör att gälla. Detta representeras som antalet sekunder från epoken.
not_before | TimeSpan när åtkomsttoken börjar gälla. Detta representeras som antalet sekunder från epoken.
authorizationAudience | Åtkomsttoken `aud` för (mål gruppen) är begäran om. Detta är samma som det som tillhandahölls i `listTokens` begäran.
resourceId | Azure-resurs-ID för Utfärdad token. Detta är antingen det hanterade program-ID: t eller det användar-tilldelade identitets-ID: t.
token_type | Typ av token.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här konfigurerar du ett hanterat program med en anpassad Provider](../custom-providers/overview.md)
