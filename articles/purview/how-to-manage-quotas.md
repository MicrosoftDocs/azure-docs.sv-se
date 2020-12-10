---
title: Hantera resurser och kvoter
titleSuffix: Azure Purview
description: Lär dig mer om kvoter och begränsningar för resurser för Azure-avdelningens kontroll och hur du begär kvot ökningar.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938842"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Hantera och öka kvoter för resurser med Azure avdelningens kontroll
 
Azure avdelningens kontroll är en moln tjänst som används av data användare. Du kan använda Azure-avdelningens kontroll för att centralt hantera data styrning över din datafastighet, och sträcker sig över både moln-och lokal miljöer. Med tjänsten kan affärsanalytiker söka efter relevanta data med hjälp av meningsfulla affärs villkor. Kontakta supporten om du vill höja gränserna upp till max gränsen för din prenumeration.
 
## <a name="azure-purview-limits"></a>Azure Purview-gränser
 
|**Resurs**|  **Standard gräns**  |**Övre gräns**|
|---|---|---|
|Avdelningens kontroll-konton per region, per klient (alla prenumerationer kombinerade)|3|Kontakta supporten|
|Virtuella kärnor tillgängligt för genomsökning, per konto *|160|160|
|Samtidiga genomsökningar per konto vid en viss tidpunkt. Gränsen baseras på den typ av data källor som genomsöks *|5 | 10 |
|Den längsta tid som en genomsökning kan köras för|7 dagar|7 dagar|
|API-anrop, per konto|10 miljoner-API: er/månad för 4 kapacitets enheter plattforms storlek. <br>40M-API: er/månad för 16 kapacitets enheter plattforms storlek |10 miljoner-API: er/månad för 4 kapacitets enheter plattforms storlek. <br>40M-API: er/månad för 16 kapacitets enheter plattforms storlek|
|Lagring, per konto|10 GB för 4 kapacitets enheter plattforms storlek. <br>40 GB för 16 kapacitets enheter plattforms storlek |10 GB för 4 kapacitets enheter plattforms storlek. <br> 40 GB för 16 kapacitets enheter plattforms storlek |
|Storlek på till gångar per konto|100 miljoner fysiska till gångar |Kontakta supporten|
|Maximal storlek på en till gång i en katalog|2 MB|2 MB|
|Maximal längd på ett till gångs namn och klassificerings namn|4 kB|4 kB|
|Maximal längd på till gångs egenskaps namn och-värde|32 KB|32 KB|
|Maximal längd på klassificering av attributnamn och värde|32 KB|32 KB|
|Maximalt antal ord listans villkor, per konto|100K|100K|
 
* Scenarier för integration runtime med egen värd är utanför omfånget för de gränser som anges i tabellen ovan. 
 
## <a name="next-steps"></a>Nästa steg
 
> [!div class="nextstepaction"]
>[Självstudie: Genomsök data med Azure avdelningens kontroll](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Självstudie: gå till start sidan och Sök efter en till gång](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Självstudie: Bläddra till gångar och visa deras härkomst](tutorial-browse-and-view-lineage.md)
