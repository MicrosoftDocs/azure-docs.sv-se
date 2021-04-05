---
title: Azure Data Lake Storage gen 1-utdata från Azure Stream Analytics
description: I den här artikeln beskrivs Azure Data Lake Storage gen 1 som ett utmatnings alternativ för Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 629a154c89ad301a3e200b1d6cd04c62057d9959
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016549"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Data Lake Storage gen 1-utdata från Azure Stream Analytics

Stream Analytics stöder [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md) -utdata. Azure Data Lake Storage är en storskalig lagrings plats för stor data analys arbets belastningar. Du kan använda Data Lake Storage för att lagra data i valfri storlek, typ och inmatnings hastighet för operativa och undersökande analyser. Stream Analytics måste ha behörighet att komma åt Data Lake Storage.

Azure Data Lake Storage utdata från Stream Analytics är inte tillgängliga i regionerna Azure Kina 21Vianet och Azure Tyskland (T-Systems International).

## <a name="output-configuration"></a>Konfiguration av utdata

I följande tabell visas egenskaps namn och beskrivningar för att konfigurera Data Lake Storage gen 1-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera utdata från frågan till Data Lake Store. |
| Prenumeration | Den prenumeration som innehåller ditt Azure Data Lake Storage-konto. |
| Kontonamn | Namnet på det Data Lake Stores konto där du vill skicka utdata. En listruta visas med Data Lake Store konton som är tillgängliga i din prenumeration. |
| Mönster för Sök vägs prefix | Den fil Sök väg som används för att skriva filer inom det angivna Data Lake Store kontot. Du kan ange en eller flera instanser av variablerna {date} och {Time}:<br /><ul><li>Exempel 1: Mapp1/logs/{date}/{time}</li><li>Exempel 2: Mapp1/logs/{date}</li></ul><br />Tidstämpeln för den skapade mappstrukturen följer UTC och inte lokal tid.<br /><br />Om fil Sök vägens mönster inte innehåller ett avslutande snedstreck (/), behandlas det sista mönstret i fil Sök vägen som ett fil namns prefix. <br /><br />Nya filer skapas under följande omständigheter:<ul><li>Ändra i utdata schema</li><li>Extern eller intern omstart av ett jobb</li></ul> |
| Datum format | Valfritt. Om token-token används i prefixets sökväg kan du välja det datum format där filerna är ordnade. Exempel: ÅÅÅÅ/MM/DD |
|Tids format | Valfritt. Om tidstoken används i prefixets sökväg anger du tids formatet som filerna är ordnade i. För närvarande är det enda värde som stöds är HH. |
| Händelseserialiseringsformat | Serialiserings formatet för utdata. JSON, CSV och Avro stöds.|
| Kodning | Om du använder CSV eller JSON-format måste du ange en kodning. UTF-8 är det enda kodnings format som stöds just nu.|
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av CSV-data. Värden som stöds är komma, semikolon, blank steg, TABB och lodrätt fält.|
| Format | Gäller endast för JSON-serialisering. **Raden separerad** anger att utdata är formaterade genom att ha varje JSON-objekt avgränsat med en ny rad. Om du väljer **rad avgränsad** läses ett objekt i taget av JSON. Själva hela innehållet skulle inte vara en giltig JSON.  **Matris** anger att utdata är formaterad som en matris med JSON-objekt. Matrisen stängs bara när jobbet stoppas eller Stream Analytics har flyttats vidare till nästa tids period. I allmänhet är det bättre att använda line-separerad JSON, eftersom det inte kräver någon särskild hantering medan utdatafilen fortfarande skrivs till.|
| Autentiseringsläge | Du kan ge åtkomst till ditt Data Lake Storage-konto med hjälp av [hanterad identitet](stream-analytics-managed-identities-adls.md) (förhands granskning) eller användartoken. När du har beviljat åtkomst kan du återkalla åtkomsten genom att ändra användar kontots lösen ord, ta bort Data Lake Storage utdata för jobbet eller ta bort Stream Analyticss jobbet. |

## <a name="partitioning"></a>Partitionering

För partitionsnyckel använder du {date} och {Time} tokens i mönstret Path-prefix. Välj ett datum format, till exempel ÅÅÅÅ/MM/DD, DD/MM/ÅÅÅÅ eller MM-DD-ÅÅÅÅ. Använd HH som tids format. Antalet utgående skrivare följer indata-partitionering för [fullständiga kan göras parallella-frågor](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata

För maximal meddelande storlek, se [data Lake Storage gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits). Använd upp till 4 MB per Skriv åtgärd för att optimera batchstorleken.

## <a name="next-steps"></a>Nästa steg

* [Autentisera Stream Analytics för att Azure Data Lake Storage Gen1 med hanterade identiteter (för hands version)](stream-analytics-managed-identities-adls.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)