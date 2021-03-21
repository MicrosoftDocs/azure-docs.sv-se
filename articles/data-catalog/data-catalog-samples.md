---
title: Exempel på Azure Data Catalog utvecklare
description: Den här artikeln innehåller en översikt över exempel på tillgängliga utvecklare för Data Catalog REST API.
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 15b48bc41e230ca5b9003675e2caab25741bcbfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674775"
---
# <a name="azure-data-catalog-developer-samples"></a>Exempel på Azure Data Catalog utvecklare

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Kom igång med att utveckla Azure Data Catalog appar med hjälp av Data Catalog REST API. Data Catalog REST API är ett REST-baserat API som ger programmatisk åtkomst till Data Catalog resurser för att registrera, kommentera och söka efter data till gångar program mässigt.

## <a name="samples-available-on-githubcom"></a>Exempel som är tillgängliga på GitHub.com

* [Kom igång med Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   Exemplet kom igång visar hur du autentiserar med Azure AD för att registrera, söka och ta bort en data till gång med hjälp av Data Catalog REST API.
   
* [Kom igång med Azure Data Catalog att använda tjänstens huvud namn](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   Det här exemplet visar hur du registrerar, söker och tar bort en data till gång med hjälp av Data Catalog REST API. I det här exemplet används autentisering av tjänstens huvud namn.

* [Import/export-verktyget för Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   Det här exemplet visar hur du använder Data Catalog REST API för att hämta resurser från Azure Data Catalog och serialisera dem till en fil. Den visar också hur du tar en uppsättning med till gångar som är serialiserade som JSON och push-överför dem till katalogen. Den stöder export av en delmängd av katalogen med hjälp av en Sök fråga.

* [Mass registrering och kommentarer i Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   Det här exemplet visar hur du kan massredigera data till gångar från en Excel-arbetsbok med hjälp av Data Catalog REST API och öppna XML.
  
* [Uttryck för Mass import av ord i Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   Det här exemplet visar hur du importerar ord listans villkor från CSV-filer till ADC ord lista.

* [Mass import av relationer till Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   Det här exemplet visar hur du program mässigt importerar Relations information från en CSV-fil till en data katalog.

* [Publicera relationer i Azure Data Catalog](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   Det här exemplet visar hur du kan publicera Relations information via programmering till en data katalog.
   
## <a name="next-steps"></a>Nästa steg
[Azure Data Catalog REST API referens](/rest/api/datacatalog/)
