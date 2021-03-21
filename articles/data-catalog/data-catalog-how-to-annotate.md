---
title: Kommentera data källor i Azure Data Catalog
description: Instruktions artikel som markerar hur du kommenterar data till gångar i Azure Data Catalog, inklusive egna namn, taggar, beskrivningar och experter.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 89fa1b542c54fc962f8c12a2142884c677afb259
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674656"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Kommentera data källor i Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion

**Microsoft Azure Data Catalog** är en fullständigt hanterad moln tjänst som fungerar som ett system för registrering och identifierings system för företags data källor. Med andra ord är Data Catalog att hjälpa människor att upptäcka, förstå och använda data källor och hjälpa organisationer att få mer värde än befintliga data. När en data källa registreras med Data Catalog kopieras och indexeras dess metadata av tjänsten, men artikeln slutar inte där. Data Catalog gör det möjligt för användarna att tillhandahålla egna beskrivande metadata – till exempel beskrivningar och taggar – för att komplettera de metadata som extraheras från data källan och för att göra data källan mer begriplig för fler personer.

## <a name="annotation-and-crowdsourcing"></a>Anteckningar och gemensamt skapade
Alla har fått ett yttrande. Och det här är en bra sak.
Data Catalog känner av att olika användare har olika perspektiv i företags data källor och att vart och ett av dessa perspektiv kan vara värdefulla. Föreställ dig följande scenario:

* System administratören känner till service nivå avtalet för de servrar eller tjänster som är värdar för data källan.
* Databas administratören känner till schemat för säkerhets kopiering för varje databas och det tillåtna ETL-bearbetnings fönstret.
* System ägaren vet att användare kan begära åtkomst till data källan.
* Data bevarandet känner till hur till gångar och attribut i data källan mappar till företags data modellen.
* Analytikern vet hur data används i samband med de affärs processer som de stöder.

Vart och ett av dessa perspektiv är värdefullt och Data Catalog använder en gemensamt skapade metod för metadata som gör att de kan samlas in och användas för att tillhandahålla en komplett bild av registrerade data källor. Med hjälp av Data Catalog Portal kan varje användare lägga till och redigera sina egna anteckningar samtidigt som du kan Visa anteckningar från andra användare.

## <a name="different-types-of-annotations"></a>Olika typer av anteckningar
Data Catalog stöder följande typer av anteckningar:

| Anteckning | Kommentarer |
| --- | --- |
| Eget namn |Du kan ange egna namn på data till gångs nivå för att göra data till gångar lättare att förstå. Användarvänliga namn är mest användbara när det underliggande objekt namnet är krypterat, förkortat eller på annat sätt inte meningsfullt för användarna. |
| Beskrivning |Beskrivningar kan anges i nivåerna data till gång och attribut/kolumn. Beskrivningar är kostnads fria korta text anteckningar som beskriver användarens perspektiv på data till gången eller dess användning. |
| Taggar (användar etiketter) |Taggar kan anges i nivåerna data till gång och attribut/kolumn. Användar taggar är användardefinierade etiketter som kan användas för att kategorisera data till gångar eller attribut. |
| Taggar (ord listans taggar) |Taggar kan anges i nivåerna data till gång och attribut/kolumn. Ord listas taggar är centralt definierade ord uppsättningar som kan användas för att kategorisera data till gångar eller attribut med en gemensam affärstaxonomi. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md) |
| Låta |Experter kan ges på data till gångs nivå. Experter identifierar användare eller grupper med expert perspektiv på data och kan fungera som kontakt punkter för användare som identifierar registrerade data källor och har frågor som inte besvaras av befintliga anteckningar. |
| Begär åtkomst |Information om begär ande åtkomst kan anges på data till gångs nivå. Den här informationen är för användare som identifierar en data källa som de inte har behörighet att komma åt. Användare kan ange e-postadressen för den användare eller grupp som beviljar åtkomst, URL: en för den process eller det verktyg som användarna behöver för att få åtkomst, eller kan ange själva processen som text. |
| Dokumentation |Dokumentation kan anges på nivån data till gång. Till gångs dokumentationen är RTF-information som kan innehålla länkar och bilder, och som kan ge information som inte förmedlas via beskrivningar och taggar. |

## <a name="annotating-multiple-assets"></a>Kommentera flera till gångar
När du väljer flera data till gångar i Data Catalog Portal kan användarna kommentera alla valda till gångar i en enda åtgärd. Anteckningarna gäller för alla valda till gångar, vilket gör det enkelt att välja och tillhandahålla en konsekvent beskrivning och uppsättningar av taggar och experter för relaterade data till gångar.

> [!NOTE]
> Taggar och experter kan också tillhandahållas när du registrerar data till gångar med hjälp av Data Catalog registrerings verktyget för data källor.
>
>

När du väljer flera tabeller och vyer visas bara kolumner som alla valda data till gångar har gemensamt i Data Catalog portalen. Detta gör att användarna kan ange Taggar och beskrivningar för alla kolumner med samma namn för alla valda till gångar.

## <a name="annotations-and-discovery"></a>Anteckningar och identifiering
Precis som de metadata som extraheras från data källan under registreringen läggs till i Data Catalog Sök indexet indexeras även användardefinierade metadata. Det innebär att inte bara göra anteckningarna gör det enklare för användarna att förstå de data de känner till, vilket gör det enklare för användarna att upptäcka de kommenterade data till gångarna genom att söka med hjälp av de termer som passar dem bäst.

## <a name="summary"></a>Sammanfattning
Registrering av en data källa med Data Catalog gör att data kan identifieras genom att kopiera strukturella och beskrivande metadata från data källan till katalog tjänsten. När en data källa har registrerats kan användarna ge anteckningar som gör det lättare att identifiera och förstå i Data Catalog portalen.

## <a name="see-also"></a>Se även
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md) själv studie kurs för steg-för-steg-anvisningar om hur du kommenterar data källor.
