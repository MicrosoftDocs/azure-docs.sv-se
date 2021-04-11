---
title: Migrera SSIS-paket till SQL-hanterad instans
titleSuffix: Azure Database Migration Service
description: Lär dig hur du migrerar SQL Server Integration Services-paket (SSIS) och projekt till en hanterad Azure SQL-instans med hjälp av Azure Database Migration Service eller Data Migration Assistant.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 1f6df64a66c0700f3a13a40ea5046515a2bf1a51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643848"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>Migrera SQL Server Integration Services-paket till en hanterad Azure SQL-instans
Om du använder SQL Server Integration Services (SSIS) och vill migrera dina SSIS-projekt/-paket från käll-SSISDB som är värd för SQL Server till målet SSISDB som finns i en hanterad Azure SQL-instans kan du använda Azure Database Migration Service.

Om den version av SSIS som du använder är tidigare än 2012 eller om du använder icke-SSISDB paket lagrings typer, innan du migrerar dina SSIS-projekt/-paket, måste du konvertera dem med hjälp av konverterings guiden för integration Services-projekt, som också kan startas från SSMS. Mer information finns i artikeln [konvertera projekt till projekt distributions modellen](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) stöder för närvarande inte Azure SQL Database som målplats för målet. Information om hur du distribuerar om SSIS-projekt/-paket till Azure SQL Database finns i artikeln [omdistribuera SQL Server Integration Services-paket till Azure SQL Database](./how-to-migrate-ssis-packages.md).

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Utvärdera käll SSIS projekt/paket.
> * Migrera SSIS-projekt/-paket till Azure.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra de här stegen:

* För att skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Mer information finns i artikeln [nätverks topologier för migrering av SQL-hanterade instanser med hjälp av Azure Database migration service]( https://aka.ms/dmsnetworkformi). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](../virtual-network/index.yml)och i synnerhet snabb starts artiklar med stegvisa anvisningar.
* För att se till att dina regler för nätverks säkerhets grupper i det virtuella nätverket inte blockerar den utgående porten 443 av ServiceTag för Service Bus, Storage och AzureMonitor. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurera Windows- [brandväggen för åtkomst till käll databas motor](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* För att öppna Windows-brandväggen så att Azure Database Migration Service får åtkomst till käll SQL Server, som standard är TCP-port 1433.
* Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
* Om du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering samt filer via SMB-port 445.
* En SQL-hanterad instans som värd för SSISDB. Om du behöver skapa ett följer du informationen i artikeln [skapa en hanterad Azure SQL-instans](../azure-sql/managed-instance/instance-create-quickstart.md).
* För att säkerställa att de inloggnings uppgifter som används för att ansluta till käll SQL Server och mål instansen är medlemmar i Server rollen sysadmin.
* För att verifiera att SSIS är etablerad i Azure Data Factory (ADF) som innehåller Azure-SSIS Integration Runtime (IR) med mål-SSISDB som hanteras av en SQL-hanterad instans (enligt beskrivningen i artikeln [skapar du Azure-SSIS integration runtime i Azure Data Factory](../data-factory/create-azure-ssis-integration-runtime.md)).

## <a name="assess-source-ssis-projectspackages"></a>Utvärdera käll SSIS projekt/paket

Även om utvärderingen av käll-SSISDB inte har integrerats i databasen Migration Assistant (DMA), kommer dina SSIS-projekt/-paket att bedömas/verifieras eftersom de distribueras om till mål SSISDB som finns på en Azure SQL-hanterad instans.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure Portal väljer du + **skapa en resurs**, söker efter **Azure Database migration service** och väljer sedan **Azure Database migration service** i list rutan.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt virtuellt nätverk eller skapa ett.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll SQL Server och mål Azure SQL-hanterad instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).

    Mer information finns i artikeln [nätverks topologier för migrering av Azure SQL-hanterad instans med hjälp av Azure Database migration service](./resource-network-topologies.md).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Skapa DMS-tjänst](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När en instans av tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta reda på alla instanser Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. På **Azure Database Migration Service-sidan** söker du efter namnet på instansen som du har skapat och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På skärmen **ny migrerings projekt** anger du ett namn för projektet i text rutan **typ av käll server** , väljer **SQL Server** i text rutan **mål server typ** , väljer **Azure SQL-hanterad instans** och väljer sedan **typ av aktivitet** för att välja **SSIS paket migrering**.

   ![Skapa DMS-projekt](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Välj **Skapa** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > TLS-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte förlita dig på TLS med självsignerade certifikat i en produktions miljö eller på servrar som är anslutna till Internet.

   ![Källinformation](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **information om migrerings mål** anger du anslutnings information för målet.

     ![Målinformation](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Välj **Spara**.

## <a name="review-the-migration-summary"></a>Granska migreringssammanfattningen

1. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

2. Ange om du vill skriva över eller ignorera befintliga SSIS-projekt och-miljöer för **SSIS-projekt och miljö (er) överskrivnings alternativ**.

    ![Sammanfattning av migreringsprojekt](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Granska och verifiera informationen som är kopplad till migreringsprojektet.

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

## <a name="next-steps"></a>Nästa steg

* Läs igenom vägledningen för migrering i Microsoft [Database migration guide](https://datamigration.microsoft.com/).