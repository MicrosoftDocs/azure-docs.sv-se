---
title: Köra SSIS-paket med Azure SQL-hanterad instans agent
description: Lär dig hur du kör SSIS-paket med hjälp av Azure SQL-hanterad instans agent.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 5f8b18375c517ab5b620b2d6d897f5133b14705d
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556468"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>Köra SSIS-paket med Azure SQL-hanterad instans agent

Den här artikeln beskriver hur du kör ett SQL Server Integration Services-paket (SSIS) med hjälp av Azure SQL-hanterad instans agent. Den här funktionen innehåller beteenden som liknar när du schemalägger SSIS-paket med hjälp av SQL Server Agent i din lokala miljö.

Med den här funktionen kan du köra SSIS-paket som lagras i SSISDB i en SQL-hanterad instans, ett fil system som Azure Files eller ett paket lager för Azure-SSIS integration Runtime.

## <a name="prerequisites"></a>Förutsättningar

[Hämta](/sql/ssms/download-sql-server-management-studio-ssms) och installera den senaste SQL Server Management Studio (SSMS) om du vill använda den här funktionen. Information om versions support enligt nedan:

- Om du vill köra paket i SSISDB eller fil system installerar du SSMS version 18,5 eller senare.
- Om du vill köra paket i paket lagret installerar du SSMS version 18,6 eller senare.

Du måste också [etablera en Azure-SSIS integration runtime](./tutorial-deploy-ssis-packages-azure.md) i Azure Data Factory. Den använder en SQL-hanterad instans som en slut punkts Server.

## <a name="run-an-ssis-package-in-ssisdb"></a>Köra ett SSIS-paket i SSISDB

I den här proceduren använder du SQL-hanterad instans agent för att anropa ett SSIS-paket som är lagrat i SSISDB.

1. I den senaste versionen av SSMS ansluter du till en SQL-hanterad instans.
1. Skapa ett nytt Agent jobb och ett nytt jobb steg. Under **SQL Server Agent** högerklickar du på mappen **jobb** och väljer sedan **nytt jobb**.

   ![Val för att skapa ett nytt Agent jobb](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. På sidan **nytt jobb steg** väljer du **SQL Server Integration Services paket** som typ.

   ![Val för att skapa ett nytt SSIS-jobb steg](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. På fliken **paket** väljer du **SSIS Catalog** som paket plats.
1. Eftersom SSISDB finns i en SQL-hanterad instans behöver du inte ange autentisering.
1. Ange ett SSIS-paket från SSISDB.

   ![Fliken paket med val för paketets käll typ](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. På fliken **konfiguration** kan du:
  
   - Ange parameter värden under **parametrar**.
   - Åsidosätt värden under **anslutnings hanterare**.
   - Åsidosätt egenskapen och välj loggnings nivå under **Avancerat**.

   ![Fliken Konfiguration med val för paketets käll typ](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Spara Agent jobb konfigurationen genom att välja **OK** .
1. Starta Agent jobbet för att köra SSIS-paketet.

## <a name="run-an-ssis-package-in-the-file-system"></a>Köra ett SSIS-paket i fil systemet

I den här proceduren använder du SQL-hanterad instans agent för att köra ett SSIS-paket som lagras i fil systemet.

1. I den senaste versionen av SSMS ansluter du till en SQL-hanterad instans.
1. Skapa ett nytt Agent jobb och ett nytt jobb steg. Under **SQL Server Agent** högerklickar du på mappen **jobb** och väljer sedan **nytt jobb**.

   ![Val för att skapa ett nytt Agent jobb](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. På sidan **nytt jobb steg** väljer du **SQL Server Integration Services paket** som typ.

   ![Val för att skapa ett nytt SSIS-jobb steg](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. På fliken **paket** :

   1. För **paket plats** väljer du **fil system**.

   1. För **fil käll typ**:

      - Om paketet har laddats upp till Azure Files väljer du **Azure-filresurs**.

        ![Alternativ för fil käll typ](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        Paket Sök vägen är **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** .

        Under **paket fil åtkomst autentiseringsuppgifter** anger du Azure-filkontots namn och konto nyckel för att få åtkomst till Azure-filen. Domänen är inställd som **Azure**.

      - Om paketet har laddats upp till en nätverks resurs väljer du **nätverks resurs**.

        Paket Sök vägen är UNC-sökvägen till paket filen med fil namns tillägget. dtsx.

        Ange motsvarande domän, användar namn och lösen ord för att få åtkomst till nätverks resursens paket fil.
   1. Om paket filen är krypterad med ett lösen ord väljer du **krypterings lösen ord** och anger lösen ordet.
1. På fliken **konfigurationer** anger du sökvägen till konfigurations filen om du behöver en konfigurations fil för att köra SSIS-paketet.
   Om du lagrar konfigurationen i Azure Files, är dess konfigurations Sök väg **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. På fliken **körnings alternativ** kan du välja om du vill använda **Windows-autentisering** eller **32-bitars körning** för att köra SSIS-paketet.
1. På fliken **loggning** kan du välja loggnings Sök väg och motsvarande autentiseringsuppgifter för inloggning för att lagra loggfilerna. 
   Som standard är loggnings Sök vägen samma som sökvägen för paketfilerna och loggningen av autentiseringsuppgifter är samma som paketets åtkomst autentiseringsuppgifter.
   Om du lagrar dina loggar i Azure Files, är din loggnings Sök väg **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. På fliken **Ange värden** kan du ange egenskapens sökväg och värde för att åsidosätta paket egenskaperna.

   Om du till exempel vill åsidosätta värdet för användar variabeln anger du dess sökväg i följande format: **`\Package.Variables[User::<variable name>].Value`** .
1. Spara Agent jobb konfigurationen genom att välja **OK** .
1. Starta Agent jobbet för att köra SSIS-paketet.

## <a name="run-an-ssis-package-in-the-package-store"></a>Köra ett SSIS-paket i paket lagret

I den här proceduren använder du SQL-hanterad instans agent för att köra ett SSIS-paket som lagras i Azure-SSIS IR-paket lagret.

1. I den senaste versionen av SSMS ansluter du till en SQL-hanterad instans.
1. Skapa ett nytt Agent jobb och ett nytt jobb steg. Under **SQL Server Agent** högerklickar du på mappen **jobb** och väljer sedan **nytt jobb**.

   ![Val för att skapa ett nytt Agent jobb](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. På sidan **nytt jobb steg** väljer du **SQL Server Integration Services paket** som typ.

   ![Val för att skapa ett nytt SSIS-jobb steg](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. På fliken **paket** :

   1. För **paket plats** väljer du **paket arkiv**.

   1. För **paket Sök väg**:

      Paket Sök vägen är **`<package store name>\<folder name>\<package name>`** .

      ![Alternativ för paket lagrings typ](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. Om paket filen är krypterad med ett lösen ord väljer du **krypterings lösen ord** och anger lösen ordet.
1. På fliken **konfigurationer** anger du sökvägen till konfigurations filen om du behöver en konfigurations fil för att köra SSIS-paketet.
   Om du lagrar konfigurationen i Azure Files, är dess konfigurations Sök väg **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. På fliken **körnings alternativ** kan du välja om du vill använda **Windows-autentisering** eller **32-bitars körning** för att köra SSIS-paketet.
1. På fliken **loggning** kan du välja loggnings Sök väg och motsvarande autentiseringsuppgifter för inloggning för att lagra loggfilerna.
   Som standard är loggnings Sök vägen samma som sökvägen för paketfilerna och loggningen av autentiseringsuppgifter är samma som paketets åtkomst autentiseringsuppgifter.
   Om du lagrar dina loggar i Azure Files, är din loggnings Sök väg **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. På fliken **Ange värden** kan du ange egenskapens sökväg och värde för att åsidosätta paket egenskaperna.

   Om du till exempel vill åsidosätta värdet för användar variabeln anger du dess sökväg i följande format: **`\Package.Variables[User::<variable name>].Value`** .
1. Spara Agent jobb konfigurationen genom att välja **OK** .
1. Starta Agent jobbet för att köra SSIS-paketet.

## <a name="cancel-ssis-package-execution"></a>Avbryta körningen av SSIS-paket

Om du vill avbryta paket körningen från ett SQL-hanterat instans Agent jobb utför du följande steg i stället för att omedelbart stoppa Agent jobbet:

1. Hitta ditt SQL-Agent- **jobId** från **msdb.dbo.sys-jobb**.
1. Hitta motsvarande SSIS- **executionId frågeparameter** baserat på jobb-ID: t med hjälp av den här frågan:
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   Om dina SSIS-paket finns i SSISDB använder du **ssisdb.internal.execution_parameter_values** som tabell för jobb körning. Om dina SSIS-paket finns i fil systemet använder du **ssisdb.internal.execution_parameter_values_noncatalog**.
1. Högerklicka på katalogen SSISDB och välj sedan **aktiva åtgärder**.

   !["Aktiva åtgärder" på snabb menyn för SSISDB-katalogen](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Stoppa motsvarande åtgärd baserat på **executionId frågeparameter**.

## <a name="next-steps"></a>Nästa steg
Du kan också schemalägga SSIS-paket med hjälp av Azure Data Factory. Stegvisa instruktioner finns i [Azure Data Factory händelse utlösare](how-to-create-event-trigger.md).