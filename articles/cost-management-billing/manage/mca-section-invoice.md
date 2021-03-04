---
title: Organisera din faktura utifrån dina behov – Azure
description: Lär dig hur du organiserar kostnader på din faktura. Du kan anpassa ditt faktureringskonto genom att skapa faktureringsprofiler och fakturaavsnitt.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: c3ee643d525cb5394369aba3b0e803bd1a94c852
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036246"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organisera kostnader genom att anpassa ditt faktureringskonto

Ditt faktureringskonto för Microsoft-kundavtal ger dig flexibiliteten att organisera kostnader baserat på dina behov, oavsett om det är efter avdelning, projekt eller utvecklingsmiljö.

I den här artikeln beskrivs hur du kan använda Azure-portalen för att organisera kostnader. Detta gäller för ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Se hur du [ordnar kostnaderna genom att anpassa ditt fakturerings konto i Microsoft Customer Agreement](https://www.youtube.com/watch?v=7RxTfShGHwU) och lär dig hur du ordnar kostnader för ditt fakturerings konto.

>[!VIDEO https://www.youtube.com/embed/7RxTfShGHwU]

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Strukturera ditt konto med faktureringsprofiler och fakturaavsnitt

I faktureringskontot för ett Microsoft-kundavtal använder du faktureringsprofiler och fakturaavsnitt för att organisera dina kostnader.

![Skärmbild som visar faktureringshierarki för Microsoft-kundavtal](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Faktureringsprofil

En faktureringsprofil representerar en faktura och relaterad faktureringsinformation, till exempel betalningsmetoder och faktureringsadress. I början av månaden skapas en månadsfaktura för varje faktureringsprofil på ditt konto. Fakturan innehåller avgifter för Azure-användning och andra inköp från föregående månad.

En faktureringsprofil skapas automatiskt tillsammans med ditt faktureringskonto när du registrerar dig för Azure. Du kan skapa ytterligare faktureringsprofiler för att organisera kostnader på flera månadsfakturor.

> [!IMPORTANT]
>
> Om du skapar ytterligare faktureringsprofiler kan det påverka totala kostnaden. Mer information finns i [Saker att tänka på när du lägger till nya faktureringsprofiler](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Fakturaavsnitt

Ett fakturaavsnitt representerar en gruppering av kostnader i din faktura. Ett fakturaavsnitt skapas automatiskt för varje faktureringsprofil i ditt konto. Du kan skapa ytterligare avsnitt för att organisera kostnader utifrån dina behov. Varje fakturaavsnitt visas på fakturan med de avgifter som tillkom den månaden.

Bilden nedan visar en faktura med två fakturaavsnitt – teknik och marknadsföring. Sammanfattnings- och detaljavgifterna för varje avsnitt visas på fakturan. De priser som visas i bilden är endast exempel och representerar inte de faktiska priserna för Azure-tjänster.

![Bild som visar en faktura med avsnitt](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Faktureringskontostruktur för vanliga scenarier

I det här avsnittet beskrivs vanliga scenarier för att organisera kostnader och motsvarande faktureringskontostrukturer:

|Scenario  |Struktur  |
|---------|---------|
|Jack registrerar sig för Azure och behöver en enskild månadsfaktura. | En faktureringsprofil och ett fakturaavsnitt. Den här strukturen konfigureras automatiskt för Jack när han registrerar sig för Azure, och det krävs inte några ytterligare steg. |

![Informationsgrafik för ett scenario med en enskild faktura.](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scenario  |Struktur  |
|---------|---------|
|Contoso är en liten organisation som behöver en enskild månadsfaktura men grupperar kostnader efter sina avdelningar – marknadsföring och teknik.  | En faktureringsprofil för Contoso och ett fakturaavsnitt var för avdelningarna marknadsföring och teknik. |

![Informationsgrafik för ett scenario med flera fakturor.](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scenario  |Struktur  |
|---------|---------|
|Fabrikam är en medelstor organisation som behöver separata fakturor för sina avdelningar för marknadsföring och teknik. För teknikavdelningen vill de gruppera kostnaderna efter miljöer – produktion och utveckling.  | En faktureringsprofil var för avdelningarna marknadsföring och teknik. För teknikavdelningen blir det ett fakturaavsnitt var för miljöerna produktion och utveckling. |

![Informationsgrafik för ett scenario med flera fakturor med separat fakturering för produktions- respektive utvecklingsmiljöer.](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Skapa ett nytt fakturaavsnitt

För att kunna skapa ett fakturaavsnitt behöver du vara **faktureringsprofilsägare** eller **faktureringsprofilsdeltagare**. Mer information finns i [Hantera fakturaavsnitt för faktureringsprofil](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/mca-section-invoice/search-cmb.png)

3. Välj **Faktureringsprofiler** i det vänstra fönstret. I listan väljer du en faktureringsprofil. Det nya avsnittet kommer att visas på fakturan för den valda faktureringsprofilen.

   [![Skärmbild som visar en lista över faktureringsprofiler.](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Välj **Fakturaavsnitt** i det vänstra fönstret och välj sedan **Lägg till** överst på sidan.

   [![Skärmbild som visar tillägg av fakturaavsnitt](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Ange ett namn för fakturaavsnittet.

   [![Skärmbild som visar sidan för skapande av fakturaavsnitt](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Välj **Skapa**.

## <a name="create-a-new-billing-profile"></a>Skapa en ny faktureringsprofil

För att kunna skapa en faktureringsprofil behöver du vara **faktureringskontoägare** eller **faktureringskontodeltagare**. Mer information finns i [Hantera faktureringsprofiler för faktureringskonto](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> Om du skapar ytterligare faktureringsprofiler kan det påverka totala kostnaden. Mer information finns i [Saker att tänka på när du lägger till nya faktureringsprofiler](#things-to-consider-when-adding-new-billing-profiles).

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/mca-section-invoice/search-cmb.png)

3. Välj **Faktureringsprofiler** i det vänstra fönstret och välj sedan **Lägg till** överst på sidan.

   [![Skärmbild som visar en lista över faktureringsprofiler med Lägg till valt.](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Om du inte ser knappen Lägg till på sidan Faktureringsprofil är funktionen inte tillgänglig för ditt konto. För närvarande är den endast tillgänglig för konton som har konfigurerats vid samarbete med en Microsoft-representant.

4. Fyll i formuläret och klicka på **Skapa**.

   [![Skärmbild som visar sidan för skapande av faktureringsprofil](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Field  |Definition  |
    |---------|---------|
    |Namn     | Ett visningsnamn som gör att du enkelt kan identifiera faktureringsprofilen i Azure-portalen.  |
    |IO-nummer    | Ett valfritt inköpsordernummer. Inköpsordernumret kommer att visas på de fakturor som genereras för faktureringsprofilen. |
    |Faktureringsadress   | Faktureringsadressen kommer att visas på de fakturor som genereras för faktureringsprofilen. |
    |E-postfaktura   | Markera rutan för e-postfaktura om du vill få fakturor för den här faktureringsprofilen via e-post. Om du inte väljer detta kan du visa och ladda ned fakturorna i Azure-portalen.|

5. Välj **Skapa**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Länka avgifter till fakturaavsnitt och faktureringsprofiler

När du har anpassat ditt faktureringskonto utefter dina behov kan du länka prenumerationer och andra produkter till det fakturaavsnitt och den faktureringsprofil som du vill använda.

### <a name="link-a-new-subscription"></a>Länka en ny prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Prenumerationer**.

   [![Skärmbild som visar en sökning efter prenumerationer i Azure-portalen.](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Välj **Lägg till** längst upp på sidan.

   ![Skärmbild som visar knappen Lägg till i prenumerationsvyn för en ny prenumeration.](./media/mca-section-invoice/subscription-add.png)

4. Om du har åtkomst till flera faktureringskonton väljer du faktureringskontot för ditt Microsoft-kundavtal.

   ![Skärmbild som visar fönstret Skapa prenumeration.](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Välj den faktureringsprofil som ska faktureras för prenumerationens användning. Avgifterna för Azure-användning och andra inköp för den här prenumerationen kommer att faktureras till den valda faktureringsprofilens faktura.

6. Välj fakturaavsnittet för att länka prenumerationens kostnader. Avgifterna kommer att visas under det här avsnittet på faktureringsprofilens faktura.

7. Välj en Azure-plan och ange ett användarvänligt namn för din prenumeration.

9. Klicka på **Skapa**.  

### <a name="link-existing-subscriptions-and-products"></a>Länka befintliga prenumerationer och produkter

Om du har befintliga Azure-prenumerationer eller andra produkter, till exempel Azure Marketplace och källresurser för appar, kan du flytta dem från deras befintliga fakturaavsnitt till ett annat fakturaavsnitt för att omorganisera dina kostnader.

> [!IMPORTANT]
>
> Prenumerationer och andra produkter kan endast flyttas mellan fakturaavsnitt som tillhör samma faktureringsprofil. Det går inte att flytta prenumerationer och produkter mellan fakturaavsnitt i olika faktureringsprofiler.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen efter Kostnadshantering + fakturering.](./media/mca-section-invoice/search-cmb.png)

3. Om du vill länka en prenumeration till ett nytt fakturaavsnitt väljer du **Azure-prenumerationer** på skärmens vänstra sida. För andra produkter såsom Azure Marketplace och källresurser för appar väljer du **Återkommande avgifter**.

   [![Skärmbild som visar alternativet för att ändra fakturaavsnitt](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. På sidan klickar du på ellipsen (tre punkter) för den prenumeration eller produkt som du vill länka till ett nytt fakturaavsnitt. Välj **Ändra fakturaavsnitt**.

5. Välj det nya fakturaavsnittet i listrutan. Listrutan visar endast fakturaavsnitt som är associerade med samma faktureringsprofil som det befintliga fakturaavsnittet.

    [![Skärmbild som visar val av ett nytt fakturaavsnitt](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Välj **Spara**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Saker att tänka på när du lägger till nya faktureringsprofiler

### <a name="azure-usage-charges-may-be-impacted"></a>Azure-användningsavgifter kan påverkas

I ditt faktureringskonto för ett Microsoft-kundavtal aggregeras Azure-användning varje månad för varje faktureringsprofil. Priserna för Azure-resurser med nivåindelade priser bestäms utifrån användningen för varje faktureringsprofil separat. Användningen aggregeras inte mellan faktureringsprofiler vid beräkning av priset. Detta kan påverka den totala kostnaden för Azure-användning för konton med flera faktureringsprofiler.

Nu ska vi titta på ett exempel på hur kostnader varierar i två scenarier. De priser som används i scenarierna är endast exempel och representerar inte de faktiska priserna för Azure-tjänster.

#### <a name="you-only-have-one-billing-profile"></a>Du har bara en faktureringsprofil.

Vi antar att du använder Azure Block Blob Storage, som kostar 0,00184 USD per GB för de första 50 terabyte (TB) och sedan 0,00177 per GB för nästföljande 450 terabyte (TB). Du har använt 100 TB i de prenumerationer som debiteras till din faktureringsprofil. Du debiteras då följande belopp.

|  Nivåpriser (USD) |Kvantitet | Belopp (USD)|
|---------|---------|---------|
|1,84 per TB för de första 50 TB/månad    | 50 TB        | 92,0   |
|1,77 per TB för nästföljande 450 TB/månad    |  50 TB         | 88,5   |
|Totalt     |     100 TB  | 180,5

De totala avgifterna för användning av 100 TB data i det här scenariot är **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Du har flera faktureringsprofiler.

Anta nu att du har skapat en till faktureringsprofil och använt 50 GB genom prenumerationer som debiteras till den första faktureringsprofilen och 50 GB via prenumerationer som debiteras till den andra faktureringsprofilen. Du debiteras då följande belopp.

`Charges for the first billing profile`

|  Nivåpriser (USD) |Kvantitet | Belopp (USD)|
|---------|---------|---------|
|1,84 per TB för de första 50 TB/månad    | 50 TB        | 92,0  |
|1,77 per TB för nästföljande 450 TB/månad    |  0 TB         | 0.0  |
|Totalt     |     50 TB  | 92,0

`Charges for the second billing profile`

|  Nivåpriser (USD) |Kvantitet | Belopp (USD)|
|---------|---------|---------|
|1,84 per TB för de första 50 TB/månad    | 50 TB        | 92,0  |
|1,77 per TB för nästföljande 450 TB/månad    |  0 TB         | 0.0  |
|Totalt     |     50 TB  | 92,0

De totala avgifterna för användning av 100 TB data i det här scenariot är **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Förmånerna för Azure-reservationer gäller inte nödvändigtvis för alla prenumerationer

Azure-reservationer med delat omfång tillämpas på prenumerationer i en enskild faktureringsprofil och delas inte mellan faktureringsprofiler.

![Informationsgrafik för reservationsprogram för olika faktureringskontostrukturer](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

I bilden ovan har Contoso två prenumerationer. Azure-reservationsförmånen tillämpas på olika sätt beroende på hur faktureringskontot är strukturerat. I scenariot till vänster tillämpas reservationsförmånen på båda prenumerationerna som debiteras till teknikavdelningens faktureringsprofil. I scenariot till höger tillämpas reservationsförmånen endast på prenumeration 1 eftersom det är den enda prenumeration som debiteras till teknikavdelningens faktureringsprofil.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Skapa ytterligare en Azure-prenumeration för Microsoft-kundavtal](create-subscription.md)
- [Hantera faktureringsroller i Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Få faktureringsägarskap för Azure-prenumerationer från användare i andra faktureringskonton](mca-request-billing-ownership.md)
