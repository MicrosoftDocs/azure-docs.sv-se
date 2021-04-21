---
title: Skapa ett Erbjudande för Dynamics 365 for Customer Engagement & PowerApps på Microsoft AppSource (Azure Marketplace).
description: Skapa ett Erbjudande för Dynamics 365 for Customer Engagement & PowerApps på Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: d8b3a2da7ccbbbf866763dbe5c8c59b00e7abd10
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820506"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Så här skapar du ett Erbjudande för Dynamics 365 for Customer Engagement & PowerApps

Den här artikeln beskriver hur du skapar ett Erbjudande för Dynamics 365 for Customer Engagement & PowerApps. Alla erbjudanden för Dynamics 365 går igenom vår certifieringsprocess. Med utvärderingsversionen kan användare distribuera din lösning till en dynamics 365-livemiljö.

Innan du börjar skapar du ett konto för den kommersiella marknadsplatsen [i Partnercenter](partner-center-portal/create-account.md) och ser till att det har registrerats i programmet för den kommersiella marknadsplatsen.

## <a name="before-you-begin"></a>Innan du börjar

Granska [Plan a Dynamics 365 offer (Planera ett Dynamics 365-erbjudande).](marketplace-dynamics-365.md) Den förklarar de tekniska kraven för det här erbjudandet och listar den information och de tillgångar som du behöver när du skapar det.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [Partner Center.](https://partner.microsoft.com/dashboard/home)
2. I den vänstra navigeringsmenyn väljer du **Översikt över Kommersiell**  >  **marknadsplats.**
3. På sidan Översikt väljer du **+ Nytt erbjudande Dynamics**  >  **365 for Customer Engagement & PowerApps**.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Visar menyalternativen i det vänstra fönstret och knappen Nytt erbjudande med Customer Engagement valt.":::

> [!IMPORTANT]
> När ett erbjudande har publicerats visas eventuella ändringar som du gör i Partnercenter den Microsoft AppSource du publicerar erbjudandet igen. Se till att alltid publicera om ett erbjudande när du har ändrat det.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID.** Det här är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webbadressen för erbjudandet och i Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. ID:t kan innehålla bindestreck och understreck, men inga blanksteg och är begränsat till 50 tecken. Om ditt utgivar-ID till exempel `testpublisherid` är och du anger **test-offer-1** blir erbjudandets webbadress `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Erbjudande-ID kan inte ändras när du har valt **Skapa.**

Ange ett **erbjudandealias**. Det här är det namn som används för erbjudandet i Partnercenter.

- Det här namnet används inte på AppSource. Det skiljer sig från erbjudandets namn och andra värden som visas för kunder.
- Det går inte att ändra det här namnet när du har valt **Skapa**.

Välj **Skapa** för att generera erbjudandet. Partnercenter öppnar sidan **För erbjudandekonfiguration.**

## <a name="alias"></a>Alias

Ange ett beskrivande namn som vi använder för att referera till det här erbjudandet enbart i Partnercenter. Erbjudandets alias (i förväg ifylld med det du angav när du skapade erbjudandet) används inte på Marketplace och skiljer sig från det erbjudandenamn som visas för kunderna. Om du vill uppdatera erbjudandets namn senare kan du gå till [sidan med erbjudandelistor.](dynamics-365-customer-engage-offer-listing.md)

## <a name="setup-details"></a>Installationsinformation

För Hur vill du att potentiella kunder ska interagera med det här **erbjudandet?** väljer du det alternativ som du vill använda för det här erbjudandet.

- **Hämta nu (kostnadsfritt)** – Visa ett erbjudande för kunder utan kostnad.
- **Kostnadsfri utvärderingsversion (lista)** – Ange ditt erbjudande för kunder med en länk till en kostnadsfri utvärderingsversion. Erbjudande med en lista över kostnadsfria utvärderingsversioner skapas, hanteras och konfigureras av din tjänst och har inte prenumerationer som hanteras av Microsoft.

    > [!NOTE]
    > De token som ditt program får via utvärderingslänken kan bara användas för att hämta användarinformation via Azure Active Directory (Azure AD) för att automatisera kontoskapandet i din app. Microsoft-konton stöds inte för autentisering med denna token.

- **Kontakta mig** – Samla in kundkontaktinformation genom att ansluta CRM-systemet (Customer Relationship Management). Kunden uppmanas att ge tillstånd att dela sin information. Dessa kunduppgifter, tillsammans med erbjudandets namn, ID och marketplace-källa där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [Kund leads](#customer-leads).

## <a name="test-drive"></a>Provkörning

En testkörning är ett bra sätt att demonstrera ditt erbjudande för potentiella kunder genom att ge dem åtkomst till en förkonfigurerad miljö under ett fast antal timmar. Att erbjuda en provkörning resulterar i en ökad konverteringstakt och genererar högt kvalificerade leads. Om du vill veta mer kan du [börja med Vad är en testkörning?](what-is-test-drive.md).

> [!TIP]
> En testkörning skiljer sig från en kostnadsfri utvärderingsversion. Du kan erbjuda antingen en provkörning, en kostnadsfri utvärderingsversion eller både och. Båda ger kunderna din lösning under en viss tidsperiod. Men en provkörning innehåller också en praktisk, guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementeringsscenario.

Om du vill aktivera en testenhet markerar du kryssrutan Aktivera en **provkörning** och väljer **typ av testenhet.** Du konfigurerar testkörningen senare. Med testkörningen måste du också konfigurera ditt erbjudande till ett CRM-system för kund leads (se nästa avsnitt). Om du vill ta bort testenheten från ditt erbjudande avmarkerar du den här kryssrutan.

## <a name="customer-leads"></a>Kund leads

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

Mer information finns i [Kund-leads från ditt erbjudande på den kommersiella marknadsplatsen.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

Välj **Spara utkast** innan du fortsätter till nästa flik i den vänstra navigeringsmenyn, **Egenskaper.**

## <a name="next-steps"></a>Nästa steg

- [Konfigurera egenskaper för erbjudande](dynamics-365-customer-engage-properties.md)
- [Metodtips för erbjudandelistor](gtm-offer-listing-best-practices.md)