---
title: ta med fil
description: ta med fil
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 10aa9b06af439fe701c53ef736ec691167560f95
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109179"
---
Azure Data Factory är en tjänst för flera innehavare som har följande standard gränser på plats för att se till att kund prenumerationer skyddas från var and ras arbets belastningar. Kontakta supporten om du vill höja gränserna upp till max gränsen för din prenumeration.

### <a name="version-2"></a>Version 2

| Resurs | Standardgräns | Övre gräns |
| -------- | ------------- | ------------- |
| Data fabriker i en Azure-prenumeration | 800 | 800 |
| Totalt antal entiteter, till exempel pipelines, data uppsättningar, utlösare, länkade tjänster, privata slut punkter och integrerings körningar i en data fabrik | 5 000 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Totalt antal processor kärnor för Azure-SSIS integration runtime under en prenumeration | 256 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga pipeline körs per data fabrik som delas mellan alla pipeliner i fabriken | 10 000  | 10 000 |
| Samtidig extern aktivitet körs per prenumeration per [Azure integration runtime region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Externa aktiviteter hanteras i integration runtime men körs på länkade tjänster, inklusive Databricks, lagrad procedur, HDInsights, webb och andra. Den här begränsningen gäller inte för IR med egen värd.</small> | 3 000 | 3 000 |
| Samtidiga pipeline-aktiviteter per prenumeration per [Azure integration runtime region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>Pipeline-aktiviteter körs på integration runtime, inklusive lookup, GetMetadata och Delete. Den här begränsningen gäller inte för IR med egen värd.</small> | 1 000 | 1 000                                                        |
| Samtidiga redigerings åtgärder per prenumeration per [Azure integration runtime region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Ta med test anslutning, bläddra i Mapplista och tabell lista, förhandsgranska data. Den här begränsningen gäller inte för IR med egen värd.</small> | 200 | 200                                                          |
| Samtidig data integrerings enheter<sup>1</sup> förbrukning per prenumeration per [Azure integration runtime region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Region grupp 1<sup>2</sup>: 6 000<br>Region grupp 2<sup>2</sup>: 3 000<br>Region grupp 3<sup>2</sup>: 1 500 | Region grupp 1<sup>2</sup>: 6 000<br/>Region grupp 2<sup>2</sup>: 3 000<br/>Region grupp 3<sup>2</sup>: 1 500 |
| Maximalt antal aktiviteter per pipeline, som omfattar inre aktiviteter för behållare | 40 | 40 |
| Maximalt antal länkade integrerings körningar som kan skapas mot en enda egen värd för integration runtime | 100 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Högsta antal parametrar per pipeline | 50 | 50 |
| ForEach-objekt | 100 000 | 100 000 |
| Förgrunds parallellitet | 20 | 50 |
| Högsta antal köade körningar per pipeline | 100 | 100 |
| Tecken per uttryck | 8 192 | 8 192 |
| Intervall för utlösare för minsta rullande-fönster | 15 min | 15 min |
| Maximal tids gräns för körning av pipeline-aktiviteter | 7 dagar | 7 dagar |
| Byte per objekt för pipeline-objekt<sup>3</sup> | 200 KB | 200 KB |
| Byte per objekt för data uppsättning och länkade tjänst objekt<sup>3</sup> | 100 kB | 2 000 KB |
| Byte per nytto last för varje aktivitets körning<sup>4</sup> | 896 KB | 896 KB |
| Data integrerings enheter<sup>1</sup> per kopierings aktivitets körning | 256 | 256 |
| Skriv API-anrop | 1200/h | 1200/h<br/><br/> Denna gräns införs av Azure Resource Manager, inte Azure Data Factory. |
| Läs API-anrop | 12500/h | 12500/h<br/><br/> Denna gräns införs av Azure Resource Manager, inte Azure Data Factory. |
| Övervaka frågor per minut | 1 000 | 1 000 |
| Maximal tid för fel sökning av data flöde | 8 timmar | 8 timmar |
| Samtidiga antalet data flöden per integration runtime | 50 | [Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga antalet fel söknings sessioner per användare per fabrik | 3 | 3 |
| Data flöde Azure IR TTL-gräns | 4 timmar |  4 timmar |

<sup>1</sup> data integrerings enheten (DIU) används i en moln-till-moln-åtgärd, Läs mer från [data integrerings enheter (version 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Information om fakturering finns i [Azure Data Factory prissättning](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure integration runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) är [globalt tillgängligt](https://azure.microsoft.com/global-infrastructure/services/) för att säkerställa efterlevnad av data, effektivitet och lägre kostnader för nätverks utgång. 

| Region grupp | Regioner |
| -------- | ------ |
| Region grupp 1 | Centrala USA, östra USA, östra USA 2, norra Europa, Västeuropa, västra USA, västra USA 2 |
| Region grupp 2 | Östra Australien, sydöstra Australien, södra Brasilien, centrala Indien, Östra Japan, norra centrala USA, södra centrala USA, Sydostasien, västra centrala USA |
| Region grupp 3 | Andra regioner |

<sup>3</sup> pipeline, data uppsättning och länkade tjänst objekt representerar en logisk gruppering av arbets belastningen. Gränserna för de här objekten relaterar inte till mängden data som du kan flytta och bearbeta med Azure Data Factory. Data Factory är utformad för att skalas för att hantera petabyte av data.

<sup>4</sup> nytto lasten för varje aktivitets körning inkluderar aktivitets konfigurationen, de associerade data uppsättningarna och de länkade tjänst (s)-konfigurationerna, och en liten del av system egenskaper som genereras per aktivitets typ. Gränsen för den här nytto Last storleken relaterar inte till mängden data som du kan flytta och bearbeta med Azure Data Factory. Läs om [symptomen och rekommendationen](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large) om du når den här gränsen.

### <a name="version-1"></a>Version 1

| **Resurs** | **Standardgräns** | **Maximal gräns** |
| --- | --- | --- |
| Pipeliner inom en data fabrik |2 500 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Data uppsättningar i en data fabrik |5 000 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Samtidiga segment per data uppsättning |10 |10 |
| Byte per objekt för pipeline-objekt<sup>1</sup> |200 KB |200 KB |
| Byte per objekt för data uppsättning och länkade tjänst objekt<sup>1</sup> |100 kB |2 000 KB |
| Azure HDInsight-kluster kärnor på begäran i en prenumeration<sup>2</sup> |60 |[Kontakta supporten](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Moln data förflyttnings enheter per kopierings aktivitet kör<sup>3</sup> |32 |32 |
| Antal återförsök för pipeline-aktiviteter körs |1 000 |MaxInt (32 bitar) |

<sup>1</sup> pipeline, data uppsättning och länkade tjänst objekt representerar en logisk gruppering av arbets belastningen. Gränserna för de här objekten relaterar inte till mängden data som du kan flytta och bearbeta med Azure Data Factory. Data Factory är utformad för att skalas för att hantera petabyte av data.

<sup>2</sup> HDInsight-kärnor på begäran har allokerats ut från den prenumeration som innehåller data fabriken. Därför är den tidigare gränsen den Data Factory-tvingade kärn gränsen för HDInsight-kärnor på begäran. Den skiljer sig från den kärn gräns som är kopplad till din Azure-prenumeration.

<sup>3</sup> data förflyttnings enheten i molnet (DMU) för version 1 används i en moln-till-moln-kopiering, Läs mer från [enheter för moln data flytt (version 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Information om fakturering finns i [Azure Data Factory prissättning](https://azure.microsoft.com/pricing/details/data-factory/).

| **Resurs** | **Nedre standard gräns** | **Minsta gräns** |
| --- | --- | --- |
| Schema intervall |15 minuter |15 minuter |
| Intervall mellan återförsök |1 sekund |1 sekund |
| Timeout-värde för återförsök |1 sekund |1 sekund |

#### <a name="web-service-call-limits"></a>Begränsningar för webb tjänst anrop
Azure Resource Manager har gränser för API-anrop. Du kan göra API-anrop till en hastighet inom [Azure Resource Manager API-gränser](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
