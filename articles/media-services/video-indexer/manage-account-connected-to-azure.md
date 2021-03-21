---
title: Hantera ett Video Indexer konto
titleSuffix: Azure Media Services
description: Lär dig hur du hanterar ett Video Indexer-konto som är anslutet till Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 25025efe5254442c4862563ce329cfae69e7d033
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898451"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Hantera ett Video Indexer-konto som är kopplat till Azure

Den här artikeln visar hur du hanterar ett Video Indexer-konto som är kopplat till din Azure-prenumeration och ett Azure Media Services-konto.

> [!NOTE]
> Du måste vara ägare till Video Indexer konto för att göra ändringar i konto konfigurationen som beskrivs i det här avsnittet.

## <a name="prerequisites"></a>Förutsättningar

Anslut ditt Video Indexer-konto till Azure enligt beskrivningen i [ansluta till Azure](connect-to-azure.md).

Se till att följa [krav](connect-to-azure.md#prerequisites-for-connecting-to-azure) och [överväganden](connect-to-azure.md#azure-media-services-considerations) vid granskning i artikeln.

## <a name="examine-account-settings"></a>Granska konto inställningar

Det här avsnittet undersöker inställningarna för ditt Video Indexer-konto.

Så här visar du inställningar:

1. Klicka på användar ikonen i det övre högra hörnet och välj **Inställningar**.

    ![Inställningar i Video Indexer](./media/manage-account-connected-to-azure/select-settings.png)

2. På sidan **Inställningar** väljer du fliken **konto** .

Om ditt video Indexer-konto är anslutet till Azure visas följande saker:

* Namnet på det underliggande Azure Media Services kontot.
* Antalet indexerings jobb som körs och har placerats i kö.
* Antalet och typen av allokerade reserverade enheter.

Om ditt konto behöver vissa justeringar visas relevanta fel och varningar om konto konfigurationen på sidan **Inställningar** . Meddelandena innehåller länkar till exakta platser i Azure Portal där du behöver göra ändringar. Mer information finns i avsnittet [fel och varningar](#errors-and-warnings) nedan.

## <a name="repair-the-connection-to-azure"></a>Reparera anslutningen till Azure

I dialog rutan **Uppdatera anslutning till Azure Media Services** på din [video Indexer](https://www.videoindexer.ai/) sida uppmanas du att ange värden för följande inställningar:

|Inställning|Beskrivning|
|---|---|
|ID för Azure-prenumeration|Prenumerations-ID kan hämtas från Azure Portal. Klicka på **alla tjänster** i den vänstra panelen och Sök efter "prenumerationer". Välj **prenumerationer** och välj önskat ID i listan med dina prenumerationer.|
|Namn på Azure Media Services resurs grupp|Namnet på resurs gruppen där du skapade Media Services-kontot.|
|Program-ID|Azure AD-programid (med behörigheter för det angivna Media Services kontot) som du skapade för det här Video Indexer-kontot. <br/><br/>Om du vill hämta app-ID: t navigerar du till Azure Portal. Under Media Services konto väljer du ditt konto och går till **API-åtkomst**. Välj **Anslut till Media Services-API med tjänstens huvud namn**  ->  **Azure AD App**. Kopiera de relevanta parametrarna.|
|Program nyckel|Den Azure AD-programnyckel som är kopplad till ditt Media Services konto som du angav ovan. <br/><br/>Om du vill hämta appens nyckel navigerar du till Azure Portal. Under Media Services konto väljer du ditt konto och går till **API-åtkomst**. Välj **Anslut till Media Services-API med tjänstens huvud namn**  ->  **hantera program**  ->  **certifikat & hemligheter**. Kopiera de relevanta parametrarna.|

## <a name="autoscale-reserved-units"></a>Autoskala reserverade enheter

På sidan **Inställningar** kan du ställa in automatisk skalning av reserverade enheter (ru). Om alternativet är **aktiverat** kan du allokera maximalt antal ru: er och se till att video Indexer stoppar/startar ru: er automatiskt. Med det här alternativet betalar du inte extra pengar för inaktiv tid, men du behöver inte heller vänta på att indexerings jobben ska slutföras under en längre tid när indexerings belastningen är hög.

Autoskalning skalar inte under 1 RU eller över standard gränsen för det Media Services kontot. Om du vill öka gränsen skapar du en tjänstbegäran. Information om kvoter och begränsningar och hur du öppnar ett support ärende finns i [kvoter och begränsningar](../../media-services/previous/media-services-quotas-and-limitations.md).

![Autoskala reserverade enheter Video Indexer](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Fel och varningar

Om ditt konto behöver vissa justeringar visas relevanta fel och varningar om konto konfigurationen på sidan **Inställningar** . Meddelandena innehåller länkar till exakta platser i Azure Portal där du behöver göra ändringar. Det här avsnittet innehåller mer information om fel-och varnings meddelanden.

* EventGrid

    Du måste registrera EventGrid-resurs leverantören med hjälp av Azure Portal. I [Azure Portal](https://portal.azure.com/)går du till **prenumerationer** > [prenumeration] > **ResourceProviders**  >  **Microsoft. EventGrid**. Om inte i det **registrerade** läget väljer du **Registrera**. Det tar några minuter att registrera sig.

* Slutpunkt för direktuppspelning

    Kontrol lera att det underliggande Media Servicess kontot har standard **slut punkten för direkt uppspelning** i ett start läge. Annars kan du inte titta på videor från det här Media Services kontot eller i Video Indexer.

* Mediereserverade enheter

    Du måste allokera medie reserverade enheter på din medie tjänst resurs för att kunna indexera videor. För optimal indexerings prestanda rekommenderar vi att du allokerar minst 10 S3-reserverade enheter. Information om priser finns i avsnittet Vanliga frågor och svar på sidan [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Nästa steg

Du kan interagera med ditt utvärderings konto eller Video Indexer konton som är anslutna till Azure genom att följa instruktionerna i: [använda API: er](video-indexer-use-apis.md).

Använd samma Azure AD-användare som du använde när du anslöt till Azure.
