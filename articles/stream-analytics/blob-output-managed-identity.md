---
title: Autentisera BLOB-utdata med hanterad identitets Azure Stream Analytics
description: Den här artikeln beskriver hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics jobb till Azure Blob Storage-utdata.
author: kim-ale
ms.author: kimal
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 369348133f7395f5db5b5923bd438cec8e4ad733
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954386"
---
# <a name="use-managed-identity-preview-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage"></a>Använd hanterad identitet (förhands granskning) för att autentisera ditt Azure Stream Analytics jobb till Azure Blob Storage

[Managed Identity Authentication](../active-directory/managed-identities-azure-resources/overview.md) (för hands version) för utdata till Azure Blob storage ger Stream Analytics-jobb direkt åtkomst till ett lagrings konto i stället för att använda en anslutnings sträng. Förutom förbättrad säkerhet kan du också använda den här funktionen för att skriva data till ett lagrings konto i en Virtual Network (VNET) i Azure.

Den här artikeln visar hur du aktiverar hanterad identitet för BLOB-utdata för ett Stream Analytics jobb via Azure Portal och via en Azure Resource Manager-distribution.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Skapa Stream Analytics jobb med hjälp av Azure Portal

1. Skapa ett nytt Stream Analytics jobb eller öppna ett befintligt jobb i Azure Portal. Välj **hanterad identitet** som finns under **Konfigurera** på Meny raden på vänster sida av skärmen. Se till att "Använd systemtilldelad hanterad identitet" är markerat och klicka sedan på knappen **Spara** längst ned på skärmen.

   ![Konfigurera Stream Analytics hanterad identitet](./media/common/stream-analytics-enable-managed-identity.png)

2. I fönstret utdata-egenskaper i Azure Blob Storage utgående mottagare väljer du List rutan autentiseringsläge och väljer **hanterad identitet**. Information om andra egenskaper för utdata finns i [förstå utdata från Azure Stream Analytics](./stream-analytics-define-outputs.md). När du är klar klickar du på **Spara**.

   ![Konfigurera Azure Blob Storage-utdata](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Nu när jobbet har skapats går du till avsnittet [ge Stream Analytics jobb åtkomst till ditt lagrings konto](#give-the-stream-analytics-job-access-to-your-storage-account) i den här artikeln.

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-distribution

Med hjälp av Azure Resource Manager kan du helt automatisera distributionen av ditt Stream Analytics-jobb. Du kan distribuera Resource Manager-mallar med antingen Azure PowerShell eller [Azure CLI](/cli/azure/). I exemplen nedan används Azure CLI.


1. Du kan skapa en **Microsoft. StreamAnalytics/streamingjobs-** resurs med en hanterad identitet genom att inkludera följande egenskap i resurs avsnittet i Resource Manager-mallen:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Den här egenskapen anger Azure Resource Manager för att skapa och hantera identiteten för ditt Stream Analytics-jobb. Nedan visas en exempel på en Resource Manager-mall som distribuerar ett Stream Analytics jobb med hanterad identitet aktive rad och en BLOB-utgående mottagare som använder hanterad identitet:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    Ovanstående jobb kan distribueras till resurs gruppen **ExampleGroup** med hjälp av följande Azure CLI-kommando:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. När jobbet har skapats kan du använda Azure Resource Manager för att hämta jobbets fullständiga definition.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Kommandot ovan kommer att returnera ett svar som nedan:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Anteckna **principalId** från jobb definitionen, som identifierar ditt jobbs hanterade identitet inom Azure Active Directory och som ska användas i nästa steg för att ge Stream Analytics jobb åtkomst till lagrings kontot.

3. Nu när jobbet har skapats går du till avsnittet [ge Stream Analytics jobb åtkomst till ditt lagrings konto](#give-the-stream-analytics-job-access-to-your-storage-account) i den här artikeln.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Ge Stream Analytics jobb åtkomst till ditt lagrings konto

Det finns två åtkomst nivåer som du kan välja för att ge ditt Stream Analytics jobb:

1. **Åtkomst till behållar nivå:** det här alternativet ger jobbet åtkomst till en speciell befintlig behållare.
2. **Åtkomst till konto nivå:** det här alternativet ger jobbet allmän åtkomst till lagrings kontot, inklusive möjligheten att skapa nya behållare.

Om du inte behöver jobbet för att skapa behållare för din räkning bör du välja **åtkomst till container nivå** eftersom det här alternativet ger jobbet den lägsta åtkomst nivån som krävs. Båda alternativen förklaras nedan för Azure Portal och kommando raden.

### <a name="grant-access-via-the-azure-portal"></a>Bevilja åtkomst via Azure Portal

#### <a name="container-level-access"></a>Åtkomst till behållar nivå

1. Navigera till behållarens konfigurations fönster i ditt lagrings konto.

2. Välj **Access Control (IAM)** på den vänstra sidan.

3. Under avsnittet "Lägg till en roll tilldelning" klickar du på **Lägg till**.

4. I fönstret roll tilldelning:

    1. Ange **rollen** som "Storage BLOB data Contributor"
    2. Se till att List rutan **tilldela** till listruta är inställd på "Azure AD User, Group eller service huvud namn".
    3. Skriv namnet på ditt Stream Analytics jobb i Sök fältet.
    4. Välj ditt Stream Analytics jobb och klicka på **Spara**.

   ![Bevilja container åtkomst](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Åtkomst till konto nivå

1. Navigera till ditt lagringskonto.

2. Välj **Access Control (IAM)** på den vänstra sidan.

3. Under avsnittet "Lägg till en roll tilldelning" klickar du på **Lägg till**.

4. I fönstret roll tilldelning:

    1. Ange **rollen** som "Storage BLOB data Contributor"
    2. Se till att List rutan **tilldela** till listruta är inställd på "Azure AD User, Group eller service huvud namn".
    3. Skriv namnet på ditt Stream Analytics jobb i Sök fältet.
    4. Välj ditt Stream Analytics jobb och klicka på **Spara**.

   ![Bevilja konto åtkomst](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Bevilja åtkomst via kommando raden

#### <a name="container-level-access"></a>Åtkomst till behållar nivå

Om du vill ge åtkomst till en speciell behållare kör du följande kommando med hjälp av Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Åtkomst till konto nivå

Om du vill ge åtkomst till hela kontot kör du följande kommando med hjälp av Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Aktivera VNET-åtkomst

När du konfigurerar ditt lagrings kontos **brand väggar och virtuella nätverk** kan du välja att tillåta nätverks trafik från andra betrodda Microsoft-tjänster. När Stream Analytics autentiserar med hjälp av hanterad identitet, ger det bevis på att begäran kommer från en betrodd tjänst. Nedan visas instruktioner för att aktivera detta undantag för VNET-åtkomst.

1.    Navigera till fönstret brand väggar och virtuella nätverk i lagrings kontots konfigurations fönster.
2.    Se till att alternativet "Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot" är aktiverat.
3.    Om du har aktiverat det klickar du på **Spara**.

   ![Aktivera VNET-åtkomst](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Den hanterade identitet som skapats för ett Stream Analytics jobb tas bara bort när jobbet tas bort. Det finns inget sätt att ta bort den hanterade identiteten utan att ta bort jobbet. Om du inte längre vill använda den hanterade identiteten kan du ändra autentiseringsmetoden för utdata. Den hanterade identiteten finns kvar tills jobbet tas bort och kommer att användas om du väljer att använda hanterad identitetsautentisering igen.

## <a name="limitations"></a>Begränsningar
Nedan visas aktuella begränsningar för den här funktionen:

1. Klassiska Azure Storage-konton.

2. Azure-konton utan Azure Active Directory.

3. Åtkomst till flera innehavare stöds inte. Tjänstens huvud namn som skapats för ett angivet Stream Analytics jobb måste finnas i samma Azure Active Directory klient som jobbet skapades i och kan inte användas med en resurs som finns i en annan Azure Active Directory klient.

4. [Användarens tilldelade identitet](../active-directory/managed-identities-azure-resources/overview.md) stöds inte. Det innebär att användaren inte kan ange sitt eget tjänst huvud namn som ska användas av deras Stream Analytics-jobb. Tjänstens huvud namn måste genereras av Azure Stream Analytics.

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics partitionering av anpassad BLOB-utdata](./stream-analytics-custom-path-patterns-blob-storage-output.md)
