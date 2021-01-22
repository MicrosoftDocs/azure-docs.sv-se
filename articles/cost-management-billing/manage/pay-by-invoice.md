---
title: Betala för Azure-prenumerationer med faktura
description: Lär dig hur du betalar Azure-prenumerationer med faktura. Mer information finns i vanliga frågor och svar.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: a5c95ca2e1fbe00f410c0a5015f8ce695783b27b
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538216"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Betala för din Azure-prenumeration med faktura

Den här artikeln gäller för kunder som har ett Microsoft-kundavtal (MCA) och som har registrerat sig för Azure via Azure-webbplatsen. [Kontrollera din åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement). Om du har registrerat dig för Azure via en Microsoft-representant är din standardbetalningsmetod redan angiven som *check eller banköverföring*.

Om du byter till att betala med faktura innebär det att du betalar din faktura inom 30 dagar från fakturadatumet via check eller banköverföring. För att du ska kunna betala Azure-prenumerationen med faktura måste du först skicka en begäran till Azure-supporten. När din begäran har godkänts kan du byta till betalning med faktura (check/banköverföring) i Azure-portalen.

> [!IMPORTANT]
> * Betalning med faktura (check/banköverföring) är endast tillgängligt för kunder som använder Azure på uppdrag av ett företag.
> * Betala alla utestående avgifter innan du byter till fakturabetalning.
> * För närvarande stöds inte fakturabetalning för Global Azure i Kina.

## <a name="request-to-pay-by-invoice"></a>Begära att betala via faktura

1. Logga in på Azure Portal för att skicka en supportbegäran. Leta upp och välj **Hjälp + support**.  
    ![Leta upp Hjälp och support, Microsoft Azure-portalen](./media/pay-by-invoice/search-for-help-and-support.png)
1. Välj **Ny supportbegäran**.  
    ![Ny länk för supportbegäranden, skärmen Hjälp och support, Microsoft Azure-portalen](./media/pay-by-invoice/help-and-support.png)
1. Välj **Fakturering** som **Problemtyp**. *Problemtyp* är kategorin för supportbegäran. Välj den prenumeration du vill betala för med faktura. Välj sedan ett supportavtal och därefter **Nästa**.
1. Välj **Betalning** som **Problemtyp**. *Problemtypen* är underkategorin för supportbegäran.
1. Välj **Byt till att betala med faktura** som **Undergrupp av problem**.
1. Ange följande information i rutan **Information** och välj sedan **Nästa**.
    - Ny eller befintlig kund:
    - Betalningsmetod för befintlig kund:
    - Order-ID (begärs för fakturaalternativet):
    - Live-ID för kontoadministratörer (eller organisations-ID) (bör vara företagsdomän):
    - Handelskonto-ID:
    - Företagets namn (som det är registrerat för moms eller myndighetswebbplats):
    - Företagets adress (som den är registrerad för moms eller myndighetswebbplats):
    - Företagets webbplats:
    - Land:
    - Organisationsnummer/momsregistreringsnummer:
    - Företaget etablerades (år):
    - Tidigare affärer med Microsoft:
    - Kontaktnamn:
    - Kontaktens telefonnummer:
    - Kontaktens e-postadress:
    - Motivering till varför du vill ha faktura i stället för kreditkort:
    - Ange följande ytterligare information om du vill öka antalet kärnor:
        - (Gammal kvot) Befintliga kärnor:
        - (Ny kvot) Begärda kärnor:
        - Specifik region och serie för prenumeration:
    - **Företagsnamn** och **Företagsadress** ska matcha den information som du angav för Azure-kontot. Om du vill visa eller uppdatera informationen går du till [Ändra information för din Azure-kontoprofil](change-azure-account-profile.md).
    - Lägg till dina kontaktuppgifter för fakturering i Azure-portalen innan kreditgränsen kan godkännas. Kontaktuppgifterna bör vara relaterade till företagets leverantörsreskontra eller ekonomiavdelning.
1. Kontrollera din kontaktinformation, din önskade kontaktmetod och välj **Skapa**.

Om vi behöver utföra en kreditkontroll på grund av den mängd kredit som du behöver skickar vi en ansökan om kreditkontroll. Vi kan be dig att tillhandahålla företagets reviderade finansiella rapporter. Om ingen finansiell information anges eller om informationen inte är tillräckligt stark för att stödja den kreditgräns som begärs kan vi be om en säkerhetsdeposition eller standby-remburs för att godkänna din begäran om checkkredit.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Byta till fakturabetalning (check/banköverföring)

När du har godkänts för betalning med faktura kan du byta till betalning med faktura (check/banköverföring) i Azure-portalen.

Om du har ett Microsoft Online Services-programkonto kan du byta till att betala Azure-prenumerationen via check/banköverföring. Med ett Microsoft-kundavtal kan du byta faktureringsprofil till check/banköverföring.

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Byta Azure-prenumeration till check/banköverföring

Följ stegen nedan för att byta din Azure-prenumeration till fakturabetalning (check/banköverföring). *När du övergår till fakturabetalning (check/banköverföring) kan du inte byta tillbaka med betalning med kreditkort*.

1. Gå till Azure-portalen för att logga in som kontoadministratör. Leta upp och välj **Kostnadshantering + fakturering**.  
    ![Skärmbild som visar sökning efter kostnadshantering och fakturering i Azure-portalen.](./media/pay-by-invoice/search.png)
1. Välj den prenumeration som du vill börja betala med faktura.
1. Välj **Betalningsmetoder**.
1. Välj knappen **Betala per faktura** i kommandofältet.  
    ![Knappen Betala per faktura, Betalningsmetoder, Microsoft Azure-portalen](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Byta faktureringsprofil till check/banköverföring

Följ stegen nedan om du vill byta faktureringsprofil till check/banköverföring. Endast den person som registrerade sig för Azure kan ändra standardbetalningsmetoden för en faktureringsprofil.

1. Gå till Azure-portalen för att visa din faktureringsinformation. Leta upp och välj **Kostnadshantering + fakturering**.
1. Välj **Faktureringsprofiler** på menyn.  
    ![Menyalternativet Faktureringsprofiler, Kostnadshantering och fakturering, Microsoft Azure-portalen](./media/pay-by-invoice/billing-profile.png)
1. Välj en faktureringsprofil.
1. Välj **Betalningsmetoder** på menyn **Faktureringsprofil**.  
   ![Menyalternativet Betalningsmetoder, Faktureringsprofiler, Kostnadshantering, Microsoft Azure-portalen](./media/pay-by-invoice/billing-profile-payment-methods.png)
1. Välj den blå banderollen där det står att du är berättigad till check/banköverföring.  
    ![Banderoll för att byta till check/banköverföring, Betalningsmetoder, Microsoft Azure-portalen](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

*Varför har jag fått en förfrågan om ett juridiskt dokument?*

Ibland behöver Microsoft juridisk dokumentation om den information du har angett är ofullständig eller inte går att verifiera. Exempel:

* Namnet på kontonamn och företagsnamn är olika
* Ändrat namn

## <a name="next-steps"></a>Nästa steg

* Om det behövs uppdaterar du dina kontaktuppgifter för fakturering i [Azure-kontocenter](https://account.azure.com/Profile).