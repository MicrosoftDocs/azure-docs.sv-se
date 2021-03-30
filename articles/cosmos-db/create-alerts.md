---
title: Skapa aviseringar för Azure Cosmos DB med Azure Monitor
description: Lär dig hur du konfigurerar aviseringar för Azure Cosmos DB med hjälp av Azure Monitor.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: e29db7e31438bc7f6ac609384d0d9b92c275e813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339554"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Skapa aviseringar för Azure Cosmos DB med Azure Monitor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Aviseringar används för att ställa in återkommande tester för att övervaka tillgängligheten och svars tiden för dina Azure Cosmos DB resurser. Aviseringar kan skicka ett meddelande i form av ett e-postmeddelande eller köra en Azure-funktion när ett av dina mått når tröskelvärdet eller om en händelse loggas i aktivitets loggen.

Du kan få en avisering baserat på måtten eller aktivitets logg händelser på ditt Azure Cosmos-konto:

* **Mått** – aviseringen utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Till exempel när det totala antalet förbrukade enheter överskrider 1000 RU/s. Den här aviseringen utlöses när villkoret först uppfylls och sedan efteråt när villkoret inte längre uppfylls. Se artikeln [övervaknings data referens](monitor-cosmos-db-reference.md#metrics) för olika mått som är tillgängliga i Azure Cosmos dB.

* **Aktivitets logg händelser** – den här aviseringen utlöses när en viss händelse inträffar. Till exempel när nycklarna för ditt Azure Cosmos-konto kan nås eller uppdateras.

Du kan ställa in aviseringar från fönstret Azure Cosmos DB eller Azure Monitor tjänsten i Azure Portal. Båda gränssnitten ger samma alternativ. Den här artikeln visar hur du konfigurerar aviseringar för Azure Cosmos DB med hjälp av Azure Monitor.

## <a name="create-an-alert-rule"></a>Skapa en varningsregel

Det här avsnittet visar hur du skapar en avisering när du får en HTTP-statuskod 429, som tas emot när begäran är begränsad. Till exempel kanske du vill få en avisering när det finns 100 eller fler hastighets begränsade begär Anden. Den här artikeln visar hur du konfigurerar en avisering för ett sådant scenario genom att använda HTTP-statuskod. Du kan också använda samma steg för att konfigurera andra typer av aviseringar, men du behöver bara välja ett annat villkor baserat på ditt krav.

1. Logga in på [Azure Portal.](https://portal.azure.com/)

1. Välj **övervaka** i det vänstra navigerings fältet och välj **aviseringar**.

1. Klicka på knappen Ny varnings regel för att öppna fönstret Skapa aviserings regel.  

1. Fyll i avsnittet **omfattning** :

   * Öppna fönstret **Välj resurs** och konfigurera följande:

   * Välj ditt **prenumerations** namn.

   * Välj **Azure Cosmos DB konton** för **resurs typen**.

   * **Platsen** för ditt Azure Cosmos-konto.

   * När du har fyllt i informationen visas en lista över Azure Cosmos-konton i det valda omfånget. Välj den som du vill konfigurera aviseringar för och välj **färdig**.

1. Fyll i avsnittet **villkor** :

   * Öppna fönstret **Välj villkor** för att öppna sidan **Konfigurera signal logik** och konfigurera följande:

   * Välj en signal. **Signal typen** kan vara ett **mått** eller en **aktivitets logg**. Välj **mått** för det här scenariot. Eftersom du vill få en avisering när det finns problem med begränsnings begränsningen på det totala måttet för enheter för begäran.

   * Välj **alla** för **övervaknings tjänsten**

   * Välj ett **signal namn**. Om du vill få en avisering för HTTP-statuskod väljer du den totala signalen för **enheter för förfrågningar** .

   * På nästa flik kan du definiera logiken för att utlösa en avisering och använda diagrammet för att visa trender för ditt Azure Cosmos-konto. Måttet för **Totalt antal begär ande enheter** stöder dimensioner. Med de här dimensionerna kan du filtrera på måttet. Om du inte väljer någon dimension ignoreras värdet.

   * Välj **StatusCode** som **Dimensions namn**. Välj **Lägg till anpassat värde** och ange status koden till 429.

   * Ange **tröskelvärdet** som **statisk** i **aviserings logiken**. Det statiska tröskelvärdet använder ett användardefinierat tröskelvärde för att utvärdera regeln, medan de dynamiska tröskelvärdena använder inbyggda Machine Learning-algoritmer för att kontinuerligt lära sig att använda måttet för mått beteendet och beräkna tröskelvärdena automatiskt.

   * Ange att **operatören** ska vara **större än**, **agg regerings typen** till **Total** och **tröskelvärdet** till **100**. Med den här logiken utlöses aviseringen om klienten ser fler än 100 förfrågningar som har 429-status kod. Du kan också konfigurera agg regerings typ, sammansättnings precision och utvärderings frekvensen utifrån ditt krav.

   * När du har fyllt formuläret väljer du **klar**. Följande skärm bild visar information om aviserings logiken:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="Konfigurera logiken för att ta emot aviseringar för begränsade/429-begäranden":::

1. Fyll i avsnittet **Åtgärds grupp** :

   * I fönstret **Skapa regel** väljer du en befintlig åtgärds grupp eller skapar en ny åtgärds grupp. Med en åtgärds grupp kan du definiera vilken åtgärd som ska vidtas när ett varnings tillstånd inträffar. I det här exemplet skapar du en ny åtgärds grupp för att få ett e-postmeddelande när aviseringen utlöses. Öppna fönstret **Lägg till åtgärds grupp** och fyll i följande information:

   * **Åtgärds grupp namn** -åtgärds gruppens namn måste vara unikt inom en resurs grupp.

   * **Kortnamn** -åtgärds gruppens kort namn. det här värdet ingår i e-post-och SMS-meddelanden för att identifiera vilken åtgärds grupp som är källan till meddelandet.

   * Välj den prenumeration och resurs grupp som den här åtgärds gruppen ska skapas i.  

   * Ange ett namn för åtgärden och välj **e-post/SMS-meddelande/push/röst** som **Åtgärds typ**. Följande skärm bild visar information om åtgärds typen:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="Konfigurera åtgärds typen, t. ex. e-postavisering, för att ta emot aviseringen":::

1. Fyll i avsnittet **aviserings regel information** :

   * Definiera ett namn för regeln, ange en valfri beskrivning, allvarlighets grad för aviseringen, Välj om du vill aktivera regeln när regeln skapas och välj sedan **Skapa regel avisering** för att skapa mått regel varningen.

När aviseringen har skapats aktive ras den inom 10 minuter.

## <a name="common-alerting-scenarios"></a>Vanliga aviserings scenarier

Här följer några scenarier där du kan använda aviseringar:

* När nycklarna för ett Azure Cosmos-konto uppdateras.
* När data-eller index användningen för en behållare, databas eller region överskrider ett visst antal byte.
* När den normaliserade RU/s-förbrukningen är större än en viss procent andel. Det normaliserade RU-förbruknings måttet ger den maximala data flödes användningen i en replik uppsättning. Mer information finns i artikeln [så här övervakar du normaliserade ru/s-](monitor-normalized-request-units.md) artiklar.  
* När en region läggs till, tas bort eller om den går offline.
* När en databas eller en behållare skapas, tas bort eller uppdateras.
* När data flödet i databasen eller behållaren ändras.

## <a name="next-steps"></a>Nästa steg

* [Övervaka normaliserat ru/s-mått](monitor-normalized-request-units.md) i Azure Cosmos-behållaren.
* [Övervaka data flödet eller begär enhets användningen](monitor-request-unit-usage.md) för en åtgärd i Azure Cosmos dB.