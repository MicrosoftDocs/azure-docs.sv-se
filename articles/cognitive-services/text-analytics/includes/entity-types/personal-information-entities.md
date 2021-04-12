---
title: Namngivna entiteter för personlig information
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 7484b49ed3c868a1ad3e0f97dffa346f350e127f
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097167"
---
> [!NOTE]
> Använd `domain=phi` parametern och modell versionen eller senare för att identifiera skyddad hälso information (Phi) `2020-04-01` .
>
> Exempelvis: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Följande enhets kategorier returneras när du skickar begär anden till `/v3.1-preview.3/entities/recognition/pii` slut punkten.


| Kategori   |  Beskrivning                          |
|------------|-------------|
| [Person](#category-person)      |  Namn på personer.  |
| [PersonType](#category-persontype) | Jobb typer eller roller som innehas av en person. |
| [Telefonnummer](#category-phonenumber) |Telefonnummer (endast USA och EU-telefonnummer). |
| [Organisation](#category-organization) |  Företag, grupper, myndighets organ och andra organisationer.  |
| [Adress](#category-address) | Fullständiga post adresser.  |
| [E-post](#category-email) | E-postadresser.   |
| [URL](#category-url) | URL: er till webbplatser.  |
| [IP-adress](#category-ip) | Nätverks-IP-adresser.  |
| [DateTime](#category-datetime) | Datum och tidpunkter på dagen. | 
| [Kvantitet](#category-quantity) | Siffror och numeriska kvantiteter.  |
| [Azure-information](#azure-information) | Identifierbar Azure-information, till exempel autentiseringsinformation.  |
| [Fastställa](#identification) | Ekonomisk och landsspecifika identifiering.  |

### <a name="category-person"></a>Kategori: person

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Person

    :::column-end:::
    :::column span="2":::
        **Information**

        Namn på personer. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `Person` `pii-categories` . `Person` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    
    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategori: PersonType

Den här kategorin innehåller följande entitet:


:::row:::
    :::column span="":::
        **Entitet**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Information**

        Jobb typer eller roller som innehas av en person.

        Lägg till i-parametern för att hämta entitets kategorin `PersonType` `pii-categories` . `PersonType` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategori: telefonnummer

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Information**

        Telefonnummer (endast USA och EU-telefonnummer). Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `PhoneNumber` `pii-categories` . `PhoneNumber` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


### <a name="category-organization"></a>Kategori: organisation

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Organisation

    :::column-end:::
    :::column span="2":::
        **Information**

        Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer. Nationella objekt och religions ingår inte i den här entitetstypen. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `Organization` `pii-categories` . `Organization` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        Sjukdom    

    :::column-end:::
    :::column span="2":::
        **Information**

        Medicinska företag och grupper.

        Lägg till i-parametern för att hämta entitets kategorin `OrganizationMedical` `pii-categories` . `OrganizationMedical` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::
      **Dokument språk som stöds**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Börs kurs

    :::column-end:::
    :::column span="2":::

        Fond börs grupper. 

        Lägg till i-parametern för att hämta entitets kategorin `OrganizationStockExchange` `pii-categories` . `OrganizationStockExchange` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Idrotts relaterade organisationer.

        Lägg till i-parametern för att hämta entitets kategorin `OrganizationSports` `pii-categories` . `OrganizationSports` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


### <a name="category-address"></a>Kategori: adress

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        Adress

    :::column-end:::
    :::column span="2":::
        **Information**

        Fullständig e-postadress. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `Address` `pii-categories` . `Address` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-email"></a>Kategori: e-post

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        E-post

    :::column-end:::
    :::column span="2":::
        **Information**

        E-postadresser. Returneras också med `domain=phi` .
      
        Lägg till i-parametern för att hämta entitets kategorin `Email` `pii-categories` . `Email` kommer att returneras i API-svaret om det upptäcks.

    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


### <a name="category-url"></a>Kategori: URL

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        URL

    :::column-end:::
    :::column span="2":::
        **Information**

        URL: er till webbplatser. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `URL` `pii-categories` . `URL` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ip"></a>Kategori: IP

Den här kategorin innehåller följande entitet:

:::row:::
    :::column span="":::
        **Entitet**

        IP-adress

    :::column-end:::
    :::column span="2":::
        **Information**

        Nätverks-IP-adresser. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `IP` `pii-categories` . `IP` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::

    :::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategori: DateTime

Den här kategorin innehåller följande entiteter:

:::row:::
    :::column span="":::
        **Entitet**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Information**

        Datum och tidpunkter på dagen. 

        Lägg till i-parametern för att hämta entitets kategorin `DateTime` `pii-categories` . `DateTime` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
:::column span="":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        Datum

    :::column-end:::
    :::column span="2":::
        **Information**

        Kalender datum. Returneras också med `domain=phi` .

        Lägg till i-parametern för att hämta entitets kategorin `Date` `pii-categories` . `Date` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategori: kvantitet

Den här kategorin innehåller följande entiteter:

:::row:::
    :::column span="":::
        **Entitet**

        Kvantitet

    :::column-end:::
    :::column span="2":::
        **Information**

        Siffror och numeriska kvantiteter.

        Lägg till i-parametern för att hämta entitets kategorin `Quantity` `pii-categories` . `Quantity` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="2":::
      **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Under Kategorier

Entiteten i den här kategorin kan ha följande under kategorier.

:::row:::
    :::column span="":::
        **Underkategori för entitet**

        Ålder

    :::column-end:::
    :::column span="2":::
        **Information**

        Personer. 

        Lägg till i-parametern för att hämta entitets kategorin `Age` `pii-categories` . `Age` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="2":::
        **Dokument språk som stöds**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure-information

Dessa entitets kategorier innehåller identifierbar Azure-information, inklusive autentiseringsinformation och anslutnings strängar. Returnerades inte med `domain=phi` parametern.

:::row:::
    :::column span="":::
        **Entitet**

        Azure DocumentDB auth-nyckel 

    :::column-end:::
    :::column span="2":::
        **Information**

        Auktoriseringskod för en Azure Cosmos DB-server.   

        Lägg till i-parametern för att hämta entitets kategorin `AzureDocumentDBAuthKey` `pii-categories` . `AzureDocumentDBAuthKey` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::
      **Dokument språk som stöds**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Databas anslutnings sträng för Azure IAAS och Azure SQL-anslutningssträng.
        

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för en Azure Infrastructure as a service-databas (IaaS) och SQL-anslutningssträng.

        Lägg till i-parametern för att hämta entitets kategorin `AzureIAASDatabaseConnectionAndSQLString` `pii-categories` . `AzureIAASDatabaseConnectionAndSQLString` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT-anslutningssträng  

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för Azure IoT. 
      
        Lägg till i-parametern för att hämta entitets kategorin `AzureIoTConnectionString` `pii-categories` . `AzureIoTConnectionString` kommer att returneras i API-svaret om det upptäcks.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Lösen ord för Azure publicerings inställning  

    :::column-end:::
    :::column span="2":::

        Lösen ord för Azures publicerings inställningar.

        Lägg till i-parametern för att hämta entitets kategorin `AzurePublishSettingPassword` `pii-categories` . `AzurePublishSettingPassword` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache anslutnings sträng 

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för Redis-cache.

        Lägg till i-parametern för att hämta entitets kategorin `AzureRedisCacheString` `pii-categories` . `AzureRedisCacheString` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för Azure program vara som en tjänst (SaaS).

        Lägg till i-parametern för att hämta entitets kategorin `AzureSAS` `pii-categories` . `AzureSAS` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus anslutnings sträng

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för en Azure Service Bus.

        Lägg till i-parametern för att hämta entitets kategorin `AzureServiceBusString` `pii-categories` . `AzureServiceBusString` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage konto nyckel 

    :::column-end:::
    :::column span="2":::

        Konto nyckel för ett Azure Storage-konto. 

        Lägg till i-parametern för att hämta entitets kategorin `AzureStorageAccountKey` `pii-categories` . `AzureStorageAccountKey` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage konto nyckel (allmän)

    :::column-end:::
    :::column span="2":::

        Allmän konto nyckel för ett Azure Storage-konto.

        Lägg till i-parametern för att hämta entitets kategorin `AzureStorageAccountGeneric` `pii-categories` . `AzureStorageAccountGeneric` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server anslutnings sträng 

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för en dator som kör SQL Server.

        Lägg till i-parametern för att hämta entitets kategorin `SQLServerConnectionString` `pii-categories` . `SQLServerConnectionString` kommer att returneras i API-svaret om det upptäcks.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](./identification-entities.md)]
