---
title: Ansluta en datafabrik till Azure Purview
description: Lär dig hur du ansluter en Data Factory till Azure-avdelningens kontroll
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: d1a07a3733bddc1516a4d2e19f3cac31a7702954
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944940"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Ansluta Data Factory till Azure-avdelningens kontroll (för hands version)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du ansluter Data Factory till Azure avdelningens kontroll och hur du rapporterar data härkomst för ADF-aktiviteter kopiera data, data flöde och kör SSIS-paket.

## <a name="connect-data-factory-to-azure-purview"></a>Anslut Data Factory till Azure-avdelningens kontroll
Azure dataavdelningens kontrolls är en ny moln tjänst som används av data användare centralt för att hantera data styrning över sin datafastighet, som sträcker sig över moln-och lokal miljöer. Du kan ansluta din data fabrik till Azure-avdelningens kontroll och anslutningen gör att du kan använda Azure avdelningens kontroll för att fånga härkomst data från kopierings-, data flödes-och EXECUTE SSIS-paket. Information om hur du registrerar data Factory i Azure avdelningens kontroll finns i [så här ansluter du Azure Data Factory och Azure avdelningens kontroll](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Rapportera härkomst-data till Azure avdelningens kontroll
När kunder kör SSIS för att kopiera, data flöde eller köra-paket i Azure Data Factory kan kunder få beroende relationen och få en översikt över hela arbets flödes processen mellan data källor och mål.
Information om hur du samlar in härkomst från Azure Data Factory finns i [Data Factory härkomst](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Nästa steg
[Användar guide för katalog härkomst](../purview/catalog-lineage-user-guide.md)

[Självstudie: push Data Factory härkomst data till Azure avdelningens kontroll](turorial-push-lineage-to-purview.md)