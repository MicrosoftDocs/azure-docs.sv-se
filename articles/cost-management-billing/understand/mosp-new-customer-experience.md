---
title: Kom igång med ditt uppdaterade Azure-faktureringskonto
description: Kom igång med ditt uppdaterade Azure-faktureringskonto och lär dig hur den nya fakturerings- och kostnadshanteringsmiljön fungerar
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: banders
ms.openlocfilehash: 37b50e8761609f864d2cb16e7869614db64bac71
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844923"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Kom igång med ditt uppdaterade Azure-faktureringskonto

Hanteringen av kostnader och fakturor är en av de viktigaste delarna i din molnmiljö, och hjälper dig att hålla koll på dina utgifter i molnet. För att göra det enklare för dig att hantera dina kostnader och fakturor uppdaterar Microsoft ditt Azure-faktureringskonto med förbättrade kostnadshanterings- och faktureringsfunktioner. Den här artikeln beskriver uppdateringarna av ditt faktureringskonto och går igenom de nya funktionerna.

> [!IMPORTANT]
> Innan ditt konto uppdateras får du ett e-postmeddelande från Microsoft som informerar dig om kontouppdateringarna. Meddelandet skickas 60 dagar innan ditt konto uppdateras.

## <a name="more-flexibility-with-your-new-billing-account"></a>Mer flexibilitet med ditt nya faktureringskonto

I följande diagram jämförs ditt gamla och nya faktureringskonto:

![Diagram med en jämförelse av det gamla och det nya kontots faktureringshierarki](./media/mosp-new-customer-experience/comparison-old-new-account.png)

Ditt nya faktureringskonto innehåller en eller flera faktureringsprofiler som du använder för att hantera dina fakturor och betalningsmetoder. Varje faktureringsprofil har ett eller flera fakturaavsnitt där du kan organisera kostnaderna på faktureringsprofilens faktura.

![Diagram som visar den nya faktureringshierarkin](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

Roller för faktureringskontot har den högsta behörighetsnivån. Dessa roller bör tilldelas till användare som behöver visa fakturor och spåra kostnader för hela ditt konto, t.ex. ekonomi- eller IT-chefer i en organisation eller den person som har registrerat sig för ett konto. Mer information finns i [Roller och uppgifter för faktureringskonton](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). När ditt konto har uppdaterats tilldelas användaren med rollen som kontoadministratör i det gamla faktureringskontot en ägarroll i det nya kontot.

## <a name="billing-profiles"></a>Faktureringsprofiler

En faktureringsprofil används för att hantera dina fakturerings- och betalningsmetoder. I början av månaden skapas en månadsfaktura för varje faktureringsprofil på ditt konto. Fakturan innehåller respektive avgifter från den föregående månaden för alla prenumerationer som är kopplade till faktureringsprofilen.

När ditt konto uppdateras skapas en faktureringsprofil automatiskt för varje prenumeration. Prenumerationsavgifterna faktureras till respektive faktureringsprofil och visas på fakturan.

Roller för faktureringsprofiler har behörighet att visa och hantera fakturor och betalningsmetoder. De här rollerna bör tilldelas till användare som betalar fakturor, t.ex. medlemmar i redovisningsteamet i en organisation. Mer information finns i [Roller och uppgifter för faktureringsprofiler](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). 

För varje prenumeration som du har gett andra behörighet att [visa fakturor](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice) för, tilldelas användare som har en Azure RBAC-roll som ägare, deltagare, läsare eller faktureringsläsare rollen som läsare i den aktuella faktureringsprofilen när ditt konto uppdateras.

## <a name="invoice-sections"></a>Fakturaavsnitt

Ett fakturaavsnitt används för att organisera kostnaderna på din faktura. Du kanske till exempel behöver en enda faktura, men vill sortera kostnaderna efter avdelning, grupp eller projekt. I det här scenariot har du en enda faktureringsprofil där du skapar ett fakturaavsnitt för varje avdelning, grupp eller projekt.

När ditt konto uppdateras skapas ett fakturaavsnitt för varje faktureringsprofil och den relaterade prenumerationen kopplas till fakturaavsnittet. När du lägger till fler prenumerationer kan du skapa ytterligare avsnitt och koppla prenumerationerna till fakturaavsnitten. Dessa avsnitt visas på faktureringsprofilens faktura och anger användningen för respektive prenumeration som du har kopplat till dem.

Roller för fakturaavsnitt har behörighet att styra vem som skapar Azure-prenumerationer. Rollerna bör tilldelas till användare som konfigurerar Azure-miljön för team i en organisation, t.ex. teknikchefer och tekniska arkitekter. Mer information finns i [Roller och uppgifter för fakturaavsnitt](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Förbättrade funktioner i den nya miljön

Den nya miljön innehåller följande kostnadshanterings- och faktureringsfunktioner som gör det enkelt att hantera kostnader och fakturor:

#### <a name="invoice-management"></a>Fakturahantering

**Mer förutsägbar månatlig faktureringsperiod** – Med det nya kontot börjar faktureringsperioden den första dagen i månaden och slutar den sista dagen i månaden, oavsett när du registrerar dig för Azure. En faktura skapas i början av varje månad och innehåller alla avgifter från föregående månad.

**Få en enda månadsfaktura för flera prenumerationer** – Du kan välja mellan att få en månadsfaktura för varje prenumeration eller en enskild faktura för flera prenumerationer.

**Få en enda månadsfaktura för Azure-prenumerationer, supportavtal och Azure Marketplace-produkter** – Du får en enda månadsfaktura för alla avgifter, inklusive användningskostnader för Azure-prenumerationer och kostnader för supportavtal och Azure Marketplace-köp.

**Gruppera kostnader på fakturan utifrån dina behov** – Du kan gruppera kostnaderna på din faktura baserat på dina behov – efter avdelning, projekt eller team.

**Ange ett valfritt inköpsordernummer på fakturan** – Ange ett inköpsordernummer om du vill koppla fakturan till dina interna ekonomisystem. Du kan hantera och uppdatera den när du vill på Azure-portalen.

#### <a name="payment-management"></a>Betalningshantering

**Betala fakturor direkt med ett kreditkort** – Du behöver inte vänta på att den registrerade betalningen ska debiteras på ditt kreditkort. Du kan använda valfritt kreditkort för att betala en obetald eller förfallen faktura på Azure-portalen.

**Håll koll på alla betalningar som är kopplade till kontot** – Visa alla betalningar som är kopplade till ditt konto, inklusive betalningsmetod, betalat belopp och betalningsdatum, på Azure-portalen.

#### <a name="cost-management"></a>Kostnadshantering

**Schemalägg månatliga exporter av användningsdata till ett lagringskonto** – Publicera dina kostnads- och användningsdata automatiskt till ett lagringskonto varje dag, vecka eller månad.

#### <a name="account-and-subscription-management"></a>Konto- och prenumerationshantering

**Ge faktureringsbehörighet till flera administratörer** – Bevilja faktureringsbehörighet till flera användare så att de kan hantera faktureringen för ditt konto. Öka flexibiliteten genom att ge andra läsbehörighet, skrivbehörighet eller både läs- och skrivbehörighet.

**Skapa fler prenumerationer direkt på Azure-portalen** – Skapa alla dina prenumerationer med ett enda klick på Azure-portalen.

#### <a name="api-support"></a>API-stöd

**Hantera fakturering och kostnader via API:er, SDK och PowerShell-** – Använd API:er för kostnadshantering, fakturering och förbrukning för att hämta fakturerings- och kostnadsdata till dina dataanalysverktyg.

**Hantera alla prenumerationsåtgärder via API:er, SDK och PowerShell** – Använd API:er för Azure-prenumerationer för att automatisera hanteringen av dina Azure-prenumerationer, t.ex. för att skapa, byta namn på eller avsluta en prenumeration.

## <a name="get-prepared-for-your-new-experience"></a>Förbered dig för den nya miljön

Vi rekommenderar att du gör följande för att förbereda dig för den nya miljön:

**Månatlig faktureringsperiod och annat fakturadatum**

I den nya miljön genereras din faktura runt den nionde dagen i varje månad, och innehåller alla avgifter från föregående månad. Datumet kan skilja sig från det datum då din faktura genereras i det gamla kontot. Om du delar dina fakturor med andra meddelar du dem om datumändringen.

**Nya API:er för fakturerings- och kostnadshantering**

Om du använder API:er för kostnadshantering och fakturering för att köra frågor mot och uppdatera fakturerings- eller kostnadsdata måste du använda nya API:er. Tabellen nedan innehåller de API:er som inte fungerar med det nya faktureringskontot och de ändringar som du måste göra i ditt nya faktureringskonto.

|API | Ändringar  |
|---------|---------|
|[API:et Billing Accounts – List](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/billingaccounts/list) | I API:et Billing Accounts – List har ditt gamla faktureringskonto agreementType **MicrosoftOnlineServiceProgram**, och ditt nya faktureringskonto har agreementType **MicrosoftCustomerAgreement**. Om du har ett agreementType-beroende uppdaterar du det. |
|[Invoices – List By Billing Subscription](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Detta API returnerar bara fakturor som genererades innan ditt konto uppdaterades. Du måste använda API:et [Invoices – List By Billing Account](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) för att få fakturor som genereras i det nya faktureringskontot. |

## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om den nya miljön.

**Inga tjänstavbrott** Azure-tjänster i din prenumeration fortsätter att köras utan avbrott. Det enda som uppdateras är faktureringsmiljön. Befintliga resurser, resursgrupper och hanteringsgrupper påverkas inte.

**Inga ändringar av Azure-resurser** Åtkomst till Azure-resurser som konfigurerades med Azure RBAC (rollbaserad åtkomstkontroll) påverkas inte av uppdateringen.

**Tidigare fakturor är tillgängliga i den nya miljön** Fakturor som skapades innan ditt konto uppdaterades är fortfarande tillgängliga på Azure-portalen.

**Fakturor för konto som uppdateras i mitten av månaden** Om ditt konto uppdateras i mitten av månaden får du en faktura för avgifter som ackumulerats fram till den dag då ditt konto uppdateras. Du får en till faktura för resten av månaden. Exempel: Ditt konto har en prenumeration och uppdateras den 15 september. Du får en faktura för avgifter som ackumulerats fram till den 15 september. Du får en ny faktura för perioden mellan den 15 september till och med den 30 september. Efter september får du en faktura per månad.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du läsa mer om ditt faktureringskonto.

- [Förstå administrativa roller för ditt nya faktureringskonto](../manage/understand-mca-roles.md)
- [Skapa en till Azure-prenumeration för ditt nya faktureringskonto](../manage/create-subscription.md)
- [Skapa avsnitt på din faktura och organisera dina kostnader](../manage/mca-section-invoice.md)
