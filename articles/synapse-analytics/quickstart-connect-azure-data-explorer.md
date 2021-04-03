---
title: 'Snabb start: ansluta Azure Datautforskaren till en Azure Synapse Analytics-arbetsyta'
description: Anslut ett Azure Datautforskaren-kluster till en Azure Synapse Analytics-arbetsyta genom att använda Apache Spark för Azure Synapse Analytics.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: ee9d137973bfa4eeb28bc6526437e76e781f3199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92172269"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Ansluta till Azure Data Explorer med Apache Spark för Azure Synapse Analytics

Den här artikeln beskriver hur du får åtkomst till en Azure Datautforskaren-databas från Synapse Studio med Apache Spark för Azure Synapse Analytics.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa ett Azure datautforskaren-kluster och-databas](/azure/data-explorer/create-cluster-database-portal).
* Ha en befintlig Azure Synapse Analytics-arbetsyta eller skapa en ny arbets yta genom att följa stegen i [snabb start: skapa en Azure Synapse-arbetsyta](./quickstart-create-workspace.md).
* Ha en befintlig Apache Spark pool eller skapa en ny pool genom att följa stegen i [snabb start: skapa en Apache Spark pool med hjälp av Azure Portal](./quickstart-create-apache-spark-pool-portal.md).
* [Skapa en Azure Active Directory-app (Azure AD) genom att tillhandahålla ett Azure AD-program](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app).
* Ge din Azure AD-app åtkomst till din databas genom att följa stegen i [Hantera behörigheter för azure datautforskaren Database](/azure/data-explorer/manage-database-permissions).

## <a name="go-to-synapse-studio"></a>Gå till Synapse Studio

Från en Azure dataSynapses-arbetsyta väljer du **Starta Synapse Studio**. På Start sidan för Synapse Studio väljer du **data** för att gå till **data Object Explorer**.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Ansluta en Azure Datautforskaren-databas till en Azure Synapse-arbetsyta

Att ansluta en Azure Datautforskaren-databas till en arbets yta görs via en länkad tjänst. Med en länkad Azure Datautforskaren-tjänst kan du söka efter och utforska data, läsa och skriva från Apache Spark för Azure-Synapse. Du kan också köra integrations jobb i en pipeline.

Från data Object Explorer följer du stegen för att ansluta ett Azure Datautforskaren-kluster direkt:

1. Välj **+** ikonen nära **data**.
1. Välj **Anslut** för att ansluta till externa data.
1. Välj **Azure-datautforskaren (Kusto)**.
1. Välj **Fortsätt**.
1. Använd ett eget namn för att namnge den länkade tjänsten. Namnet kommer att visas i data Object Explorer och används av Azure Synapse-körningar för att ansluta till databasen.
1. Välj Azure Datautforskaren-kluster från din prenumeration eller ange URI: n.
1. Ange **tjänstens huvud namns-ID** och **tjänstens huvud nyckel**. Se till att tjänstens huvud namn har åtkomst till databasen för Läs-och Skriv behörighet för att mata in data.
1. Ange namnet på Azure Datautforskaren-databasen.
1. Välj **Testa anslutning** för att se till att du har rätt behörigheter.
1. Välj **Skapa**.

    ![Skärm bild som visar en ny länkad tjänst.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Valfritt **Test anslutning** verifierar inte skriv åtkomst. Se till att tjänstens huvud namns-ID har Skriv behörighet till Azure Datautforskaren-databasen.

1. Azure Datautforskaren kluster och databaser visas på fliken **länkad** under avsnittet **Azure datautforskaren** .

    ![Skärm bild som visar bläddring efter kluster.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE]
    > I den aktuella versionen fylls databas objekt i baserat på Azure AD-kontots behörigheter på Azure Datautforskaren-databaserna. När du kör Apache Spark antecknings böcker eller integrations jobb används autentiseringsuppgifterna i länk tjänsten (till exempel tjänstens huvud namn).

## <a name="quickly-interact-with-code-generated-actions"></a>Interagera snabbt med kod genererade åtgärder

När du högerklickar på en databas eller tabell visas en lista över exempel på Spark-anteckningsböcker. Välj ett alternativ för att läsa, skriva eller strömma data till Azure Datautforskaren.

[![Skärm bild som visar nya exempel antecknings böcker.](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

Här är ett exempel på att läsa data. Koppla antecknings boken till Spark-poolen och kör cellen.

[![Skärm bild som visar en ny Läs antecknings bok.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > Det kan ta mer än tre minuter att initiera Spark-sessionen. Efterföljande körningar blir betydligt snabbare.

## <a name="limitations"></a>Begränsningar

Azure Datautforskaren-anslutningen stöds för närvarande inte med Azure Synapse-hanterade virtuella nätverk.

## <a name="next-steps"></a>Nästa steg

* [Exempel kod med avancerade alternativ](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure Datautforskaren (Kusto) Spark-anslutning](https://github.com/Azure/azure-kusto-spark)