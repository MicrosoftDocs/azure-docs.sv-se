---
title: Rekommendations sammandrag för Azure Advisor
description: Hämta periodisk Sammanfattning för dina aktiva rekommendationer
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 1c82abf3c8bea07941f860b94fcfe92954fb7641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579815"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Konfigurera regelbunden Sammanfattning för rekommendationer

Sammandrag av Advisor- **rekommendation** ger ett enkelt och proaktivt sätt att hålla koll på dina aktiva rekommendationer, i olika kategorier. Funktionen ger möjlighet att konfigurera periodiska meddelanden för sammanfattningen av alla dina aktiva rekommendationer, i olika kategorier. Du kan välja önskad kanal för meddelanden som e-post, SMS eller andra med hjälp av åtgärds grupper. Den här artikeln visar hur du konfigurerar ett **rekommendations sammandrag** för rekommendationerna i Advisor.


## <a name="setting-up-your-recommendation-digest"></a>Konfigurera din rekommendations sammandrag 

**Rekommendationen** att skapa sammandrag hjälper dig att konfigurera sammanfattningen. Du kan välja följande parametrar för konfigurationer:
1. Kategori: vi har rekommendations kategorier som kostnad, hög tillgänglighet, prestanda och drift kvalitet. Funktionen är inte tillgänglig för säkerhets rekommendationer än.
2. Frekvensen sammandrag: frekvensen för sammanfattnings meddelanden kan vara veckovis, varannan vecka och varje månad.
3. Åtgärds grupp: du kan antingen välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp. Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../azure-monitor/alerts/action-groups.md).
4. Språk för sammanfattningen
5. Namn på rekommendations sammandrag: du kan använda en användarvänlig sträng för att bättre spåra och övervaka sammanfattningarna.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Steg för att skapa rekommendations sammandrag i Azure Portal

Här följer stegen för att skapa **rekommendation sammandrag:**
* **Steg 1:** I Azure Portal går du till **Advisor** och under avsnittet **övervakning** väljer du **rekommendation sammandrag** 

   ![Rekommendation sammandrag post-Point](./media/digest-0.png)

* **Steg 2:** Välj **ny rekommendation sammandrag** från översta raden enligt nedan:

   ![Skapa rekommendations sammandrag](./media/digest-5.png)

* **Steg 3:** I avsnittet **omfattning** väljer du **prenumerationen** för din sammanfattning

   ![Tillhandahåll sammanfattnings indata för rekommendation](./media/digest-1.png)

* **Steg 4:** I avsnittet **villkor** väljer du konfigurationer som **kategori**, **frekvens** och **språk**

   ![Tillhandahåll ingångs villkor för rekommendations Sammanfattning](./media/digest-2.png)

* **Steg 5:** I avsnittet **Åtgärds grupp** väljer du **Åtgärds gruppen** för sammanfattningen. Du kan lära dig mer här – [skapa och hantera åtgärds grupper](../azure-monitor/alerts/action-groups.md)

   ![Tillhandahåll åtgärds grupp för sammanfattning av rekommendation](./media/digest-3.png)

* **Steg 6:** I det här sista avsnittet för **sammanfattnings information** kan du tilldela namn och tillstånd till din rekommendation sammandrag. Klicka på **skapa rekommendation sammandrag** för att slutföra inställningen.
   ![Slutför skapande av rekommendations sammandrag](./media/digest-4.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Rekommendationer om Advisor-kostnader](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
