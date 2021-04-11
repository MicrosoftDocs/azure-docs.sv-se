---
title: Skapa planer för ditt SaaS-erbjudande i Microsoft Partner Center
description: Skapa planer för ett nytt SaaS-erbjudande (Software as a Service) med hjälp av Microsofts handels Marketplace-portal i Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: fb1514c146dcd352451597e6182c6d99ed8836d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044727"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Skapa planer för ditt SaaS-erbjudande

Erbjudanden som säljs via Microsofts kommersiella marknads plats måste ha minst en plan. Du kan skapa flera olika planer med olika alternativ i samma erbjudande. Dessa planer (kallas ibland SKU: er) kan variera beroende på version, monetarisering eller tjänst nivåer. Detaljerad vägledning om planer finns i [planer och priser för kommersiella Marketplace-erbjudanden](plans-pricing.md).

> [!NOTE]
> Om du väljer att bearbeta transaktioner oberoende av visas inte det här alternativet. Gå istället till [hur du säljer ditt SaaS-erbjudande](create-new-saas-offer-marketing.md).

## <a name="create-a-plan"></a>Skapa en plan

1. Längst upp på fliken **plan översikt** väljer du **+ Skapa ny plan**.

1. I dialog rutan som visas i rutan **plan-ID** anger du ett unikt plan-ID. Använd upp till 50 gemena alfanumeriska tecken, bindestreck eller under streck. Du kan inte ändra plan-ID när du har valt **skapa**.

1. Ange ett unikt namn för den här planen i rutan **planerat namn** . Använd högst 50 tecken.

1. Välj **Skapa**.

## <a name="define-the-plan-listing"></a>Definiera plan listan

På fliken **plan lista** kan du definiera plan namn och beskrivning som du vill att de ska visas på den kommersiella Marketplace.

1. I rutan **plan namn** visas namnet som du angav tidigare för den här planen här. Du kan ändra det när du vill. Det här namnet visas på den kommersiella marknads platsen som rubrik för ditt erbjudandes program varu avtal.

1. I rutan **plan Beskrivning** förklarar du vad som gör den här program varu planen unik och eventuella skillnader jämfört med andra planer i erbjudandet. Beskrivningen får innehålla upp till 500 tecken.
1. Välj **Spara utkast** innan du fortsätter till nästa flik: **priser och tillgänglighet**.

## <a name="define-markets-pricing-and-availability"></a>Definiera marknader, priser och tillgänglighet

Varje plan måste vara tillgänglig på minst en marknad. På fliken **prissättning och tillgänglighet** kan du konfigurera de marknader som planen kommer att vara tillgänglig i, den önskade uppräknings modellen, pris-och fakturerings villkoren. Dessutom kan du ange om du vill att planen ska vara synlig för alla eller bara för vissa kunder (även kallat privat plan).

1. Under **marknader** väljer du länken **Redigera marknader** .
1. I dialog rutan som visas väljer du de marknads platser där du vill göra ditt abonnemang tillgängligt. Du måste välja minst en och högst 141 marknader.

   > [!NOTE]
   > Den här dialog rutan innehåller en sökruta och ett alternativ som du kan använda för att filtrera efter "skatte mottagare", där Microsoft betalar och använder moms för din räkning.

1. Stäng dialog rutan genom att välja **Spara**.

## <a name="define-a-pricing-model"></a>Definiera en pris sättnings modell

Du måste koppla en pris modell till varje plan: antingen _fast pris_ eller _per användare_. Alla planer i samma erbjudande måste använda samma pris modell. Ett erbjudande kan till exempel inte ha en plan som är fast priss taxa och en annan plan per användare. Mer information finns i [pris modeller för SaaS](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> När ditt erbjudande har publicerats kan du inte ändra pris sättnings modellen. Dessutom måste alla planer för samma erbjudande dela samma pris modell.

### <a name="configure-flat-rate-pricing"></a>Konfigurera priser för fast pris

1. På fliken **priser och tillgänglighet** under **priser** väljer du **fast pris**.
1. Markera antingen kryss rutan **månatlig** eller **årlig** eller båda och ange sedan priset.

### <a name="add-a-custom-meter-dimension"></a>Lägg till en anpassad mätnings dimension

Det här alternativet är bara tillgängligt om du har valt pris för fast pris. Mer information finns i [mäta fakturering för SaaS med hjälp av den kommersiella tjänsten för avläsning av marknads platser](./partner-center-portal/saas-metered-billing.md).

1. Under **Marketplace mått för avläsning av tjänst** väljer du länken **Lägg till en anpassad mätnings dimension (max 30)** .
1. I rutan **ID** anger du referensen för det oåterkalleliga ID: t vid sändning av användnings händelser.
1. I rutan **visnings namn** anger du det visnings namn som är associerat med dimensionen. Till exempel "textmeddelanden har skickats".
1. I rutan mått **enhet** anger du en beskrivning av fakturerings enheten. Till exempel "per textmeddelande" eller "per 100-e-post".
1. I rutan **pris per enhet i USD** anger du priset för en dimensions enhet.
1. I rutan **månatlig kvantitet som ingår i bas** anger du kvantiteten (som ett heltal) för dimensionen som ingår varje månad för kunder som betalar den återkommande månads avgiften. Markera kryss rutan i stället om du vill ange en obegränsad kvantitet.
1. I rutan **årlig kvantitet ingår i bas** anger du hur många dimensioner (som ett heltal) som ingår varje månad för kunder som betalar den återkommande årliga avgiften. Markera kryss rutan i stället om du vill ange en obegränsad kvantitet.
1. Om du vill lägga till en annan anpassad mätnings dimension väljer du länken **Lägg till en annan dimension** och upprepar sedan steg 1 till 7.

### <a name="configure-per-user-pricing"></a>Konfigurera priser per användare

1. På fliken **priser och tillgänglighet** under **prissättning** väljer du **per användare**.
2. Om det är tillämpligt anger du det lägsta och högsta antalet användare för den här planen under **användar gränser**.
3. Under **fakturerings period** anger du ett månads pris, ett årligt pris eller både och.

### <a name="validate-custom-prices"></a>Validera anpassade priser

Om du vill ange anpassade priser på en enskild marknad, exportera, ändra och importera sedan pris kalkyl bladet. Du ansvarar för att verifiera priset och äga dessa inställningar. Detaljerad information finns i [anpassade priser](plans-pricing.md#custom-prices).

1. Du måste först spara dina pris ändringar för att möjliggöra export av pris information. Nästan längst ned på fliken **priser och tillgänglighet** väljer du **Spara utkast**.
1. Under **prissättning** väljer du länken **Exportera pris data** .
1. Öppna exportedPrice.xlsx-filen i Microsoft Excel.
1. I kalkyl bladet gör du de uppdateringar som du vill ha på din pris information och sparar sedan. CSV-fil.<br> Du kan behöva aktivera redigering i Excel innan du kan uppdatera filen.
2. På fliken **priser och tillgänglighet** under **prissättning** väljer du länken **Importera prissättnings data** .
3. I dialog rutan som visas klickar du på **Ja**.
4. Välj den exportedPrice.xlsx-fil som du har uppdaterat och klicka sedan på **Öppna**.

### <a name="enable-a-free-trial"></a>Aktivera en kostnads fri utvärderings version

Du kan konfigurera en kostnads fri utvärderings version för varje plan i erbjudandet. Markera kryss rutan om du vill tillåta en månads kostnads fri utvärderings version. Den här kryss rutan är inte tillgänglig för planer som använder tjänsten Marketplace-avläsning. Mer information finns i [kostnads fria utvärderings versioner](plans-pricing.md#free-trials).

> [!IMPORTANT]
> När ditt transactable-erbjudande har publicerats med en kostnads fri utvärderings version kan det inte inaktive ras för den planen. Kontrol lera att den här inställningen är korrekt innan du publicerar erbjudandet för att undvika att behöva återskapa planen.

- Under den **kostnads fria utvärderings versionen** markerar du kryss rutan **Tillåt en kostnads fri utvärderings version** av en månad.

## <a name="choose-who-can-see-your-plan"></a>Välj vem som kan se din plan

Du kan konfigurera varje plan så att den blir synlig för alla eller bara för en speciell mål grupp. Du beviljar åtkomst till en privat plan med klient-ID: n med alternativet att inkludera en beskrivning av varje klient-ID som du tilldelar. Du kan lägga till högst 10 klient-ID: n manuellt eller upp till 20 000 klient-ID: n med hjälp av en. CSV-fil.

> [!NOTE]
> Om du publicerar en privat plan kan du ändra dess synlighet till offentlig senare. Men när du har publicerat ett offentligt abonnemang kan du inte ändra dess synlighet till privat.

### <a name="make-your-plan-public"></a>Gör din plan offentlig

1. Under **plan synlighet** väljer du den **offentliga** rutan.
1. Välj **Spara utkast** och gå sedan till den övre vänstra delen av fliken och välj **plan översikt** för att återgå till fliken **plan översikt** .
1. Om du vill skapa en annan plan för det här erbjudandet går du till början av fliken **plan översikt** och väljer **+ Skapa ny plan**. Upprepa sedan stegen i avsnittet [skapa en plan](#create-a-plan) . Annars går du till [Visa dina planer](#view-your-plans).

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Lägg till klient-ID: n manuellt för en privat plan 

1. Under **plan synlighet** väljer du rutan **det här är en privat plan** .
1. I rutan **klient-ID** som visas anger du ID för Azure AD-klient för den mål grupp som du vill bevilja åtkomst till den privata planen. Minst ett klient-ID krävs.
1. Valfritt Ange en beskrivning av den här mål gruppen i rutan **Beskrivning** .
1. Om du vill lägga till ett annat klient-ID upprepar du steg 2 och 3.
1. När du har lagt till klient-ID: n väljer du **Spara utkast**, och i det övre vänstra hörnet av fliken väljer du **plan översikt** för att återgå till fliken **plan översikt** .
1. Om du vill skapa en annan plan för det här erbjudandet går du till början av fliken **plan översikt** och väljer **+ Skapa ny plan**. Upprepa sedan stegen i avsnittet [skapa en plan](#create-a-plan) . Annars går du till [Visa dina planer](#view-your-plans).

### <a name="use-a-csv-file-for-a-private-plan"></a>Använd en. CSV-fil för en privat plan

1. Under **plan synlighet** väljer du rutan **det här är en privat plan** .
2. Välj länken **Exportera mål grupp (CSV)** .
3. Öppna. CSV-fil och Lägg till de Azure-ID: n som du vill bevilja åtkomst till det privata erbjudandet i **ID-** kolumnen.
4. Alternativt kan du ange en beskrivning för varje mål grupp i kolumnen **Beskrivning** .
5. Lägg till "TenantID" i kolumnen **typ** för varje rad med ett Azure-ID.
6. Spara. CSV-fil.
7. På fliken **priser och tillgänglighet** under **plan synlighet** väljer du länken **Importera mål grupp (CSV)** .
8. I dialog rutan som visas väljer du **Ja**.
9. Välj. CSV-fil och välj sedan **Öppna**.
10. Välj **Spara utkast** och gå sedan till den övre vänstra delen av fliken och välj **plan översikt** för att återgå till fliken **plan översikt** .
11. Om du vill skapa ett annat schema för det här erbjudandet väljer du **+ Skapa ny plan** längst upp på fliken **plan översikt** . Upprepa sedan stegen i avsnittet [skapa en plan](#create-a-plan) . Annars, om du är klar med att skapa planer, går du till nästa avsnitt: **Visa dina planer**.

## <a name="view-your-plans"></a>Visa dina planer

När du har skapat ett eller flera planer ser du ditt prenumerations namn, ditt plan-ID, pris modell, tillgänglighet (offentlig eller privat), aktuell publicerings status och alla tillgängliga åtgärder på fliken **plan översikt** .

De åtgärder som är tillgängliga i kolumnen **åtgärd** på fliken **plan översikt** varierar beroende på status för din plan och kan innehålla följande:

- Om plan status är **utkast**, kommer länken i kolumnen **åtgärd** att säga **ta bort utkast**.
- Om plan status är **Live**, kommer länken i kolumnen **åtgärd** att **stoppa Sälj planen** eller **synkronisera den privata mål gruppen**. Länken **Synkronisera privat publik** kommer bara att publicera ändringar i dina privata mål grupper utan att publicera några andra uppdateringar som du har gjort i erbjudandet.

## <a name="before-you-publish-your-offer"></a>Innan du publicerar erbjudandet

Om du inte redan har gjort det skapar du ett utvecklings-och testnings erbjudande (DEV) för att testa ditt erbjudande innan du publicerar ditt produktions erbjudande Live. Mer information finns i [skapa ett utvecklings-och test erbjudande](create-saas-dev-test-offer.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du säljer ditt SaaS-erbjudande](create-new-saas-offer-marketing.md) genom **medförsäljning med Microsoft** och som **återförsäljs via CSP** -program.
- [Testa och publicera ett SaaS-erbjudande på den kommersiella marknads platsen](test-publish-saas-offer.md).
