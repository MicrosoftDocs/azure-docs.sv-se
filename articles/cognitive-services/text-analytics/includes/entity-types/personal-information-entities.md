---
title: Namngivna entiteter för personlig information
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 97167485dae155670f0eb83fc3ef9cb658952251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751001"
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

        Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer. Nationella objekt och religions ingår inte i den här entitetstypen.
      
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
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Börs kurs

    :::column-end:::
    :::column span="2":::

        Fond börs grupper. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Idrotts relaterade organisationer.
      
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

        Fullständig e-postadress.
      
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

        E-postadresser.
      
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

        URL: er till webbplatser. 
      
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

        nätverks-IP-adresser. 
      
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

        Kalender datum.
      
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
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Anslutnings sträng för Azure IAAS Database och Azure SQL-anslutningssträng

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för en Azure Infrastructure as a service-databas (IaaS) och SQL-anslutningssträng.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SQL-anslutningssträng

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för en databas i Azure SQL Database.
      
    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT-anslutningssträng  

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för Azure IoT. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Lösen ord för Azure publicerings inställning  

    :::column-end:::
    :::column span="2":::

        Lösen ord för Azures publicerings inställningar.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache anslutnings sträng 

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för Redis-cache.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för Azure program vara som en tjänst (SaaS).
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus anslutnings sträng

    :::column-end:::
    :::column span="2":::

        Anslutnings sträng för en Azure Service Bus.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage konto nyckel 

    :::column-end:::
    :::column span="2":::

       Konto nyckel för ett Azure Storage-konto. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage konto nyckel (allmän)

    :::column-end:::
    :::column span="2":::

       Allmän konto nyckel för ett Azure Storage-konto.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server anslutnings sträng 

    :::column-end:::
    :::column span="2":::

       Anslutnings sträng för en dator som kör SQL Server.
      
    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](./identification-entities.md)]
