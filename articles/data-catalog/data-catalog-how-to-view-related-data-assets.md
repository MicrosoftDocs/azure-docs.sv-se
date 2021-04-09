---
title: Så här visar du relaterade data till gångar i Azure Data Catalog
description: Den här artikeln förklarar hur du visar relaterade data till gångar för en vald data till gång i Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: c1c5ddcacfc529f8fc4ab9a70cea540c44da9fa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674792"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Hur visar jag relaterade data till gångar i Azure Data Catalog?

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Med Azure Data Catalog kan du visa data till gångar som är relaterade till en vald data till gång och Visa relationer mellan dem. 

## <a name="supported-data-sources"></a>Datakällor som stöds 
När du registrerar data till gångar från följande data källor registrerar Azure Data Catalog automatiskt metadata om kopplings relationer mellan de valda data till gångarna. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> För att Data Catalog ska kunna importera relationen mellan två data till gångar måste du registrera båda till gångarna på samma gång. Om du har lagt till en av dem separat lägger du till den igen och den andra data till gången för att importera relationen mellan dem.

## <a name="view-related-data-assets"></a>Visa relaterade datatillgångar
Om du vill visa data till gångar som är relaterade till en vald data uppsättning, använder du fliken **relationer** som du ser i följande bild: 

![Azure Data Catalog-Visa relaterade data till gångar](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

I det här exemplet finns det två relationer för den valda **ProductSubcategory** -data till gången: 

- Kolumnen ProductSubcategoryID i produkt tabellen har en sekundär nyckel relation med ProductSubcategoryID-kolumnen i den valda ProductSubcategory-tabellen. 
- Kolumnen ProductCategoryID i tabellen ProductSubCategory har en sekundär nyckel relation med ProductCategoryID-kolumnen för den valda ProductCategory-tabellen.

> [!NOTE]
> Observera pilens riktning i trädvyn relationer.  

Om du vill se mer information, till exempel det fullständigt kvalificerade namnet på kolumnen, flyttar du musen och ser ett popup-fönster som liknar följande bild: 

![Popup-fönster för Azure Data Catalog-relation](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Om du vill inkludera relationer mellan till gångar som redan har registrerats registrerar du dessa till gångar på nytt.

## <a name="next-steps"></a>Nästa steg
- [Hantera datatillgångar](data-catalog-how-to-manage.md)
