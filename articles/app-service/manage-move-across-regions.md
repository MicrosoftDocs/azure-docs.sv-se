---
title: Flytta en app till en annan region
description: Lär dig hur du flyttar App Service resurser från en region till en annan.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86524862"
---
# <a name="move-an-app-service-resource-to-another-region"></a>Flytta en App Service resurs till en annan region

Den här artikeln beskriver hur du flyttar App Service resurser till en annan Azure-region. Du kan flytta dina resurser till en annan region av olika anledningar. Till exempel, för att dra nytta av en ny Azure-region, för att bara distribuera funktioner eller tjänster som är tillgängliga i vissa regioner, för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven.

App Service-resurser är landsspecifika och kan inte flyttas mellan regioner. Du måste skapa en kopia av dina befintliga App Service-resurser i mål regionen och sedan flytta innehållet till den nya appen. Om din käll-app använder en anpassad domän kan du [migrera den till den nya appen i mål regionen](manage-custom-dns-migrate-domain.md) när du är klar.

För att göra det enklare att kopiera appen kan du [klona en enskild app service app](app-service-web-app-cloning.md) i en app service plan i en annan region, men den har [begränsningar](app-service-web-app-cloning.md#current-restrictions), särskilt att den inte stöder Linux-appar.

## <a name="prerequisites"></a>Förutsättningar

- Se till att App Service-appen finns i den Azure-region som du vill flytta från.
- Kontrol lera att mål regionen stöder App Service och relaterade tjänster, vars resurser du vill flytta.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Förbereda

Identifiera alla App Service resurser som du använder just nu. Exempel:

- App Service-appar
- [App Service-planer](overview-hosting-plans.md)
- [Distributionsfack](deploy-staging-slots.md)
- [Anpassade domäner som köpts i Azure](manage-custom-dns-buy-domain.md)
- [SSL-certifikat](configure-ssl-certificate.md)
- [Azure Virtual Network-integrering](web-sites-integrate-with-vnet.md)
- [Hybrid anslutningar](app-service-hybrid-connections.md).
- [Hanterade identiteter](overview-managed-identity.md)
- [Inställningar för säkerhets kopiering](manage-backup.md)

Vissa resurser, t. ex. importerade certifikat eller hybrid anslutningar, innehåller integrering med andra Azure-tjänster. Information om hur du flyttar dessa resurser över flera regioner finns i dokumentationen för respektive tjänst.

## <a name="move"></a>Flytta

1. [Skapa en säkerhets kopia av käll appen](manage-backup.md).
1. [Skapa en app i en ny app service plan i mål regionen](app-service-plan-manage.md#create-an-app-service-plan).
2. [Återställa säkerhets kopieringen i mål programmet](web-sites-restore.md)
2. Om du använder en anpassad domän [binder du den förebyggande syfte till mål-appen](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) med `awverify.` och [aktiverar domänen i mål programmet](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Konfigurera allt annat i din mål-app så att det är samma som käll-appen och verifiera konfigurationen.
4. När du är redo för den anpassade domänen att peka på mål programmet, [mappa om domän namnet](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Rensa käll resurser

Ta bort käll-app och App Service plan. [En App Service plan på den icke-kostnads fria nivån medför en avgift, även om ingen App körs i den.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Nästa steg

[Azure App Service kloning av appar med hjälp av PowerShell](app-service-web-app-cloning.md)