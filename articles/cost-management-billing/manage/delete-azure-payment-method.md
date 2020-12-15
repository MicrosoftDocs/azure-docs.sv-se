---
title: Ta bort en betalningsmetod för Azure-fakturering
description: Beskriver hur du tar bort en betalningsmetod som används av en Azure-prenumeration.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/07/2020
ms.author: banders
ms.openlocfilehash: 15d6c7731b541de638ceaf7828a7ce962cbf154a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827553"
---
# <a name="delete-an-azure-billing-payment-method"></a>Ta bort en betalningsmetod för fakturor i Azure

Det här dokumentet innehåller anvisningar som hjälper dig att ta bort en betalningsmetod, till exempel ett kreditkort, från olika typer av Azure-prenumerationer. Du kan ta bort en betalningsmetod för:

- Microsoft-kundavtal (MCA)
- Microsoft Online-prenumerationsprogrammet (MOSP) kallas även Betala per användning

Oavsett vilken typ av Azure-prenumeration du har måste du avbryta den så att du kan ta bort dess associerade betalningsmetod.

Att ta bort en betalningsmetod för andra typer av Azure-prenumerationer såsom Microsoft-partneravtal och Enterprise-avtal stöds inte.

## <a name="delete-an-mca-payment-method"></a>Ta bort en MCA-betalningsmetod

Endast den användare som skapade Microsoft-kundavtalskontot kan ta bort en betalningsmetod.

Om du vill ta bort en betalningsmetod för ett Microsoft-kundavtal utför du följande steg.

1. Logga in på Azure Portal på https://portal.azure.com/.
1. Gå till **Kostnadshantering + fakturering**.
1. Om det behövs väljer du ett faktureringsomfång.
1. I den vänstra menylistan under **Fakturering** väljer du **Faktureringsprofiler**.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Exempelskärmbild som visar Faktureringsprofiler i Azure-portalen" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. I listan över faktureringsprofiler väljer du den profil där betalningsmetoden används.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Exempelbild som visar listan över faktureringsprofiler" :::
1. I den vänstra menylistan, under **Inställningar**, väljer du **Betalningsmetoder**.
1. På sidan betalningsmetoder för din faktureringsprofil visas en tabell med betalningsmetoder under avsnittet **Dina kreditkort**. Leta upp det kreditkort som du vill ta bort, välj ellipsen ( **...** ) och välj sedan **Ta bort**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Exempel som visar var du tar bort ett kreditkort" :::
1. Sidan Ta bort en betalningsmetod visas. Azure kontrollerar om betalningsmetoden används.
    - När betalningsmetoden inte används är alternativet **Ta bort** aktiverat. Välj det för att ta bort kreditkortsinformationen.
    - Om betalningsmetoden används måste den ersättas eller kopplas från. Fortsätt att läsa följande avsnitt. Där förklaras hur du **kopplar från** den betalningsmetod som används av din prenumeration.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Koppla från betalningsmetod som används av en MCA-faktureringsprofil

Om din betalningsmetod används av en MCA-faktureringsprofil visas ett meddelande som liknar följande exempel.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Exempelbild som visar att en betalningsmetod används av ett Microsoft-kundavtal" :::

En lista över villkor måste uppfyllas för att du ska kunna koppla från en betalningsmetod. Om något villkor inte uppfylls visas anvisningar som förklarar hur du uppfyller villkoret. Det visas även en länk som leder till den plats där du kan lösa villkoret.

När alla villkor är uppfyllda kan du koppla från betalningsmetoden från faktureringsprofilen.

> [!NOTE]
> När standardbetalningsmetoden kopplas från placeras faktureringsprofilen i ett _inaktivt_ tillstånd. Saker som tas bort i den här processen kommer inte att kunna återställas. När en faktureringsprofil anges som inaktiv måste du registrera dig för en ny Azure-prenumeration för att kunna skapa nya resurser.

#### <a name="to-detach-a-payment-method"></a>Koppla från en betalningsmetod

1. I avsnittet Ta bort en betalningsmetod väljer du länken **Koppla från den aktuella betalningsmetoden**.
1. Om alla villkor är uppfyllda väljer du **Koppla från**. Annars fortsätter du till nästa steg.
1. Om Koppla från inte är tillgängligt visas en lista med villkor. Vidta de åtgärder som anges. Välj den länk som visas i fältet Koppla bort standardbetalningsmetod. Här är ett exempel på en korrigeringsåtgärd som förklarar de åtgärder du behöver vidta.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Exempel som visar en korrigeringsåtgärd som krävs för att koppla från en betalningsmetod för MCA" :::
1. När du väljer länken för korrigeringsåtgärden omdirigeras du till den Azure-sida där du vidtar åtgärder. Vidta den korrigeringsåtgärd som behövs.
1. Vid behov slutför du alla andra korrigeringsåtgärder.
1. Gå tillbaka till **Kostnadshantering + fakturering** > **Faktureringsprofiler** > **Betalningsmetoder**. Välj **Koppla från**. Längst ned på sidan Koppla från standardbetalningsmetod väljer du **Koppla från**.

> [!NOTE]
> - När du har avbrutit en prenumeration kan det ta upp till 90 dagar innan prenumerationen tas bort. Om du vill förkorta väntetiden öppnar du en Azure-supportbegäran och ber om att prenumerationen ska tas bort omedelbart.
> - Du kan bara ta bort en betalningsmetod när alla tidigare avgifter för en faktureringsprofil har kvittats. Om du befinner dig i en aktiv faktureringsperiod måste du vänta till slutet av faktureringsperioden för att ta bort betalningsmetoden. **Se till att alla andra frånkopplingsvillkor uppfylls medan du väntar på att faktureringsperioden ska ta slut**.

## <a name="delete-a-mosp-payment-method"></a>Ta bort en MOSP-betalningsmetod

Du måste vara kontoadministratör för att kunna ta bort en betalningsmetod.

Om betalningsmetoden används av en MOSP-prenumeration utför du följande steg.

1. Logga in på Azure Portal på https://portal.azure.com/.
1. Gå till **Kostnadshantering + fakturering**.
1. Om det behövs väljer du ett faktureringsomfång.
1. I den vänstra menylistan under **Fakturering** väljer du **Betalningsmetoder**.
1. I avsnittet Betalningsmetoder väljer du den _rad_ som din betalningsmetod finns på. Välj inte länken för betalningsmetod. Det finns kanske inte någon visuell indikation på att du har valt betalningsmetoden.
1. Välj **Ta bort**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Exempel som visar en korrigeringsåtgärd som krävs för att koppla från en betalningsmetod för MOSP" :::
1. I avsnittet Ta bort en betalningsmetod väljer du **Ta bort** om alla villkor är uppfyllda. Om Ta bort inte är tillgängligt fortsätter du till nästa steg.
1. En lista med villkor visas. Vidta de åtgärder som anges. Välj den länk som visas i fältet Ta bort en betalningsmetod.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Exempelbild som visar att en betalningsmetod används av en MOSP-prenumeration" :::
1. När du väljer länken för korrigeringsåtgärden omdirigeras du till den Azure-sida där du vidtar åtgärder. Vidta den korrigeringsåtgärd som behövs.
1. Vid behov slutför du alla andra korrigeringsåtgärder.
1. Gå tillbaka till **Kostnadshantering + fakturering** > **Faktureringsprofiler** > **Betalningsmetoder** och ta bort betalningsmetoden.

> [!NOTE]
> När du har avbrutit en prenumeration kan det ta upp till 90 dagar innan prenumerationen tas bort. Om du vill förkorta väntetiden öppnar du en Azure-supportbegäran och ber om att prenumerationen ska tas bort omedelbart.

## <a name="next-steps"></a>Nästa steg

- Om du behöver mer information om hur du avbryter din Azure-prenumeration kan du läsa [Avbryta din Azure-prenumeration](cancel-azure-subscription.md).
- Mer information om hur du lägger till eller uppdaterar ett kreditkort finns i [Lägga till eller uppdatera ett kreditkort för Azure](change-credit-card.md).