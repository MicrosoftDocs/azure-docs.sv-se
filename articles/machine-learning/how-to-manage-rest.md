---
title: Använd REST för att hantera ML-resurser
titleSuffix: Azure Machine Learning
description: 'Använda REST-API: er för att skapa, köra och ta bort Azure Machine Learning resurser, till exempel en arbets yta eller registrera modeller.'
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: bf1d6f5838e467c5f44a0090a4f1a15cd9d4ac77
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692461"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Skapa, köra och ta bort Azure ML-resurser med REST



Det finns flera sätt att hantera dina Azure ML-resurser. Du kan använda [portalen](https://portal.azure.com/), [kommando rads gränssnittet](/cli/azure/?preserve-view=true&view=azure-cli-latest)eller [python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Alternativt kan du välja REST API. I REST API används HTTP-verb på ett standardiserat sätt för att skapa, Hämta, uppdatera och ta bort resurser. REST API fungerar med valfritt språk eller verktyg som kan göra HTTP-förfrågningar. I resten är en enkel struktur att det är ett bra val i skript miljöer och för MLOps Automation. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hämta en autentiseringstoken
> * Skapa en korrekt formaterad REST-begäran med tjänstens huvud namns autentisering
> * Använd GET-begäranden för att hämta information om Azure MLs hierarkiska resurser
> * Använd skicka och publicera-begär Anden för att skapa och ändra resurser
> * Rensa resurser med DELETE-begäranden 
> * Använd nyckelbaserad auktorisering för att ange distribuerade modeller

## <a name="prerequisites"></a>Förutsättningar

- En **Azure-prenumeration** som du har administratörs behörighet för. Om du inte har en sådan prenumeration kan du prova den [kostnads fria eller betalda personliga prenumerationen](https://aka.ms/AMLFree)
- En [Azure Machine Learning-arbetsyta](./how-to-manage-workspace.md)
- Administrativa REST-begäranden använder autentisering av tjänstens huvud namn. Följ stegen i [Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden](./how-to-setup-authentication.md#service-principal-authentication) för att skapa ett huvud namn för tjänsten på din arbets yta
- Verktyget **vändning** . Programmet **svänga** är tillgängligt i [Windows-undersystemet för Linux](/windows/wsl/install-win10) eller UNIX-distribution. I PowerShell är **sväng** ett alias för **Invoke-webbegäran** och `curl -d "key=val" -X POST uri` blir `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Hämta en autentiseringstoken för tjänstens huvud namn

Administrativa REST-begäranden autentiseras med ett OAuth2 implicit flöde. Det här autentiseringsschemat använder en token som tillhandahålls av prenumerationens tjänst huvud namn. Om du vill hämta denna token behöver du:

- Ditt klient-ID (identifierar organisationen som din prenumeration tillhör)
- Ditt klient-ID (som ska associeras med den skapade token)
- Din klient hemlighet (som du bör skydda)

Du bör ha dessa värden från svaret på skapandet av tjänstens huvud namn. Att hämta dessa värden beskrivs i [Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden](./how-to-setup-authentication.md#service-principal-authentication). Om du använder din företags prenumeration kanske du inte har behörighet att skapa ett huvud namn för tjänsten. I så fall bör du använda antingen en [kostnads fri eller betald personlig prenumeration](https://aka.ms/AMLFree).

Hämta en token:

1. Öppna ett terminalfönster
1. Ange följande kod på kommando raden
1. Ersätt dina egna värden för `{your-tenant-id}` , `{your-client-id}` och `{your-client-secret}` . I den här artikeln är strängar som omges av klammerparenteser variabler som du måste ersätta med dina egna värden.
1. Köra kommandot

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Svaret ska ge en lämplig åtkomsttoken i en timme:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Anteckna token eftersom du använder den för att autentisera alla efterföljande administrativa förfrågningar. Du gör detta genom att ange ett Authorization-huvud i alla begär Anden:

```bash
curl -h "Authorization:Bearer {your-access-token}" ...more args...
```

Observera att värdet börjar med strängen "Bearer", inklusive ett enda blank steg innan du lägger till token.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Hämta en lista över resurs grupper som är kopplade till din prenumeration

För att hämta listan över resurs grupper som är associerade med din prenumeration, kör:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

I Azure publiceras många REST-API: er. Varje tjänst leverantör uppdaterar sitt API på sin egen takt, men gör det utan att behöva bryta befintliga program. Tjänste leverantören använder `api-version` argumentet för att säkerställa kompatibiliteten. `api-version`Argumentet varierar från tjänst till tjänst. För Machine Learning tjänst är till exempel den aktuella API-versionen `2019-11-01` . För lagrings konton är det `2019-06-01` . För nyckel valv är det `2019-09-01` . Alla REST-anrop ska ange `api-version` argumentet till det förväntade värdet. Du kan förlita dig på syntaxen och semantiken för den angivna versionen även om API: et fortsätter att utvecklas. Om du skickar en begäran till en provider utan `api-version` argumentet innehåller svaret en skrivskyddad lista med värden som stöds. 

Anropet ovan resulterar i ett komprimerat JSON-svar i formuläret: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Öka detalj nivån i arbets ytor och deras resurser

Om du vill hämta en uppsättning arbets ytor i en resurs grupp kör du följande, Ersätt `{your-subscription-id}` , `{your-resource-group}` och `{your-access-token}` : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Återigen får du en JSON-lista, den här tiden innehåller en lista, varje objekt som innehåller information om en arbets yta:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Om du vill arbeta med resurser inom en arbets yta växlar du från den allmänna **Management.Azure.com** -servern till en REST API server som är speciell för arbets ytans plats. Anteckna värdet för `discoveryUrl` nyckeln i ovanstående JSON-svar. Om du får den URL: en får du ett svar något som:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Värdet för `api` svaret är URL: en för den server som du ska använda för ytterligare begär Anden. Om du vill visa experiment, till exempel, skickar du följande kommando. Ersätt `regional-api-server` med värdet för `api` Response (t `centralus.api.azureml.ms` . ex.). Byt även ut `your-subscription-id` ,, `your-resource-group` `your-workspace-name` och `your-access-token` som vanligt:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

På samma sätt kan du hämta registrerade modeller i din arbets yta genom att skicka:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Observera att om du vill visa en lista över experiment börjar sökvägen med `history/v1.0` när du ska lista modeller `modelmanagement/v1.0` . REST API delas upp i flera operativa grupper, var och en med en distinkt sökväg. 

|Område|Sökväg|
|-|-|
|Artifacts|/rest/api/azureml|
|Datalager|/azure/machine-learning/how-to-access-data|
|Finjustering av hyperparametrar|HyperDrive/v 1.0/|
|Modeller|modelmanagement/v 1.0/|
|Körningshistorik|körning/v 1.0/och historik/v 1.0/|

Du kan utforska REST API med det allmänna mönstret för:

|URL-komponent|Exempel|
|-|-|
| https://| |
| regional-API-Server/ | centralus.api.azureml.ms/ |
| Operations-Path/ | historik/v 1.0/ |
| prenumerationer/{din-Subscription-ID}/ | prenumerationer/abcde123-ABAB-ABAB-1234-0123456789abc/ |
| resourceGroups/{The-Resource-Group}/ | resourceGroups/MyResourceGroup/ |
| providers/åtgärd-Provider/ | providers/Microsoft. MachineLearningServices/ |
| Provider – resurs-sökväg/ | arbets ytor/MLWorkspace//min arbets yta/FirstExperiment/körs/1/ |
| åtgärder-Endpoint/ | artefakter/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Skapa och ändra resurser med hjälp av skicka och publicera-begäranden

Förutom resurs hämtning med GET-verbet kan REST API skapa alla resurser som behövs för att träna, distribuera och övervaka ML-lösningar. 

För utbildning och körning av ML-modeller krävs beräknings resurser. Du kan visa en lista med beräknings resurser för en arbets yta med: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Om du vill skapa eller skriva över en namngiven beräknings resurs, använder du en begäran om placering. I följande, utöver de nu välkända ersättningarna `your-subscription-id` , `your-resource-group` ,, `your-workspace-name` och `your-access-token` , Substitute `your-compute-name` och värden för,,,, `location` `vmSize` `vmPriority` `scaleSettings` `adminUserName` och `adminUserPassword` . Som anges i referensen i [Machine Learning-beräkning-skapa eller uppdatera SDK-referens](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)skapar följande kommando en dedikerad Standard_D1 för en enda nod (en grundläggande processor beräknings resurs) som kommer att skalas ned efter 30 minuter:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> I Windows-terminaler kan du behöva kringgå dubbel citat tecken när du skickar JSON-data. Det vill säga text, till exempel `"location"` blir `\"location\"` . 

En lyckad begäran får ett `201 Created` svar, men Observera att det här svaret bara innebär att etablerings processen har påbörjats. Du måste avsöka (eller använda portalen) för att bekräfta att åtgärden har slutförts.

### <a name="create-an-experimental-run"></a>Skapa en experimentell körning

Om du vill starta en körning inom ett experiment behöver du en zip-mapp som innehåller ditt utbildnings skript och relaterade filer och en Run-definitions-JSON-fil. Zip-mappen måste ha filen python i dess rot Katalog. Du kan t. ex. zippa ett trivial python-program, till exempel följande i en mapp med namnet **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Spara nästa kodfragment som **definition.jspå**. Bekräfta att "script"-värdet matchar namnet på den python-fil som du precis har zippat upp. Bekräfta att "target"-värdet matchar namnet på en tillgänglig beräknings resurs. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Publicera de här filerna på servern med hjälp av `multipart/form-data` innehåll:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

En lyckad POST-begäran genererar en `200 OK` status med en svars text som innehåller ID: n för den skapade körningen:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Du kan övervaka en körning med hjälp av REST-full-mönstret som nu bör vara välbekant:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Ta bort resurser som du inte längre behöver

Vissa, men inte alla, resurser stöder DELETE-verbet. Kontrol lera [API-referensen](/rest/api/azureml/) innan du genomför till REST API för borttagning av användnings fall. Om du t. ex. vill ta bort en modell kan du använda:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Använd REST för att räkna med en distribuerad modell

Även om det är möjligt att distribuera en modell så att den autentiseras med ett huvud namn för tjänsten, använder de flesta klient-riktade distributioner nyckelbaserad autentisering. Du hittar lämplig nyckel på din distributions sida på fliken **slut punkter** i Studio. På samma plats visas din slut punkts bedömnings-URI. Modellens indata måste modelleras som en JSON-matris med namnet `data` :

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Skapa en arbets yta med REST 

Varje Azure ML-arbetsyta har ett beroende på fyra andra Azure-resurser: ett behållar register med administration aktiverat, ett nyckel valv, en Application Insights resurs och ett lagrings konto. Det går inte att skapa en arbets yta förrän dessa resurser finns. Mer information om hur du skapar varje sådan resurs finns i REST API referens.

Om du vill skapa en arbets yta sätter du ett anrop som liknar följande för `management.azure.com` . Även om det här anropet kräver att du anger ett stort antal variabler, är det strukturellt identiskt med andra anrop som denna artikel har diskuterat. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Du bör få ett `202 Accepted` svar och, i de returnerade rubrikerna, en `Location` URI. Du kan hämta den här URI: n för information om distributionen, inklusive användbar fel söknings information om det uppstår problem med en av dina beroende resurser (till exempel om du har glömt att aktivera administratörs åtkomst i behållar registret). 

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resurs leverantörs fel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbets ytan

> [!WARNING]
> Det finns inte stöd för att flytta Azure Machine Learning arbets ytan till en annan prenumeration eller flytta den ägande prenumerationen till en ny klient. Detta kan orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Tar bort Azure Container Registry

I arbets ytan Azure Machine Learning används Azure Container Registry (ACR) för vissa åtgärder. En ACR-instans skapas automatiskt när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

- Utforska den kompletta [azureml-REST API referensen](/rest/api/azureml/).
- Lär dig hur du använder design verktyget för att [förutsäga det mobila priset med designern](./tutorial-designer-automobile-price-train-score.md).
- Utforska [Azure Machine Learning med Jupyter-anteckningsböcker](..//machine-learning/samples-notebooks.md).
