---
title: Vanliga scenarier för Azure Data Catalog
description: En översikt över vanliga scenarier för Azure Data Catalog, inklusive registrering och identifiering av data källor med hög värde, aktivera självbetjänings Business Intelligence och samla in befintliga kunskaper om data källor och processer.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 4f76a9696d613a4a974c9ae7c7b2578e56f2fcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674622"
---
# <a name="azure-data-catalog-common-scenarios"></a>Vanliga scenarier för Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Den här artikeln visar vanliga scenarier där Azure Data Catalog kan hjälpa din organisation att hämta mer värde från befintliga data källor.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenario 1: registrering av centrala data källor
Organisationer har ofta många data källor med höga värden. De här data källorna innehåller OLTP-system (Line-of-Business, Online Transaction Processing), informations lager och Business Intelligence/analys-databaser. Antalet system, och överlappningen mellan dem, ökar vanligt vis med tiden när affärs behoven utvecklas och verksamheten utvecklas genom, till exempel fusioner och förvärv.

Det kan vara svårt för organisations medlemmar att veta var du kan hitta data i dessa data källor. Frågor som följande är för vanliga:

* Av de tre HR-systemen som används i företaget, som ska jag använda för att skapa den här typen av rapport?
* Var ska jag hämta de certifierade kund numren för det räkenskapsår som precis slutade?
* Vem ska jag ställa in eller vilken process jag ska använda för att få åtkomst till data lagret?
* Jag vet inte om talen är rätt. Vem kan jag be om insikter om hur dessa data ska användas innan jag delar den här instrument panelen med mitt team?

Med dessa och andra frågor kan Azure Data Catalog ge svar. De centrala, högpresterande, IT-hanterade data källor som används i organisationer är ofta den logiska start punkten för att fylla katalogen. Även om en användare kan registrera en data källa, där katalogen är igång med de data källor som är mest sannolika för att ge ett värde till det största antalet användare, hjälper det att implementera och använda systemet. 

Om du kommer igång med Azure Data Catalog kan identifiering och registrering av viktiga data källor som används av många olika grupper av data konsumenter vara det första steget att lyckas.

Det här scenariot ger också en möjlighet att kommentera data källor med höga värden så att de blir enklare att förstå och komma åt. En viktig aspekt av den här ansträngningen är att inkludera information om hur användare kan begära åtkomst till data källan. Med Azure Data Catalog kan du ange e-postadressen till den användare eller det team som ansvarar för att kontrol lera åtkomst till data källan, länkar till befintliga verktyg eller dokumentation eller fritext som beskriver processen för åtkomstbegäran. Den här informationen hjälper medlemmar som identifierar registrerade data källor men som ännu inte har behörighet att komma åt data för att enkelt begära åtkomst med hjälp av de processer som definieras och styrs av ägare av data källa.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenario 2: självbetjänings Business Intelligence
Även om traditionella företags lösningar för företags information fortsätter att vara en värdefull del av många organisationers data landskap, har den ändrade takten i verksamheten gjort självbetjänings-BI och viktigare. Genom att använda självbetjänings-BI kan informations arbetare och analytiker skapa egna rapporter, arbets böcker och instrument paneler utan att behöva lita på ett central IT-team eller begränsas av IT-teamets schema och tillgänglighet.

I självbetjänings BI-scenarier kombinerar användarna ofta data från flera källor, och många av dessa kanske inte tidigare har använts för BI och analyser. Även om vissa av dessa data källor kanske redan är kända, kan det vara svårt att upptäcka vad du ska göra för att hitta och utvärdera potentiella data källor för en viss uppgift.

Traditionellt är den här identifierings processen en manuell: analytiker använder sina peer-nätverksanslutningar för att identifiera andra som arbetar med de data som söks. När en data källa har hittats och använts upprepas processen igen för varje efterföljande självbetjänings-och BI-ansträngning, med flera användare som utför en redundant manuell process identifiering.

Med Azure Data Catalog kan din organisation avbryta den här arbets cykeln. När du har identifierat en data källa på traditionella sätt kan en analytiker registrera den så att den blir lättare att upptäcka av andra användare i framtiden. Även om analytikern kan lägga till mer värde genom att kommentera registrerade data till gångar, behöver denna anteckning inte ske samtidigt som registreringen. Användare kan bidra över tid, eftersom deras scheman tillåter, att gradvis lägga till värden i de data källor som är registrerade i katalogen.

Den här organiska tillväxten för katalog innehållet är ett naturligt komplement till den första registreringen av centrala data källor. Att fylla i katalogen med data som många användare behöver kan vara en motiverad första användning och identifiering. Att göra det möjligt för användare att registrera och kommentera ytterligare källor kan vara ett sätt att hålla dem och andra organisations medlemmar sysselsatta.

Det är värt att Observera att även om det här scenariot fokuserar på självbetjänings-BI, gäller samma mönster och utmaningar även storskaliga företags BI-projekt. Genom att använda Data Catalog kan din organisation förbättra all ansträngning som omfattar en manuell process för identifiering av data källor.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenario 3: fånga stambaserad-kunskap
Hur vet du vilka data du behöver för att utföra ditt jobb och var du hittar dessa data?

Om du har använt jobbet en stund vet du förmodligen bara. Du har genomgått en gradvis inlärnings process och över tid har lärt dig om de data källor som är viktiga för ditt dagliga arbete.

Hur vet personen vilka data som krävs för jobbet och var du hittar det när en ny medarbetare ansluter till ditt team?

Strider är den nya personen som medföljer dessa frågor.

Den här pågående överföringen av stambaserad-kunskap är en del av identifierings processen för data källor i organisationer som är stora och små. Fler erfarna och erfarna team medlemmar har skapat kunskaper under åren och nyare team medlemmar har lärt sig att ställa dem när de har frågor. Den viktigaste informationen finns ofta bara i huvudena om några viktiga personer, och när dessa personer är på semester eller lämnar gruppen, är organisationen lidande.

Data experter gör vanligt vis en ansträngning för att dokumentera sina kunskaper, dela den via e-post eller i Word-dokument på en SharePoint-webbplats för en grupp. Även om den här metoden kan vara värdefull, introducerar den ett nytt identifierings problem: Hur vet användarna vilken dokumentation som finns och var de hittar den?

Med Azure Data Catalog har din organisation en enda, central plats för att lagra och dela den här stambaserad-kunskapen och för att göra det enkelt att upptäcka. I Data Catalog kan dina data experter kommentera data till gångar direkt och tillhandahålla länkar till befintlig dokumentation. När organisations medlemmar använder katalogen för att identifiera en data källa hittar de inte bara själva källan, utan även den kunskap som tidigare endast fanns i ångrar i din organisations experter.
