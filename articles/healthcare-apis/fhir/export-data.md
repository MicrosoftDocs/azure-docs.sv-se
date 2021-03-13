---
title: Köra exporten genom att anropa $export kommandot på Azure API för FHIR
description: Den här artikeln beskriver hur du exporterar FHIR-data med $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 2/19/2021
ms.author: cavoeg
ms.openlocfilehash: 9ed78baed35312b9a33c71a3e49b7e9dca22eb9f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020309"
---
# <a name="how-to-export-fhir-data"></a>Så här exporterar du FHIR-data


Mass export funktionen gör att data kan exporteras från FHIR-servern enligt FHIR- [specifikationen](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Innan du använder $export bör du se till att Azure API för FHIR är konfigurerat för att använda det. Information om hur du konfigurerar export inställningar och skapar Azure Storage-konton finns på [sidan Konfigurera export data](configure-export-data.md).

## <a name="using-export-command"></a>Använda $export kommandot

När du har konfigurerat Azure API för FHIR för export kan du använda kommandot $export för att exportera data från tjänsten. Data lagras i det lagrings konto du angav när du konfigurerade exporten. Information om hur du anropar $export kommandot i FHIR-servern finns i dokumentationen om [HL7 FHIR $export Specification](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Azure API för FHIR har stöd för $export på följande nivåer:
* [System](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Patient](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupp av patienter *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) – Azure API för FHIR exporterar alla relaterade resurser men exporterar inte egenskaperna för gruppen: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

När data exporteras skapas en separat fil för varje resurs typ. För att säkerställa att de exporterade filerna inte blir för stora skapar vi en ny fil när storleken på en exporterad fil blir större än 64 MB. Resultatet är att du kan få flera filer för varje resurs typ, som kommer att räknas upp (t. ex. patient-1. ndjson, patient-2. ndjson). 


> [!Note] 
> `Patient/$export` och `Group/[ID]/$export` kan exportera dubbla resurser om resursen finns i ett utrymme med fler än en resurs eller finns i flera grupper.

Dessutom stöds att kontrol lera export statusen via URL: en som returnerades av plats rubriken under köerna, tillsammans med att avbryta det faktiska export jobbet.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Exportera FHIR-data till ADLS Gen2

För närvarande stöder vi $export för ADLS Gen2-aktiverade lagrings konton, med följande begränsning:

- Användaren kan inte dra nytta av [hierarkiska namn områden](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) än; Det finns inte något sätt att rikta in på att exportera till en angiven under katalog i behållaren. Vi tillhandahåller bara möjligheten att rikta en speciell behållare (där vi skapar en ny mapp för varje export).

- När en export är klar exporterar vi aldrig något till den mappen igen, eftersom efterföljande exporter till samma behållare kommer att finnas i en nyligen skapad mapp.


## <a name="settings-and-parameters"></a>Inställningar och parametrar

### <a name="headers"></a>Sidhuvuden
Det finns två obligatoriska huvud parametrar som måste anges för $export-jobb. Värdena definieras av den aktuella [$export specifikationen](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Acceptera** -Application/FHIR + JSON
* **Föredra** – svara-asynkron

### <a name="query-parameters"></a>Frågeparametrar
Azure API för FHIR stöder följande frågeparametrar. Alla dessa parametrar är valfria:

|Frågeparameter        | Definieras av FHIR-specifikationen?    |  Beskrivning|
|------------------------|---|------------|
| \_outputFormat | Ja | Stöder för närvarande tre värden för att justeras till FHIR-specifikationen: Application/FHIR + ndjson, Application/ndjson eller bara ndjson. Alla export jobb kommer att returneras `ndjson` och det skickade värdet påverkar inte kod beteendet. |
| \_Starta | Ja | Gör att du endast kan exportera resurser som har ändrats sedan den angivna tiden |
| \_bastyp | Ja | Gör att du kan ange vilka typer av resurser som ska tas med. Skriv till exempel \_ = patient returnerar endast patient resurser|
| \_typefilter | Ja | Om du vill begära detaljerad filtrering kan du använda \_ typeFilter tillsammans med \_ typ parametern. Värdet för parametern _typeFilter är en kommaavgränsad lista över FHIR-frågor som ytterligare begränsar resultaten |
| \_fönster | Inga |  Anger behållaren i det konfigurerade lagrings kontot där data ska exporteras. Om en behållare anges exporteras data till den behållaren i en ny mapp med namnet. Om behållaren inte anges exporteras den till en ny behållare med hjälp av timestamp och jobb-ID. |

## <a name="secure-export-to-azure-storage"></a>Säker export till Azure Storage

Azure API för FHIR har stöd för en säker export åtgärd. Ett alternativ för att köra en säker export är att tillåta att vissa IP-adresser som är kopplade till Azure API för FHIR får åtkomst till Azure Storage-kontot. Konfigurationerna skiljer sig beroende på om lagrings kontot finns på samma eller en annan plats än Azure-API: t för FHIR.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>När Azure Storage-kontot finns i en annan region

Välj bladet nätverk för Azure Storage-kontot från portalen. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Azure Storage nätverks inställningar." lightbox="media/export-data/storage-networking.png":::
   
Välj "valda nätverk" och ange IP-adressen i rutan **adress intervall** under avsnittet i brand väggen \| Lägg till IP-intervall för att tillåta åtkomst från Internet eller dina lokala nätverk. Du hittar IP-adressen från tabellen nedan för den Azure-region där Azure API för FHIR-tjänsten är etablerad.

|**Azure-region**         |**Offentlig IP-adress** |
|:----------------------|:-------------------|
| Australien, östra       | 20.53.44.80       |
| Kanada, centrala       | 20.48.192.84      |
| Central US           | 52.182.208.31     |
| East US              | 20.62.128.148     |
| USA, östra 2            | 20.49.102.228     |
| USA, östra 2 EUAP       | 20.39.26.254      |
| Tyskland, norra        | 51.116.51.33      |
| Tyskland, västra centrala | 51.116.146.216    |
| Japan, östra           | 20.191.160.26     |
| Sydkorea, centrala        | 20.41.69.51       |
| USA, norra centrala     | 20.49.114.188     |
| Europa, norra         | 52.146.131.52     |
| Sydafrika, norra   | 102.133.220.197   |
| USA, södra centrala     | 13.73.254.220     |
| Sydostasien       | 23.98.108.42      |
| Schweiz, norra    | 51.107.60.95      |
| Storbritannien, södra             | 51.104.30.170     |
| Storbritannien, västra              | 51.137.164.94     |
| USA, västra centrala      | 52.150.156.44     |
| Europa, västra          | 20.61.98.66       |
| USA, västra 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>När Azure Storage-kontot finns i samma region

Konfigurations processen är samma som ovan, förutom ett särskilt IP-adressintervall i CIDR-format används istället 100.64.0.0/10. Anledningen till att IP-adressintervallet, som innehåller 100.64.0.0 – 100.127.255.255, måste anges beror på att den faktiska IP-adressen som används av tjänsten varierar, men är inom intervallet för varje $export begäran.

> [!Note] 
> Det är möjligt att en privat IP-adress inom intervallet 10.0.2.0/24 kan användas i stället. I så fall kommer $export åtgärden att Miss lyckas. Du kan försöka med $export begäran, men det finns ingen garanti för att en IP-adress inom intervallet 100.64.0.0/10 kommer att användas nästa gången. Det är det som är känt nätverks beteendet genom att utforma. Alternativet är att konfigurera lagrings kontot i en annan region.
    
## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du exporterar FHIR-resurser med hjälp av kommandot $export. Sedan kan du läsa mer om hur du exporterar de data som identifieras:
 
>[!div class="nextstepaction"]
>[Exportera de data som har identifierats](de-identified-export.md)
