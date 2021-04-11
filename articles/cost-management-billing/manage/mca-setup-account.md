---
title: Konfigurera fakturering för Microsoft-kundavtal – Azure
description: Lär dig hur du konfigurerar ditt faktureringskonto för ett Microsoft-kundavtal. Se krav för installationen och Visa andra tillgängliga resurser.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/19/2021
ms.author: banders
ms.openlocfilehash: 15aa3acab9fe98a4c2f5103ba211dde34220c54e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255692"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Konfigurera ditt faktureringskonto för ett Microsoft-kundavtal

Om din direkta Enterprise-avtalsregistrering har upphört att gälla eller snart upphör kan du teckna ett Microsoft-kundavtal för att förnya registreringen. I den här artikeln beskrivs ändringarna i din befintliga fakturering efter konfigurationen, och du får hjälp med konfigurationen av ditt nya faktureringskonto. För närvarande går det inte att förnya förfallna indirekta Enterprise-avtal med ett Microsoft-kundavtal.

Förnyelsen omfattar följande steg:

1. Godkänna det nya Microsoft-kundavtalet. Arbeta med din Microsoft-fältrepresentant för att förstå detaljerna och godkänna det nya avtalet.
2. Konfigurera det nya faktureringskonto som skapas för det nya Microsoft-kundavtalet.

För att konfigurera faktureringskontot måste du överföra faktureringen av Azure-prenumerationer från din Enterprise-avtalsregistrering till det nya kontot. Installations programmet påverkar inte Azure-tjänster som körs i dina prenumerationer. Den ändrar dock ditt sätt att hantera faktureringen för dina prenumerationer.

- I stället för [EA-portalen](https://ea.azure.com) hanterar du Azure-tjänster och -fakturering i [Azure-portalen](https://portal.azure.com).
- Du får en månatlig, digital faktura för dina avgifter. Du kan visa och analysera fakturan på sidan för Azure-kostnadshantering och fakturering.
- I stället för avdelningar och konton i din Enterprise-avtalsregistrering använder du faktureringsstrukturen och omfången från det nya kontot för att hantera och organisera din fakturering.

Innan du påbörjar konfigurationen rekommenderar vi att du genomför följande åtgärder:

- **Förstå ditt nya faktureringskonto**
  - Ditt nya konto förenklar faktureringen för din organisation. [Få en snabb översikt över ditt nya faktureringskonto](../understand/mca-overview.md)
- **Kontrollera din åtkomst för att slutföra konfigurationen**
  - Endast användare med vissa administrativa behörigheter kan slutföra konfigurationen. Kontrollera om du har [nödvändig åtkomst för att slutföra konfigurationen](#access-required-to-complete-the-setup).
- **Förstå ändringar i din faktureringshierarki**
  - Det nya faktureringskontot är organiserat på ett annat sätt än din Enterprise-avtalsregistrering. [Förstå ändringar i din faktureringshierarki i det nya kontot](#understand-changes-to-your-billing-hierarchy).
- **Förstå ändringar i din faktureringsadministratörs åtkomst**
  - Administratörer från din Enterprise-avtalsregistrering får åtkomst till faktureringsomfången i det nya kontot.[Förstå ändringar av deras åtkomst](#changes-to-billing-administrator-access).
- **Visa Enterprise-avtalsfunktioner som har ersatts av det nya kontot**
  - Visa funktioner i Enterprise-avtalsregistrering som ersätts av funktioner i det nya kontot.
- **Visa svar på de vanligaste frågorna**
  - Visa [ytterligare information](#additional-information) om du vill veta mer om konfigurationen.

## <a name="access-required-to-complete-the-setup"></a>Åtkomst för att slutföra konfigurationen

För att kunna slutföra konfigurationen behöver du följande åtkomst:

- Ägare till den faktureringsprofil som skapades när Microsoft-kundavtalet signerades. Mer information om faktureringsprofiler finns i [förstå faktureringsprofiler](../understand/mca-overview.md#billing-profiles).  
&mdash; Särskilt &mdash;
- Företagsadministratör för den registrering som förnyas.

### <a name="start-migration-and-get-permission-needed-to-complete-setup"></a>Starta migreringen och få behörighet som krävs för att slutföra installationen

Du kan använda följande alternativ för att starta migreringen av din EA-registrering till ditt Microsoft-kund avtal.


- Logga in på Azure-portalen med hjälp av länken i det e-postmeddelande som skickades till dig när du signerade Microsoft-kundavtalet.

- Om du inte har e-postadressen loggar du in med följande länk. Ersätt `enrollmentNumber` med registreringsnumret för ditt Enterprise-avtal som förnyades.

  `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

Om du har både rollen företags administratör och fakturerings kontots ägare eller roll för fakturerings profil, ser du följande sida i Azure Portal. Du kan fortsätta att konfigurera dina EA-registreringar och fakturerings kontot för Microsofts kund avtal för över gång.

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page.png" alt-text="Skärm bild som visar sidan Konfigurera ditt fakturerings konto" lightbox="./media/mca-setup-account/setup-billing-account-page.png" :::

Om du inte har rollen företags administratör för företags avtalet eller ägaren av fakturerings profilen för Microsofts kund avtal använder du följande information för att få åtkomst till den åtkomst som du behöver för att slutföra installationen.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Om du inte är företagsadministratör för registreringen

Du ser följande sida i Azure Portal om du har ett fakturerings konto eller en roll för fakturerings profil, men du inte är företags administratör.

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page-not-ea-administrator.png" alt-text="Skärm bild som visar sidan Konfigurera ditt fakturerings konto – Förbered dina Enterprise-avtal registreringar för över gång." lightbox="./media/mca-setup-account/setup-billing-account-page-not-ea-administrator.png" :::

Du kan välja mellan två alternativ:

- Be företags administratören om registreringen att ge dig rollen som företags administratör. Mer information finns i [skapa en annan företags administratör](ea-portal-administration.md#create-another-enterprise-administrator).
-  Du kan ge en företags administratör rollen som fakturerings konto ägare eller ägare till fakturerings profil. Mer information finns i [hantera fakturerings roller i Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

Om du får rollen som företags administratör kopierar du länken på sidan Konfigurera ditt fakturerings konto. Öppna den i webbläsaren om du vill fortsätta med konfigurationen av ditt Microsoft-kund avtal. Annars skickar du den till företags administratören.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Om du inte är ägare till faktureringsprofilen

Om du är företags administratör men inte har något konto för fakturerings konto eller fakturerings profil ägare för ditt Microsoft-kundavtal, ser du följande sida i Azure Portal.

Om du tror att du har fakturerings profil ägarens åtkomst till rätt kund avtal från Microsoft och att du ser följande meddelande kontrollerar du att du har rätt klient organisation. Du kan behöva ändra kataloger.

:::image type="content" source="./media/mca-setup-account/setup-billing-account-page-not-billing-account-profile-owner.png" alt-text="Skärm bild som visar sidan Konfigurera ditt fakturerings konto – fakturerings konto för Microsofts kund avtal." lightbox="./media/mca-setup-account/setup-billing-account-page-not-billing-account-profile-owner.png" :::

Du kan välja mellan två alternativ:

- Be en befintlig ägare av fakturerings kontot att ge dig fakturerings kontots ägare eller ägare rollen för fakturerings profilen. Mer information finns i [hantera fakturerings roller i Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- Ge rollen företags administratör till en befintlig fakturerings konto ägare. Mer information finns i [skapa en annan företags administratör](ea-portal-administration.md#create-another-enterprise-administrator).

Om du har fått rollen som fakturerings konto ägare eller ägare till fakturerings profil, kopierar du länken på sidan Konfigurera ditt fakturerings konto. Öppna den i webbläsaren om du vill fortsätta med konfigurationen av ditt Microsoft-kund avtal. Annars skickar du länken till fakturerings kontots ägare.

## <a name="understand-changes-to-your-billing-hierarchy"></a>Förstå ändringar i din faktureringshierarki

Ditt nya faktureringskonto förenklar faktureringen för din organisation och ger dig förbättrade funktioner för fakturering och kostnadshantering. I följande diagram förklaras hur faktureringen organiseras på det nya faktureringskontot.

![Bild på ea-mca-post-transition-hierarchy](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. Du använder faktureringskontot för att hantera faktureringen för ditt Microsoft-kundavtal. Enterprise-administratörer blir ägare till faktureringskontot. Mer information om faktureringskonto finns i [förstå faktureringskonto](../understand/mca-overview.md#your-billing-account).
2. Du använder faktureringsprofilen för att hantera faktureringen för din organisation på ett liknande sätt som för din Enterprise-avtalsregistrering. Företagsadministratörer blir ägare till faktureringsprofilen. Mer information om faktureringsprofiler finns i [förstå faktureringsprofiler](../understand/mca-overview.md#billing-profiles).
3. Du kan använda ett fakturaavsnitt för att organisera dina kostnader utifrån dina behov, ungefär som avdelningar i din Enterprise-avtalsregistrering. Avdelning blir fakturaavsnitt, och avdelningsadministratörer blir ägare av respektive fakturaavsnitt. Mer information om fakturaavsnitt finns i [Förstå fakturaavsnitt](../understand/mca-overview.md#invoice-sections).
4. De konton som skapades i ditt Enterprise-avtal stöds inte i det nya faktureringskontot. Kontots prenumerationer tillhör respektive fakturaavsnitt för deras avdelning. Kontoägare kan skapa och hantera prenumerationer för sina fakturaavsnitt.

## <a name="changes-to-billing-administrator-access"></a>Ändringar av faktureringsadministratörers åtkomst

Beroende på åtkomsten kan faktureringsadministratörer i din Enterprise-avtalsregistrering få åtkomst till faktureringsomfången på det nya kontot. I följande tabell förklaras ändringen av åtkomst under konfigurationen:

| Befintlig roll | Roll efter övergång |
| --- | --- |
| **Företagsadministratör (Skrivskyddad = Nej)** | **– Faktureringskontoägare** </br> Hantera allt i faktureringskontot </br> **– Faktureringsprofilsägare** </br> Hantera allt i faktureringsprofilen </br> **– Fakturaavsnittsägare för alla fakturaavsnitt** </br> Hantera allt i fakturaavsnitten |
| **Företagsadministratör (Skrivskyddad = Ja)** | **– Faktureringskontoläsare** </br> Skrivskyddad vy över allt på faktureringskontot</br> **– Faktureringsprofilsläsare** </br> Skrivskyddad vy över allt i faktureringsprofilen</br>**– Fakturaavsnittsläsare i alla fakturaavsnitt**</br> Skrivskyddad vy över allt i fakturaavsnitten|
| **Avdelningsadministratör (Skrivskyddad = Nej)** |**– Fakturaavsnittsägare i det fakturaavsnitt som skapats för respektive avdelning** </br>Hantera allt i fakturaavsnittet|
| **Avdelningsadministratör (Skrivskyddad = Ja)**|**– Fakturaavsnittsläsare i det fakturaavsnitt som skapats för respektive avdelning**</br> Skrivskyddad vy över allt i fakturaavsnittet|
| **Kontoägare** | **– Azure-prenumerationsskapare i det fakturaavsnitt som skapats för respektive avdelning** </br>  Skapa Azure-prenumerationer för sitt fakturaavsnitt|

Du väljer en Azure Active Directory-klientorganisation (AD) för det nya faktureringskontot när du godkänner ditt Microsoft-kundavtal. Om en klientorganisation inte finns för din organisation skapas en ny klientorganisation. Klientorganisationen representerar din organisation i Azure Active Directory. Globala klientorganisationsadministratörer i din organisation använder klientorganisationen för att hantera åtkomst till program och data i din organisation.

Ditt nya konto stöder endast användare från den klientorganisation som valdes vid signeringen av Microsoft-kundavtalet. Om användare med administrativ behörighet i ditt Enterprise-avtal är en del av klientorganisationen får de åtkomst till det nya faktureringskontot under konfigurationen. Om de inte är en del av klientorganisationen kan de inte komma åt det nya faktureringskontot såvida du inte bjuder in dem.

När du bjuder in användare läggs de till i klientorganisationen som gästanvändare och får åtkomst till faktureringskontot. För att gäster ska kunna bjudas in måste gäståtkomst vara aktiverad för klientorganisationen. Mer information finns i [kontrollera gäståtkomst i Azure Active Directory](/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Om gäståtkomsten är avstängd kontaktar du de globala administratörerna för din klientorganisation för att aktivera den. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Visa ersatta funktioner

Följande Enterprise-avtalsfunktioner ersätts med nya funktioner i faktureringskontot för ett Microsoft-kundavtal.

### <a name="enterprise-agreement-accounts"></a>Enterprise-avtalskonton

De konton som skapades i din Enterprise-avtalsregistrering stöds inte på det nya faktureringskontot. Kontots prenumerationer tillhör det fakturaavsnitt som skapats för deras respektive avdelning. Kontoägare blir Azure-prenumerationsskapare och kan skapa och hantera prenumerationer för sina fakturaavsnitt.

### <a name="notification-contacts"></a>Meddelandekontakter

Meddelandekontakter får e-postmeddelanden om Azure Enterprise-avtalet. De stöds inte på det nya faktureringskontot. E-postmeddelanden om Azure-krediter och fakturor skickas till användare som har åtkomst till faktureringsprofiler i ditt faktureringskonto.

### <a name="spending-quotas"></a>Utgiftskvoter

Utgiftskvoter som angavs för avdelningar i din Enterprise-avtalsregistrering ersätts med budgetar i det nya faktureringskontot. En budget skapas för varje utgiftskvot som angetts för avdelningar i din registrering. Mer information om budgetar finns i [Självstudie: Skapa och hantera Azure-budgetar](../costs/tutorial-acm-create-budgets.md).

### <a name="cost-centers"></a>Kostnadsställen

Kostnadsställen som angavs för Azure-prenumerationerna i din Enterprise-avtalsregistrering följer med i det nya faktureringskontot. Dock stöds inte kostnadsställen för avdelningar och Enterprise-avtalskonton.

## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om hur du konfigurerar ditt faktureringskonto.

### <a name="no-service-downtime"></a>Inget avbrott i tjänsten

Azure-tjänster i din prenumeration fortsätter att köras utan avbrott. Vi flyttar bara faktureringsrelationen för dina Azure-prenumerationer. Befintliga resurser, resursgrupper och hanteringsgrupper påverkas inte.

### <a name="user-access-to-azure-resources"></a>Användaråtkomst till Azure-resurser

Åtkomst till Azure-resurser som angavs med hjälp av rollbaserad åtkomstkontroll i Azure (Azure RBAC) påverkas inte under övergången.

### <a name="azure-reservations"></a>Azure-reservationer

Azure-reservationer i din Enterprise-avtalsregistrering flyttas till ditt nya faktureringskonto. Vid övergången sker inga ändringar av reservationsrabatter som tillämpas på dina prenumerationer.

### <a name="azure-marketplace-products"></a>Azure Marketplace-produkter

Alla Azure Marketplace-produkter i din Enterprise-avtalsregistrering flyttas tillsammans med prenumerationerna. Det kommer inte att ske några ändringar i tjänståtkomsten för Marketplace-produkterna under övergången.

### <a name="support-plan"></a>Supportplan

Supportförmåner överförs inte som en del av övergången. Köp en ny supportplan för att få förmåner för Azure-prenumerationer i ditt nya faktureringskonto.

### <a name="past-charges-and-balance"></a>Tidigare avgifter och saldo

Avgifts- och kreditsaldo före övergången kan visas i din Enterprise-avtalsregistrering via Azure-portalen. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>När ska konfigurationen slutföras?

Slutför konfigurationen av ditt faktureringskonto innan din Enterprise-avtalsregistrering upphör att gälla. Om registreringen upphör fortsätter tjänsterna i dina Azure-prenumerationer att köras utan avbrott. Du debiteras dock enligt betala per användning för tjänsterna.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Ändringar i Enterprise-avtalsregistreringen efter konfigurationen

Azure-prenumerationer som skapas för Enterprise-avtalsregistreringen efter övergången kan flyttas manuellt till det nya faktureringskontot. Mer information finns i [få faktureringsägarskap för Azure-prenumerationer från andra användare](mca-request-billing-ownership.md). Om du vill flytta Azure-reservationer som köps efter övergången [kontaktar du Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Du kan även ge användare åtkomst till faktureringskontot efter övergången. Mer information finns i [hantera faktureringsroller i Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Ångra övergången

Övergången kan inte ångras. När faktureringen av dina Azure-prenumerationer flyttas till det nya faktureringskontot kan du inte gå tillbaka till din Enterprise-avtalsregistrering.

### <a name="closing-your-browser-during-setup"></a>Stänga webbläsaren under konfigurationen

Innan du väljer **Starta övergången** kan du stänga webbläsaren. Du kan gå tillbaka till konfigurationen via den länk som du fick i e-postmeddelandet och starta övergången. Om du stänger webbläsaren efter att övergången har startats fortsätter övergången att köras. Gå tillbaka till sidan för övergångsstatus om du vill se senaste status för övergången. Du får ett e-postmeddelande när övergången är klar.

## <a name="complete-the-setup-in-the-azure-portal"></a>Slutföra konfigurationen i Azure-portalen

För att slutföra konfigurationen behöver du tillgång till både det nya faktureringskontot och Enterprise-avtalsregistreringen. Mer information finns i [åtkomst krävs för att slutföra konfigurationen av ditt faktureringskonto](#access-required-to-complete-the-setup).

1. Logga in på Azure-portalen med hjälp av länken i det e-postmeddelande som skickades till dig när du signerade Microsoft-kundavtalet.

2. Om du inte har e-postadressen loggar du in med följande länk. Ersätt `<enrollmentNumber>` med registreringsnumret för ditt Enterprise-avtal som förnyades.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Välj **Starta övergången** i det sista steget i konfigurationen. När du har valt att starta övergången:

    ![Skärmbild som visar konfigurationsguiden](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - En faktureringshierarki som motsvarar din Enterprise-avtalshierarki skapas i det nya faktureringskontot. Mer information finns i [förstå ändringar i din faktureringshierarki](#understand-changes-to-your-billing-hierarchy).
    - Administratörer från din Enterprise-avtalsregistrering får åtkomst till det nya faktureringskontot så att de fortsätter att hantera faktureringen för din organisation.
    - Faktureringen för dina Azure-prenumerationer överförs till det nya kontot. **Dina Azure-tjänster påverkas inte under övergången. De fortsätter att köras utan avbrott**.
    - Om du har Azure-reservationer flyttas de till det nya faktureringskontot utan att fördelarna eller perioden ändras.

4. Du kan övervaka status för övergången på sidan **Övergångsstatus**.

   ![Skärmbild som visar övergångsstatus](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>Verifiera faktureringskontos inställningar

 Kontrollera följande för att se till att ditt nya faktureringskonto är korrekt konfigurerat:

### <a name="azure-subscriptions"></a>Azure-prenumerationer

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/mca-setup-account/search-cmb.png)

3. Välj faktureringskontot. Faktureringskontot har typen **Microsoft-kundavtal**.

4. Välj **Azure-prenumerationer** på vänster sida.

   ![Skärmbild som visar en lista över prenumerationer](./media/mca-setup-account/mca-subscriptions-post-transition.png)

Azure-prenumerationer som överförs från din Enterprise-avtalsregistrering till det nya faktureringskontot visas på sidan Azure-prenumerationer. Om du tror att någon prenumeration saknas kan du överföra faktureringen för prenumerationen manuellt i Azure-portalen. Mer information finns i [få faktureringsägarskap för Azure-prenumerationer från andra användare](mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure-reservationer

Azure-reservationer i Enterprise-avtalsregistreringen flyttas till det nya faktureringskontot utan att fördelarna eller perioden ändras. Transaktioner som slutförs före övergången visas inte på det nya faktureringskontot. Du kan dock kontrollera att fördelarna med dina reservationer tillämpas på prenumerationerna på [sidan med Azure-reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>Företagsadministratörers åtkomst till faktureringskontot

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/mca-setup-account/search-cmb.png)

3. Välj faktureringskontot för ditt **Microsoft-kundavtal**.

4. Välj **Åtkomstkontroll (IAM)** på vänster sida.

   ![Skärmbild som visar åtkomst för företagsadministratörer som anges som faktureringskontoägare efter övergången.](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

Företagsadministratörer anges som faktureringskontoägare medan företagsadministratörer med skrivskyddad behörighet anges som faktureringskontoläsare. Om du tror att åtkomsten för någon företagsadministratör saknas kan du ge den åtkomst i Azure-portalen. Mer information finns i [hantera faktureringsroller i Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Åtkomst för företagsadministratörer i faktureringsprofilen

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/mca-setup-account/search-cmb.png)

3. Välj den faktureringsprofil som skapats för din registrering. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du Faktureringsprofiler och sedan faktureringsprofilen.

4. Välj **Åtkomstkontroll (IAM)** på vänster sida.

   ![Skärmbild som visar åtkomst för företagsadministratörer som anges som faktureringsprofilägare efter övergången.](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

Företagsadministratörer anges som faktureringsprofilägare medan företagsadministratörer med skrivskyddad behörighet anges som faktureringsprofilläsare. Om du tror att åtkomsten för någon företagsadministratör saknas kan du ge den åtkomst i Azure-portalen. Mer information finns i [hantera faktureringsroller i Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Åtkomst för företagsadministratörer, avdelningsadministratörer och kontoägare i fakturaavsnitt

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/mca-setup-account/search-cmb.png).

3. Välj ett fakturaavsnitt. Fakturaavsnitten har samma namn som sina respektive avdelningar i Enterprise-avtalsregistreringar. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Från faktureringskontot väljer du **Faktureringsprofiler** och sedan **Fakturaavsnitt**. Välj ett fakturaavsnitt i listan med fakturaavsnitt.

   ![Skärmbild som visar listan över fakturaavsnitt efter övergången](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. Välj **Åtkomstkontroll (IAM)** på vänster sida.

    ![Skärmbild som visar åtkomst för avdelningsadministratörer och kontoadministratörer efter övergång](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

Företagsadministratörer och avdelningsadministratörer anges som fakturaavsnittsägare eller fakturaavsnittsläsare medan kontoägare på avdelningen anges som Azure-prenumerationsskapare. Upprepa steget för alla fakturaavsnitt för att kontrollera åtkomsten för alla avdelningar i din Enterprise-avtalsregistrering. Kontoägare som inte tillhörde någon avdelning får behörighet i ett fakturaavsnitt som heter **Default invoice section** (Standardfakturaavsnitt). Om du tror att åtkomsten för någon administratör saknas kan du ge den åtkomst i Azure-portalen. Mer information finns i [hantera faktureringsroller i Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Komma igång med ditt nya faktureringskonto](../understand/mca-overview.md)

- [Slutföra Enterprise-avtalsuppgifter i ditt faktureringskonto för ett Microsoft-kundavtal](mca-enterprise-operations.md)

- [Hantera åtkomst till ditt faktureringskonto](understand-mca-roles.md)