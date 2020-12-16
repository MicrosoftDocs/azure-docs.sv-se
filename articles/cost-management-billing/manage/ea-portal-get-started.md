---
title: Komma igång med Azure Enterprise-portalen
description: Den här artikeln förklarar hur kunder med Azure Enterprise-avtal (Azure EA) använder Azure Enterprise-portalen.
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 88bb4c24489b973aa4230ba6f2b34756f83c7dda
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030696"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Komma igång med Azure Enterprise-portalen

Den här hjälper direkta och indirekta kunder med Azure Enterprise-avtal (Azure EA) med att börja använda [Azure Enterprise-portalen](https://ea.azure.com). Du får grundläggande information om:

- Strukturen hos Azure Enterprise-portalen.
- Roller som används i Azure Enterprise-portalen.
- Skapande av prenumerationer.
- Kostnadsanalys i Azure Enterprise-portalen och i Azure-portalen.

## <a name="get-started-with-ea-onboarding"></a>Komma igång med EA-registrering

En Azure EA-registreringsguide finns i [Azure EA-registreringsguiden (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).

Titta på den här videon för att se en fullständig registreringssession för Azure Enterprise-portalen:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>Förstå EA-användarroller och introduktion till användarhierarkin

Azure-kunder med ett Enterprise-avtal (EA) kan tilldela fem olika administratörsroller för att hantera organisationens användning och utgifter:

- Företagsadministratör
- Företagsadministratör (skrivskyddad)
- Avdelningsadministratör
- Avdelningsadministratör (skrivskyddad)
- Kontoägare

Varje roll har olika grader av begränsningar och behörigheter. Mer information finner du i artikeln [Organisationsstruktur och behörigheter per roll](./understand-ea-roles.md#organization-structure-and-permissions-by-role).

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>Aktivera en registrering, skapa en prenumeration och andra administrativa uppgifter

Mer information om hur du aktiverar registreringen, skapar en avdelning eller en prenumeration, lägger till administratörer och kontoägare, och andra administrativa uppgifter finns i [Administration i Azure EA-portalen](./ea-portal-administration.md).

Om du vill veta mer om hur du överför en Enterprise-prenumeration till en prenumeration där du betalar per användning kan du läsa mer i [Azure Enterprise-överföringar](./ea-transfers.md).

## <a name="view-usage-summary-and-download-reports"></a>Visa användningssammanfattning och nedladdning av rapporter

Du kan hantera och agera på din Azure EA-faktura. Din faktura är en representation av din räkning och du bör därför granska den för att se om den är korrekt.

Läs [Fakturor för Azure Enterprise-registrering](./ea-portal-enrollment-invoices.md) för att se användningssammanfattningar, ladda ned rapporter och hantera registreringsfakturor.

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>Nu när du känner till grunderna kan du använda dessa länkar för att komma igång

[Prissättning för Azure EA](./ea-pricing-overview.md) innehåller information om hur användningen beräknas och förklarar priser för olika Azure-tjänster i Enterprise-avtalet där beräkningarna är mer komplexa.

Läs [Reserverade instanser av virtuella Azure EA-datorer](./ea-portal-vm-reservations.md) om du vill veta mer om hur du kan spara pengar i din företagsregistrering med hjälp av reserverade VM-instanser i Azure.

Läs [REST-API:er för Azure Enterprise](./ea-portal-rest-apis.md) för att få information om vilka REST-API:er du bör använda med din Azure Enterprise-registrering och en förklaring till hur du löser vanliga problem med REST-API:er.

Artikeln [Azure EA-avtal och ändringar](./ea-portal-agreements.md) beskriver hur Azure EA-avtal och ändringar kan påverka åtkomst, användning och betalningar för Azure-tjänster.

Artikeln [Azure Marketplace](./ea-azure-marketplace.md) förklarar hur EA-kunder och -partner kan se Marketplace-avgifter och aktivera Azure Marketplace-köp.

I artikeln [Administration i Azure EA-portalen för partner](./ea-partner-portal-administration.md) förklaras några vanliga uppgifter som en EA-administratör hos en partner kan utföra i Azure EA-portalen.

## <a name="get-started-on-azure-ea---faq"></a>Komma igång med Azure EA – Vanliga frågor och svar

Det här avsnittet innehåller information om vanliga frågor som ställts av kunder under registreringsprocessen.  

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Jag associerade av misstag mitt befintliga Azure-konto med Azure EA-registrering. Därför har jag förlorat min månadskredit. Kan jag få tillbaka min månadskredit?

Om du har loggat in som Azure EA-kontoinnehavare med samma autentiseringsuppgifter som för din Visual Studio-prenumeration kan du återställa din enskilda Azure-förmån för Visual Studio-prenumeration genom att utföra någon av följande åtgärder:

- Ta bort kontoinnehavaren från Azure Enterprise-portalen efter att du har tagit bort eller flyttat eventuella associerade Azure-prenumerationer. Registrera dig sedan för enskilda Visual Studio Azure-förmåner på nytt.
- Ta bort Visual Studio-prenumeranten från administrationsplatsen i VLSC och tilldela om prenumerationen till ett konto med andra autentiseringsuppgifter den här gången. Registrera dig sedan för enskilda Visual Studio Azure-förmåner på nytt.

### <a name="what-type-of-subscription-should-i-create"></a>Vilken typ av prenumeration ska jag skapa?

Azure Enterprise-portalen har två typer av prenumerationer för företagskunder:

- Microsoft Azure Enterprise, som passar för:
  - All produktionsanvändning
  - Bästa priser baserat på infrastrukturutgifter

  [Kontakta Azure-säljavdelningen](https://azure.microsoft.com/pricing/enterprise-agreement/) om du vill få mer information.

- Enterprise Dev/Test, som passar för:
  - Alla utvecklar-/testarbetsbelastningar
  - Medelstor till stor enskild utvecklar-/testarbetsbelastning
  - Åtkomst till särskilda MSDN-avbildningar och priser för förmånstjänster

  Mer information finns i [Enterprise Dev/Test-erbjudande](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Mitt prenumerationsnamn är detsamma som namnet på erbjudandet. Ska jag ändra prenumerationsnamnet till något som är meningsfullt för min organisation?

När du skapar en prenumeration blir namnet som standard den erbjudandetyp som du väljer. Vi rekommenderar att du ändrar prenumerationsnamnet till något som gör det lätt för dig att spåra prenumerationen.

Så här ändrar du namnet:

1. Logga in på [https://account.windowsazure.com](https://account.windowsazure.com).
1. Välj prenumerationslistan.
1. Välj den prenumeration som du vill redigera.
1. Välj ikonen **Hantera prenumeration**.
1. Redigera prenumerationsinformation.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Hur spårar jag kostnader som uppstått på ett kostnadsställe?

För att spåra kostnader per kostnadsställe behöver du definiera kostnadsstället på någon av följande nivåer:

- Avdelning
- Konto
- Prenumeration

Utifrån dina behov kan du använda samma kostnadsställe för att spåra användning och kostnader som är associerade med ett visst kostnadsställe.

Om du till exempel vill spåra kostnaden för ett särskilt projekt där flera avdelningar är inblandade kan du definiera kostnadsstället på en prenumerationsnivå för att spåra användningen och kostnaderna.

Du kan inte definiera ett kostnadsställe på servicenivå. Om du vill spåra användning på servicenivå kan du använda funktionen _Tagg_, som finns på servicenivå.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Hur spårar jag användning och utgifter av olika avdelningar i min organisation?

Du kan skapa så många avdelningar som du behöver under din Azure EA-registrering. För att kunna spåra användningen på rätt sätt ska du se till att prenumerationer inte delas mellan olika avdelningar.

När du har skapat avdelningar och prenumerationer kan du se data i användningsrapporten. Den här informationen kan hjälpa dig att spåra användning samt hantera kostnader och utgifter på avdelningsnivå.

Du kan även komma åt användningsdata via rapporterings-API:et. Detaljerad information och exempelkod finns i [REST-API:er för Azure Enterprise](./ea-portal-rest-apis.md).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Kan jag ange en utgiftskvot och få aviseringar när jag närmar mig min gräns?

Du kan ange en utgiftskvot på avdelningsnivå, så meddelar systemet dig automatiskt när utgiftsgränsen når 50 %, 75 %, 90 % och 100 % av den kvot som du definierar.

Du definierar utgiftskvoten genom att välja en avdelning och sedan välja redigeringsikonen. När du har redigerat informationen för utgiftsgräns väljer du **Spara**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Jag använde resursgrupper för att implementera RBAC och spåra användning. Hur visar jag associerad användningsinformation?

Om du använder _resursgrupper_ och _taggar_ spåras den här informationen på servicenivå, och du kan komma åt den i nedladdningsfilen (CSV) för detaljerad användning. Se [ladda ned användningsrapport](https://ea.azure.com/report/downloadusage) i Azure Enterprise-portalen.

Du kan även komma åt användning via API. Detaljerad information och exempelkod finns i [REST-API:er för Azure Enterprise](./ea-portal-rest-apis.md).

> [!NOTE]
> Du kan endast använda taggar på resurser som stöder Azure Resource Manager-åtgärder. Om du har skapat en virtuell dator, ett virtuellt nätverk eller lagring via den klassiska distributionsmodellen (t.ex. via den klassiska portalen) kan du inte använda en tagg på den resursen. Du måste distribuera dessa resurser igen via Resource Manager för att stödja taggning. Alla andra resurser stöder taggning.

### <a name="can-i-perform-analyses-using-power-bi"></a>Kan jag göra analyser med Power BI?

Ja. Med Microsoft Azure Enterprise-innehållspaketet för Power BI kan du:

- Snabbt importera och analysera Azure-förbrukning för din företagsregistrering.
- Ta reda på vilken avdelning, vilket konto eller vilken prenumeration som förbrukade mest användning.
- Lära dig vilken tjänst som din organisation använde mest.
- Spåra utgifts- och användningstrender.

Så här använder du Power BI:

1. Gå till Power BI-webbplatsen.
1. Logga in med ett giltigt arbets- eller skolkonto.

   Arbets- eller skolkontot kan vara detsamma eller ett annat än det som används för att nå registreringen via Azure Enterprise-portalen.
1. På instrumentpanelen för tjänster väljer du Microsoft Azure Enterprise-panelen och sedan **Anslut**.
1. På skärmen **Anslut till Azure Enterprise** anger du:
    - URL till Azure-miljön: [https://ea.azure.com](https://ea.azure.com)
    - Antal månader: mellan 1 och 36
    - Registreringsnummer: ditt registreringsnummer
1. Välj **Nästa**.
1. Ange API-nyckel i rutan **Kontonyckel**.

   Du hittar API-nyckeln i Azure Enterprise-portalen. Titta på fliken **Ladda ned användning** och välj sedan **API-åtkomstnyckel**. Kopiera den och klistra in nyckeln i rutan **Kontonyckel** i Power BI.

Beroende på datamängdens storlek kan det ta mellan fem och trettio minuter för data att läsas in Power BI.

Power BI-rapportering är tillgängligt för Azure EA-direktkunder, partner och indirekta kunder som kan visa faktureringsinformation.

## <a name="next-steps"></a>Nästa steg

- Administratörer i Azure Enterprise-portalen bör läsa [Administration i Azure Enterprise-portalen](ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
- Om du behöver hjälp med att felsöka problem med Azure Enterprise-portalen kan du läsa [Felsöka åtkomst till Azure Enterprise-portalen](ea-portal-troubleshoot.md).