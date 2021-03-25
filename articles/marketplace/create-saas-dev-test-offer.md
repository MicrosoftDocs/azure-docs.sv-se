---
title: Skapa ett test erbjudande
description: Så här skapar du ett separat utvecklings erbjudande för att testa produktions erbjudandet i programmet för kommersiella marknads platser i Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 58649e9a864e64ab5781cff3b663e190dac50cb6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050725"
---
# <a name="create-a-test-offer"></a>Skapa ett test erbjudande

För att utveckla i en separat miljö från produktions erbjudandet skapar du ett separat test-och utvecklings erbjudande (DEV) och ett separat produktions erbjudande (PROD). Information om fördelarna med att använda ett separat utvecklings erbjudande finns i [planera ett SaaS-erbjudande](plan-saas-offer.md#test-offer).

Du konfigurerar de flesta inställningar i utvecklings-och produktions erbjudandena. Till exempel bör det officiella marknadsförings språket och till gångar som skärm bilder och logo typer vara samma. I de fall där konfigurationen är densamma kan du kopiera och klistra in fält från planerna i utvecklings erbjudandet till planerna i produktions erbjudandet.

I följande avsnitt beskrivs konfigurations skillnaderna mellan utvecklings-och produktions erbjudandena.

## <a name="offer-setup-page"></a>Installations sida för erbjudande

Vi rekommenderar att du använder samma alias i rutan **alias** i båda erbjudandena, men lägger till "_Test" i aliaset för dev-erbjudandet. Om t. ex. aliaset för ditt produktions erbjudande är "contososolution" ska aliaset för DEV-erbjudandet vara "contososolution_test". På så sätt kan du enkelt identifiera vilka DEV-erbjudanden som finns i ditt erbjudande.

I avsnittet **kund leads** , använder du och Azure-tabellen eller en test-CRM-miljö för utvecklings erbjudandet. Använd det avsedda hanterings systemet för leads för produkt erbjudande.

## <a name="properties-page"></a>Sidan Egenskaper

Konfigurera den här sidan både i utvecklings-och produktions erbjudandena.

## <a name="offer-listing-page"></a>Sida för erbjudande lista

Konfigurera den här sidan både i utvecklings-och produktions erbjudandena.

## <a name="preview-audience"></a>Förhandsgranska mål grupp

I DEV-erbjudandet inkluderar du Azure Active Directory (AAD) User Principal Name eller Microsoft-konto (MSA) e-postadress till utvecklare och testare, inklusive dig själv. Observera att User Principal Name av en användare på AAD kan vara annorlunda än e-postmeddelandet för användaren. Fungerar till exempel jane.doe@contoso.com inte, men janedoe@contoso.com kommer att fungera. Detta är de personer som har åtkomst till DEV-erbjudandet när du delar för **hands versions** länken under utvecklings-och testnings fasen.

I produktions erbjudandet inkluderar du Azure AD-User Principal Name eller Microsoft-kontots e-postadress för de användare som ska verifiera erbjudandet innan du väljer **knappen gå live** för att publicera erbjudandet Live.

## <a name="technical-configuration-page"></a>Sidan teknisk konfiguration

I den här tabellen beskrivs skillnaderna mellan inställningarna för erbjudanden för utveckling och erbjudanden.

***Tabell 1: skillnader i tekniska konfigurationer***

| Inställning | UTVECKLINGS erbjudande | Erbjudande om produkt |
| ------------ | ------------- | ------------- |
| URL för landnings sida | Ange din utvecklings-/test slut punkt. | Ange din produktions slut punkt. |
| Anslutning-webhook | Ange din utvecklings-/test slut punkt. | Ange din produktions slut punkt. |
| Azure Active Directory klient-ID | Ange klient-ID för testappens registrering (AAD-katalog-ID). | Ange klient-ID för din produktions program registrering. |
| ID för Azure Active Directory-programmet | Ange testappens registrerings program-ID (klient-ID). | Ange appens registrerings program-ID. |
||||

## <a name="plan-overview-page"></a>Sidan plan översikt

När du skapar dina planer rekommenderar vi att du använder samma _plan-ID_ och _plan namn_ i både dev-och Prod-erbjudanden, förutom att lägga till plan-ID: t i dev-erbjudandet med **_Test**. Om t. ex. Plans-ID i produktions erbjudandet är "Enterprise", ska plan-ID: t i DEV-erbjudandet vara "enterprise_test". På så sätt kan du enkelt identifiera vilka DEV-erbjudanden som finns i ditt erbjudande. Du skapar planer i produktions erbjudandet med de pris modeller och priser som du bestämmer bäst för ditt erbjudande.

### <a name="plan-listing"></a>Plan lista

  >  Ange samma plan beskrivning i både utvecklings-och produktions planerna på fliken **lista** för plan översikt.

### <a name="pricing-and-availability-page"></a>Sidan priser och tillgänglighet

Det här avsnittet innehåller rikt linjer för att fylla i sidan med  >  **pris och tillgänglighet** för prenumerations översikt.

#### <a name="markets"></a>Marknaden

Välj samma marknader för utvecklings-och produktions erbjudandena.

#### <a name="pricing"></a>Priser

Använd DEV-erbjudandet för att experimentera med pris modeller. När du har kontrollerat vilken pris modell eller vilka modeller som fungerar bäst skapar du planerna i produktions erbjudandet med de pris modeller och priser som du vill ha.

UTVECKLINGS erbjudandet bör ha planer med noll eller mycket låga priser i planerna. PRODUKTIONS erbjudandet kommer att ha de priser som du vill debitera till kunderna.

> [!NOTE]
> Information som användaren bör märka även om skimmingPurchases som görs i för hands versionen kommer att bearbetas för både DEV-och PROD-erbjudanden. Om ett erbjudande har priset 100/Mo debiteras ditt företag $100. Om detta händer kan du öppna ett [support ärende](support.md) och vi kommer att utfärda en utbetalning för hela beloppet (och inte ta ut någon byrå avgift).

#### <a name="pricing-model"></a>Prismodell

Använd samma pris modell i planerna för utvecklings-och produktions erbjudandena. Till exempel, om planen i produkt erbjudandet är fast pris, med en fakturerings period per månad konfigurerar du planen i DEV-erbjudandet med samma modell.

För att minska kostnaderna för att testa pris modellerna, inklusive anpassade mätar dimensioner, rekommenderar vi att du konfigurerar avsnittet **prissättning** på fliken **priser och tillgänglighet** i utvecklings erbjudandet med lägre priser än vad som finns i produktions erbjudandet. Här följer några rikt linjer som du kan följa när du ställer in priser för planer i utvecklings erbjudandet.

***Tabell 2: rikt linjer för prissättning***

| Pris | Kommentar |
| ------------ | ------------- |
| $0,00 | Ange en total transaktions kostnad på noll om du inte vill ha någon ekonomisk påverkan. Använd det här priset vid anrop till API: erna för avläsning eller för att testa inköps planer i erbjudandet när du utvecklar din lösning. |
| $0,01 – $49,99 | Använd det här pris intervallet för att testa analyser, rapportering och inköps processen. |
| $50,00 och uppåt | Använd det här pris intervallet för att testa utbetalning. Information om vårt betalnings schema finns i [utbetalnings scheman och processer](/partner-center/payout-policy-details). |
|||

Öppna ett [support ärende](support.md)för att undvika att debitera en bearbetnings avgift på ditt test.

#### <a name="free-trial"></a>Kostnadsfri utvärderingsversion

Aktivera inte en kostnads fri utvärderings version av DEV-erbjudandet.

## <a name="co-sell-with-microsoft-page"></a>Samförsäljning med Microsoft Page

Konfigurera inte fliken **Co-försäljning med Microsoft** i utvecklings erbjudandet.

## <a name="resell-through-csps"></a>Sälja via CSP-partner

Konfigurera inte **Upförsäljning via fliken kryptografiproviders** i utvecklings erbjudandet.

## <a name="next-steps"></a>Nästa steg

- [Testa och publicera ett SaaS-erbjudande](test-publish-saas-offer.md)
