---
title: Använd hanterad identitet för att autentisera ditt Azure Stream Analytics jobb för att Power BI utdata
description: I den här artikeln beskrivs hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics jobb för att Power BI utdata.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 7c1ddbbbd8198cf769e89cfa824de370184a992c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589692"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Använd hanterad identitet för att autentisera ditt Azure Stream Analytics jobb till Power BI

[Hanterad identitets autentisering](../active-directory/managed-identities-azure-resources/overview.md) för utdata till Power BI ger Stream Analytics-jobb direkt åtkomst till en arbets yta i ditt Power BI-konto. Den här funktionen gör det möjligt att distribuera Stream Analytics jobb helt automatiserade, eftersom det inte längre krävs för att en användare ska kunna logga in interaktivt på Power BI via Azure Portal. Dessutom stöds långa jobb som skrivs till Power BI bättre, eftersom du inte behöver godkänna jobbet med jämna mellanrum.

Den här artikeln visar hur du aktiverar hanterad identitet för Power BI utdata för ett Stream Analytics jobb via Azure Portal och genom en Azure Resource Manager distribution.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att använda den här funktionen:

- Ett Power BI konto med en [Pro-licens](/power-bi/service-admin-purchasing-power-bi-pro).

- En uppgraderad arbets yta i ditt Power BI-konto. Mer information finns i [Power BIs meddelande](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) om den här funktionen.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Skapa ett Stream Analytics jobb med hjälp av Azure Portal

1. Skapa ett nytt Stream Analytics jobb eller öppna ett befintligt jobb i Azure Portal. Välj **hanterad identitet** som finns under **Konfigurera** på Meny raden på vänster sida av skärmen. Se till att "Använd systemtilldelad hanterad identitet" är markerat och välj sedan knappen **Spara** längst ned på skärmen.

   ![Konfigurera Stream Analytics hanterad identitet](./media/common/stream-analytics-enable-managed-identity.png)

2. Innan du konfigurerar utdata ger du Stream Analytics jobb åtkomst till din Power BI arbets yta genom att följa anvisningarna i avsnittet [ge Stream Analytics jobb åtkomst till din Power BI arbets yta](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) i den här artikeln.

3. Gå till avsnittet **utdata** i Stream-analysens jobb, Välj **+ Lägg till** och välj sedan **Power BI**. Välj sedan knappen **auktorisera** och logga in med ditt Power BI-konto.

   ![Auktorisera med Power BI konto](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. När det är auktoriserat fylls en listruta i med alla de arbets ytor som du har åtkomst till. Välj den arbets yta som du auktoriserade i föregående steg. Välj sedan **hanterad identitet** som "autentiseringsläge". Välj slutligen knappen **Spara** .

   ![Konfigurera Power BI utdata med hanterad identitet](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-distribution

Med Azure Resource Manager kan du helt automatisera distributionen av ditt Stream Analytics-jobb. Du kan distribuera Resource Manager-mallar med antingen Azure PowerShell eller [Azure CLI](/cli/azure/). I exemplen nedan används Azure CLI.


1. Du kan skapa en **Microsoft. StreamAnalytics/streamingjobs-** resurs med en hanterad identitet genom att inkludera följande egenskap i resurs avsnittet i Resource Manager-mallen:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Den här egenskapen anger Azure Resource Manager för att skapa och hantera identiteten för ditt Stream Analytics-jobb. Nedan visas en exempel på en Resource Manager-mall som distribuerar ett Stream Analytics jobb med hanterad identitet aktive rad och en Power BI utgående mottagare som använder hanterad identitet:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Distribuera jobbet ovan till resurs gruppens **ExampleGroup** med hjälp av följande Azure CLI-kommando:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. När jobbet har skapats använder du Azure Resource Manager för att hämta jobbets fullständiga definition.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Kommandot ovan kommer att returnera ett svar som nedan:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Om du planerar att använda Power BI REST API för att lägga till Stream Analytics-jobbet i Power BI arbets ytan noterar du den returnerade "principalId".

3. Nu när jobbet har skapats fortsätter du till avsnittet [ge Stream Analytics jobb åtkomst till din Power BI arbets yta](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) i den här artikeln.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Ge Stream Analytics jobb åtkomst till din Power BI arbets yta

Nu när Stream Analytics jobbet har skapats kan det ges åtkomst till en Power BI-arbetsyta. När du har fått åtkomst till ditt jobb kan du göra några minuter så att identiteten kan spridas.

### <a name="use-the-power-bi-ui"></a>Använd Power BI användar gränssnitt

   > [!Note]
   > För att kunna lägga till Stream Analytics jobb till din Power BI-arbetsyta med hjälp av användar gränssnittet måste du också aktivera tjänstens huvud namn i **inställningarna för utvecklare** i Power BI administrations portalen. Se [Kom igång med ett huvud namn för tjänsten](/power-bi/developer/embed-service-principal) för mer information.

1. Navigera till arbets ytans åtkomst inställningar. Mer information finns i den här artikeln: [ge åtkomst till din arbets yta](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Skriv namnet på ditt Stream Analytics jobb i text rutan och välj **Contributor** som åtkomst nivå.

3. Välj **Lägg till** och Stäng fönstret.

   ![Lägg till Stream Analytics jobb till Power BI arbets yta](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Använd Power BI PowerShell-cmdletar

1. Installera PowerShell- `MicrosoftPowerBIMgmt` cmdletarna för Power BI.

   > [!Important]
   > Kontrol lera att du använder version 1.0.821 eller senare av cmdletarna.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Logga in på Power BI.

```powershell
Login-PowerBI
```

3. Lägg till ditt Stream Analytics-jobb som deltagare i arbets ytan.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Använd Power BI REST API

Stream Analyticss jobbet kan också läggas till som deltagare till arbets ytan med hjälp av REST API "Lägg till grupp användare" direkt. Fullständig dokumentation för detta API finns här: [grupper – Lägg till grupp användare](/rest/api/power-bi/groups/addgroupuser).

**Exempelförfrågan**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Begärandetext
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Den hanterade identitet som skapats för ett Stream Analytics jobb tas bara bort när jobbet tas bort. Det finns inget sätt att ta bort den hanterade identiteten utan att ta bort jobbet. Om du inte längre vill använda den hanterade identiteten kan du ändra autentiseringsmetoden för utdata. Den hanterade identiteten finns kvar tills jobbet tas bort och kommer att användas om du väljer att använda hanterad identitetsautentisering igen.

## <a name="limitations"></a>Begränsningar
Nedan visas begränsningarna för den här funktionen:

- Klassiska Power BI-arbetsytor stöds inte.

- Azure-konton utan Azure Active Directory.

- Åtkomst till flera innehavare stöds inte. Tjänstens huvud namn som skapats för ett angivet Stream Analytics jobb måste finnas i samma Azure Active Directory klient som jobbet skapades i och kan inte användas med en resurs som finns i en annan Azure Active Directory klient.

- [Användarens tilldelade identitet](../active-directory/managed-identities-azure-resources/overview.md) stöds inte. Det innebär att du inte kan ange ditt eget tjänst huvud namn som ska användas av sina Stream Analytics-jobb. Tjänstens huvud namn måste genereras av Azure Stream Analytics.

## <a name="next-steps"></a>Nästa steg

* [Power BI instrument panels integrering med Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Förstå utdata från Azure Stream Analytics](./stream-analytics-define-outputs.md)
