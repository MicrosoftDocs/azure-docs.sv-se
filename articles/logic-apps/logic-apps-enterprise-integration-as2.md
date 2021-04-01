---
title: Skicka och ta emot AS2-meddelanden för B2B
description: Exchange AS2-meddelanden för B2B Enterprise integration-scenarier med hjälp av Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 1845de8332287f85e96b4e29c40caa66cb8704d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91856678"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Utbyta AS2-meddelanden för B2B-företagsintegrering i Azure Logic Apps med Enterprise-integrationspaket

> [!IMPORTANT]
> Den ursprungliga AS2-anslutningen är inaktuell, så om du inte behöver spåra funktioner kan du använda **AS2-anslutningsprogrammet (v2)** i stället. Förutom spårning tillhandahåller v2-anslutaren samma funktioner som den ursprungliga versionen, är inbyggd i Logic Apps Runtime och har betydande prestanda förbättringar i meddelande storlek, data flöde och svars tid. V2-anslutaren kräver inte heller att du skapar en anslutning till ditt integrations konto. I stället bör du, enligt beskrivningen i kraven, se till att du länkar ditt integrations konto till den Logic-app där du planerar att använda anslutnings tjänsten.

Om du vill arbeta med AS2-meddelanden i Azure Logic Apps kan du använda AS2-anslutningen, som tillhandahåller utlösare och åtgärder som stöder och hanterar AS2-kommunikation (version 1,2). Om du till exempel vill skapa säkerhet och tillförlitlighet när du skickar meddelanden kan du använda följande åtgärder:

* [ **AS2** -åtgärd](#encode) för att tillhandahålla kryptering, digital signering och bekräftelser genom meddelande dispositions meddelanden (MDN) som hjälper till att stödja oavvislig het. Den här åtgärden använder till exempel AS2/HTTP-huvuden och utför dessa uppgifter när de konfigureras:

  * Signerar utgående meddelanden.
  * Krypterar utgående meddelanden.
  * Komprimerar meddelandet.
  * Överför fil namnet i MIME-huvudet.

* [ **AS2-avkodnings** åtgärd](#decode) för att tillhandahålla dekryptering, digital signering och bekräftelser via meddelanden om meddelande disposition (MDN). Den här åtgärden utför till exempel följande uppgifter:

  * Bearbetar AS2/HTTP-huvuden.
  * Synkroniserar mottagna MDNs med de ursprungliga utgående meddelandena.
  * Uppdaterar och korrelerar poster i databasen för oavvislig het.
  * Skriver poster för AS2 status rapportering.
  * Matar ut innehållet i nytto lasten som Base64-kodat.
  * Anger om MDNs krävs. Baserat på AS2-avtalet bestämmer om MDNs ska vara synkron eller asynkron.
  * Genererar synkron eller asynkron MDNs baserat på AS2-avtalet.
  * Anger korrelations-tokens och egenskaper för MDNs.

  Den här åtgärden utför även dessa uppgifter när de konfigureras:

  * Verifierar signaturen.
  * Dekrypterar meddelandena.
  * Expanderar meddelandet.
  * Kontrol lera och neka dubbletter av meddelande-ID.

Den här artikeln visar hur du lägger till AS2-kodning och avkodnings åtgärder i en befintlig Logic app.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic-app från vilken du vill använda AS2-anslutaren och en utlösare som startar din Logic app-arbetsflöde. AS2-anslutaren tillhandahåller endast åtgärder, inte utlösare. Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är associerat med din Azure-prenumeration och länkat till Logic-appen där du planerar att använda AS2-anslutningen. Både din Logic app och ditt integrations konto måste finnas på samma plats eller i Azure-regionen.

* Minst två [handels partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som du redan har definierat i integrations kontot med AS2-identitets kvalificeraren.

* Innan du kan använda AS2-anslutningsprogrammet måste du skapa ett AS2- [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md) mellan dina handels partner och lagra avtalet i ditt integrations konto.

* Om du använder [Azure Key Vault](../key-vault/general/overview.md) för certifikat hantering kontrollerar du att dina valv nycklar tillåter åtgärder för att **kryptera** och **dekryptera** . Annars fungerar inte kodnings-och avkodnings åtgärderna.

  I Azure Portal går du till nyckeln i ditt nyckel valv, granskar nyckelns **tillåtna åtgärder** och bekräftar att åtgärderna **kryptera** och **dekryptera** är markerade, till exempel:

  ![Kontrol lera valv nyckel åtgärder](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Koda AS2-meddelanden

1. Om du inte redan har gjort det går du till [Azure Portal](https://portal.azure.com)och öppnar din Logic app i Logic App Designer.

1. Lägg till en ny åtgärd i din Logic app i designern.

1. Under **Välj en åtgärd** och sökrutan väljer du **alla**. I sökrutan anger du "AS2 Encoder" och kontrollerar att du väljer AS2 (v2) åtgärd: **AS2-kodning**

   ![Välj "AS2 Encoder"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Ange nu information för dessa egenskaper:

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Meddelande att koda** | Meddelandets nytto Last |
   | **AS2 från** | ID för meddelande avsändaren som anges i ditt AS2-avtal |
   | **AS2 till** | Identifieraren för meddelande mottagaren som anges i ditt AS2-avtal |
   |||

   Exempel:

   ![Egenskaper för meddelande kodning](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Om du får problem när du skickar signerade eller krypterade meddelanden kan du prova med olika SHA256. AS2-specifikationen ger inte någon information om SHA256-format, så varje provider använder sin egen implementering eller sitt eget format.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Avkoda AS2-meddelanden

1. Om du inte redan har gjort det går du till [Azure Portal](https://portal.azure.com)och öppnar din Logic app i Logic App Designer.

1. Lägg till en ny åtgärd i din Logic app i designern.

1. Under **Välj en åtgärd** och sökrutan väljer du **alla**. I sökrutan anger du "AS2 avkoda" och ser till att du väljer AS2 (v2) åtgärd: **AS2-avkodning**

   ![Välj "AS2 avkoda"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. För att **meddelandet ska koda** och egenskaper för **meddelande rubriker** väljer du dessa värden från föregående utlösare eller åtgärds resultat.

   Anta till exempel att din Logic app tar emot meddelanden via en begär ande utlösare. Du kan välja utdata från den utlösaren.

   ![Välj brödtext och rubriker från begär ande utdata](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Exempel

Om du vill prova att distribuera en fullständigt fungerande Logic app och exempel scenario för AS2, se [mallen för AS2 Logic app och scenariot](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/as2/). 

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version de [B2B-meddelande gränserna för ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
