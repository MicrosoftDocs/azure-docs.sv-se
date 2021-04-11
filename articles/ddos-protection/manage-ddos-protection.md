---
title: Hantera Azure DDoS Protection standard med hjälp av Azure Portal
description: Lär dig hur du använder Azure DDoS Protection standard för att minimera ett angrepp.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: b38f2831136b0ccec4aa241135f2fd342c939845
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936894"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Snabb start: skapa och konfigurera Azure DDoS Protection standard

Kom igång med Azure DDoS Protection standard med hjälp av Azure Portal. 

En DDoS skydds plan definierar en uppsättning virtuella nätverk som har DDoS Protection standard aktiverat, mellan prenumerationer. Du kan konfigurera en DDoS skydds plan för din organisation och länka virtuella nätverk från flera prenumerationer till samma plan. 

I den här snabb starten skapar du en DDoS skydds plan och länkar den till ett virtuellt nätverk. 

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Logga in på Azure Portal på https://portal.azure.com. Se till att ditt konto har tilldelats rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder i instruktionen instruktions [Guide.](manage-permissions.md)

## <a name="create-a-ddos-protection-plan"></a>Skapa en DDoS skydds plan

1. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal.
2. Sök på termen *DDoS*. När **DDoS Protection Plan** visas i Sök resultaten väljer du den.
3. Välj **Skapa**.
4. Ange eller Välj följande värden och välj sedan **skapa**:

    |Inställning        |Värde                                              |
    |---------      |---------                                          |
    |Namn           | Ange _MyDdosProtectionPlan_.                     |
    |Prenumeration   | Välj din prenumeration.                         |
    |Resursgrupp | Välj **Skapa ny** och ange _MyResourceGroup_.|
    |Location       | Ange _USA, östra_.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Aktivera DDoS-skydd för ett virtuellt nätverk

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Aktivera DDoS-skydd för ett nytt virtuellt nätverk

1. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Ange eller Välj följande värden, acceptera återstående standardvärden och välj sedan **skapa**:

    | Inställning         | Värde                                           |
    | ---------       | ---------                                       |
    | Namn            | Ange _MyVnet_.                                 |
    | Prenumeration    | Välj din prenumeration.                                    |
    | Resursgrupp  | Välj **Använd befintlig** och välj sedan **MyResourceGroup** |
    | Location        | Ange _USA, östra_                                                    |
    | DDoS Protection Standard | Välj **Aktivera**. Den plan du väljer kan vara i samma eller en annan prenumeration än det virtuella nätverket, men båda prenumerationerna måste vara kopplade till samma Azure Active Directory-klient.|

Du kan inte flytta ett virtuellt nätverk till en annan resurs grupp eller prenumeration när DDoS standard är aktive rad för det virtuella nätverket. Om du behöver flytta ett virtuellt nätverk med DDoS standard aktiverat inaktiverar du DDoS standard först, flyttar det virtuella nätverket och aktiverar sedan DDoS standard. Efter flyttningen återställs automatiskt justerade princip tröskelvärden för alla skyddade offentliga IP-adresser i det virtuella nätverket.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Aktivera DDoS-skydd för ett befintligt virtuellt nätverk

1. Skapa en DDoS skydds plan genom att slutföra stegen i [skapa en DDoS-skydds plan](#create-a-ddos-protection-plan), om du inte har en befintlig DDoS Protection-plan.
2. Ange namnet på det virtuella nätverk som du vill aktivera DDoS Protection standard för i **rutan Sök efter resurser, tjänster och dokument** högst upp i Azure Portal. När namnet på det virtuella nätverket visas i Sök resultatet väljer du det.
3. Välj **DDoS-skydd** under **Inställningar**.
4. Välj **standard**. Under **DDoS Protection Plan** väljer du en befintlig DDoS-skydds plan eller den plan som du skapade i steg 1 och väljer sedan **Spara**. Den plan du väljer kan vara i samma eller en annan prenumeration än det virtuella nätverket, men båda prenumerationerna måste vara kopplade till samma Azure Active Directory-klient.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>Aktivera DDoS-skydd för alla virtuella nätverk

Den här [principen](https://aka.ms/ddosvnetpolicy) identifierar virtuella nätverk i en definierad omfattning som inte har DDoS Protection standard aktiverat och du kan också skapa en reparations aktivitet som skapar associationen för att skydda det virtuella nätverket. Detaljerade steg-för-steg-instruktioner om hur du distribuerar den här principen finns i https://aka.ms/ddosvnetpolicy-techcommunity .

## <a name="validate-and-test"></a>Validera och testa

Börja med att kontrol lera informationen om din DDoS-skydds plan:

1. Välj **Alla tjänster** längst upp till vänster i portalen.
2. Ange *DDoS* i **filter** rutan. När **DDoS skydds planer** visas i resultaten väljer du det.
3. Välj din DDoS-skydds plan från listan.

Det virtuella _MyVnet_ -nätverket ska visas. 

### <a name="view-protected-resources"></a>Visa skyddade resurser
Under **skyddade resurser** kan du Visa dina skyddade virtuella nätverk och offentliga IP-adresser eller lägga till fler virtuella nätverk i DDoS-skydds planen:

![Visa skyddade resurser](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina resurser för nästa självstudie. Om du inte längre behöver tar du bort resurs gruppen _MyResourceGroup_ . När du tar bort resurs gruppen tar du även bort DDoS-skydds planen och alla relaterade resurser. Om du inte tänker använda den här DDoS skydds planen bör du ta bort resurser för att undvika onödiga kostnader.

   >[!WARNING]
   >Den här åtgärden är irreversable.

1. I Azure Portal kan du söka efter och välja **resurs grupper** eller välja **resurs grupper** på Azure Portal-menyn.

2. Filtrera eller rulla nedåt för att hitta resurs gruppen _MyResourceGroup_ .

3. Välj resurs gruppen och välj sedan **ta bort resurs grupp**.

4. Skriv namnet på den resurs grupp som ska verifieras och välj sedan **ta bort**.

Så här inaktiverar du DDoS-skydd för ett virtuellt nätverk: 

1. Ange namnet på det virtuella nätverk som du vill inaktivera DDoS-skydds standard för i **rutan Sök efter resurser, tjänster och dokument** överst i portalen. När namnet på det virtuella nätverket visas i Sök resultatet väljer du det.
2. Under **DDoS Protection standard** väljer du **inaktivera**.

Om du vill ta bort en DDoS skydds plan måste du först ta bort alla virtuella nätverk från den. 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig att visa och konfigurera telemetri för din DDoS-skydds plan fortsätter du till självstudierna.

> [!div class="nextstepaction"]
> [Visa och konfigurera telemetri för DDoS-skydd](telemetry.md)
