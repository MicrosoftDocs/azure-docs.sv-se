---
title: Begär ande kvot ökar och får support
description: Så här skapar du en support förfrågan i Azure Portal för Azure Synapse Analytics. Begär ande kvot ökar eller får support för problem matchning.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: gaursa
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b5b7563b49c6f0eb155223026797f8306e642f5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585663"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Begär ande kvot ökar och får support för Azure Synapse Analytics

Den här artikeln beskriver hur du skickar in ett support ärende i Azure Portal för Azure Synapse Analytics. Med den här processen kan du begära en kvot ökning eller skicka en begäran om teknisk support för support teamet för tekniska support.

## <a name="create-a-support-ticket"></a>Skapa ett supportärende

Använd följande steg för att skapa en ny supportbegäran från Azure Portal för Azure Synapse Analytics.

1. På [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**.

   ![Länken Hjälp + Support](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. I **Hjälp + Support** väljer du **ny supportbegäran**.

    ![Skapa en ny supportbegäran](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Granska [support avtalet för Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
   * **Reparations** support tillhandahålls via [utvecklare](https://azure.microsoft.com/support/plans/developer/), [standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)eller [Premier](https://azure.microsoft.com/support/plans/premier/) support. Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
   * **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) och [Premier](https://azure.microsoft.com/support/plans/premier/).

   Om du har ett Premier support-avtal kan du även rapportera problem med Azure Synapse Analytics på [Microsoft Premier Online-portalen](https://premier.microsoft.com/). Se [support](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) avtalen för Azure om du vill veta mer om olika support avtal, inklusive omfattning, svars tider, priser osv.  Vanliga frågor och svar om support för Azure finns i vanliga frågor och [svar om support för Azure](https://azure.microsoft.com/support/faq/).

1. För **typ av problem** väljer du lämplig typ av problem. För problem med att lösa problemet väljer du **teknisk**. För förfrågningar om ökad kvot väljer du **tjänst-och prenumerations gränser (kvoter)**.

   ![Välj en typ av problem](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Den här återstoden av den här artikeln fokuserar på kvot-öka förfrågningar. Men du kan också välja **teknisk** här för förfrågningar om problemlösnings support. Om du väljer **teknisk** uppmanas du att ange en sammanfattning och sedan identifiera en problem typ genom att välja **Välj typ av problem**. Du kan se lösningar som kan hjälpa dig att lösa problemet. Om lösningarna som presenteras inte löser problemet väljer du **Nästa: information** och fyller i formuläret för att skicka in support ärendet.

1. Välj **Azure Synapse Analytics** som **kvot typ** för förfrågningar om ökad kvot. Välj sedan **Nästa: lösningar >>**.

   ![Välj en kvot typ](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. I **informations** fönstret väljer du **Ange information** för att ange ytterligare information.

   ![Länken "Tillhandahåll information"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typer av kvot förfrågningar

Om du väljer **Ange information** visas fönstret med **kvot information** som gör att du kan lägga till ytterligare information. I följande avsnitt beskrivs de olika kvot begär Anden som är tillgängliga för Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Synapse SQL-pool, data lager enheter (DWU: er) per server

Använd följande steg för att begära en ökning av DWU: er per server.

1. Välj kvot typen **SYNAPSE SQL-poolens DWU: er per server** .

1. Välj den **resurs** som du vill tillämpa kvoten på genom att använda List rutan.

1. Ange din nya kvot i avsnittet **kvot för begäran** .

1. Välj **Spara och fortsätt**.

   ![Information om DWU-kvot](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Servrar per prenumeration

Om du vill begära en ökning av antalet servrar per prenumeration måste du utföra följande steg:

1. Välj **SQL-servrarna per prenumeration** som kvot typ.

1. I listan **plats** väljer du den Azure-region som du vill använda. Kvoten är per prenumeration i varje region.

1. I fältet **begär kvot** anger du din begäran om maximalt antal servrar i den regionen.

   ![Information om servrars kvot](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Välj **Spara och fortsätt**.

Vissa erbjudande typer är inte tillgängliga i varje region. Följande fel kan visas:

![Fel vid region åtkomst](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Aktivera prenumerations åtkomst till en region

Om du vill aktivera regions åtkomst för en prenumeration måste du utföra följande steg:  

1. Välj den **SYNAPSE SQL-pool (Data Warehouse) åtkomst** kvot typ för region.

1. Välj region genom att välja en **plats** i list rutan.

1. Ange DWU prestanda krav i avsnittet **DWU krävs** .

1. Ange din **Beskrivning av affärs krav**. 

1. Välj **Spara och fortsätt**.

![Regionåtkomst](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>För andra kvot begär Anden

Välj **annan kvot förfrågan** från den nedrullningsbara menyn kvot typ för andra typer av kvot begär Anden:

![Annan kvot information](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>Skicka din begäran

Det sista steget är att fylla i återstående information om din SQL Database support förfrågan. Välj sedan **Nästa: granska + skapa>>**.

![Granska skapa information](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

När du har granskat informationen om begäran väljer du **skapa** för att skicka begäran.

![Skapa biljett](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Övervaka ett supportärende

När du har skickat in support förfrågan kontaktar support teamet för Azure. Om du vill kontrol lera status och information för begäran väljer du **alla support förfrågningar** på instrument panelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Andra resurser

Du kan också ansluta till Azure Synapse Analytics-communityn på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) eller via webbplatsen [Microsoft Q&en fråga för Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html).