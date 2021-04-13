---
title: Förstå och arbeta med Azure Cost Management-omfång
description: Den här artikeln hjälper dig att förstå tillgängliga omfång för fakturering och resurshantering i Azure och hur du använder omfången i Cost Management och API:er.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 693045d094ddbf5ad6df81eca8cd16d9cac62fe4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308616"
---
# <a name="understand-and-work-with-scopes"></a>Förstå och arbeta med omfång

Den här artikeln hjälper dig att förstå tillgängliga omfång för fakturering och resurshantering i Azure och hur du använder omfången i Cost Management och API:er.

## <a name="scopes"></a>Omfattningar

Ett _omfång_ är en nod i Azure-resurshierarkin där Azure AD-användarna får åtkomst till och hanterar tjänster. De flesta Azure-resurser skapas och distribueras till resursgrupper, som ingår i prenumerationer. Microsoft erbjuder även två hierarkier över Azure-prenumerationerna som har specialiserade roller för att hantera faktureringsdata:
- Faktureringsdata, till exempel betalningar och fakturor
- Molntjänster, till exempel kostnads- och principstyrning

Omfång är den plats där du hanterar faktureringsdata, har specifika roller för betalningar, ser fakturor, samt utför allmän kontohantering. Rollerna för fakturering och konton hanteras separat från de som används för resurshantering. Dessa använder [Azure RBAC](../../role-based-access-control/overview.md). För att tydligt särskilja avsikten med de separata omfången, inklusive skillnaderna i åtkomstkontroll, kallas dessa för _faktureringsomfång_ respektive _Azure RBAC-omfång_.

Vill du veta mer om omfång kan du se videon [Cost Management setting up hierarchies](https://www.youtube.com/watch?v=n3TLRaYJ1NY) (konfigurera hierarkier i Cost Management). Om du vill titta på andra videor går du till [YouTube-kanalen för Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Hur Cost Management använder omfång

Cost Management kan användas i alla omfång ovanför resurser för att organisationer ska kunna hantera kostnader på den nivå som de har åtkomst till, oavsett om det är hela faktureringskontot eller en enda resursgrupp. Även om faktureringsomfången kan skilja sig åt beroende på Microsoft-avtal (prenumerationstyp) gäller det inte för Azure RBAC-omfången.

## <a name="azure-rbac-scopes"></a>Azure RBAC-omfång

Azure stöder tre omfång för resurshantering. Varje omfång stöder hantering av åtkomst och styrning, inklusive men inte begränsat till kostnadshantering.

- [**Hanteringsgrupper**](../../governance/management-groups/overview.md) – Hierarkiska containrar, upp till åtta nivåer, som organiserar Azure-prenumerationer.

    Resurstyp: [Microsoft.Management/managementGroups](/rest/api/managementgroups/)

- **Prenumerationer** – Primära containrar för Azure-resurser.

    Resurstyp: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Resursgrupper**](../../azure-resource-manager/management/overview.md#resource-groups) – Logiska grupperingar av relaterade resurser för en Azure-lösning som delar samma livscykel. Till exempel resurser som distribueras och tas bort tillsammans.

    Resurstyp: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Med hanteringsgrupper kan du organisera prenumerationer i en hierarki. Du kan till exempel skapa en logisk organisationshierarki med hjälp av hanteringsgrupper. Därefter ger du teamen prenumerationer på produktions- och Dev/Test-arbetsbelastningar. Skapa sedan resursgrupper i prenumerationerna för att hantera varje delsystem eller komponent.

Genom att skapa en organisationshierarki kan du ackumulera kostnader och kompatibilitetsprinciper organisatoriskt. Därefter kan varje ledare se och analysera sina aktuella kostnader. De kan sedan skapa budgetar för att upptäcka bristfälliga utgiftsmönster och optimera kostnader med Advisor-rekommendationer på den lägsta nivån.

Att bevilja åtkomst för att kunna se kostnader och eventuellt hantera kostnadskonfiguration, till exempel budgetar och exporter, utförs med Azure RBAC på styrningsomfång. Du använder Azure RBAC när du ger Azure AD-användare och -grupper åtkomst för att kunna utföra en fördefinierad uppsättning åtgärder som definieras i en roll upp till ett specifikt omfång. En roll som exempelvis tilldelas till en hanteringsgrupps omfång ger även samma behörighet till kapslade prenumerationer och resursgrupper.

Cost Management stöder följande inbyggda roller för de olika omfången nedan:

- [**Ägare**](../../role-based-access-control/built-in-roles.md#owner) – Kan se kostnader och hantera allt, inklusive kostnadskonfiguration.
- [**Deltagare**](../../role-based-access-control/built-in-roles.md#contributor) – Kan se kostnader och hantera allt inklusive kostnadskonfiguration, men exklusive åtkomstkontroll.
- [**Läsare**](../../role-based-access-control/built-in-roles.md#reader) – kan visa allt, inklusive kostnadsdata och konfiguration, men kan inte göra ändringar.
- [**Cost Management-deltagare**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – Kan se kostnader, hantera kostnadskonfiguration och se rekommendationer.
- [**Cost Management-läsare**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – Kan se kostnader, hantera kostnadskonfiguration och se rekommendationer.

Cost Management-deltagare är den rekommenderade rollen för minsta privilegium. Rollen ger behörighet att skapa och hantera budgetar och exporter för att kunna övervaka och rapportera kostnader mer effektivt. Cost Management-deltagare kan också behöva ha ytterligare roller för att kunna använda komplexa kostnadshanteringsscenarier. Fundera över följande scenarier:

- **Rapporter om resursanvändning** – Azure Cost Management visar kostnaden i Azure Portal. Den innehåller användning eftersom den gäller kostnader för de fullständiga användnings mönstren. Den här rapporten kan också visa API och nedladdningsavgifter, men du kanske även vill se detaljerad användningsstatistik i Azure Monitor för att få en djupare förståelse. Överväg att bevilja åtkomst för [Övervakningsläsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) till alla omfång där du även behöver rapporter om detaljerade användningsmått.
- **Agera när budgetar överskrids** – Cost Management-deltagare måste också ha behörighet att skapa och/eller hantera åtgärdsgrupper för att automatiskt kunna agera vid överförbrukning. Du kan bevilja [Övervakningsdeltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) till en resursgrupp som innehåller den åtgärdsgrupp som ska användas när budgettrösklar överskrids. Att automatisera vissa åtgärder kräver ytterligare roller för de tjänster som används, till exempel Automation och Azure Functions.
- **Schemalägga export av kostnadsdata** – Cost Management-deltagare behöver också åtkomst till att hantera lagringskonton för att kunna schemalägga en export som kopierar data till ett lagringskonto. Du kan bevilja [Lagringskontodeltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor) till en resursgrupp som innehåller lagringskontot dit kostnadsdata exporteras.
- **Visa rekommendationer om kostnadsbesparingar** – Cost Management-läsare och Cost Management-deltagare har tillgång till att *visa* kostnadsrekommendationer som standard. Att kunna följa kostnadsrekommendationerna kräver dock åtkomst till enskilda resurser. Du kan bevilja en [tjänstspecifik roll](../../role-based-access-control/built-in-roles.md#all) om du vill kunna följa en kostnadsbaserad rekommendation.

Hanteringsgrupper stöds endast om de innehåller Enterprise-avtal (EA), PAYG (betala per användning) eller interna Microsoft-prenumerationer. Hanteringsgrupper med andra prenumerationstyper, t. ex. Microsoft-kundavtal eller Azure Active Directory-prenumerationer, kan inte visa kostnader. Om du har en blandning av prenumerationer flyttar du prenumerationerna som inte stöds till en separat del i hanteringsgruppens hierarki för att aktivera kosthantering för de prenumerationer som stöds. Skapa exempelvis två hanteringsgrupper under rothanteringsgruppen: **Azure AD** och **Min org**. Flytta din Azure AD-prenumeration till hanteringsgruppen **Azure AD** och visa och hantera sedan kostnader med hanteringsgruppen **Min org**.

## <a name="enterprise-agreement-scopes"></a>Omfång för Enterprise-avtal

Enterprise-avtalens faktureringskonton, som även kallas registreringar, har följande omfång:

- [**Faktureringskonto**](../manage/view-all-accounts.md) – Motsvarar en EA-registrering. Fakturor genereras i detta omfång. Köp som inte är användningsbaserade, till exempel Marketplace och reservationer, är bara tillgängliga i det här omfånget. De visas inte för avdelningar eller registreringskonton. Reservationsanvändning och all annan användning tillämpas på enskilda resurser. Användningen slås samman till prenumerationer inom faktureringskontot. Om du vill se reservationskostnader uppdelade på varje resurs växlar du till vyn **Amorterad kostnad** i kostnadsanalys.

    Resurstyp: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Avdelning** – Valfri gruppering av registreringskonton.

    Resurstyp: `Billing/billingAccounts/departments`

- **Registreringskonto** – Motsvarar en enskild kontoinnehavare. Saknar stöd för att bevilja åtkomst till flera personer.

    Resurstyp: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Även om styrningsomfång är bundna till en enda katalog, är EA-faktureringsomfång det inte. Ett EA-faktureringskonto kan ha prenumerationer i flera Azure AD-kataloger.

EA-faktureringsomfång stöder följande roller:

- **Företagsadministratör** – Kan hantera inställningar och åtkomst för faktureringskonton, se alla kostnader och hantera kostnadskonfiguration. Till exempel budgetar och exporter. EA-faktureringsomfånget är i princip detsamma som [Azure-rollen Cost Management-deltagare](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Företagsanvändare med läsbehörighet** – Kan se inställningar för faktureringskonton, kostnadsdata och kostnadskonfiguration. Till exempel budgetar och exporter. EA-faktureringsomfånget är i princip detsamma som [Azure-rollen Cost Management-läsare](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Avdelningsadministratör** – Kan hantera avdelningsinställningar, till exempel kostnadsställe, samt kan öppna och se alla kostnader och hantera kostnadskonfigurationen. Till exempel budgetar och exporter.  Faktureringskontots inställningar för **Visa avgifter för DA** måste vara aktiverade för att avdelningsadministratörer och användare med läsbehörighet ska kunna se kostnaderna. Om alternativet **Visa avgifter för DA** option is har inaktiverats kan avdelningsanvändarna inte se kostnader på någon nivå, även om de är konto- eller prenumerationsinnehavare.
- **Avdelningsanvändare med läsbehörighet** – Kan se avdelningsinställningar, kostnadsdata och kostnadskonfiguration. Till exempel budgetar och exporter. Om alternativet **Visa avgifter för DA** option is har inaktiverats kan avdelningsanvändarna inte se kostnader på någon nivå, även om de är konto- eller prenumerationsinnehavare.
- **Kontoinnehavare** – Kan hantera inställningar för registreringskontot (till exempel kostnadsställe), se alla kostnader och hantera kostnadskonfigurationen (till exempel budgetar och export) för registreringskontot. Faktureringskontots inställning för **Visa avgifter för AO** måste vara aktiverad för att kontoinnehavare och Azure RBAC-användare ska kunna se kostnaderna.

Användare av EA-faktureringskontot har inte någon direkt åtkomst till fakturor. Fakturor är tillgängliga från ett externt volymlicensieringssystem.

Azure-prenumerationer kapslas under registreringskonton. Faktureringsanvändare har åtkomst till kostnadsdata för de prenumerationer och resursgrupper som tillhör deras omfång. De har inte åtkomst till att se eller hantera resurser i Azure-portalen. Användarna kan se kostnader genom att gå till **Kostnadshantering + fakturering** i tjänstlistan i Azure Portal. Därefter kan de filtrera kostnader till de specifika prenumerationer och resursgrupper som de ska rapportera om.

Faktureringsanvändare har inte åtkomst till hanteringsgrupper eftersom de inte uttryckligen finns i något specifikt faktureringskonto. Åtkomst måste beviljas explicit till hanteringsgrupperna. Hanteringsgrupperna ackumulerar kostnader från alla kapslade prenumerationer. De innefattar dock bara användningsbaserade inköp. De innefattar inte köp som t.ex. reservationer och Marketplace-erbjudanden från tredje part. Om du vill se dessa kostnader använder du EA-faktureringskontot.

## <a name="individual-agreement-scopes"></a>Omfång för enskilda avtal

Azure-prenumerationer som skapats från enskilda erbjudanden där man betalar per användning, eller relaterade typer som kostnadsfria utvärderingsversioner och Dev/Test-erbjudanden, har inget explicit faktureringskontoomfång. I stället har varje prenumeration en kontoinnehavare eller kontoadministratör, till exempel EA-kontoinnehavaren.

- [**Faktureringskonto**](../manage/view-all-accounts.md) – Motsvarar en enskild kontoinnehavare av en eller flera Azure-prenumerationer. Den har för närvarande inte stöd för att bevilja åtkomst till flera personer eller åtkomst till aggregerade kostnadsvyer.

    Resurstyp: Inte tillämpligt

Enskilda Azure-prenumerations konto administratörer kan visa och hantera fakturerings data, till exempel fakturor och betalningar, från [Azure Portal](https://portal.azure.com)  >  **prenumerationer** > välja en prenumeration.

Till skillnad från EA kan administratörer av enskilda Azure-prenumerationskonton se sina fakturor i Azure-portalen. Tänk på att rollerna Cost Management-läsare och Cost Management-deltagare inte ger åtkomst till fakturor. Mer information finns i [Bevilja åtkomst till fakturor](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Omfång för Microsoft-kundavtal

Faktureringskonton för Microsoft-kundavtal har följande omfång:

- **Faktureringskonto** – Motsvarar ett kundavtal för flera Microsoft-produkter och tjänster. Faktureringskonton för kundavtal fungerar inte på samma sätt som EA-registreringar. EA-registreringar är mer anpassade efter faktureringsprofiler.

    Resurstyp: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Faktureringsprofil** – Definierar de prenumerationer som ingår i en faktura. Faktureringsprofiler är den funktionella motsvarigheten till en EA-registrering, eftersom det är det omfång som fakturorna genereras från. På liknande sätt är köp som inte är användarbaserade (till exempel Marketplace och reservationer) bara tillgängliga i det här omfånget. De ingår inte i fakturaavsnitten.

    Resurstyp: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Fakturaavsnitt** – Motsvarar en grupp med prenumerationer på en faktura eller i en faktureringsprofil. Fakturaavsnitt liknar avdelningar – flera personer kan ha åtkomst till ett fakturaavsnitt.

    Resurstyp: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Kund** – Motsvarar en grupp med prenumerationer som är associerade till en specifik kund som har registrerats med ett Microsoft-kundavtal av en partner. Det här omfånget är endast till för molnlösningsleverantörer (CSP).

Till skillnad från faktureringsomfången för EA _är_ faktureringskonton för kundavtal bundna till en enda katalog och kan inte ha prenumerationer i flera Azure AD-kataloger.

Faktureringsomfången för kundavtal gäller inte för partners. Partnerroller och behörigheter beskrivs i [Tilldela användarroller och behörigheter](/partner-center/permissions-overview).

Faktureringsomfången för kundavtal har stöd för följande roller:

- **Ägare** – Kan hantera faktureringsinställningar och åtkomst, se alla kostnader och hantera kostnadskonfiguration. Till exempel budgetar och exporter. Faktureringsomfånget för kundavtal är i princip detsamma som [Azure-rollen Cost Management-deltagare](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Deltagare** – Kan hantera faktureringsinställningar förutom åtkomst, se alla kostnader och hantera kostnadskonfiguration. Till exempel budgetar och exporter. Faktureringsomfånget för kundavtal är i princip detsamma som [Azure-rollen Cost Management-deltagare](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Läsare** – Kan se faktureringsinställningar, kostnadsdata och kostnadskonfiguration. Till exempel budgetar och exporter. Faktureringsomfånget för kundavtal är i princip detsamma som [Azure-rollen Cost Management-läsare](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Fakturaansvarig** – Kan se och betala fakturor, samt se kostnadsdata och konfiguration. Till exempel budgetar och exporter. Faktureringsomfånget för kundavtal är i princip detsamma som [Azure-rollen Cost Management-läsare](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Azure-prenumerationens skapare** – Kan skapa Azure-prenumerationer, se kostnader och hantera kostnadskonfiguration. Till exempel budgetar och exporter. Faktureringsomfånget för kundavtal är i princip detsamma som rollen som kontoinnehavare för EA-registreringskontot.

Azure-prenumerationer kapslas under fakturaavsnitt, på ungefär samma sätt som i EA-registreringskonton. Faktureringsanvändare har åtkomst till kostnadsdata för de prenumerationer och resursgrupper som tillhör deras omfång. De har dock inte åtkomst att se eller hantera resurser i Azure-portalen. Faktureringsanvändare kan se kostnader genom att gå till **Cost Management och fakturering** i Azure-portalens lista med tjänster. Därefter kan de filtrera kostnaderna till de specifika prenumerationer och resursgrupper som de ska rapportera om.

Faktureringsanvändare har inte åtkomst till hanteringsgrupper, eftersom de inte uttryckligen finns i faktureringskontot. Men när hanteringsgrupper aktiveras för organisationen sammanställs alla prenumerationskostnader på faktureringskontot och i rothanteringsgruppen eftersom de båda är begränsade till en enda katalog. Hanteringsgrupper innehåller endast inköp som är användningsbaserade. Inköp som reservationer och Marketplace-erbjudanden från tredje part ingår inte i hanteringsgrupper. Därför kan faktureringskontot och rothanteringsgruppen rapportera olika summor. Om du vill se dessa kostnader använder du faktureringskontot eller faktureringsprofilen.

## <a name="aws-scopes"></a>AWS-omfång

När AWS-integreringen är klar kan du läsa om att [konfigurera AWS-integrering](aws-integration-set-up-configure.md). Följande omfång är tillgängliga:

- **Externt faktureringskonto** – Motsvarar ett kundavtal med en tredjepartsleverantör. Detta liknar EA-faktureringskontot.

    Resurstyp: `Microsoft.CostManagement/externalBillingAccounts`

- **Extern prenumeration** – Motsvarar kundens driftskonto hos en tredjepartsleverantör. Det liknar en Azure-prenumeration.

    Resurstyp: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Omfång för leverantörer av molnlösningar (CSP)

Följande omfång stöds för CSP:er med kunder i ett Microsoft-kundavtal:

- **Faktureringskonto** – Motsvarar ett kundavtal för flera Microsoft-produkter och tjänster. Faktureringskonton för kundavtal fungerar inte på samma sätt som EA-registreringar. EA-registreringar är mer anpassade efter faktureringsprofiler.

    Resurstyp: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Faktureringsprofil** – Definierar de prenumerationer som ingår i en faktura. Faktureringsprofiler är den funktionella motsvarigheten till en EA-registrering, eftersom det är det omfång som fakturorna genereras från. På liknande sätt är köp som inte är användarbaserade (till exempel Marketplace och reservationer) bara tillgängliga i det här omfånget.

    Resurstyp: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Kund** – Motsvarar en grupp med prenumerationer som är associerade till en specifik kund som har registrerats med ett Microsoft-kundavtal av en partner.

Det är bara användare med rollen *Global administratör* och *Administratörsagent* som kan hantera och se kostnader för faktureringskonton, faktureringsprofiler och kunder direkt i partnerns Azure-klient. Mer information om Partner Center-roller finns i [Tilldela användarroller och behörigheter](/partner-center/permissions-overview).

Azure Cost Management stöder bara CSP-partnerkunder om kunderna har ett Microsoft-kundavtal. Se [Partner Center](/azure/cloud-solution-provider/overview/partner-center-overview) för CSP-kunder som inte har något Microsoft-kundavtal än.

Hanteringsgrupper i CSP-omfång stöds inte av Cost Management. Om du har en CSP-prenumeration och anger omfattningen till en hanteringsgrupp i kostnadsanalysen visas ett fel som detta:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Växla mellan omfång i Cost Management

I alla Cost Management-vyer i Azure-portalen går det att välja **Omfång** längst upp till vänster i vyn. Använd funktionen för att snabbt ändra omfång. Öppna omfångsväljaren genom att välja **Omfång**. Här visas faktureringskonton, rothanteringsgruppen och eventuella prenumerationer som inte är kapslade under rothanteringsgruppen. Om du vill välja ett omfång klickar du på bakgrunden för att markera det och sedan på **Välj** längst ned. Om du vill se mera om kapslade omfång, t.ex. resursgrupper i en prenumeration, klickar du på omfångsnamnslänken. Om du vill välja ett överordnat omfång på en kapslad nivå klickar du på **Välj det här &lt;omfånget&gt;** högst upp i omfångsväljaren.

## <a name="identify-the-resource-id-for-a-scope"></a>Identifiera resurs-ID för ett omfång

När du arbetar med API:er i Cost Management är det viktigt att känna till omfånget. Använd följande information för att skapa rätt omfångs-URI för API:er i Cost Management.

### <a name="billing-accounts"></a>Faktureringskonton

1. Öppna Azure-portalen och gå sedan till **Kostnadshantering och fakturering** i listan med tjänster.
2. Välj **Egenskaper** i faktureringskontots meny.
3. Kopiera faktureringskontots ID.
4. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Faktureringsprofiler

1. Öppna Azure-portalen och gå sedan till **Kostnadshantering och fakturering** i listan med tjänster.
2. Välj **Faktureringsprofiler** i faktureringskontots meny.
3. Välj faktureringsprofilens namn.
4. Välj **Egenskaper** i faktureringskontots meny.
5. Kopiera ID:n för faktureringskonto och faktureringsprofil.
6. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Fakturaavsnitt

1. Öppna Azure-portalen och gå sedan till **Kostnadshantering och fakturering** i listan med tjänster.
2. Välj **Fakturaavsnitt** i faktureringskontots meny.
3. Välj fakturaavsnittets namn.
4. Välj **Egenskaper** i menyn för fakturaavsnittet.
5. Kopiera ID:n för faktureringskonto och fakturaavsnitt.
6. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-avdelningar

1. Öppna Azure-portalen och gå sedan till **Kostnadshantering och fakturering** i listan med tjänster.
2. Välj **Avdelningar** i faktureringskontots meny.
3. Välj avdelningens namn.
4. Välj **Egenskaper** i avdelningsmenyn.
5. Kopiera ID:n för faktureringskonto och avdelning.
6. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA-registreringskonto

1. Öppna Azure-portalen och gå till **Kostnadshantering och fakturering** i listan med tjänster.
2. Välj **Registreringskonton** i faktureringskontots meny.
3. Välj registreringskontots namn.
4. Välj **Egenskaper** i registreringskontots meny.
5. Kopiera ID:n för faktureringskonto och registreringskonto.
6. Ditt omfång är: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Hanteringsgrupp

1. Öppna Azure-portalen och gå till **Hanteringsgrupper** i listan med tjänster.
2. Gå till hanteringsgruppen.
3. Kopiera hanteringsgruppens ID från tabellen.
4. Ditt omfång är: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Prenumeration

1. Öppna Azure-portalen och gå till **Prenumerationer** i listan med tjänster.
2. Kopiera prenumerationens ID från tabellen.
3. Ditt omfång är: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Resursgrupper

1. Öppna Azure-portalen och gå till **Resursgrupper** i listan med tjänster.
2. Välj resursgruppens namn.
3. Välj **Egenskaper** i menyn för resursgruppen.
4. Kopiera fältvärdet för resurs-ID:t.
5. Ditt omfång är: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management stöds för närvarande i [Azure globalt](https://management.azure.com) och [Azure Government](https://management.usgovcloudapi.net). Mer information om Azure Government finns i [API-slutpunkter för Azure globalt och Azure Government](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har slutfört den första Cost Management-snabbstarten kan du gå till [Börja analysera kostnader](quick-acm-cost-analysis.md).
