---
title: Testa och publicera ett Azure Application erbjudande
description: Använd partner Center för att skicka in ditt erbjudande om Azure-program till för hands version, för hands version av erbjudandet, test och publicera det på Microsofts kommersiella marknads plats.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 461f9354bb3a6eae0af186de8fe9f39c6b5fff2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620927"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>Testa och publicera ett Azure Application erbjudande

Den här artikeln förklarar hur du använder Partner Center för att skicka in ditt Azure Application-erbjudande för publicering, för hands version av erbjudandet, testa det och sedan publicera det live på den kommersiella marknads platsen. Du måste redan ha skapat ett erbjudande som du vill publicera.

## <a name="submit-your-offer-for-publishing"></a>Skicka in ditt erbjudande om publicering

1. Logga in på instrument panelen för kommersiellt marknads platser i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. På sidan **Översikt** väljer du det erbjudande du vill publicera.
1. I det övre högra hörnet i portalen väljer du **Granska och publicera**.
1. Kontrol lera att kolumnen **status** för varje sida säger **slutförd**. De tre möjliga statusarna är följande:
    - **Inte startat** – sidan är ofullständig.
    - **Ofullständig** – sidan saknar nödvändig information eller innehåller fel som behöver åtgärdas. Du måste gå tillbaka till sidan och uppdatera den.
    - **Klar** – sidan har slutförts. Alla nödvändiga data har angetts och det finns inga fel.
1. Om någon av sidorna har en annan status än **slutförd** väljer du namnet på sidan, korrigerar problemet, sparar sidan och väljer sedan **Granska och publicera** igen för att återgå till den här sidan.
1. När alla sidor har slutförts, i rutan **kommentarer för certifiering** , anger du test instruktioner till certifierings teamet för att se till att appen testas korrekt. Ge eventuella kompletterande anteckningar som hjälper dig att förstå din app.
1. Starta publicerings processen för ditt erbjudande genom att välja **publicera**. Sidan **erbjudande översikt** visas och visar erbjudandets **publicerings status**.

Ditt erbjudandes publicerings status kommer att ändras när det flyttas genom publicerings processen. Detaljerad information om den här processen finns i [validerings-och publicerings steg](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Förhandsgranska och testa ditt erbjudande

När erbjudandet är klart för din signering skickar vi ett e-postmeddelande till dig som du kan granska och godkänna för hands versionen av erbjudandet. Du kan också uppdatera sidan **erbjudande översikt** i webbläsaren för att se om ditt erbjudande har nått utgivarens signerings fas. Om den har det är knappen **Go Live** och för hands versions länken tillgänglig. Om du har valt att sälja ditt erbjudande via Microsoft kan alla som har lagts till i förhands gransknings gruppen testa förvärvet och distributionen av ditt erbjudande för att säkerställa att det uppfyller dina krav under det här skedet.

Följande skärm bild visar sidan **erbjudande översikt** för ett erbjudande med två för hands versions länkar under knappen **gå live** . De verifierings steg som visas på den här sidan varierar beroende på vilka val du gjorde när du skapade erbjudandet.

[![Visar sidan erbjudande översikt för ett erbjudande i Partner Center. Knappen gå live och för hands versions länkarna visas.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

Använd följande steg för att förhandsgranska erbjudandet.

1. På sidan **erbjudande översikt** väljer du en förhands gransknings länk under knappen **gå live** . 

1. Om du vill kontrol lera inköps-och installations flödet från slut punkt till slut punkt köper du ditt erbjudande när det är i för hands version. Meddela först Microsoft med ett [support ärende](https://aka.ms/marketplacesupport) för att se till att vi inte behandlar någon avgift.

1. Om ditt Azure-program stöder [avgiftsbelagda faktureringar med hjälp av den kommersiella tjänsten för avläsning](./partner-center-portal/azure-app-metered-billing.md)av program vara, granskar och följer du de rekommenderade metoder som beskrivs i [Marketplace för avgiftsbelagda fakturerings-API: er](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Om du behöver göra ändringar efter att ha granskat och testat erbjudandet kan du redigera och skicka om för att publicera en ny för hands version. Mer information finns i [Uppdatera ett befintligt erbjudande i den kommersiella marknads platsen](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publicera ditt erbjudande Live

När du har slutfört alla tester i för hands versionen väljer du **Go Live** för att publicera erbjudandet Live på den kommersiella marknads platsen.

   > [!TIP]
   > Om ditt erbjudande redan är aktivt på den kommersiella marknads platsen, kommer alla uppdateringar du gör inte att vara aktiva förrän du väljer **Go Live**.

Nu när du har valt att göra ditt erbjudande tillgängligt på den kommersiella marknads platsen, utför vi en serie slutliga verifierings kontroller för att säkerställa att Live-erbjudandet har kon figurer ATS precis som för hands versionen av erbjudandet. Mer information om dessa verifierings kontroller finns i [publicerings fasen](review-publish-offer.md#publish-phase).

När dessa verifierings kontroller har slutförts är ditt erbjudande Live på Marketplace.

### <a name="errors-and-review-feedback"></a>Fel och granska feedback

Det **manuella validerings** steget i publicerings processen representerar en omfattande granskning av ditt erbjudande och dess tillhör ande tekniska till gångar (särskilt Azure Resource Manager mal len) problem visas vanligt vis som pull-begäranden (PR). En förklaring av hur du kan visa och svara på dessa pull finns i [hantera granskning av feedback](partner-center-portal/azure-apps-review-feedback.md).

Om du har fel i ett eller flera publicerings steg, korrigerar du dem innan du publicerar erbjudandet igen.

## <a name="next-step"></a>Nästa steg

- [Få åtkomst till analys rapporter för den kommersiella marknads platsen i Partner Center](partner-center-portal/analytics.md)
- Lär dig [hur du säljer ditt Azure Applications erbjudande](create-new-azure-apps-offer-marketing.md) genom medförsäljning med Microsoft och som återförsäljs via CSP-program.
