---
title: Byta Azure-prenumerationserbjudande
description: Lär dig hur du ändrar din Azure-prenumeration och byter till ett annat erbjudande.
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: banders
ms.openlocfilehash: dd8040effc5972d86e620793e437f5b185e12603
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685471"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Byt erbjudande för din Azure-prenumeration

Som en kund med [prenumerationen betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning kan du byta Azure-prenumeration till ett annat erbjudande i Azure Portal. Du kan till exempel använda den här funktionen för att dra nytta av [de månatliga krediterna för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Vill du bara uppgradera från den kostnadsfria utvärderingsversionen?** Läs mer i [Uppgradera din prenumeration](upgrade-azure-subscription.md).

## <a name="whats-supported"></a>Följande stöds:

Du kan växla från en prenumeration där du betalar per användning till:

- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för andra erbjudandeändringar.

## <a name="switch-subscription-offer"></a>Byt prenumerationserbjudande

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till **prenumerationer** och välj sedan prenumerationen betala per användning.
1. Välj **switch-erbjudande** längst upp på sidan. Alternativet är bara tillgängligt om du har en prenumeration där du betalar per användning och har slutfört din första fakturerings period.  
    :::image type="content" source="./media/switch-azure-offer/switch-offer.png" alt-text="ALTImage visar prenumerations information med optionTEXT för Switch-erbjudandet" lightbox="./media/switch-azure-offer/switch-offer.png" :::
1. Välj det erbjudande som du vill använda i listan med erbjudanden som din prenumeration kan växlas till. Den här listan varierar beroende på vilka medlemskap ditt konto är associerat med. Om inget är tillgängligt kontrollerar du [listan över tillgängliga erbjudanden som du kan byta till](#whats-supported) och kontrollerar att du har rätt medlemskap. Välj sedan **Nästa**.
    :::image type="content" source="./media/switch-azure-offer/select-offer.png" alt-text="Välj ett erbjudande som du vill byta till" lightbox="./media/switch-azure-offer/select-offer.png" :::
    Beroende på vilket erbjudande du byter till kan en anmärkning visas som beskriver effekten av bytet. Gå igenom listan noga och följ anvisningarna innan du fortsätter. Du kan också behöva verifiera ditt telefonnummer.
1. När du har granskat anteckningar eller verifierat ditt telefonnummer väljer du **Växla erbjudande**.
1. Nu är din prenumeration kopplad till det nya erbjudandet.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
I följande avsnitt får du svar på vanliga frågor.

### <a name="what-is-an-azure-offer"></a>Vad är ett Azure-erbjudande?

Ett Azure-erbjudande är den *typ* av Azure-prenumeration som du har. Exempel på Azure-erbjudanden är [en prenumeration med prismodellen Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure i Open-licensiering](https://azure.microsoft.com/offers/ms-azr-0111p/) och [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/). Varje erbjudande har olika [villkor](https://azure.microsoft.com/support/legal/offer-details/) och vissa har särskilda förmåner. Prenumerationens erbjudande visas på sidan med prenumerations information.

:::image type="content" source="./media/switch-azure-offer/subscription-details.png" alt-text="Sidan prenumerations information som visar erbjudande typen" lightbox="./media/switch-azure-offer/subscription-details.png" :::

### <a name="why-dont-i-see-the-button"></a>Varför visas inte knappen?

Du kanske inte ser alternativet för **växel erbjudandet** om:

* Du inte har en [prenumeration med prismodellen Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/). För närvarande kan endast prenumerationer med användningsbaserad betalning omvandlas till ett annat erbjudande.
  * Om du har en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) läser du hur du kan [uppgradera till Betala per användning](upgrade-azure-subscription.md).
  * [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du vill byta erbjudande från en annan prenumeration.
* Du är fortfarande på din första faktureringsperiod. Du måste vänta tills din första faktureringsperiod avslutats innan du kan byta erbjudande.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Varför får jag ett meddelande om att det inte finns några tillgängliga erbjudanden i min region eller mitt land?

* Du kanske inte har rätt att byta erbjudande. Kontrollera [listan över tillgängliga erbjudanden som du kan byta till](#whats-supported) och kontrollera att du har aktiverat rätt förmåner med Visual Studio eller BizSpark.
* Vissa erbjudanden är inte tillgängliga i alla länder/regioner.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Hur påverkas min tjänst och faktureringen om jag byter Azure-erbjudande?

Här följer information om vad som händer när du byter Azure-erbjudanden.

#### <a name="no-service-downtime"></a>Inget avbrott i tjänsten

Det förekommer inga tjänstavbrott för användare som är kopplade till prenumerationen. Det kan dock finnas begränsningar för det erbjudande som du byter till. Till exempel tillåts inte användning i produktionsmiljö med vissa erbjudanden, vilket betyder att produktionsresurser måste flyttas till en annan prenumeration.

#### <a name="quota-increases-are-reset"></a>Kvotökningar återställs

När du byter erbjudande återställs [gräns- eller kvotökningar som överskrider standardgränsen](../../azure-portal/supportability/resource-manager-core-quotas-request.md). Det förekommer inga tjänstavbrott, även om du har fler resurser än standardgränsen. Om du exempelvis har 200 kärnor i din prenumeration och sedan byter erbjudande, återställs din kvot till standardkvoten på 20 kärnor. De virtuella datorer som använder 200 kärnor påverkas inte och fortsätter att köras. Du måste dock begära en ny kvotökning för att kunna etablera fler kärnor.

#### <a name="billing"></a>Fakturering

Den dag du byter erbjudande genereras en faktura för alla utestående debiteringar. Därefter faktureras din prenumeration enligt prisvillkoren för det nya erbjudandet. Årsdagen för prenumerationens fakturering ändras till det datum då du bytte erbjudande. Användnings- och faktureringsdata före bytet av erbjudande sparas inte. Därför rekommenderar vi att du laddar ned en kopia innan du byter.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>Kan jag migrera från en prenumeration med prismodellen Betala per användning till Leverantör av molnlösningar (CSP) eller Enterprise-avtal (EA)?

* Om du vill migrera till en molnlösningsleverantör läser du [Överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer](transfer-subscriptions-subscribers-csp.md).
* Om du vill migrera till EA ber du din registreringsadministratör att lägga till ditt konto i EA. Följ anvisningarna i e-postinbjudan för att flytta dina prenumerationer under EA-registreringen.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Kan jag migrera data och tjänster till en ny prenumeration?

* Du kan migrera resurserna direkt till den nya prenumerationen. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Om du vill överföra ägarskapet för en Azure-prenumeration och allt innehåll i den till någon annan läser du [Överföra ägarskapet för en Azure-prenumeration](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](../costs/quick-acm-cost-analysis.md)
