---
title: Granska och publicera ett erbjudande på Microsofts kommersiella marknads platser
description: Använd partner Center för att skicka in ditt erbjudande till Preview, förhandsgranska ditt erbjudande och publicera det sedan på Microsofts kommersiella marknads plats.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: f8ddcbef6d2ce4a9cba625374db9908335954424
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630902"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Granska och publicera ett erbjudande på den kommersiella Marketplace

Den här artikeln visar hur du använder Partner Center för att skicka in ditt erbjudande för publicering, för hands version av ditt erbjudande och sedan publicera det på den kommersiella Marketplace. Vi tar också upp hur du kontrollerar publicerings statusen när den går igenom publicerings stegen. Du måste redan ha skapat ett erbjudande som du vill publicera.

## <a name="offer-status"></a>Erbjudandestatus

Du kan granska din erbjudande status på fliken **Översikt** på instrument panelen för extern Marketplace i [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). En av följande status indikatorer visas i kolumnen **status** för varje erbjudande.

| Status | Beskrivning |
| ------------ | ------------- |
| Draft | Erbjudandet har skapats men det har inte publicerats. |
| Publicering pågår | Erbjudandet fungerar på ett sätt genom publicerings processen. |
| Åtgärd krävs | Vi har identifierat ett kritiskt problem under certifieringen eller under en annan publicerings fas. |
| Förhandsgranskning | Vi har certifierat erbjudandet, som nu väntar en slutgiltig verifiering av utgivaren. Välj **Go Live** för att publicera erbjudandet Live. |
| Direktsändning | Erbjudandet är Live på Marketplace och kan ses och förvärvas av kunder. |
| Väntande stoppa försäljning | Utgivare valde "stoppa försäljning" på ett erbjudande eller en plan, men åtgärden har ännu inte slutförts. |
| Inte tillgängligt i Marketplace | Ett tidigare publicerat erbjudande i Marketplace har tagits bort. |
|||

## <a name="validation-and-publishing-steps"></a>Validerings-och publicerings steg

När du är redo att skicka in ett erbjudande om publicering väljer du **Granska och publicera** i det övre högra hörnet i portalen. Sidan **Granska och publicera** visar status för varje sida för ditt erbjudande, som kan vara något av följande:

   - **Inte startat** – sidan är ofullständig.
   - **Ofullständig** – sidan saknar nödvändig information eller innehåller fel som behöver åtgärdas. Du måste gå tillbaka till sidan och uppdatera den.
   - **Klar** – sidan har slutförts. Alla nödvändiga data har angetts och det finns inga fel.

Om någon av sidorna har en annan status än **fullständig**, måste du korrigera problemet på sidan och sedan gå tillbaka till sidan **Granska och publicera** för att kontrol lera att statusen nu visas som **slutförd**. Vissa erbjudande typer kräver testning. I så fall visas ett fält **för certifiering** där du måste ange test instruktioner till certifierings teamet och eventuella kompletterande kommentarer som hjälper dig att förstå din app.

När alla sidor har slutförts och du har angett tillämpliga test anteckningar väljer du **publicera** för att påbörja processerna för validering och publicering. Faserna och den övergripande sekvensen kan variera beroende på vilken typ av erbjudande du publicerar. I följande tabell visas ett möjligt publicerings flöde. Varje fas förklaras i detalj i följande avsnitt.

| Fas | Vad händer |
| ------------ | ------------- | ------------- |
| [Automatiserad verifiering](#automated-validation-phase) | Vi bearbetar en uppsättning automatiserade verifieringar. |
| [Certifiering](#certification-phase) | Vi utför manuella verifieringar. |
| [Skapa för hands version](#preview-creation-phase) | List sidan för för hands versionen av erbjudandet är tillgänglig för alla som har en för hands versions länk. Om ditt erbjudande kommer att säljas via Microsoft (transactable) kan bara den mål grupp som du har angett på sidan för **hands version** av erbjudandet köpa och få åtkomst till erbjudandet om testning. |
| [Utgivarens utloggning](#publisher-sign-off-phase) | Vi skickar dig ett e-postmeddelande med en begäran om att förhandsgranska och godkänna ditt erbjudande. |
| [Publicera](#publish-phase) | Vi kör en serie steg för att kontrol lera att för hands versions erbjudandet publiceras Live till den kommersiella marknads platsen. |
|||

## <a name="automated-validation-phase"></a>Automatiserad validerings fas

Det första steget i publicerings processen är en uppsättning automatiserade verifieringar. Varje validerings steg motsvarar en funktion som du valde när du skapade erbjudandet. Varje verifierings kontroll måste slutföras innan erbjudandet kan gå vidare till nästa steg i publicerings processen.

- **Konfigurera konfiguration av inköps flöde** (<10 min)

   Vi garanterar att ditt erbjudande kan uppfyllas när kunderna köpts via Azure Portal. Det här steget gäller bara för erbjudanden som säljs via Microsoft.

- **Test enhet för data verifiering** (~ 5 min)

   Vi verifierar de data som du angav på sidan teknisk konfiguration i erbjudandet. Vi testar och godkänner funktionen testa enheten. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Test av enhets etablering** (~ 30 min)

    När du har verifierat data och funktioner på test enheten i föregående steg distribuerar vi och replikerar instanser av test enheten så att de är redo för kund användning. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Verifiering och registrering av lead-hantering** (<15 min)

    Vi bekräftar att ditt lead management-system kan ta emot kund leads utifrån den information du angav på sidan **erbjudande konfiguration** . Det här steget gäller bara för erbjudanden där lead management har Aktiver ATS.

- **Erbjudande verifiering** (<30 min)

    För SaaS-erbjudanden utför vi snabba valideringar för innehåll och tekniska konfigurationer. Det går inte att skapa en för hands version av verifierings problem som upptäckts i det här steget. Om vi hittar några problem som kan göra att certifieringen inte kan utföras visas länken **Visa verifierings rapport** på sidan **erbjudande översikt** . Rapporten innehåller beskrivningar av eventuella princip överträdelser.

    > [!IMPORTANT]
    > Se till att granska och adressera alla varningar från steget för att validera erbjudandet innan du väljer knappen **Go Live** . I annat fall riskerar du att en certifiering som gör att ditt erbjudande upphör att fungera.

## <a name="certification-phase"></a>Certifierings fas

Erbjudanden som skickas till den kommersiella marknaden måste certifieras innan de kan publiceras. Erbjudanden genomgå rigorös testning, vissa automatiserade och andra manuella. Mer information finns i [certifierings principer för kommersiella Marketplace](/legal/marketplace/certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Typer av verifieringar som sker under certifieringen

Det finns tre nivåer av verifiering som ingår i certifierings processen för varje skickat erbjudande.

- Affärs berättigande för utgivare
- Innehålls validering
- Teknisk validering

#### <a name="publisher-business-eligibility"></a>Affärs berättigande för utgivare

Varje erbjudande typ kontrollerar en uppsättning nödvändiga bas Berättiganderegler. Detta kriterium kan omfatta utgivar MPN status, kompetenser, kompetens nivåer och så vidare.

#### <a name="content-validation"></a>Innehålls validering

Informationen som angavs när du skapade erbjudandet kontrol leras för kvalitet och relevans. Dessa kontroller granskar dina poster för information om Marketplace-listor, prissättning, tillgänglighet, associerade planer och så vidare. För att uppfylla registrerings villkoren för Microsoft AppSource och Azure Marketplace, kommer vi att validera att erbjudandet innehåller:

- En rubrik som korrekt beskriver erbjudandet
- Välskrivna beskrivningar som ger en grundlig översikt och ett värde förslag
- Kvalitets skärm bilder och videor
- En förklaring av hur erbjudandet använder Microsoft-plattformar och-verktyg.

Läs mer om kriterierna för innehålls validering genom att läsa [allmänna registrerings principer](/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Teknisk validering

Under teknisk validering genomgår erbjudandet (paketet eller den binära) följande kontroller.

- Genomsökt efter skadlig kod
- Övervakade nätverks anrop
- Paketet har analyser ATS
- Grundlig genomsökning av erbjudandets funktioner

Erbjudandet testas på olika plattformar och versioner för att säkerställa att det är robust.

### <a name="certification-failure-report"></a>Rapport över certifierings problem

Om erbjudandet Miss lyckas med någon av inställningarna för registrering, teknik eller princip, eller om du inte är berättigad till att skicka ett erbjudande av den typen, e-posta vi en certifierings fel rapport till dig.

Den här rapporten innehåller beskrivningar av eventuella principer som misslyckats, tillsammans med gransknings anteckningar. Läs den här e-postrapporten, åtgärda eventuella problem, gör uppdateringar till ditt erbjudande vid behov och skicka erbjudandet på nytt med hjälp av den [kommersiella Marketplace-portalen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) i Partner Center. Du kan skicka in erbjudandet igen så många gånger som behövs tills du skickar certifieringen.

## <a name="preview-creation-phase"></a>Fasen Skapa förhandsversion

Under skapandet av för hands versionen skapar vi en version av ditt erbjudande som bara är tillgänglig för den mål grupp som du har angett på sidan för **förhands granskning** av ditt erbjudande. För hands versionen av ditt erbjudande är inte tillgänglig för någon utanför förhands gransknings gruppen förrän du publicerar erbjudandet Live.

> [!NOTE]
> Använd inte förhands gransknings gruppen för att ge personer utanför organisationen insyn i ett erbjudande. Använd alternativet för privat erbjudande i stället. I det här läget har ditt erbjudande inte testats fullständigt och godkänts och är inte redo för distribution utanför företaget.

## <a name="cancel-publishing"></a>Avbryt publicering

Så här avbryter du ett erbjudande med status **publicering pågår** :

1. Välj namnet på erbjudandet för att öppna sidan **erbjudande översikt** .
1. Välj **Avbryt publicera** i det övre högra hörnet på sidan.
1. Bekräfta att du vill stoppa att erbjudandet publiceras.

Om du vill publicera erbjudandet vid ett senare tillfälle måste du starta publicerings processen.

> [!NOTE]
> Du kan förhindra att ett erbjudande bara publiceras om erbjudandet ännu inte har gått vidare till utgivarens signerings steg. När du har valt **gå live** har du inte möjlighet att avbryta publiceringen längre.

## <a name="publisher-sign-off-phase"></a>Utgivar signerings fas

När erbjudandet är klart för att du ska kunna granska och vara inloggad skickar vi ett e-postmeddelande till dig om att granska och godkänna din för hands version av erbjudandet. Du kan också uppdatera sidan **erbjudande översikt** i webbläsaren för att se om ditt erbjudande har nått utgivarens signerings fas. Om den har det är knappen **Go Live** och för hands versions länkarna tillgänglig.

Följande skärm bild visar sidan **erbjudande översikt** i programmet för kommersiella marknads platser i Partner Center. De verifierings steg som visas på den här sidan varierar beroende på erbjudande typen och de val du gjorde när du skapade erbjudandet. Observera att certifierings fasen kan ske under överföring till för hands version eller överföring till Live beroende på typen av erbjudande.

![Visar sidan erbjudande översikt för ett erbjudande i Partner Center. Knappen gå live och för hands versions länkarna visas.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>För hands Grans kar och godkänner ditt erbjudande

> [!IMPORTANT]
> Om du vill kontrol lera inköps-och installations flödet från slut punkt till slut punkt köper du ditt erbjudande när det är i för hands version. Meddela först Microsoft med ett [support ärende](https://aka.ms/marketplacesupport) för att se till att vi inte behandlar någon avgift. Om ditt erbjudande är en lista med _kontakt_ information kan du testa att ett lead skapas som förväntat genom att tillhandahålla informationen om kontakt information under för hands versionen.

På sidan **erbjudande översikt** visas förhands gransknings länkar under knappen **gå live** . Det finns en länk till antingen AppSource Preview, Azure Marketplace Preview eller båda beroende på vilka alternativ du valde när du skapade erbjudandet. Om du har valt att sälja ditt erbjudande via Microsoft kan alla som har lagts till i förhands gransknings gruppen testa förvärvet och distributionen av ditt erbjudande för att säkerställa att det uppfyller dina krav under det här skedet.

> [!IMPORTANT]
> Se till att granska och adressera alla varningar från steget för att _validera erbjudandet_ innan du väljer knappen **Go Live** . I annat fall riskerar du att en certifiering som gör att ditt erbjudande upphör att fungera.

När du har godkänt din för hands version väljer du **Go Live** för att publicera erbjudandet Live på den kommersiella marknads platsen. 

Om du vill göra ändringar efter att ha granskat erbjudandet kan du redigera och skicka begäran om publicering. Om ditt erbjudande redan är aktivt och tillgängligt för allmänheten på Marketplace, är de uppdateringar du gör inte aktiva förrän du väljer **Go Live*. Mer information finns i [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](partner-center-portal/update-existing-offer.md)

## <a name="publish-phase"></a>Publicerings fas

Nu när du har valt att sätta igång med ditt erbjudande, vilket gör det tillgängligt på den kommersiella Marketplace, utför vi en serie slutliga verifierings kontroller för att säkerställa att Live-erbjudandet är konfigurerat precis som för hands versionen av erbjudandet.

- **Konfigurera konfiguration av inköps flöde** (>10 min)

    Vi garanterar att ditt erbjudande kan uppfyllas när kunderna köpts via Azure Portal. Det här steget gäller bara för erbjudanden som säljs via Microsoft.

- **Test enhet för data verifiering** (~ 5 min)

    Vi verifierar de data som du angav på sidan teknisk konfiguration i erbjudandet. Vi testar och godkänner funktionen testa enheten. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Test av enhets etablering** (~ 30 min)

    Vi distribuerar och replikerar instanser av test enheten så att de är redo för kund användning. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Verifiering och registrering av lead-hantering** (>15 min)

    Vi bekräftar att ditt lead-hanterings system kan ta emot kund leads utifrån informationen på **installations** sidan för erbjudandet. Det här steget gäller bara för erbjudanden där lead management har Aktiver ATS.

- **Slutgiltig publicering (>30 minuter)**

    Vi garanterar att erbjudandet blir offentligt tillgängligt på Marketplace.

När dessa verifierings kontroller har slutförts är ditt erbjudande Live på Marketplace.

## <a name="publishing-history"></a>Publicerings historik

Sidan **Historik** i Partner Center visar publicerings händelser för dina kommersiella Marketplace-erbjudanden. För varje händelse visar sidan den användare som initierade åtgärden, händelse typ och datum och tid för händelsen. [Validerings-och publicerings stegen](#validation-and-publishing-steps) visas med datum och tid för slut för ande.

Så här visar du historiken för ditt erbjudande:

1.    Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2.    I navigerings menyn till vänster väljer du **kommersiell Marketplace**-  >  **Översikt**.
3.    Välj ditt erbjudande.
4.    Välj fliken **Historik** i navigerings menyn till vänster. Sidan innehåller information om följande händelser, efter vad som är tillämpligt för ditt erbjudande:

|Händelse    |Beskrivning    |
|---------|---------------|
|Skapat erbjudande    |Erbjudandet skapades i Partner Center. En användare har valt erbjudande typ, erbjudande-ID och erbjudande-alias i den **kommersiella Marketplace**-  >  **översikten**.    |
|Skapad plan: *plan namn*    |En användare skapade en ny plan genom att ange plan-ID och plan namn på fliken **plan översikt** .</br>*Den här händelsen gäller endast för de typer av erbjudanden som har stöd för planer*.    |
|Borttagen plan    |En användare tog bort en utkast plan som inte har publicerats genom att välja **ta bort utkast** från sidan **plan översikt** .</br>*Den här händelsen gäller endast för de typer av erbjudanden som har stöd för planer*.    |
|Initierad plan slutar sälja: *planerat namn*    |En användare initierade en plan-Stop-säljning genom att välja **sluta sälja** från sidan **plan översikt** .</br>*Den här händelsen gäller endast för de typer av erbjudanden som har stöd för planer*.    |
|Ångra plan stoppa Sälj: *plan namn*    |En användare avbröt en plans topp-försäljning genom att välja **Ångra sluta sälja** från sidan **plan översikt** .</br>*Den här händelsen gäller endast för de typer av erbjudanden som har stöd för planer*.    |
|Skickat erbjudande till för hands version    |En användare skickade erbjudandet till för hands versionen genom att välja **publicera** på sidan **Granska och publicera** .    |
|Initierad sändning för att avbryta för hands version    |En användare begärde att avbryta publiceringen av erbjudandet till för hands versionen genom att välja **Avbryt publicering** på sidan **erbjudande översikt** efter att du har skickat in den för hands version.</br>*Den här händelsen visas när begäran om annullering bearbetas*.    |
|Överföringen avbröts för för hands versionen    |En användare avbröt erbjudande publikationen till för hands versionen genom att välja **Avbryt publicering** på sidan **erbjudande översikt** efter att du har skickat in den för hands version.</br>*Den här händelsen visas när begäran om annullering har bearbetats*.    |
|Logga ut för att gå live    |En användare publicerade erbjudandet till den kommersiella marknads platsen genom att välja **Go Live** på sidan **erbjudande översikt** .    |
|Startade annullering av publicera på Marketplace    |En användare begärde att avbryta publiceringen av erbjudandet genom att välja **Avbryt publicering** på sidan **erbjudande översikt** efter att du är inloggad.</br>*Den här händelsen visas när begäran om annullering bearbetas*.    |
|Avbruten publicering till den kommersiella marknads platsen    |En användare avbröt erbjudandet genom att välja **Avbryt publicering** på sidan **erbjudande översikt** efter att du är inloggad.</br>*Den här händelsen visas när begäran om annullering har bearbetats*.    |
|Synkronisera privat mål grupp    |En användare uppdaterade och synkroniserade den privata mål gruppen genom att välja **Synkronisera privat publik** på sidan **plan översikt** eller på sidan **prenumerations pris & tillgänglighet** .</br>*Den här händelsen gäller endast för typer av erbjudanden som stöder privata planer*.    |
|Stoppa Sälj erbjudandet    |En användare slutade sälja erbjudandet genom att välja **sluta sälja** från sidan **erbjudande översikt** .    |

> [!NOTE]
> Sidan historik säger inte när ett erbjudande utkast har sparats.

### <a name="filter-options"></a>Filtrera alternativ

Du kan använda filter för att begränsa den fullständiga historiken för ditt erbjudande till specifika publicerings händelser:

1.    Välj filter knappen i det övre högra hörnet på sidan.
2.    Välj ett filter och välj sedan **tillämpa** för att se vilka historik händelser motsvarar de kriterier som du har valt.
3.    Välj **Rensa filter** om du vill återgå till den fullständiga historiken för ditt erbjudande.

Det finns fyra filter:
* Händelser
* Användare
* Datum
* Sidor

När du väljer filtret **sidor** kan du välja någon av de partner Center-sidor som gäller för din erbjudande typ. När detta används visar filtret **Pages** alla **skickade erbjudanden för för hands versions** händelser med ändringar på sidan som du har valt.

* För alla erbjudanden ingår sidan **erbjudande konfiguration** som standard för varje överförings händelse.
* För erbjudanden som stöder avtal ingår **plan översikts** sidan för varje överförings händelse.
* För erbjudanden som stöder Test Drive tas sidan **test enhet** med för varje överförings händelse.

### <a name="users"></a>Användare

Om en händelse initierades av en användare visar historik sidan användaren enligt följande scenarier:

#### <a name="the-event-was-initiated-by-the-publisher"></a>Händelsen initierades av utgivaren

Användare med publicerings behörigheter för ett erbjudande får sitt namn för de publicerings händelser som de initierar.

[![Sidan historik visar namnet på användare med publicerings behörigheter.](./media/review-publish-offer/event-initiated-by-publisher.png)](./media/review-publish-offer/event-initiated-by-publisher.png#lightbox)

#### <a name="the-event-was-initiated-by-microsoft"></a>Händelsen initierades av Microsoft

Du kan ge Microsoft-administratörer behörighet att initiera åtgärder för din räkning eller vidta korrigerande åtgärder efter ett oväntat systemfel. Microsofts namn och logo typ visas för publicering av händelser som initierats av Microsoft på uppdrag av ditt konto.

[![Exempel på hur historik sidan visar händelser som initierats av Microsoft.](./media/review-publish-offer/event-initiated-by-microsoft.png)](./media/review-publish-offer/event-initiated-by-microsoft.png#lightbox)

#### <a name="the-event-was-initiated-by-an-unidentified-user"></a>Händelsen initierades av en oidentifierad användare

Användare som inte längre är kopplade till ett konto får sitt namn bort från kolumnen **initierad av** efter att publicerings behörigheterna har återkallats.

[![Exempel på hur historik sidan visar händelser som initieras av oidentifierade användare.](./media/review-publish-offer/event-initiated-by-unidentified-user.png)](./media/review-publish-offer/event-initiated-by-unidentified-user.png#lightbox)

#### <a name="validation-and-publishing-steps"></a>Validerings-och publicerings steg

Ingen användare visas för system processer som motsvarar [validerings-och publicerings steg](#validation-and-publishing-steps). Dessa händelser färgkodas enligt händelsens slut för ande status.

[![Exempel på hur historik sidan visar validerings-och publicerings steg.](./media/review-publish-offer/validation-and-publishing-event.png)](./media/review-publish-offer/validation-and-publishing-event.png#lightbox)

## <a name="next-steps"></a>Nästa steg

[Få åtkomst till analys rapporter för den kommersiella marknads platsen i Partner Center](partner-center-portal/analytics.md)
