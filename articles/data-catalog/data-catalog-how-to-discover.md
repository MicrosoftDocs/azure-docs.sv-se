---
title: Identifiera data källor i Azure Data Catalog
description: I den här artikeln beskrivs hur du identifierar registrerade data till gångar med Azure Data Catalog, inklusive sökning och filtrering och användning av träff markerings funktionerna i Azure Data Catalogs portalen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52aaa11278e5bb523594936c75d6810c1638fa7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674945"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Identifiera data källor i Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion

Azure Data Catalog är en helt hanterad moln tjänst som fungerar som ett registrerings-och identifierings system för företags data källor. Med andra ord kan Data Catalog hjälpa människor att upptäcka, förstå och använda data källor. Det hjälper organisationer att få mer värde än befintliga data. När en data källa har registrerats med Data Catalog indexeras dess metadata av tjänsten så att du enkelt kan söka efter de data du behöver.

## <a name="searching-and-filtering"></a>Sökning och filtrering

Identifiering i Data Catalog använder två primära mekanismer: sökning och filtrering.

Sökningen har utformats att vara både intuitiv och kraftfull. Som standard matchas sökvillkor mot en egenskap i katalogen, inklusive kommentarer av användaren.

Filtreringen är avsedd att komplettera sökningen. Du kan välja särskilda egenskaper, till exempel experter, typ av data källa, objekt typ och taggar. Du kan bara Visa matchande data till gångar och begränsa Sök resultaten till matchande till gångar.

Genom att använda en kombination av sökning och filtrering kan du snabbt navigera i data källorna som har registrerats med Data Catalog för att identifiera de data källor som du behöver.

## <a name="search-syntax"></a>Söksyntax

Standardvärdet för texts ökning är enkelt och intuitivt, men du kan också använda Data Catalog söksyntax för att få bättre kontroll över Sök resultaten. Data Catalog search stöder följande tekniker:

| Teknik | Användning | Exempel |
| --- | --- | --- |
| Grundläggande sökning |Grundläggande sökning som använder en eller flera Sök villkor. Resultat är alla till gångar som matchar en egenskap med en eller flera av de angivna villkoren. |`sales data` |
| Egenskaps omfång |Returnera endast data källor där Sök termen matchar den angivna egenskapen. |`name:finance` |
| Booleska operatorer |Utöka eller begränsa en sökning med hjälp av booleska åtgärder. |`finance NOT corporate` |
| Gruppera med parentes |Använd parenteser för att gruppera delar av frågan för att uppnå en logisk isolering, särskilt i kombination med booleska operatorer. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Jämförelseoperatorer |Använd andra jämförelser än jämlikhet för egenskaper som har numeriska data typer och datum. |`modifiedTime > "11/05/2014"` |

Mer information om Data Catalog sökning finns i [Azure Data Catalog](/rest/api/datacatalog/#search-syntax-reference) artikeln.

## <a name="hit-highlighting"></a>Träffmarkering

När du visar Sök resultaten markeras alla egenskaper som matchar de angivna Sök villkoren (till exempel namn, beskrivning och taggar för data till gångar) för att göra det enklare att identifiera varför en viss data till gång returnerades av en viss sökning.

> [!NOTE]
> Om du vill inaktivera träff markeringar använder du **markerings** växeln i Data Catalog-portalen.

När du visar Sök resultat är det inte alltid uppenbart varför en data till gång ingår, även om träff markering har Aktiver ATS. Eftersom alla egenskaper genomsöks som standard kan en data till gång returneras på grund av en matchning på en egenskap på en kolumn nivå. Eftersom flera användare kan kommentera registrerade data till gångar med sina egna taggar och beskrivningar, visas inte alla metadata i listan med Sök resultat.

I vyn standard panel visas varje panel i Sök resultatet en ikon för **visnings Sök villkor** , så att du snabbt kan se antalet matchningar och deras plats och för att hoppa till dem om du vill.

 ![Träff markeringar och Sök matchningar i Azure Data Catalog Portal](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Sammanfattning

Eftersom registrering av en data källa med Data Catalog kopierar strukturella och beskrivande metadata från data källan till katalog tjänsten blir data källan lättare att upptäcka och förstå. När du har registrerat en data källa kan du identifiera den med hjälp av filtrering och sökning i Data Catalog portalen.

## <a name="next-steps"></a>Nästa steg

* Steg för steg-information om hur du identifierar data källor finns i [Kom igång med Azure Data Catalog](data-catalog-get-started.md).
