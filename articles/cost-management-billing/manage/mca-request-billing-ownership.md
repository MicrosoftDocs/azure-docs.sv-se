---
title: Överföra faktureringsägarskapet för en Azure-prenumeration för Microsoft-kundavtal
description: Lär dig hur du överför faktureringsägarskapet för Azure-prenumerationer.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/08/2021
ms.author: banders
ms.openlocfilehash: 4cfb5c0b746ab8cf4ebb26ee0f755b34b6289982
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481437"
---
# <a name="transfer-azure-subscription-billing-ownership-for-a-microsoft-customer-agreement"></a>Överföra faktureringsägarskapet för en Azure-prenumeration för Microsoft-kundavtal

Du vill kanske ta över ägarskapet för Azure-prenumerationer om den befintliga faktureringsägaren lämnar din organisation, eller när du vill betala för prenumerationerna via ditt faktureringskonto. Om du tar över ägarskapet överförs faktureringsansvar för prenumerationer till ditt konto.

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-for-access).

För att kunna begära faktureringsägarskap måste du vara **fakturaavsnittsägare** eller **fakturaavsnittsdeltagare**. Mer information finns i [uppgifter för roller i fakturaavsnitt](understand-mca-roles.md#invoice-section-roles-and-tasks).

Den användare som du begär faktureringsägarskap från måste ha någon av följande roller:

- För ett Microsoft-kundavtal för företag måste användaren vara faktureringsägare.
- För ett Enterprise-avtal måste användaren vara kontoägare.
- För ett Microsoft Online Service Program-faktureringskonto måste användaren vara kontoadministratör.

Mer information finns i [visa dina faktureringskonton i Azure-portalen](view-all-accounts.md).

## <a name="request-billing-ownership"></a>Begära faktureringsägarskap

1. Logga in på [Azure-portalen](https://portal.azure.com) som fakturaavsnittsägare eller -deltagare för ett faktureringskonto för Microsoft-kundavtal.
1. Sök efter **Kostnadshantering + fakturering**.  
    ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. På sidan för faktureringsomfång väljer du **Faktureringsomfattningar**. Välj sedan det faktureringskonto som ska användas för att betala för användningen av prenumerationerna. Faktureringskontot bör vara ett **Microsoft-kundavtal**.  
    [![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/mca-request-billing-ownership/list-of-scopes.png)](./media/mca-request-billing-ownership/list-of-scopes.png#lightbox)
    > [!NOTE]
    > Azure-portalen kommer ihåg det senaste faktureringsomfånget som du använder och visar omfånget nästa gång du kommer till sidan Kostnadshantering + fakturering. Sidan för faktureringsomfång visas inte om du har besökt sidan Kostnadshantering + fakturering. I så fall kontrollerar du att du befinner dig i [rätt omfång](#check-for-access). Annars [byter du omfång](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) och väljer faktureringskontot för ett Microsoft-kundavtal.
1. Välj **Faktureringsprofiler** till vänster.  
    [![Skärmbild som visar hur du väljer faktureringsprofiler](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
    > [!Note]
    > Om du inte ser Faktureringsprofiler beror det på att du inte är i rätt faktureringsomfång. Du måste välja ett faktureringskonto för ett Microsofts-kundavtal och sedan välja Faktureringsprofiler. Information om hur du byter omfång finns i avsnittet om hur du [byter faktureringsomfång på Azure-portalen](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).
1. Välj en **faktureringsprofil** i listan. När du tar över ägarskapet av prenumerationerna kommer deras användning att debiteras till den här faktureringsprofilen.
1. Välj **Fakturaavsnitt** på den vänstra sidan.  
    [![Skärmbild som visar val av fakturaavsnitt](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Välj ett fakturaavsnitt i listan. När du tar över ägarskapet av prenumerationerna tilldelas användningen till det här avsnittet i faktureringsprofilens faktura.
1. Välj **Överföringsbegäranden** nere till vänster och välj sedan **Lägg till en ny begäran**.  
    [![Skärmbild som visar val av överföringsbegäranden](./media/mca-request-billing-ownership/mca-select-transfer-requests.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests.png#lightbox)
1. Ange e-postadressen till den användare som du begär faktureringsägarskap från. Välj **Skicka överföringsbegäran**.  
    [![Skärmbild som visar sändning av en överföringsbegäran](./media/mca-request-billing-ownership/mca-send-transfer-requests.png)](./media/mca-request-billing-ownership/mca-send-transfer-requests.png#lightbox)
1. Användaren får ett e-postmeddelande med instruktioner för att granska din överföringsförfrågan.  
    ![Skärmbild som visar granskning av e-postmeddelande med överföringsbegäran](./media/mca-request-billing-ownership/mca-review-transfer-request-email.png)
1. För att godkänna överföringsbegäran väljer användaren länken i e-postmeddelandet och följer anvisningarna.
    [![Skärmbild som visar granskning av e-postmeddelande med överföringsbegäran](./media/mca-request-billing-ownership/review-transfer-requests.png)](./media/mca-request-billing-ownership/review-transfer-requests.png#lightbox) Användaren kan välja det faktureringskonto de vill överföra Azure-produkter från. När det har valts visas valbara produkter som kan överföras. **Obs!** Inaktiverade prenumerationer kan inte överföras och visas i listan ”Ej överföringsbara Azure-produkter” om tillämpligt. När du har valt de Azure-produkter som ska överföras väljer du **Validera**.
1. Området **Resultat av överföringvalidering** visar effekten på de Azure-produkter som ska överföras. Följande tillstånd är möjliga:
    * **Godkändes** – Valideringen för den här Azure-produkten har godkänts och kan överföras.
    * **Varning** – Det finns en varning för den markerade Azure-produkten. Produkten kan fortfarande överföras, men om det görs kan det ha en effekt som användaren behöver vara medveten om ifall användaren vill vidta åtgärder mot det. Till exempel kan Azure-prenumerationen som överförs dra fördel av en reserverad instans. Efter överföringen får prenumerationen inte längre den fördelen. Om du vill maximera besparingarna ser du till att den reserverade instansen associeras med en annan prenumeration som kan dra nytta av dess fördelar. Användaren kan också välja att gå tillbaka till sidan med prenumerationsval och avmarkera den här Azure-prenumerationen.
    * **Misslyckades** – Den markerade Azure-produkten kan inte överföras på grund av ett fel. Användaren måste gå tillbaka till sidan med prenumerationsval och avmarkera den här produkten för att överföra de andra markerade Azure-produkterna.  
    ![Skärmbild som visar valideringsmiljön](./media/mca-request-billing-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Kontrollera statusen för överföringsbegäran

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering + fakturering**.  
    ![Skärmbild som visar en sökning i Azure-portalen efter kostnadshantering och fakturering](./media/mca-request-billing-ownership/billing-search-cost-management-billing.png)
1. På sidan för faktureringsomfång väljer du det faktureringskonto som överföringsbegäran skickades för.
1. Välj **Faktureringsprofiler** till vänster.  
    [![Skärmbild som visar hur du väljer faktureringsprofiler](./media/mca-request-billing-ownership/mca-select-profiles.png)](./media/mca-request-billing-ownership/mca-select-profiles.png#lightbox)
1. Välj den **faktureringsprofil** som överföringsbegäran skickades för.
1. Välj **Fakturaavsnitt** på den vänstra sidan.  
    [![Skärmbild som visar val av fakturaavsnitt](./media/mca-request-billing-ownership/mca-select-invoice-sections.png)](./media/mca-request-billing-ownership/mca-select-invoice-sections.png#lightbox)   
1. Välj det fakturaavsnitt i listan som överföringsbegäran skickades för.
1. Välj **Överföringsbegäranden** på den nedre vänstra sidan. På sidan Överföringsbegäranden visas följande information:  
    [![Skärmbild som visar lista med överföringsbegäranden](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)](./media/mca-request-billing-ownership/mca-select-transfer-requests-for-status.png#lightbox)

   |Kolumn|Definition|
   |---------|---------|
   |Datum för begäran|Det datum då överföringsbegäran skickades|
   |Mottagare|E-postadressen för den användare till vilken du skickade begäran om att överföra faktureringsägarskapet|
   |Förfallodatum|Det datum då begäran upphör att gälla|
   |Status|Status för överföringsbegäran|

    Överföringsbegäran kan ha en av följande statusar:

   |Status|Definition|
   |---------|---------|
   |Pågår|Användaren har inte accepterat överföringsbegäran|
   |Bearbetar|Användaren har accepterat överföringsbegäran. Fakturering för prenumerationer som användaren valde överförs till ditt fakturaavsnitt|
   |Slutfört| Fakturering för prenumerationer som användaren valde har överförts till ditt fakturaavsnitt|
   |Slutförd med fel|Begäran slutfördes men faktureringen för vissa prenumerationer som användaren valde kunde inte överföras|
   |Upphörd|Användaren accepterade inte begäran i tid, och den upphörde|
   |Avbrutna|Någon med åtkomst till överföringsbegäran avbröt begäran|
   |Nekad|Användaren nekade överföringsbegäran|

1. Välj en överföringsbegäran för att visa information. På sidan för överföringsbegäran visas följande information:  
    [![Skärmbild som visar en lista med överförda prenumerationer](./media/mca-request-billing-ownership/mca-transfer-completed.png)](./media/mca-request-billing-ownership/mca-transfer-completed.png#lightbox)

   |Kolumn  |Definition|
   |---------|---------|
   |ID för överföringsbegäran|Unikt ID för din överföringsbegäran. Om du skickar en supportbegäran kan du uppge ID:t för Azure-supporten för att påskynda ärendet|
   |Överföringen begärdes den|Det datum då överföringsbegäran skickades|
   |Överföringen begärdes av|E-postadressen för den användare som skickade överföringsbegäran|
   |Överföringsbegäran upphör den| Det datum då överföringsbegäran upphör|
   |Mottagarens e-postadress|E-postadressen för den användare till vilken du skickade begäran om att överföra faktureringsägarskapet|
   |Överföringslänk som skickats till mottagaren|Den URL som skickades till användaren för granskning av överföringsbegäran|

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Du kan begära faktureringsägarskap för de prenumerationstyper som anges nedan.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Azure i öppen licensiering](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Sponsring av Azure-pass](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure-sponsrat erbjudande](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft-kundavtal](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise-prenumeranter (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise-prenumeranter (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional-prenumeranter](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Eventuell kredit som är tillgänglig i prenumerationen kommer inte att vara tillgänglig i det nya kontot efter överföringen.

\*\* Stöds endast för prenumerationer i konton som skapas vid registrering på Azure-webbplatsen.

## <a name="additional-information"></a>Ytterligare information

Följande avsnitt innehåller ytterligare information om överföring av prenumerationer.

### <a name="no-service-downtime"></a>Inget avbrott i tjänsten

Azure-tjänster i prenumerationen fortsätter att köras utan avbrott. Vi överför endast faktureringsrelationen för de Azure-prenumerationer som användaren väljer att överföra.

### <a name="disabled-subscriptions"></a>Inaktiverade prenumerationer

Inaktiverade prenumerationer kan inte överföras. Prenumerationer måste vara i aktivt tillstånd för att deras faktureringsägarskap ska överföras.

### <a name="azure-resources-transfer"></a>Överföring av Azure-resurser

Alla resurser från prenumerationer såsom virtuella datorer, diskar och webbplatser överförs.

### <a name="azure-marketplace-products-transfer"></a>Överföring av Azure Marketplace-produkter

Azure Marketplace-produkter överförs tillsammans med sina respektive prenumerationer.

### <a name="azure-reservations-transfer"></a>Överföring av Azure-reservationer

Om du överför EA-prenumerationer (Enterprise-avtal) eller Microsoft-kundavtal flyttas inte Azure-reservationerna automatiskt med prenumerationerna. [Kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att flytta reservationer.

### <a name="access-to-azure-services"></a>Åtkomst till Azure-tjänster

Åtkomst för befintliga användare, grupper eller tjänsthuvudnamn som tilldelades med hjälp av [rollbaserad åtkomstkontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) påverkas inte under övergången.

### <a name="azure-support-plan"></a>Azure-supportplan

Azure-support överförs inte med prenumerationerna. Om användaren överför alla Azure-prenumerationer ber du den att säga upp sin supportplan.

### <a name="charges-for-transferred-subscription"></a>Avgifter för överförd prenumeration

Den ursprungliga faktureringsägaren för prenumerationerna ansvarar för eventuella avgifter som rapporterades fram till den tidpunkt då överföringen slutfördes. Ditt fakturaavsnitt ansvarar för avgifter som rapporteras från och med tiden för överföring. Det kan finnas en vissa avgifter som ägde rum före överföringen men som rapporterades efteråt. De här avgifterna visas i ditt fakturaavsnitt.

### <a name="cancel-a-transfer-request"></a>Avbryta en överföringsbegäran

Du kan avbryta överföringsbegäran tills begäran har godkänts eller nekats. Om du vill avbryta överföringsbegäran går du till [sidan med överföringsinformation](#check-the-transfer-request-status) och väljer Avbryt längst ned på sidan.

### <a name="software-as-a-service-saas-transfer"></a>Överföring av SaaS (Software as a Service)

SaaS-produkter överförs inte med prenumerationerna. Be användaren att [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att överföra faktureringsägarskap för SaaS-produkter. Utöver faktureringsägarskapet kan användaren även överföra resursägarskapet. Med resursägarskap kan du utföra hanteringsåtgärder som att ta bort och visa information om produkten. Användaren måste vara resursägare för SaaS-produkten för att överföra resursägarskap.

## <a name="check-for-access"></a>Kontrollera åtkomst
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- Faktureringsägarskapet för Azure-prenumerationerna överförs till ditt fakturaavsnitt. Håll koll på avgifterna för dessa prenumerationer i [Azure-portalen](https://portal.azure.com).
- Ge andra behörigheter att visa och hantera fakturering för dessa prenumerationer. Mer information finns i [Roller och uppgifter för fakturaavsnitt](understand-mca-roles.md#invoice-section-roles-and-tasks).
