---
title: Testa och publicera ett SaaS-erbjudande på Microsofts kommersiella marknads plats
description: Använd partner Center för att skicka ditt SaaS-erbjudande till för hands version, för hands version av erbjudandet, testa och publicera det på Microsofts kommersiella marknads plats.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ea647591194dfd6ba0bf818f4ce4d3a2005f0bc4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630795"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Testa och publicera ett SaaS-erbjudande på den kommersiella Marketplace

Den här artikeln förklarar hur du använder Partner Center för att skicka in ditt SaaS-erbjudande för publicering, för hands version av erbjudandet, testa det och sedan publicera det live på den kommersiella marknads platsen. Du måste redan ha skapat ett erbjudande som du vill publicera.

## <a name="submit-your-offer-for-publishing"></a>Skicka in ditt erbjudande om publicering

1. Logga in på instrument panelen för kommersiellt marknads platser i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. På sidan **Översikt** väljer du det erbjudande du vill publicera.
1. I det övre högra hörnet i portalen väljer du **Granska och publicera**.
2. Kontrol lera att kolumnen **status** för varje sida säger **slutförd**. De tre möjliga statusarna är följande:

   - **Inte startat** – sidan är ofullständig.
   - **Ofullständig** – sidan saknar nödvändig information eller innehåller fel som behöver åtgärdas. Du måste gå tillbaka till sidan och uppdatera den.
   - **Klar** – sidan har slutförts. Alla nödvändiga data har angetts och det finns inga fel.

1. Om någon av sidorna har en annan status än **slutförd** väljer du namnet på sidan, korrigerar problemet, sparar sidan och väljer sedan **Granska och publicera** igen för att återgå till den här sidan.
1. När alla sidor har slutförts, i rutan **kommentarer för certifiering** , anger du test instruktioner till certifierings teamet för att se till att appen testas korrekt. Ge eventuella kompletterande anteckningar som hjälper dig att förstå din app.
1. Starta publicerings processen för ditt erbjudande genom att välja **publicera**. Sidan **erbjudande översikt** visas och visar erbjudandets **publicerings status**.

Ditt erbjudandes publicerings status kommer att ändras när det flyttas genom publicerings processen. Detaljerad information om den här processen finns i [validerings-och publicerings steg](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Förhandsgranska och testa ditt erbjudande

När erbjudandet är klart för din signering skickar vi ett e-postmeddelande till dig som du kan granska och godkänna för hands versionen av erbjudandet. Du kan också uppdatera sidan **erbjudande översikt** i webbläsaren för att se om ditt erbjudande har nått utgivarens signerings fas. Om den har det är knappen **Go Live** och för hands versions länkarna tillgänglig. Det finns en länk till antingen Microsoft AppSource för hands version, Azure Marketplace Preview eller båda beroende på vilka alternativ du valde när du skapade erbjudandet. Om du har valt att sälja ditt erbjudande via Microsoft kan alla som har lagts till i förhands gransknings gruppen testa förvärvet och distributionen av ditt erbjudande för att säkerställa att det uppfyller dina krav under det här skedet.

Följande skärm bild visar sidan **erbjudande översikt** för ett SaaS-erbjudande med två för hands versions länkar under knappen **Go Live** . De verifierings steg som visas på den här sidan varierar beroende på vilka val du gjorde när du skapade erbjudandet.

![Visar sidan erbjudande översikt för ett erbjudande i Partner Center. Knappen gå live och för hands versions länkarna visas. Länken Visa verifierings rapport visas också under automatisk verifiering.](./media/review-publish-offer/publish-status-saas.png)

Använd följande steg för att förhandsgranska erbjudandet.

1. På sidan **erbjudande översikt** väljer du en förhands gransknings länk under knappen **gå live** .

1. Om du vill kontrol lera inköps-och installations flödet från slut punkt till slut punkt köper du ditt erbjudande när det är i för hands version. Meddela först Microsoft med ett [support ärende](https://aka.ms/marketplacesupport) för att se till att vi inte behandlar någon avgift.

1. Om ditt SaaS-erbjudande har stöd [för avgiftsbelagda faktureringar med hjälp av den kommersiella tjänsten för avläsning av marknads platser](./partner-center-portal/saas-metered-billing.md), kan du granska och följa de test metoder som beskrivs i [Marketplace för avgiftsbelagda fakturerings-API](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Granska och följ test instruktionerna i [SaaS-API version 2 på Microsofts kommersiella marknads](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) plats för att se till att ditt erbjudande integreras med API: erna innan du publicerar erbjudandet Live.

1. Om validerings steget för erbjudandet resulterade i varningar visas länken **Visa verifierings rapport** på sidan **erbjudande översikt** . Se till att granska rapporten och åtgärda problemen innan du väljer knappen **gå live** . Annars kommer certifieringen troligen att Miss förväntas och försena ditt erbjudande från att gå live.

1. Om du behöver göra ändringar efter att ha granskat och testat erbjudandet kan du redigera och skicka om för att publicera en ny för hands version. Mer information finns i [Uppdatera ett befintligt erbjudande i den kommersiella marknads platsen](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publicera ditt erbjudande Live

När du har slutfört alla tester i för hands versionen väljer du **Go Live** för att publicera erbjudandet Live på den kommersiella marknads platsen.

   > [!TIP]
   > Om ditt erbjudande redan är aktivt på den kommersiella marknads platsen, kommer alla uppdateringar du gör inte att vara aktiva förrän du väljer **Go Live**.

Nu när du har valt att göra ditt erbjudande tillgängligt på den kommersiella marknads platsen, utför vi en serie slutliga verifierings kontroller för att säkerställa att Live-erbjudandet har kon figurer ATS precis som för hands versionen av erbjudandet. Mer information om dessa verifierings kontroller finns i [publicerings fasen](review-publish-offer.md#publish-phase).

När dessa verifierings kontroller har slutförts är ditt erbjudande Live på Marketplace.

## <a name="next-steps"></a>Nästa steg

- [Få åtkomst till analys rapporter för den kommersiella marknads platsen i Partner Center](./partner-center-portal/analytics.md)
