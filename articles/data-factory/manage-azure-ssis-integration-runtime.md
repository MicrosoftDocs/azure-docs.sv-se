---
title: Omkonfigurera Azure SSIS-integreringskörningen
description: Lär dig hur du konfigurerar om en Azure-SSIS integration runtime i Azure Data Factory när du redan har gjort det.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370690"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Omkonfigurera Azure SSIS-integreringskörningen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du konfigurerar om en befintlig Azure-SSIS integration Runtime. Om du vill skapa en Azure-SSIS integration Runtime (IR) i Azure Data Factory, se [skapa en Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt 
Du kan använda Data Factory UI för att stoppa, redigera/omkonfigurera eller ta bort ett Azure-SSIS IR. 

1. Öppna Data Factory UI genom att välja panelen **författare & Monitor** på Start sidan för din data fabrik.
2. Välj **hanterings** hubben under **Start**, **Redigera** och **övervaka** hubbar för att visa fönstret **anslutningar** .

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Så här konfigurerar du om en Azure-SSIS IR
I fönstret **anslutningar** i **Hantera** hubb växlar du till sidan **integrerings körningar** och väljer **Uppdatera**. 

   ![Fönstret anslutningar](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Du kan redigera/konfigurera om din Azure-SSIS IR genom att välja dess namn. Du kan också välja relevanta knappar för att övervaka/starta/stoppa/ta bort Azure-SSIS IR, generera en ADF-pipeline automatiskt med kör SSIS-paket-aktivitet för att köra på din Azure-SSIS IR och Visa JSON-koden/nytto lasten för dina Azure-SSIS IR.  Det går bara att redigera eller ta bort Azure-SSIS IR när den har stoppats.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du har etablerat och startat en instans av Azure-SSIS integration runtime kan du konfigurera den igen genom att köra en sekvens med `Stop`  -  `Set`  -  `Start` PowerShell-cmdlets i följd. Följande PowerShell-skript ändrar till exempel antalet noder som har allokerats för Azure-SSIS integration runtime-instansen till fem.

### <a name="reconfigure-an-azure-ssis-ir"></a>Konfigurera om en Azure-SSIS IR

1. Stoppa först integrerings körningen för Azure-SSIS med hjälp av cmdleten [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Detta kommando frigör alla noder och avslutar faktureringen.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. Konfigurera sedan Azure-SSIS IR med hjälp av cmdleten [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . Följande exempel kommando skalar upp en Azure-SSIS integration runtime till fem noder.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. Starta sedan Azure-SSIS integration runtime genom att använda cmdleten [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Detta kommando allokerar alla noder för att köra SSIS-paket.   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>Ta bort ett Azure-SSIS IR
1. Börja med att lista alla befintliga Azure SSIS-IRs under din data fabrik.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. Stoppa sedan alla befintliga Azure SSIS-IRs i din data fabrik.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. Ta sedan bort alla befintliga Azure SSIS-IRs i din data fabrik en i taget.

   ```powershell
   Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
4. Slutligen tar du bort data fabriken.

   ```powershell
   Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
   ```
5. Om du har skapat en ny resurs grupp tar du bort resurs gruppen.

   ```powershell
   Remove-AzResourceGroup -Name $ResourceGroupName -Force 
   ```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS runtime finns i följande avsnitt: 

- [Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller konceptuell information om integrerings körningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](./tutorial-deploy-ssis-packages-azure.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder Azure SQL Database som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln expanderar i självstudien och innehåller instruktioner om hur du använder Azure SQL-hanterad instans och ansluter till IR till ett virtuellt nätverk. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen.