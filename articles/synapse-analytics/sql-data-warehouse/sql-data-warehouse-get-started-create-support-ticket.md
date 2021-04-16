---
title: Begära kvotökningar och få support
description: Så här skapar du en supportbegäran i Azure Portal för Azure Synapse Analytics. Begär kvotökningar eller få support för problemlösning.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: julieMSFT
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5256c6d25a9c7acfc45cdffee05c95fb3407c24a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568326"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Begär kvotökningar och få support för Azure Synapse Analytics

Den här artikeln beskriver hur du skickar en supportbiljett i Azure Portal för Azure Synapse Analytics. Med den här processen kan du begära en kvotökning eller skicka en teknisk supportbegäran till det tekniska supportteamet.

## <a name="create-a-support-ticket"></a>Skapa ett supportärende

Använd följande steg för att skapa en ny supportbegäran från Azure Portal för Azure Synapse Analytics.

1. På menyn [Azure Portal](https://portal.azure.com) väljer du **Hjälp + support.**

   ![Hjälp + support-länken](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. I **Hjälp + support** väljer du Ny **supportbegäran.**

    ![Skapa en ny supportbegäran](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Granska din [Azure-supportplan.](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)

   * Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
   * **Support för korrigering** tillhandahålls via [Developer,](https://azure.microsoft.com/support/plans/developer/) [Standard,](https://azure.microsoft.com/support/plans/standard/) [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)eller [Premier](https://azure.microsoft.com/support/plans/premier/) Support. Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
   * **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) och [Premier](https://azure.microsoft.com/support/plans/premier/).

   Om du har en Premier Support-plan kan du även rapportera Azure Synapse Analytics på [Microsoft Premier-onlineportalen.](https://premier.microsoft.com/) I [Azure-supportplaner](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) kan du läsa mer om de olika supportplanerna, inklusive omfattning, svarstider, priser osv.  Vanliga frågor och svar om Azure-support finns i [Vanliga frågor och svar om Azure-support.](https://azure.microsoft.com/support/faq/)

1. För **Typ av** problem väljer du lämplig typ av problem. Välj Teknisk för att lösa **problem.** För begäranden om kvotökning väljer du **Tjänst- och prenumerationsgränser (kvoter).**

   ![Välj en problemtyp](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Den här resten av den här artikeln fokuserar på begäranden om kvotökning. Men du kan också välja **Teknisk här** för supportförfrågningar om problemlösning. Om du väljer **Teknisk** uppmanas du att ange en sammanfattning och sedan identifiera en problemtyp genom att välja **Välj problemtyp.** Du kan se lösningar som hjälper dig att lösa problemet. Om lösningarna som visas inte löser problemet väljer du **Nästa:Information** och fyller i formuläret för att skicka supportbiljetten.

1. För begäranden om kvotökning väljer **Azure Synapse Analytics** som **Kvottyp.** Välj sedan **Nästa: Lösningar >>**.

   ![Välj en kvottyp](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. I fönstret **Information** väljer du Ange **information för** att ange ytterligare information.

   ![Länken "Ange information"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typer av kvotbegäran

Om **du väljer Ange** information visas fönstret **Kvotinformation** där du kan lägga till ytterligare information. I följande avsnitt beskrivs de olika kvotbegäranden som är tillgängliga för Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Synapse SQL för Data Warehouse (DWUs) per server

Använd följande steg för att begära en ökning av DWUs per server.

1. Välj den **Synapse SQL DWUs per** serverkvottyp.

1. Välj den **resurs** som du vill tillämpa kvotökningen på med hjälp av listrutan.

1. Ange din nya kvot i **avsnittet Förfrågningskvot.**

1. Välj **Spara och fortsätt**.

   ![Information om DWU-kvot](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Servrar per prenumeration

Om du vill begära en ökning av antalet servrar per prenumeration måste du utföra följande steg:

1. Välj **SQL-servrar per prenumeration** som kvottyp.

1. I listan **Plats** väljer du den Azure-region som ska användas. Kvoten är per prenumeration i varje region.

1. I fältet **Begärandekvot** anger du din begäran om det maximala antalet servrar i den regionen.

   ![Information om servrars kvot](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Välj **Spara och fortsätt**.

Vissa typer av erbjudanden är inte tillgängliga i varje region. Du kan se följande fel:

![Fel vid regionsåtkomst](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Aktivera prenumerationsåtkomst till en region

Om du vill aktivera regionsåtkomst för en prenumeration måste du utföra följande steg:  

1. Välj **åtkomstkvottypen Synapse SQL (informationslager)** för poolen (informationslagret).

1. Välj region genom att välja **en** Plats i listrutan.

1. Ange ditt DWU-prestandakrav i **avsnittet DWU obligatoriskt.**

1. Ange **beskrivningen av affärskraven.** 

1. Välj **Spara och fortsätt**.

![Regionåtkomst](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>För andra kvotbegäranden

Välj **Annan kvotbegäran** i listrutan kvottyp för andra typer av kvotbegäran:

![Annan kvotinformation](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>Skicka din begäran

Det sista steget är att fylla i återstående information om din SQL Database supportbegäran. Välj sedan **Nästa: Granska + skapa>>**.

![Granska informationen om att skapa](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

När du har granskat begärandeinformationen väljer **du Skapa** för att skicka begäran.

![Skapa ärende](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Övervaka ett supportärende

När du har skickat supportbegäran kontaktar Azure-supportteamet dig. Om du vill kontrollera status och information om din begäran väljer **du Alla supportbegäranden** på instrumentpanelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Andra resurser

Du kan också ansluta till Azure Synapse Analytics communityn [på Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) eller via Microsoft Q&A question page [for Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html).