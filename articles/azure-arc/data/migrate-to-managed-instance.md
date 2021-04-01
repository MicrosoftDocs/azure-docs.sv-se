---
title: Migrera en databas från SQL Server till Azure Arc-aktiverad SQL-hanterad instans
description: Migrera databas från SQL Server till Azure Arc Enabled SQL-hanterad instans
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90941760"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>Migrera: SQL Server till Azure Arc-aktiverad SQL-hanterad instans

Det här scenariot vägleder dig genom stegen för att migrera en databas från en SQL Server instans till en Azure SQL-hanterad instans i Azure Arc via två olika metoder för säkerhets kopiering och återställning.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Använd Azure Blob Storage 

Använd Azure Blob Storage för att migrera till Azure Arc-aktiverad SQL-hanterad instans.

Med den här metoden används Azure Blob Storage som tillfällig lagrings plats som du kan säkerhetskopiera till och sedan återställa från.

### <a name="prerequisites"></a>Förutsättningar

- [Installera Azure Data Studio](install-client-tools.md)
- [Installera Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- Azure-prenumeration

### <a name="step-1-provision-azure-blob-storage"></a>Steg 1: etablera Azure Blob Storage

1. Följ stegen som beskrivs i [skapa ett Azure Blob Storage-konto](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)
1. Starta Azure Storage Explorer
1. [Logga in på Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) för att få åtkomst till blob-lagringen som skapades i föregående steg
1. Högerklicka på Blob Storage-kontot och välj **skapa BLOB-behållare** för att skapa en ny behållare där säkerhets kopian sparas

### <a name="step-2-get-storage-blob-credentials"></a>Steg 2: Hämta autentiseringsuppgifter för lagrings-BLOB

1. I Azure Storage Explorer högerklickar du på den BLOB-behållare som nyss skapades och väljer **Hämta signatur för delad åtkomst**

1. Välj **läsa**, **skriva** och **lista**

1. Välj **Skapa**

   Anteckna URI: n och frågesträngen från den här skärmen. De kommer att behövas i senare steg. Klicka på **kopierings** knappen för att spara till en anteckning/OneNote osv.

1. Stäng fönstret **signatur för delad åtkomst** .

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>Steg 3: Säkerhetskopiera databas filen till Azure Blob Storage

I det här steget ska vi ansluta till käll SQL Server och skapa säkerhets kopian av databasen som vi vill migrera till SQL-hanterad instans – Azure-båge.

1. Starta Azure Data Studio
1. Anslut till SQL Server-instansen som innehåller den databas som du vill migrera till SQL-hanterad instans – Azure Arc
1. Högerklicka på databasen och välj **ny fråga**
1. Förbered din fråga i följande format och ersätt plats hållarna som anges av `<...>` med hjälp av informationen från signaturen för delad åtkomst i föregående steg.  När du har ersatt värdena kör du frågan.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. På samma sätt förbereder du **säkerhets kopierings databas** kommandot enligt följande för att skapa en säkerhets kopierings fil till BLOB-behållaren.  När du har ersatt värdena kör du frågan.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Öppna Azure Storage Explorer och kontrol lera att säkerhets kopierings filen som skapades i föregående steg är synlig i BLOB-behållaren

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>Steg 4: återställa databasen från Azure Blob Storage till SQL Managed instance – Azure Arc

1. Från Azure Data Studio loggar du in och ansluter till den SQL-hanterade instansen – Azure-bågen.
1. Expandera **system databaserna**, högerklicka på **huvud** databasen och välj **ny fråga**.
1. I fönstret Frågeredigeraren förbereder du och kör samma fråga från föregående steg för att skapa autentiseringsuppgifterna.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Förbered och kör kommandot nedan för att kontrol lera att säkerhets kopierings filen är läsbar och intakt.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Förbered och kör kommandot **restore Database** på följande sätt för att återställa säkerhets kopian till en databas på SQL-hanterad instans – Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

Lär dig mer om att säkerhetskopiera till URL: en:

[URL-dokument för säkerhets kopiering](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Säkerhetskopiera till URL med hjälp av SQL Server Management Studio (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>Metod 2: kopiera säkerhets kopian till en Azure SQL-hanterad instans – Azure Arc-Pod med kubectl

Den här metoden visar hur du gör en säkerhets kopia som du skapar via vilken metod som helst och sedan kopierar den till lokal lagring i Azure SQL Managed instance Pod så att du kan återställa därifrån på samma sätt som i ett typiskt fil system på Windows eller Linux. I det här scenariot kommer du att använda kommandot `kubectl cp` för att kopiera filen från en plats till POD fil system.

### <a name="prerequisites"></a>Förutsättningar

- Installera och konfigurera kubectl så att det pekar på ditt Kubernetes-kluster där Azure Arc Data Services har distribuerats
- Ha ett verktyg som Azure Data Studio eller SQL Server hanterings server installerat och anslutet till den SQL Server där du vill skapa säkerhets kopian eller ha en befintlig. bak-fil som redan har skapats i det lokala fil systemet.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>Steg 1: säkerhetskopiera databasen om du inte redan har gjort det

Säkerhetskopiera SQL Server-databasen till din lokala fil Sök väg, t. ex. vanliga SQL Server säkerhets kopiering till disk:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>Steg 2: kopiera säkerhets kopian till fil systemet i pod

Hitta namnet på pod där SQL-instansen har distribuerats. Vanligt vis bör det se ut `pod/<sqlinstancename>-0`

Hämta listan över alla poddar genom att köra:

 ```console
kubectl get pods -n <namespace of data controller>
```

Exempel:

Kopiera säkerhets kopian från den lokala lagrings platsen till SQL-Pod i klustret.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>Steg 3: Återställ databasen

Förbered och kör kommandot Restore för att återställa säkerhets kopian till den Azure SQL-hanterade instansen – Azure-bågen

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Exempel:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>Nästa steg

[Lär dig mer om funktioner och funktioner i Azure Arc-aktiverad SQL-hanterad instans](managed-instance-features.md)

[Börja med att skapa en datakontrollant](create-data-controller.md)

[Har du redan skapat en datakontrollant? Skapa en Azure Arc-aktiverad SQL-hanterad instans](create-sql-managed-instance.md)