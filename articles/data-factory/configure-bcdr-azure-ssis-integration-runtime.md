---
title: Konfigurera Azure-SSIS integration runtime för SQL Database redundans
description: Den här artikeln beskriver hur du konfigurerar Azure-SSIS integration runtime med Azure SQL Database geo-replikering och redundans för SSISDB-databasen
ms.service: data-factory
ms.devlang: powershell
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: e12939d1003ce708889ca0b3dbc710096f9ee955
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364451"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Konfigurera Azure-SSIS integration runtime med SQL Database geo-replikering och redundans

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du konfigurerar Azure-SSIS integration Runtime (IR) med Azure SQL Database geo-replikering för SSISDB-databasen. När en redundansväxling inträffar kan du se till att Azure-SSIS IR fortsätter att arbeta med den sekundära databasen.

Mer information om geo-replikering och redundans för SQL Database finns i [Översikt: aktiva grupper för geo-replikering och automatisk redundans](../azure-sql/database/auto-failover-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Azure-SSIS IR redundans med en SQL-hanterad instans

### <a name="prerequisites"></a>Förutsättningar

En Azure SQL-hanterad instans använder en *huvud nyckel för databasen (DMK)* för att skydda data, autentiseringsuppgifter och anslutnings information som lagras i en databas. Om du vill aktivera automatisk dekryptering av DMK krypteras en kopia av nyckeln via *serverns huvud nyckel (SMK)*. 

SMK replikeras inte i en failover-grupp. Du måste lägga till ett lösen ord på både den primära och sekundära instansen för DMK dekryptering efter redundansväxlingen.

1. Kör följande kommando för SSISDB på den primära instansen. Det här steget lägger till ett nytt krypterings lösen ord.

   ```sql
   ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
   ```

2. Skapa en failover-grupp på en SQL-hanterad instans.

3. Kör **sp_control_dbmasterkey_password** på den sekundära instansen genom att använda det nya krypterings lösen ordet.

   ```sql
   EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'<password>', @action = N'add';  
   GO
   ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scenario 1: Azure-SSIS IR som pekar på en Läs-och skriv lyssnar-slutpunkt

Om du vill att Azure-SSIS IR ska peka på en slut punkt för Läs/skriv-lyssnare måste du först peka på den primära server slut punkten. När du har angett SSISDB i en failover-grupp kan du stoppa Azure-SSIS IR, ändra den så att den pekar på slut punkten Läs/skriv lyssnare med Azure PowerShell och sedan starta om den.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Managed Instance read/write listener endpoint"
```

#### <a name="solution"></a>Lösning

Utför följande steg när redundans inträffar:

1. Stoppa Azure-SSIS IR i den primära regionen.

2. Redigera Azure-SSIS IR med en ny region, ett virtuellt nätverk och en signatur för signatur för delad åtkomst (SAS) för anpassad installation på den sekundära instansen. Eftersom Azure-SSIS IR pekar på en Läs-och skriv lyssnare och slut punkten är transparent för Azure-SSIS IR, behöver du inte redigera slut punkten.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Starta om Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scenario 2: Azure-SSIS IR pekar på en primär server slut punkt

Det här scenariot är lämpligt om Azure-SSIS IR pekar på en primär server slut punkt.

#### <a name="solution"></a>Lösning

Utför följande steg när redundans inträffar:

1. Stoppa Azure-SSIS IR i den primära regionen.

2. Redigera Azure-SSIS IR med ny region, slut punkt och information om virtuellt nätverk för den sekundära instansen.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Starta om Azure-SSIS IR.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-managed-instance"></a>Scenario 3: Azure-SSIS IR pekar på en offentlig slut punkt för en SQL-hanterad instans

Det här scenariot är lämpligt om Azure-SSIS IR pekar på en offentlig slut punkt för en hanterad Azure SQL-instans och det inte går att ansluta till ett virtuellt nätverk. Den enda skillnaden från scenario 2 är att du inte behöver redigera information om virtuellt nätverk för Azure-SSIS IR efter redundansväxlingen.

#### <a name="solution"></a>Lösning

Utför följande steg när redundans inträffar:

1. Stoppa Azure-SSIS IR i den primära regionen.

2. Redigera Azure-SSIS IR med den nya regionen och slut punkts informationen för den sekundära instansen.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database server endpoint" `
      -CatalogAdminCredential "Azure SQL Database server admin credentials" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Starta om Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 4: koppla en befintlig SSISDB-instans (SSIS Catalog) till en ny Azure-SSIS IR

Det här scenariot är lämpligt om du vill att SSISDB ska fungera med en ny Azure-SSIS IR i en ny region när en Azure Data Factory eller Azure-SSIS IR katastrof inträffar i den aktuella regionen.

#### <a name="solution"></a>Lösning

Utför följande steg när redundans inträffar.

> [!NOTE]
> Använd PowerShell för steg 4 (generering av IR). Om du inte gör det rapporterar Azure Portal ett fel som säger att SSISDB redan finns.

1. Stoppa Azure-SSIS IR i den primära regionen.

2. Kör en lagrad procedur för att uppdatera metadata i SSISDB för att godkänna anslutningar från **\<new_data_factory_name\>** och **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Skapa en ny data fabrik med namnet **\<new_data_factory_name\>** i den nya regionen.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Mer information om PowerShell-kommandot finns i [skapa en Azure-datafabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md).

4. Skapa ett nytt Azure-SSIS IR med namnet **\<new_integration_runtime_name\>** i den nya regionen med hjälp av Azure PowerShell.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
      -DataFactoryName "new data factory name" `
      -Name "<new_integration_runtime_name>" `
      -Description $AzureSSISDescription `
      -Type Managed `
      -Location $AzureSSISLocation `
      -NodeSize $AzureSSISNodeSize `
      -NodeCount $AzureSSISNodeNumber `
      -Edition $AzureSSISEdition `
      -LicenseType $AzureSSISLicenseType `
      -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
      -VnetId "new vnet" `
      -Subnet "new subnet" `
      -CatalogServerEndpoint $SSISDBServerEndpoint `
      -CatalogPricingTier $SSISDBPricingTier
   ```
   
   Mer information om PowerShell-kommandot finns i [Skapa Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="azure-ssis-ir-failover-with-sql-database"></a>Azure-SSIS IR redundans med SQL Database

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scenario 1: Azure-SSIS IR som pekar på en Läs-och skriv lyssnar-slutpunkt

Det här scenariot är lämpligt när:

- Azure-SSIS IR pekar på den läsa/Skriv lyssnare-slutpunkten för gruppen redundans.
- SQL Database servern har *inte* kon figurer ATS med regeln för tjänst slut punkten för virtuellt nätverk.

Om du vill att Azure-SSIS IR ska peka på en slut punkt för Läs/skriv-lyssnare måste du först peka på den primära server slut punkten. När du har angett SSISDB i en failover-grupp kan du stoppa Azure-SSIS IR, ändra den så att den pekar på slut punkten Läs/skriv lyssnare med Azure PowerShell och sedan starta om den.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Database read/write listener endpoint"
```

#### <a name="solution"></a>Lösning

När redundansväxlingen inträffar är det transparent för Azure-SSIS IR. Azure-SSIS IR ansluter automatiskt till den nya primära gruppen för redundans. 

Om du vill uppdatera regionen eller annan information i Azure-SSIS IR kan du stoppa den, redigera och starta om.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scenario 2: Azure-SSIS IR pekar på en primär server slut punkt

Det här scenariot är lämpligt om Azure-SSIS IR pekar på en primär server slut punkt.

#### <a name="solution"></a>Lösning

Utför följande steg när redundans inträffar:

1. Stoppa Azure-SSIS IR i den primära regionen.

2. Redigera Azure-SSIS IR med ny region, slut punkt och information om virtuellt nätverk för den sekundära instansen.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Starta om Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3: koppla en befintlig SSISDB (SSIS Catalog) till en ny Azure-SSIS IR

Det här scenariot är lämpligt om du vill etablera en ny Azure-SSIS IR i en sekundär region. Det är också lämpligt om du vill att din SSISDB ska fortsätta att arbeta med en ny Azure-SSIS IR i en ny region när en Azure Data Factory eller Azure-SSIS IR katastrof inträffar i den aktuella regionen.

#### <a name="solution"></a>Lösning

Utför följande steg när redundans inträffar.

> [!NOTE]
> Använd PowerShell för steg 4 (generering av IR). Om du inte gör det rapporterar Azure Portal ett fel som säger att SSISDB redan finns.

1. Stoppa Azure-SSIS IR i den primära regionen.

2. Kör en lagrad procedur för att uppdatera metadata i SSISDB för att godkänna anslutningar från **\<new_data_factory_name\>** och **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Skapa en ny data fabrik med namnet **\<new_data_factory_name\>** i den nya regionen.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Mer information om PowerShell-kommandot finns i [skapa en Azure-datafabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md).

4. Skapa ett nytt Azure-SSIS IR med namnet **\<new_integration_runtime_name\>** i den nya regionen med hjälp av Azure PowerShell.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
      -DataFactoryName "new data factory name" `
      -Name "<new_integration_runtime_name>" `
      -Description $AzureSSISDescription `
      -Type Managed `
      -Location $AzureSSISLocation `
      -NodeSize $AzureSSISNodeSize `
      -NodeCount $AzureSSISNodeNumber `
      -Edition $AzureSSISEdition `
      -LicenseType $AzureSSISLicenseType `
      -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
      -VnetId "new vnet" `
      -Subnet "new subnet" `
      -CatalogServerEndpoint $SSISDBServerEndpoint `
      -CatalogPricingTier $SSISDBPricingTier
   ```

   Mer information om PowerShell-kommandot finns i [Skapa Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="next-steps"></a>Nästa steg

Överväg följande konfigurations alternativ för Azure-SSIS IR:

- [Konfigurera Azure-SSIS integration runtime för hög prestanda](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassa konfigurationen av Azure SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Etablera Enterprise Edition för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
