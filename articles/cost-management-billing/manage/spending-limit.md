---
title: Utgiftsgräns i Azure
description: I den här artikeln beskrivs hur en utgiftsgräns för Azure fungerar samt hur du tar bort den.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.author: banders
ms.openlocfilehash: 47f1a1ed182420852682e908c7cd8b5e094bc085
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932564"
---
# <a name="azure-spending-limit"></a>Utgiftsgräns i Azure

Utgiftsgränsen i Azure gör att du inte kan spendera mer än ditt kreditbelopp. Alla nya kunder som registrerar sig för ett kostnadsfritt Azure-konto eller prenumerationer som innefattar krediter under flera månader har utgiftsgränsen aktiverad som standard. Utgifts gränsen är lika med kredit beloppet. Du kan inte ändra utgifts gränsen. Om du till exempel har registrerat dig för ett kostnadsfritt Azure-konto är din utgiftsgräns 200 USD och du kan inte ändra den till 500 USD. Dock kan du när som helst ta bort utgiftsgränsen. Så du har antingen ingen gräns eller så har du en gräns som motsvarar kreditbeloppet. Det här spärrar dig från de flesta typer av utgifter. Utgiftsgränsen är inte tillgänglig för prenumerationer med åtagandeplaner eller prissättning enligt Betala per användning. Mer information finns i den [fullständiga listan med typer av Azure-prenumerationer och tillgänglighet för utgiftsgränsen](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Uppnå en utgiftsgräns

När din användning leder till avgifter som överstiger utgiftsgränsen så inaktiveras de tjänster du har distribuerat under resten av faktureringsperioden.

När du till exempel spenderar all kredit som ingår i ett kostnadsfritt Azure-konto tas de Azure-resurser du har distribuerat bort från produktion och dina virtuella Azure-datorer stoppas och avallokeras. Data i dina lagringskonton är tillgängliga som skrivskyddade.

Om din prenumerationstyp omfattar krediter under flera månader aktiveras prenumerationen igen automatiskt i början av nästa faktureringsperiod. Sedan kan du distribuera om dina Azure-resurser och få fullständig åtkomst till dina lagringskonton och databaser.

Azure skickar e-postaviseringar när du når utgiftsgränsen. Logga in i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) för att se aviseringar om prenumerationer som har nått utgiftsgränsen.

Om du har ett kostnadsfritt Azure-konto och når utgiftsgränsen kan du uppgradera till att [betala per användning](upgrade-azure-subscription.md) för att ta bort utgiftsgränsen och automatiskt aktivera prenumerationen på nytt.

## <a name="remove-the-spending-limit-in-azure-portal"></a>Ta bort utgiftsgränsen i Azure-portalen

Du kan ta bort utgiftsgränsen när som helst så länge det finns en giltig betalningsmetod som är associerad med din Azure-prenumeration. För prenumerationstyper som har kredit under flera månader, som Visual Studio Enterprise och Visual Studio Professional, kan du välja att ta bort utgiftsgränsen på obestämd tid eller bara för den aktuella faktureringsperioden. Om du väljer endast för den aktuella faktureringsperioden aktiveras utgiftsgränsen automatiskt i början av nästa faktureringsperiod.

Om du har ett kostnadsfritt Azure-konto kan du läsa mer om att ta bort utgiftsgränsen i [Uppgradera din Azure-prenumeration](upgrade-azure-subscription.md). Annars kan du ta bort utgiftsgränsen på följande sätt:

<a id="remove"></a>

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar en sökning efter kostnadshantering och fakturering ](./media/spending-limit/search-bar.png)

1. I listan **Mina prenumerationer** väljer du din prenumeration. Till exempel *Visual Studio Enterprise*.

   ![Skärmbild som visar Mina prenumerationer-rutnätet i översikt](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Om du inte ser några av dina Visual Studio-prenumerationer här, kan det bero på att du har ändrat prenumerationskatalogen vid något tillfälle. För dessa prenumerationer måste du ändra katalog till den ursprungliga katalogen (katalogen där du registrerade dig). Upprepa sedan steg 2.

1. I prenumerationsöversikten klickar du på den orangefärgade banderollen för att ta bort utgiftsgränsen.

    ![Skärmbild som visar banderollen Ta bort utgiftsgräns](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. Välj om du vill ta bort utgiftsgränsen på obestämd tid eller bara för den aktuella faktureringsperioden.

      ![Skärmbild som visar bladet Ta bort utgiftsgräns](./media/spending-limit/remove-spending-limit-blade-x.png)

      | Alternativ | Effekt |
      | --- | --- |
      | Ta bort utgiftsgränsen på obestämd tid | Utgiftsgränsen aktiveras inte automatiskt igen vid början av nästa faktureringsperiod. Men du kan aktivera den igen när du vill. |
      | Ta bort utgiftsgränsen för den aktuella faktureringsperioden | Utgiftsgränsen aktiveras automatiskt igen vid början av nästa faktureringsperiod. |


1. Klicka på **Välj betalningsmetod** för att välja en betalningsmetod för prenumerationen. Det blir den aktiva betalningsmetoden för din prenumeration.

1. Klicka på **Finish**.


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Därför bör du kanske ta bort utgiftsgränsen

Utgiftsgränsen kan hindra dig från att distribuera eller använda vissa tjänster från tredje part och Microsoft-tjänster. Här följer några situationer där du bör ta bort utgiftsgränsen för din prenumeration.

-  Du planerar att distribuera avbildningar från tredje part som Oracle, eller tjänster som Azure DevOps Services. Den här situationen gör att du når upp till utgiftsgränsen nästan omedelbart och din prenumeration inaktiveras.
- Du har tjänster som du inte vill ska störas. När du når upp till utgiftsgränsen tas de Azure-resurser du har distribuerat bort från produktionen och dina virtuella Azure-datorer stoppas och avallokeras. Om du har tjänster där du vill undvika avbrott måste du ta bort utgiftsgränsen.
- Du har tjänster och resurser med inställningar, till exempel virtuella IP-adresser, som du inte vill förlora. Dessa inställningar förloras när du når upp till utgiftsgränsen och tjänsterna och resurserna avallokeras.

## <a name="turn-on-the-spending-limit-after-removing"></a>Aktivera utgiftsgränsen efter borttagning

Den här funktionen är bara tillgänglig när du har tagit bort utgiftsgränsen tills vidare för prenumerationstyper med krediter under flera månader. Du kan använda den här funktionen till att aktivera utgiftsgränsen automatiskt i början av nästa faktureringsperiod.


1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar en sökning efter kostnadshantering och fakturering ](./media/spending-limit/search-bar.png)

1. I listan **Mina prenumerationer** väljer du din prenumeration. Till exempel *Visual Studio Enterprise*.

   ![Skärmbild som visar Mina prenumerationer-rutnätet i översikt](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Om du inte ser några av dina Visual Studio-prenumerationer här, kan det bero på att du har ändrat prenumerationskatalogen vid något tillfälle. För dessa prenumerationer måste du ändra katalog till den ursprungliga katalogen (katalogen där du registrerade dig). Upprepa sedan steg 2.

1. I prenumerationsöversikten klickar du på banderollen längst upp på sidan för att aktivera utgiftsgränsen igen.

## <a name="custom-spending-limit"></a>Anpassad utgiftsgräns

Anpassade utgiftsgränser är inte tillgängliga.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>En utgiftsgräns förhindrar inte alla typer av utgifter

[Vissa externa tjänster som publicerats på Azure Marketplace](../understand/understand-azure-marketplace-charges.md) kan inte användas med dina prenumerationskrediter, och kan medföra separata avgifter även när din utgiftsgräns har angetts. Exempel är Visual Studio-licenser, Azure Active Directory Premium, supportavtal och de flesta varumärkestjänster från tredje part. När du etablerar en ny extern tjänst visas en varning om att tjänsterna faktureras separat:

![Varning om inköp på Marketplace](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Uppgradera till en plan med [Betala per användning-priser](upgrade-azure-subscription.md).
