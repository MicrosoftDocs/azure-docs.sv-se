---
title: Konfigurera Azure-SSIS integration runtime för verksamhets kontinuitet och haveri beredskap (BCDR)
description: Den här artikeln beskriver hur du konfigurerar Azure-SSIS integration runtime i Azure Data Factory med Azure SQL Database/Hanterad instans redundans grupp för affärs kontinuitet och haveri beredskap (BCDR).
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: 2744d51b6d68ed494050be10a9f0e4d1f59cdc49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204073"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Konfigurera Azure-SSIS integration runtime för verksamhets kontinuitet och haveri beredskap (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure SQL Database/Hanterad instans och SQL Server Integration Services (SSIS) i Azure Data Factory (ADF) kan kombineras som den rekommenderade lösningen all-Platform as a Service (PaaS) för SQL Server migrering. Du kan distribuera dina SSIS-projekt till SSIS Catalog-databasen (SSISDB) som hanteras av Azure SQL Database/Hanterad instans och köra dina SSIS-paket på Azure SSIS integration Runtime (IR) i ADF.

För verksamhets kontinuitet och haveri beredskap (BCDR) kan Azure SQL Database/Hanterad instans konfigureras med en [grupp för geo-replikering/redundans](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)där SSISDB i en primär Azure-region med Läs-och skriv åtkomst (primär roll) kontinuerligt replikeras till en sekundär region med skrivskyddad åtkomst (sekundär roll). När en katastrof uppstår i den primära regionen utlöses en redundansväxling, där den primära och sekundära SSISDBs kommer att byta roll.

För BCDR kan du också konfigurera ett dubbelt vänte läge för Azure SSIS-IR som fungerar i Sync med Azure SQL Database/Hanterad instans redundans grupp. På så sätt kan du ha ett par med att köra Azure-SSIS IRs som vid en specifik tidpunkt bara kan komma åt den primära SSISDB för att hämta och köra paket, samt köra loggar för Skriv paket (primär roll), medan den andra bara kan göra samma för paket som distribuerats någon annan stans, till exempel i Azure Files (sekundär roll). När SSISDB-redundans inträffar, kommer den primära och sekundära Azure-SSIS-IRs också att växla roller och om båda körs är det en stillestånds tid på nästan noll.

I den här artikeln beskrivs hur du konfigurerar Azure-SSIS IR med gruppen Azure SQL Database/Hanterad instans redundans för BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Konfigurera ett dubbelt standby Azure-SSIS IR-par med Azure SQL Database redundans

Om du vill konfigurera ett dubbelt standby Azure-SSIS IR-par som fungerar i synkronisera med Azure SQL Database redundans, utför du följande steg.

1. Med hjälp av Azure Portal/ADF UI kan du skapa en ny Azure-SSIS IR med din primära Azure SQL Database-Server som värd för SSISDB i den primära regionen. Om du har en befintlig Azure-SSIS IR som redan är kopplad till SSIDB som finns på den primära Azure SQL Database-servern och den fortfarande körs, måste du först stoppa den för att konfigurera om den. Detta är din primära Azure-SSIS IR.

   När [du väljer att använda SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) på sidan **distributions inställningar** i installations fönstret för **integration runtime** , markerar du kryss rutan **Använd dubbla vänte läge Azure-SSIS integration runtime par med SSISDB redundans** . Ange ett namn för att identifiera ditt par av primär och sekundär Azure-SSIS IRs för **dubbla standby-par**. När du har skapat den primära Azure-SSIS IR startas den och ansluts till en primär SSISDB som skapas för din räkning med Läs-och Skriv behörighet. Om du precis har konfigurerat om den måste du starta om den.

1. Med hjälp av Azure Portal kan du kontrol lera om den primära SSISDB har skapats på **översikts** sidan för den primära Azure SQL Database servern. När den har skapats kan du [skapa en failover-grupp för dina primära och sekundära Azure SQL Database-servrar och lägga till SSISDB till den](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#2---create-the-failover-group) på sidan **grupper för växling vid fel** . När du har skapat redundansväxlingen kan du kontrol lera om den primära SSISDB har repliker ATS till en sekundär med skrivskyddad åtkomst på sidan **Översikt** på den sekundära Azure SQL Database servern.

1. Med hjälp av Azure Portal/ADF UI kan du skapa en annan Azure-SSIS IR med din sekundära Azure SQL Database-Server som värd för SSISDB i den sekundära regionen. Detta är din sekundära Azure-SSIS IR. För fullständig BCDR, se till att alla resurser som den är beroende av också skapas i den sekundära regionen, till exempel Azure Storage för att lagra anpassade installations skript/-filer, ADF för Orchestration/schema körningar osv.

   När [du väljer att använda SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) på sidan **distributions inställningar** i installations fönstret för **integration runtime** , markerar du kryss rutan **Använd dubbla vänte läge Azure-SSIS integration runtime par med SSISDB redundans** . För **dubbla standby-par** anger du samma namn för att identifiera ditt par av primär och sekundär Azure-SSIS IRS. När du har skapat den sekundära Azure-SSIS IR kommer den att startas och kopplas till den sekundära SSISDB.

1. Om du vill ha en avbrotts tid som är nästan noll när SSISDB-redundansväxlingen sker, behåller du båda Azure-SSIS-IRs som körs. Endast din primära Azure-SSIS IR kan komma åt den primära SSISDB för att hämta och köra paket, samt körnings loggar för Skriv paket, medan den sekundära Azure-SSIS IR bara kan göra samma för paket som distribuerats någon annan stans, till exempel i Azure Files.

   Om du vill minimera din löpande kostnad kan du stoppa den sekundära Azure-SSIS IR när den har skapats. När SSISDB-redundansväxlingen inträffar byter ditt primära och sekundära Azure-SSIS-IRs roller. Om din primära Azure-SSIS IR stoppas måste du starta om den. Beroende på om den har matats in i ett virtuellt nätverk och den inmatnings metod som används, tar det inom 5 minuter eller cirka 20-30 minuter innan den kan köras.

1. Om du [använder ADF för Orchestration/schema körningar](./how-to-invoke-ssis-package-ssis-activity.md), se till att alla relevanta ADF-pipelines med kör SSIS-paket-aktiviteter och associerade utlösare kopieras till din sekundära ADF med utlösarna inaktiverade initialt. När SSISDB-redundansväxlingen inträffar måste du aktivera dem.

1. Du kan [testa Azure SQL Database redundans-gruppen](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#3---test-failover) och kontrol lera [Azure-SSIS IR övervaknings sidan på ADF-portalen](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) om ditt primära och sekundära Azure-SSIS-IRS har växlat roller. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Konfigurera ett dubbelt standby Azure-SSIS IR-par med en failover-grupp för Azure SQL-hanterad instans

Följ stegen nedan om du vill konfigurera ett dubbelt standby Azure-SSIS IR-par som fungerar med synkronisering med en failover-grupp för Azure SQL-hanterad instans.

1. Med hjälp av Azure Portal kan du [skapa en grupp för växling vid fel för dina primära och sekundära Azure SQL-hanterade instanser](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal) på sidan **grupper** i den primära Azure SQL-hanterade instansen.

1. Med hjälp av Azure Portal/ADF UI kan du skapa en ny Azure-SSIS IR med din primära Azure SQL-hanterade instans som värd för SSISDB i den primära regionen. Om du har en befintlig Azure-SSIS IR som redan är kopplad till SSIDB som finns i den primära Azure SQL-hanterade instansen och den fortfarande körs, måste du först stoppa den för att konfigurera om den. Detta är din primära Azure-SSIS IR.

   När [du väljer att använda SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) på sidan **distributions inställningar** i installations fönstret för **integration runtime** , markerar du kryss rutan **Använd dubbla vänte läge Azure-SSIS integration runtime par med SSISDB redundans** . Ange ett namn för att identifiera ditt par av primär och sekundär Azure-SSIS IRs för **dubbla standby-par**. När du har skapat den primära Azure-SSIS IR startas den och ansluts till en primär SSISDB som skapas för din räkning med Läs-och Skriv behörighet. Om du precis har konfigurerat om den måste du starta om den. Du kan också kontrol lera om den primära SSISDB har repliker ATS till en sekundär instans med skrivskyddad åtkomst på **översikts** sidan för den sekundära Azure SQL-hanterade instansen.

1. Med hjälp av Azure Portal/ADF UI kan du skapa en annan Azure-SSIS IR med din sekundära Azure SQL-hanterade instans som värd för SSISDB i den sekundära regionen. Detta är din sekundära Azure-SSIS IR. För fullständig BCDR, se till att alla resurser som den är beroende av också skapas i den sekundära regionen, till exempel Azure Storage för att lagra anpassade installations skript/-filer, ADF för Orchestration/schema körningar osv.

   När [du väljer att använda SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) på sidan **distributions inställningar** i installations fönstret för **integration runtime** , markerar du kryss rutan **Använd dubbla vänte läge Azure-SSIS integration runtime par med SSISDB redundans** . För **dubbla standby-par** anger du samma namn för att identifiera ditt par av primär och sekundär Azure-SSIS IRS. När du har skapat den sekundära Azure-SSIS IR kommer den att startas och kopplas till den sekundära SSISDB.

1. Azure SQL Managed instance kan skydda känsliga data i databaser, till exempel SSISDB, genom att kryptera dem med hjälp av huvud nyckeln för databasen (DMK). DMK är i sin tur krypterad med tjänstens huvud nyckel (SMK) som standard. Vid den tidpunkt då replikeringen inte replikeras replikeras inte SMK från den primära Azure SQL-hanterade instansen, så DMK och i sin tur SSISDB kan inte dekrypteras på den sekundära Azure SQL-hanterade instansen efter att redundansväxlingen inträffar. Du kan undvika detta genom att lägga till en lösen ords kryptering för DMK som ska dekrypteras på den sekundära Azure SQL-hanterade instansen. Utför följande steg med hjälp av SSMS.

   1. Kör följande kommando för SSISDB i din primära Azure SQL-hanterade instans för att lägga till ett lösen ord för kryptering av DMK.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. Kör följande kommando för SSISDB i både dina primära och sekundära Azure SQL-hanterade instanser för att lägga till det nya lösen ordet för dekryptering av DMK.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. Om du vill ha en avbrotts tid som är nästan noll när SSISDB-redundansväxlingen sker, behåller du båda Azure-SSIS-IRs som körs. Endast din primära Azure-SSIS IR kan komma åt den primära SSISDB för att hämta och köra paket, samt körnings loggar för Skriv paket, medan den sekundära Azure-SSIS IR bara kan göra samma för paket som distribuerats någon annan stans, till exempel i Azure Files.

   Om du vill minimera din löpande kostnad kan du stoppa den sekundära Azure-SSIS IR när den har skapats. När SSISDB-redundansväxlingen inträffar byter ditt primära och sekundära Azure-SSIS-IRs roller. Om din primära Azure-SSIS IR stoppas måste du starta om den. Beroende på om den har matats in i ett virtuellt nätverk och den inmatnings metod som används, tar det inom 5 minuter eller cirka 20-30 minuter innan den kan köras.

1. Om du [använder Azure SQL-hanterade instans agenter för Dirigerings-eller schema körningar](./how-to-invoke-ssis-package-managed-instance-agent.md), se till att alla relevanta SSIS-jobb med sina jobb steg och associerade scheman kopieras till den sekundära Azure SQL-hanterade instansen med scheman inaktiverade initialt. Utför följande steg med hjälp av SSMS.

   1. För varje SSIS-jobb högerklickar du och väljer **skriptet jobb som**, **skapa till** och **nytt redigerings fönster** List Rute meny objekt för att generera skriptet.

      ![Generera jobb skript för SSIS](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. För varje genererat jobb skript för SSIS, hitta kommandot för att köra `sp_add_job` den lagrade proceduren och ändra/ta bort värde tilldelningen till `@owner_login_name` argumentet vid behov.

   1. För varje uppdaterat SSIS jobb skript kör du det på din sekundära Azure SQL-hanterade instans för att kopiera jobbet med jobb stegen och associerade scheman.

   1. Använd följande skript för att skapa ett nytt T-SQL-jobb för att aktivera/inaktivera SSIS-jobb scheman baserat på den primära/sekundära SSISDB-rollen i både dina primära och sekundära Azure SQL-hanterade instanser och kör den regelbundet. När SSISDB-redundansväxlingen inträffar aktive ras SSIS jobb scheman som inaktiverades och vice versa.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Om du [använder ADF för Orchestration/schema körningar](./how-to-invoke-ssis-package-ssis-activity.md), se till att alla relevanta ADF-pipelines med kör SSIS-paket-aktiviteter och associerade utlösare kopieras till din sekundära ADF med utlösarna inaktiverade initialt. När SSISDB-redundansväxlingen inträffar måste du aktivera dem.

1. Du kan [testa din Azure SQL-grupp för hanterade instanser](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal#test-failover) och kontrol lera [Azure-SSIS IR övervaknings sidan på ADF-portalen](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) om ditt primära och sekundära Azure-SSIS-IRS har bytt roll. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Bifoga en ny Azure-SSIS IR till befintliga SSISDB som hanteras av Azure SQL Database/hanterade instansen

Om en katastrof inträffar och påverkar din befintliga Azure-SSIS IR men inte Azure SQL Database/Hanterad instans i samma region, kan du ersätta den med en ny region. Slutför följande steg för att koppla din befintliga SSISDB med Azure SQL Database/hanterade instansen till en ny Azure-SSIS IR.

1. Om din befintliga Azure-SSIS IR fortfarande körs måste du stoppa den först med Azure Portal/ADF UI eller Azure PowerShell. Om haverien också påverkar ADF i samma region kan du hoppa över det här steget.

1. Med hjälp av SSMS kör du följande kommando för SSISDB i Azure SQL Database/hanterade instansen för att uppdatera de metadata som ska tillåta anslutningar från din nya ADF/Azure-SSIS IR.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Använd [Azure Portal/ADF UI](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) eller [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)och skapa din nya ADF/Azure-SSIS IR med namnet *YourNewADF* / *YourNewAzureSSISIR* i en annan region. Om du använder Azure Portal/ADF-användargränssnitt kan du ignorera alternativet testa anslutnings fel på sidan **distributions inställningar** i installations fönstret för **integration runtime** .

## <a name="next-steps"></a>Nästa steg

Du kan tänka på följande konfigurations alternativ för din Azure-SSIS IR:

- [Konfigurera paket Arkiv för din Azure-SSIS IR](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Konfigurera anpassade inställningar för din Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Konfigurera virtuell nätverks inmatning för din Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Konfigurera egen värd-IR som en proxy för din Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)
