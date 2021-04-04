---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001764"
---
### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Med den **länkade tjänsten Azure Storage** kan du länka ett Azure Storage-konto till en Azure-datafabrik med hjälp av **konto nyckeln**, som tillhandahåller data fabriken med global åtkomst till Azure Storage. Följande tabell innehåller en beskrivning av JSON-element som är speciella för Azure Storage länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till: **AzureStorage** |Ja |
| Begär |Ange information som krävs för att ansluta till Azure Storage för egenskapen connectionString. |Ja |

Information om hur du hämtar åtkomst nycklar för lagrings kontot finns i [Hantera åtkomst nycklar för lagrings kontot](../articles/storage/common/storage-account-keys-manage.md).

**Exempel:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-länkad tjänst
En signatur för delad åtkomst (Shared Access Signature, SAS) ger delegerad åtkomst till resurser på ditt lagringskonto. Det gör att du kan ge en klient begränsad behörighet till objekt i ditt lagrings konto under en angiven tids period och med en angiven uppsättning behörigheter, utan att behöva dela dina konto åtkomst nycklar. SAS är en URI som omfattar den information som krävs för autentiserad åtkomst till en lagrings resurs i dess frågeparametrar. För att få åtkomst till lagrings resurser med SAS måste klienten bara skicka in SAS till lämplig konstruktor eller metod. Mer information om SAS finns i [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> Azure Data Factory har nu bara stöd för **tjänst-SAS** , men inte konto säkerhets associationer. Observera att SAS URL-generable från Azure Portal eller Storage Explorer är en konto säkerhets Association, vilket inte stöds.

> [!TIP]
> Du kan köra följande PowerShell-kommandon för att skapa en tjänst-SAS för ditt lagrings konto (Ersätt plats hållare och bevilja nödvändig behörighet): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Med den länkade tjänsten Azure Storage SAS kan du länka ett Azure Storage konto till en Azure-datafabrik med hjälp av en signatur för delad åtkomst (SAS). Den tillhandahåller data fabriken med begränsad/tidsbegränsad åtkomst till alla/angivna resurser (BLOB/container) i lagrings utrymmet. Följande tabell innehåller en beskrivning av JSON-element som är speciella för Azure Storage SAS-länkad tjänst. 

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till: **AzureStorageSas** |Ja |
| sasUri |Ange signatur-URI för delad åtkomst till Azure Storage resurser som BLOB, container eller Table.  |Ja |

**Exempel:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

När du skapar en **SAS-URI**, som beaktar följande:  

* Ange lämpliga Läs-/skriv **behörigheter** för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsa/skriva) används i din data fabrik.
* Ange **förfallo tid** på lämpligt sätt. Se till att åtkomsten till Azure Storage objekt upphör att gälla inom den aktiva perioden för pipelinen.
* URI: n måste skapas på rätt behållare/BLOB eller tabell nivå baserat på behovet. En SAS-URI till en Azure-Blob gör att Data Factorys tjänsten kan komma åt den specifika blobben. Med en SAS-URI till en Azure Blob-behållare kan Data Factorys tjänsten iterera genom blobbar i den behållaren. Om du behöver ge åtkomst mer/färre objekt senare, eller uppdatera SAS-URI: n, måste du komma ihåg att uppdatera den länkade tjänsten med den nya URI: n.   

