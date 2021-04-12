---
title: Frågor och svar om hantering av leads – Microsoft Partner Center
description: Läs om vanliga fel och frågor när du konfigurerar kommersiella Marketplace-leads i Partner Center
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330928"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Vanliga frågor och fel sökning för ledar konfiguration

Den här artikeln innehåller svar på några vanliga frågor om lead-hantering för dina kommersiella Marketplace-erbjudanden. Det tar också itu med fel som kan uppstå när du konfigurerar leads till ditt CRM-system (Customer Relations hip Management) i Partner Center.

## <a name="common-questions-about-lead-management"></a>Vanliga frågor om ledar hantering

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Var kan jag få hjälp med att ställa in mitt lead-mål?

Se [kund ledare från ditt kommersiella Marketplace-erbjudande](partner-center-portal/commercial-marketplace-get-customer-leads.md) för en översikt över hur du ansluter ditt CRM-system till dina kommersiella Marketplace-erbjudanden. Om du har ett fel kan du läsa fel söknings anvisningarna nedan. Om du vill ha mer support skickar du ett support ärende via [Hjälp och support för partner Center](https://aka.ms/marketplacepublishersupport). Välj sedan **erbjud skapa**  >  **din typ av**  >  **konfiguration för hantering av lead-hantering**.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Är jag tvungen att konfigurera ett lead-mål för att publicera ett erbjudande i den kommersiella marknads platsen?

Svaret beror på vilken typ av erbjudande du publicerar. Program vara som en tjänst (SaaS) och Dynamics 365 kund engagemang använder **kontakta mig** för att lista alla Dynamics 365 för finans-och drift erbjudanden, alla Dynamics 365 Business Central-erbjudanden och alla konsult tjänst erbjudanden. Det innebär att de kräver en anslutning till ett lead-mål. Om erbjudande typen inte finns med, krävs ingen anslutning till ett lead-mål. Vi rekommenderar att du konfigurerar ett lead-mål så att du inte saknar affärs möjligheter.

#### <a name="how-can-i-find-the-test-lead"></a>Hur kan jag hitta testets lead?

Sök efter `"MSFT_TEST"` i ditt lead-mål. Nedan visas ett exempel på ett lead-test från Microsoft. Observera att test-leadets format varierar beroende på lead-målet.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Jag har ett Live-erbjudande, men varför ser jag inte några leads?

Kontrol lera att din anslutning till målet för målet är giltig. Vi skickar ett test lead när du har valt **publicera** på ditt erbjudande i Partner Center. Om du ser testets lead är anslutningen giltig. Du kan också testa din lead-anslutning genom att försöka skaffa för hands versionen av erbjudandet under för hands versions steget. Välj **Hämta nu**, **kontakta mig** eller **kostnads fri utvärderings version** på listan på den kommersiella Marketplace.

Kontrol lera också att du letar efter rätt data. Se [förstå lead-data](partner-center-portal/commercial-marketplace-get-customer-leads.md) för en förklaring av lead-data som vi skickar till ditt lead-mål.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Jag har konfigurerat Azure Blob Storage som mitt lead-mål, men varför ser jag inte mitt lead?

Azure Blob Storage stöds inte längre som ett lead-mål, så du saknar kund ledare som genereras av ditt erbjudande. Växla till något av de andra [mål alternativen för lead](partner-center-portal/commercial-marketplace-get-customer-leads.md). 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Jag har fått ett e-postmeddelande från den kommersiella marknads platsen, men varför kan jag inte hitta något lead i min CRM?

Det är möjligt att slutanvändarens e-postdomän är från. edu. Av säkerhets skäl skickar vi inte personlig information från. edu-domänen. Skicka in ett support ärende via [Hjälp och support för partner Center](https://aka.ms/marketplacepublishersupport).

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Jag har konfigurerat en Azure-tabell som mitt lead-mål. Hur kan jag Visa leads?

Du kan komma åt lead-data som lagras i Azure-tabellen från Azure Portal. Du kan också hämta och installera [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) kostnads fritt om du vill visa tabell data för ditt Azure Storage-konto.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Jag har konfigurerat en Azure-tabell som mitt lead-mål. Kan jag få ett meddelande när ett nytt lead från en kommersiell marknads plats skickas?

Ja. Följ anvisningarna i [Konfigurera hantering av leads med hjälp av en Azure-tabell](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) för att konfigurera ett Microsoft Flow som skickar ett e-postmeddelande om ett lead läggs till i Azure-tabellen.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Jag har konfigurerat Salesforce som mitt lead-mål, men varför kan jag inte hitta några leads?

Kontrol lera om webb-till-lead-formuläret är ett obligatoriskt fält baserat på en plock lista. Om så är fallet byter du fältet till ett fält som inte är obligatoriskt.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Det uppstod ett problem med mitt lead-mål och jag missade några leads. Kan jag skicka dem till mig i ett e-postmeddelande?

På grund av personliga informations principer kan vi inte dela information om leads via oskyddad e-post.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Jag har konfigurerat en Azure-tabell som mitt lead-mål. Hur mycket kommer det att kosta?

Data för lead-generering är låg. Det är mindre än 1 GB för nästan alla utgivare. Kostnaden beror på antalet mottagna leads. Om till exempel 1 000 leads tas emot under en månad är kostnaden cirka 50 cent. Mer information om lagrings priser finns i [Azure Storage översikts priser](https://azure.microsoft.com/pricing/details/storage/).

Om din fråga inte besvaras kontaktar du Microsoft Support via [partner Center Hjälp och support](https://aka.ms/marketplacepublishersupport). Välj sedan **erbjud skapa**  >  **din typ av**  >  **konfiguration för hantering av lead-hantering**.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Jag får e-postmeddelanden när nya kund ledare tas emot. Hur kan jag konfigurera någon annan att ta emot e-postmeddelanden?

Kom åt ditt erbjudande i Partner Center och gå till sidan för **erbjudande konfiguration** > **hantering av lead-hantering**  >  . Uppdatera e-postadresserna under fältet **kontakt epost** .

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Felsöka fel vid lead-konfiguration

**Det gick inte att spara leadet i Dynamics CRM. Kontrol lera konto inställningarna för Dynamics CRM. LastCRMError: det går inte att logga in på Dynamics CRM, LastCRMException:** 

> Om Microsoft 365 autentisering valdes kontrollerar du om användar kontot och lösen ordet är giltigt. Om Azure Active Directory valdes kontrollerar du om klient-ID, program-ID och program hemlig nyckel matchar vad som har kon figurer ATS på Azure Active Directory. Följ instruktionerna [här](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Om kontots användar namn/lösen ord är giltigt kontrollerar du att det har åtkomst till Dynamics 365 och har en tilldelad licens (steg 11-15 om du använder Azure Active Directory eller säkerhets inställningar om du använder en Office-användare). 

**Det gick inte att spara leadet i Dynamics CRM. Användaren har inte behörighet att skapa leadsourcecode-attribut i entiteten lead** 

> Programmet/användaren saknar säkerhets roll (er) för att Microsoft Marketplace ledar skrivare. Följ steg 11-15 om du använder Azure Active Directory eller säkerhets inställningar om du använder en Office-användare [här](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Det gick inte att spara leadet i Dynamics CRM med AAD. Undantag:: klienten hittades inte. Den här instansen kan inträffa om det inte finns några aktiva prenumerationer för klienten.**  

> Katalog-ID: t i avsnittet om lead management är inte en giltig katalog. Hämta katalog-ID: t baserat på anvisningarna i steg 2 (under Azure Active Directory) [här](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Det gick inte att spara leadet i Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser misslyckades-inga roller har tilldelats till användaren.**  

> Lösning: tilldela en säkerhets roll till Microsoft Marketplace ledar skrivare. Följ anvisningarna [här](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) under säkerhets inställningar.

**Det gick inte att spara leadet i Dynamics CRM med AAD. Undantag:: det gick inte att hitta programmet med identifieraren i katalogen** 

> Det program-ID som angavs i lead management-avsnittet är inte en giltig katalog. Hämta katalog-ID: t baserat på anvisningarna i steg 8 (under Azure Active Directory [härifrån](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Det gick inte att spara leadet i Dynamics CRM med AAD. Undantag:: begärd klient-ID är inte giltigt och ogiltigt format för extern domän** 

> Katalog-ID: t i avsnittet om lead management är inte en giltig katalog. Hämta katalog-ID: t baserat på instruktionerna i steg 2 (under Azure Active Directory, härifrån [).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Det gick inte att spara leadet i Dynamics CRM med AAD. Undantag:: fel vid verifiering av autentiseringsuppgifter.: ogiltig klient hemlighet har angetts.** 

> Lösning: Logga in på Azure Portal, kontrol lera om program nyckeln matchar det som finns i Partner Center. Skapa lösen ord baserat på instruktionen i steg 10 (under [Azure Active Directory), härifrån.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Det gick inte att spara leadet i Dynamics CRM. LastCRMError: begär ande kanalen nådde tids gränsen i väntan på ett svar efter 00:02:00. Öka timeout-värdet som skickas till anropet till begäran eller öka SendTimeout-värdet för bindningen. Den tid som tilldelats den här åtgärden kan ha varit en del av en längre tids gräns.**  

> Lösning: Logga in på Partner Center, kontrol lera erbjudandet >> kund leads >> URL, kontrol lera om det är en giltig dynamisk CRM-instans.

