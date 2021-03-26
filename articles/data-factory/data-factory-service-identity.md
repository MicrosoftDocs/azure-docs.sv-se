---
title: Hanterad identitet för Data Factory
description: Lär dig mer om hanterad identitet för Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: jingwang
ms.openlocfilehash: 65512f8e46b5545929a798392ac5f19ddeab39ed
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562468"
---
# <a name="managed-identity-for-data-factory"></a>Hanterad identitet för Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln hjälper dig att förstå vad en hanterad identitet är för Data Factory (tidigare kallat Hanterad tjänstidentitet/MSI) och hur det fungerar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När du skapar en data fabrik kan du skapa en hanterad identitet tillsammans med fabriks skapande. Den hanterade identiteten är ett hanterat program som är registrerat för Azure Active Directory och representerar den specifika data fabriken.

Hanterad identitet för Data Factory fördelar följande funktioner:

- [Lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md), i vilket fall Data Factory-hanterad identitet används för Azure Key Vault autentisering.
- Få åtkomst till data lager eller beräkningar med hanterad identitetsautentisering, inklusive Azure Blob Storage, Azure Datautforskaren, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL-hanterad instans, Azure Synapse Analytics, REST, Databricks-aktivitet, webb aktivitet med mera. Mer information finns i artikeln om koppling och aktiviteter.

## <a name="generate-managed-identity"></a>Generera hanterad identitet

Hanterad identitet för Data Factory genereras på följande sätt:

- När du skapar Data Factory via **Azure Portal eller PowerShell** skapas alltid hanterad identitet automatiskt.
- När du skapar Data Factory via **SDK** skapas hanterad identitet endast om du anger "identitet = New FactoryIdentity ()" i objektet Factory för att skapa. Se exempel i [.net snabb start – skapa data fabrik](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- När du skapar Data Factory via **REST API** skapas hanterad identitet endast om du anger avsnittet "identitet" i begär ande texten. Se exempel i [rest-snabb start – skapa data fabrik](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Om du hittar din data fabrik inte har en hanterad identitet som är kopplad till följande [Hämta hanterad identitets](#retrieve-managed-identity) instruktion kan du uttryckligen generera en genom att uppdatera data fabriken med identitets initieraren program mässigt:

- [Generera hanterad identitet med PowerShell](#generate-managed-identity-using-powershell)
- [Generera hanterad identitet med hjälp av REST API](#generate-managed-identity-using-rest-api)
- [Skapa hanterad identitet med hjälp av en Azure Resource Manager mall](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generera hanterad identitet med hjälp av SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Hanterad identitet kan inte ändras. Att uppdatera en data fabrik som redan har en hanterad identitet har ingen påverkan, den hanterade identiteten sparas oförändrad.
>- Om du uppdaterar en data fabrik som redan har en hanterad identitet utan att ange "ID"-parameter i Factory-objektet eller utan att ange "Identity"-avsnittet i REST-begäran, får du ett fel meddelande.
>- När du tar bort en data fabrik tas den tillhör ande hanterade identiteten bort tillsammans.

### <a name="generate-managed-identity-using-powershell"></a>Generera hanterad identitet med PowerShell

Anropa **set-AzDataFactoryV2** kommandot visas "Identity"-fält som nyligen genereras:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Generera hanterad identitet med hjälp av REST API

Anropa nedanstående API med avsnittet "Identity" i begär ande texten:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Brödtext i begäran**: Lägg till "identitet": {"typ": "SystemAssigned"}.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Svar**: hanterad identitet skapas automatiskt och avsnittet "Identity" fylls i.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Skapa hanterad identitet med hjälp av en Azure Resource Manager mall

**Mall**: Lägg till "identitet": {"typ": "SystemAssigned"}.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Generera hanterad identitet med hjälp av SDK

Anropa Data Factory-create_or_update funktionen med identitet = New FactoryIdentity (). Exempel kod med .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Hämta hanterad identitet

Du kan hämta den hanterade identiteten från Azure Portal eller program mässigt. I följande avsnitt visas några exempel.

>[!TIP]
> Om du inte ser den hanterade identiteten kan du [Generera en hanterad identitet](#generate-managed-identity) genom att uppdatera fabriken.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Hämta hanterad identitet med hjälp av Azure Portal

Du kan hitta informationen om hanterad identitet från Azure Portal-> Data Factory-> egenskaper.

- Objekt-ID för hanterad identitet
- Hanterad identitet klient

Den hanterade identitets informationen visas också när du skapar en länkad tjänst, som har stöd för hanterad identitets autentisering, t. ex. Azure Blob, Azure Data Lake Storage, Azure Key Vault osv.

När du beviljar behörighet, i Azure-resursens Access Control (IAM) TABB-> Lägg till roll tilldelning-> tilldela åtkomst till-> väljer du Data Factory under systemtilldelad hanterad identitet – > väljer efter fabriks namn; i allmänhet kan du använda objekt-ID eller data fabriks namn (som namn på hanterad identitet) för att hitta den här identiteten. Om du behöver hämta program-ID: t för den hanterade identiteten kan du använda PowerShell.

### <a name="retrieve-managed-identity-using-powershell"></a>Hämta hanterad identitet med PowerShell

Det hanterade identitetens huvud-ID och klient-ID returneras när du hämtar en data fabrik enligt följande. Använd **PrincipalId** för att bevilja åtkomst:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Du kan hämta program-ID genom att kopiera ovanstående huvud-ID, och sedan köra det Azure Active Directory kommandot med huvud-ID som parameter.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>Hämta hanterad identitet med hjälp av REST API

Det hanterade identitetens huvud-ID och klient-ID returneras när du hämtar en data fabrik enligt följande.

Anropa nedanstående API i begäran:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Svar**: du får svar som visas i exemplet nedan. Avsnittet "identitet" fylls i på motsvarande sätt.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Om du vill hämta den hanterade identiteten från en ARM-mall lägger du till avsnittet **utdata** i arm-JSON:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
Se följande avsnitt som introducerar när och hur du använder Data Factory-hanterad identitet:

- [Lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopiera data från/till Azure Data Lake Store med hanterade identiteter för Azure-resurser autentisering](connector-azure-data-lake-store.md)

Mer bakgrunds information om hanterade identiteter för Azure-resurser finns i [Översikt över Managed identiteter för Azure](../active-directory/managed-identities-azure-resources/overview.md) -resurser, vilken Data Factory-hanterad identitet baseras på.