---
title: URL-typer som stöds för import-QnA Maker
description: 'Förstå hur olika typer av URL: er används för att importera och skapa QnA-par.'
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91776705"
---
# <a name="urls-supported-for-importing-documents"></a>URL: er som stöds för att importera dokument

Förstå hur olika typer av URL: er används för att importera och skapa QnA-par.

## <a name="faq-urls"></a>URL: er för vanliga frågor

QnA Maker kan stödja vanliga frågor och svar webb sidor i tre olika former:

* Vanliga FAQ-sidor
* Vanliga frågor och svar-sidor med länkar
* Vanliga frågor och svar med en sida med ämnes sidor

### <a name="plain-faq-pages"></a>Vanliga FAQ-sidor

Detta är den vanligaste typen av vanliga frågor och svar, där svaren omedelbart följer frågorna på samma sida.

Nedan visas ett exempel på en sida med vanliga frågor och svar:

![Exempel sidan Vanliga frågor och svar om en kunskaps bas](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Vanliga frågor och svar-sidor med länkar

På den här typen av vanliga frågor och svar sammanställs frågor tillsammans och är länkade till svar som finns i olika delar av samma sida eller på olika sidor.

Nedan visas ett exempel på en sida med vanliga frågor och svar med länkar i avsnitt som finns på samma sida:

 ![Exempel på avsnittet Vanliga frågor och svar om avsnitts länk för en kunskaps bas](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Sidan för överordnad ämnes sida länkar till underordnade svars sidor

Den här typen av vanliga frågor och svar innehåller en avsnitts sida där varje ämne är länkat till en motsvarande uppsättning frågor och svar på en annan sida. QnA Maker crawla alla länkade sidor för att extrahera motsvarande frågor & svar.

Nedan visas ett exempel på en ämnes sida med länkar till FAQ-avsnitt på olika sidor.

 ![Exempel på vanliga frågor om djupgående Länkar för en kunskaps bas](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Support-URL: er

QnA Maker kan bearbeta delvis strukturerade support webb sidor, till exempel webb artiklar som beskriver hur du utför en specifik uppgift, hur du diagnostiserar och löser ett specifikt problem och vilka metoder som är bästa praxis för en specifik process. Extraktion fungerar bäst på innehåll som har en tydlig struktur med hierarkiska rubriker.

> [!NOTE]
> Extrahering av support artiklar är en ny funktion och är i tidiga steg. Det fungerar bäst för enkla sidor, som är väl strukturerade och inte innehåller komplexa sidhuvuden och sid fötter.

![QnA Maker stöder extrahering från halv strukturerade webb sidor där en tydlig struktur visas med hierarkiska rubriker](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)