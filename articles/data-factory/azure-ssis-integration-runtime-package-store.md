---
title: Hantera paket med Azure-SSIS Integration Runtime-paket arkiv
description: Lär dig hur du hanterar paket med Azure-SSIS Integration Runtime Package Store.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 66fdd20e66360be7cdaa6d918e18ca09c413da07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393844"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Hantera paket med Azure-SSIS Integration Runtime-paket arkiv

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Om du vill lyfta & flytta dina lokala arbets belastningar SQL Server Integration Services (SSIS) till molnet kan du etablera Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF). Mer information finns i [etablera en Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md). En Azure-SSIS IR stöder:

- Köra paket som distribuerats i SSIS-katalogen (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans (projekt distributions modell)
- Köra paket som distribuerats i fil systemet, Azure Files eller SQL Server databas (MSDB) som hanteras av Azure SQL-hanterad instans (paket distributions modell)

När du använder paket distributions modell kan du välja om du vill etablera Azure-SSIS IR med paket lager. De tillhandahåller ett paket hanterings lager ovanpå fil systemet, Azure Files eller MSDB som hanteras av en hanterad Azure SQL-instans. Med Azure-SSIS IR paket Store kan du importera/exportera/ta bort/köra paket och övervaka/stoppa paket som körs via SQL Server Management Studio (SSMS) som liknar det [äldre SSIS-paket lagret](/sql/integration-services/service/package-management-ssis-service). 

## <a name="connect-to-azure-ssis-ir"></a>Anslut till Azure-SSIS IR

När din Azure-SSIS IR har tillhandahållits kan du ansluta till den för att bläddra igenom paket arkiven på SSMS.

![Anslut till Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Välj **Azure-SSIS integration runtime** på den nedrullningsbara menyn **Anslut** på **Object Explorers** fönstret för SSMS. Logga sedan in på Azure och välj relevant prenumeration, ADF och Azure-SSIS IR som du har etablerad med paket lager. Din Azure-SSIS IR visas med noderna för att **köra paket** och **lagrade paket** under. Expandera noden **lagrade paket** för att se dina paket butiker under. Expandera paket arkiven för att se mappar och paket under. Du kan uppmanas att ange autentiseringsuppgifter för dina paket lager, om SSMS inte kan ansluta till dem automatiskt. Om du till exempel expanderar ett paket lager ovanpå MSDB kan du först uppmanas att ansluta till din Azure SQL-hanterade instans.

![Ansluta till en Azure SQL-hanterad instans](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Hantera mappar och paket

När du har anslutit till din Azure-SSIS IR på SSMS kan du högerklicka på alla paket lager, mappar eller paket för att popup-menyn och välja **ny mapp**, **Importera paket**, **Exportera paket**, **ta bort** eller **Uppdatera**.

   ![Hantera mappar och paket](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Välj **ny mapp** för att skapa en ny mapp för importerade paket.

   *  Välj **Importera paket** om du vill importera paket från **fil system**, **SQL Server** (msdb) eller det äldre **SSIS-paket arkivet** till ditt paket lager.

      ![Importera paket](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Beroende på vilken **paket plats** som ska importeras från väljer du den relevanta **serverautentisering** / , anger autentiseringsuppgifter vid behov, väljer **paket Sök vägen** och anger det nya **paket namnet**. När du importerar paket går det inte att ändra deras skydds nivå. Använd SQL Server Data Tools (SSDT) eller kommando rads verktyg för att ändra det `dtutil` .

      > [!NOTE]
      > Import av SSIS-paket till Azure-SSIS IR-paket arkiv kan bara göras en i taget och kopierar dem helt enkelt till den underliggande MSDB/fil systemet/Azure Files samtidigt som deras SQL Server-/SSIS-version bevaras. 
      >
      > Eftersom Azure-SSIS IR för närvarande baseras på **SQL Server 2017**, uppgraderas dem till SSIS 2017-paket vid körning. Det finns inte stöd för att köra paket med högre version.
      >
      > Eftersom äldre SSIS-paket arkiv är kopplade till särskilda SQL Server-versioner och bara kan nås på SSMS för den versionen, måste dessutom lägre versions paket i de äldre SSIS-paketen exporteras till fil systemet först med den angivna SSMS versionen innan de kan importeras till Azure-SSIS IR paket lager med SSMS 2019 eller senare versioner.
      >
      > Om du vill importera flera SSIS-paket till Azure-SSIS IR-paket arkiv samtidigt som du växlar skydds nivån, kan du använda kommando rads verktyget [dtutil](/sql/integration-services/dtutil-utility) , se [distribuera flera paket med dtutil](#deploying-multiple-packages-with-dtutil).

   *  Välj **Exportera paket** för att exportera paket från ditt paket lager till **fil system**, **SQL Server** (msdb) eller det äldre **SSIS-paket arkivet**.

      ![Exportera paket](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Beroende på vilken **paket plats** som exporten ska exporteras till väljer du **önskad** / **typ av serverautentisering**, anger autentiseringsuppgifter vid behov och väljer **paket Sök vägen**. När du exporterar paket, om de är krypterade, anger du lösen orden för att dekryptera dem först och sedan kan du ändra skydds nivån, till exempel undvika att lagra känsliga data eller kryptera den eller alla data med användar nyckel eller lösen ord.

      > [!NOTE]
      > Export av SSIS-paket från Azure-SSIS IR-paket arkiv kan bara utföras en i taget och göra det utan att byta skydds nivå, kopierar dem bara när de bevarar sina SQL Server/SSIS-versioner, annars uppgraderas de till SSIS 2019 eller senare versions paket.
      >
      > Eftersom Azure-SSIS IR för närvarande baseras på **SQL Server 2017**, uppgraderas dem till SSIS 2017-paket vid körning. Det finns inte stöd för att köra paket med högre version.
      >
      > Om du vill exportera flera SSIS-paket från Azure-SSIS IR-paket arkiv samtidigt som du växlar skydds nivån, kan du använda kommando rads verktyget [dtutil](/sql/integration-services/dtutil-utility) , se [distribuera flera paket med dtutil](#deploying-multiple-packages-with-dtutil).

   *  Välj **ta bort** för att ta bort befintliga mappar/paket från ditt paket lager.

   *  Välj **Uppdatera** för att visa nyligen tillagda mappar/paket i ditt paket lager.

## <a name="execute-packages"></a>Köra paket

När du har anslutit till din Azure-SSIS IR på SSMS kan du högerklicka på alla lagrade paket för att öppna en meny och välja **Kör paket**.  Då öppnas dialog rutan **verktyg för körning av paket** där du kan konfigurera dina paket körningar på Azure-SSIS IR som kör SSIS paket aktiviteter i ADF-pipeline.

![Verktyg för körning av paket sidor 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Verktyg för körning av paket Pages 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Sidan **Allmänt**, **konfigurationer**, **körnings alternativ** och **loggnings** sidor i **verktyg för körning av paket** -dialogrutan motsvarar fliken  **Inställningar** för aktiviteten kör SSIS-paket. På dessa sidor kan du ange krypterings lösen ordet för ditt paket och komma åt information för paket konfigurations filen. Du kan också ange dina autentiseringsuppgifter och egenskaper för paket körning, samt åtkomst information för loggmappen.  Sidan **Ange värden** i **verktyg för körning av paket** dialog rutan motsvarar **egenskapen åsidosättningar** för aktiviteten kör SSIS-paket där du kan ange dina befintliga paket egenskaper för åsidosättning. Mer information finns i [köra SSIS-paket som kör SSIS-paket aktiviteter i ADF-pipeline](./how-to-invoke-ssis-package-ssis-activity.md).

När du väljer **Kör** -knappen skapas och aktive ras en ny ADF-pipeline med kör SSIS paket-aktivitet automatiskt. Om det redan finns en ADF-pipeline med samma inställningar kommer den att köras igen och en ny pipeline skapas inte. Aktiviteten ADF pipeline och kör SSIS-paket får namnet respektive `Pipeline_SSMS_YourPackageName_HashString` `Activity_SSMS_YourPackageName` .

![verktyg för körning av paket knapp](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Kör SSIS-paket-aktivitet](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Övervaka och stoppa paket som körs

När du har anslutit till din Azure-SSIS IR på SSMS kan du expandera noden **paket som körs** för att se dina paket som körs under.  Högerklicka på någon av dem för att popup-menyn och välj **stoppa** eller **Uppdatera**.

   ![Övervaka och stoppa paket som körs](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Välj **Avbryt om du vill** avbryta den pågående ADF-pipeline som kör paketet som EXECUTe SSIS-paket-aktivitet.

   *  Välj **Uppdatera** för att visa nyligen använda paket från dina paket lager.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Övervaka Azure-SSIS IR och redigera paket arkiv

När du har anslutit till din Azure-SSIS IR på SSMS kan du högerklicka på den för att öppna en meny och välja **gå till Azure Data Factory Portal** eller **Uppdatera**.

   ![Gå till ADF-portalen](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Välj **gå till Azure Data Factory Portal** för att öppna sidan **integrerings körningar** i ADF Monitoring Hub, där du kan övervaka Azure-SSIS IR. På panelen **paket arkiv** kan du se hur många paket lager som är kopplade till din Azure-SSIS IR.  Om du väljer det numret visas ett fönster där du kan redigera ADF-länkade tjänster som lagrar åtkomst informationen för dina paket lager.

      ![Redigera paket arkiv](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Välj **Uppdatera** om du vill visa nyligen tillagda mappar/paket i dina paket lager och köra paket från dina paket lager.

## <a name="deploying-multiple-packages-with-dtutil"></a>Distribuera flera paket med dtutil

Om du vill lyfta & att flytta dina lokala SSIS-arbetsbelastningar till SSIS i ADF samtidigt som du behåller den äldre distributions modellen för paketet måste du distribuera paketen från fil systemet, MSDB som hanteras av SQL Server eller äldre SSIS-paket till Azure Files, MSDB som hanteras av Azure SQL Managed instance eller Azure-SSIS IR paket arkiv. Samtidigt bör du även byta skydds nivå från kryptering genom att användar nyckeln är okrypterad eller krypterad av lösen ord om du inte redan har gjort det.

Du kan använda kommando rads verktyget [dtutil](/sql/integration-services/dtutil-utility) som medföljer SQL Server/SSIS-installationen för att distribuera flera paket i batchar. Den är baserad på en speciell SSIS-version, så om du använder den för att distribuera paket med lägre version utan att byta skydds nivå, kopieras de bara när du behåller sin SSIS-version. Om du använder den för att distribuera dem och samtidigt byta skydds nivå till den, kommer den att uppgraderas till dess SSIS-version.

 Eftersom Azure-SSIS IR för närvarande baseras på **SQL Server 2017**, uppgraderas dem till SSIS 2017-paket vid körning. Det finns inte stöd för att köra paket med högre version.

För att undvika körnings uppgraderingar bör du därför använda dtutil 2017 som medföljer SQL Server/SSIS 2017-installationen för att distribuera paket som ska köras på Azure-SSIS IR i paket distributions modellen. Du kan ladda ned och installera den kostnads fria [SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) för detta ändamål. När du har installerat hittar du dtutil 2017 på den här mappen: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Distribuera flera paket från fil systemet lokalt till Azure Files med dtutil

 Om du vill distribuera flera paket från fil systemet till Azure Files och samtidigt byta skydds nivå kan du köra följande kommandon i en kommando tolk. Ersätt alla strängar som är speciella för ditt ärende.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Om du vill köra kommandona ovan i en kommando fil ersätter du `%f` med `%%f` .

Om du vill distribuera flera paket från äldre SSIS-paket arkiv ovanpå fil systemet i Azure Files och byta skydds nivå samtidigt, kan du använda samma kommandon, men ersätta `YourLocalDrive:\...\YourPackageFolder` med en lokal mapp som används av äldre SSIS-paket arkiv: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Om ditt äldre SSIS-paket arkiv till exempel är kopplat till SQL Server 2016, går du till `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  Du kan hitta värdet för `YourSQLServerDefaultCompatibilityLevel` från en [lista med SQL Server standard nivåer för kompatibilitet](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments).

Om du har konfigurerat Azure-SSIS IR paket arkiv ovanpå Azure Files visas dina distribuerade paket i dem när du ansluter till din Azure-SSIS IR på SSMS 2019 eller senare versioner.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Distribuera flera paket från MSDB lokalt till MSDB i Azure med dtutil

 Om du vill distribuera flera paket från MSDB som finns i SQL Server eller äldre SSIS-paket arkiv ovanpå MSDB till MSDB som värd av Azure SQL-hanterad instans och samtidigt byta skydds nivå, kan du ansluta till din SQL Server på SSMS, högerklicka på `Databases->System Databases->msdb` noden i **Object Explorer** för SSMS för att öppna ett **nytt frågefönster** och köra följande T-SQL-skript. Ersätt alla strängar som är speciella för ditt ärende:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Om du vill använda den privata/offentliga slut punkten för din Azure SQL-hanterade instans ersätter du `YourSQLManagedInstanceEndpoint` med `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` respektive.

Skriptet genererar dtutil kommando rader för alla paket i MSDB som du kan välja mellan, kopiera & klistra in och köra i en kommando tolk.

![Generera dtutil kommando rader](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Om du har konfigurerat Azure-SSIS IR paket arkiv ovanpå MSDB visas dina distribuerade paket i dem när du ansluter till din Azure-SSIS IR på SSMS 2019 eller senare versioner.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Distribuera flera paket från MSDB lokalt till Azure Files med dtutil

 Om du vill distribuera flera paket från MSDB som hanteras av SQL Server eller äldre SSIS-paket arkiv ovanpå MSDB till Azure Files och samtidigt byta skydds nivå, kan du ansluta till din SQL Server på SSMS, högerklicka på `Databases->System Databases->msdb` noden i **Object Explorer** för SSMS för att öppna ett **nytt frågefönster** och köra följande T-SQL-skript. Ersätt alla strängar som är speciella för ditt ärende:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Skriptet genererar dtutil kommando rader för alla paket i MSDB som du kan välja mellan, kopiera & klistra in och köra i en kommando tolk.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Om du har konfigurerat Azure-SSIS IR paket arkiv ovanpå Azure Files visas dina distribuerade paket i dem när du ansluter till din Azure-SSIS IR på SSMS 2019 eller senare versioner.

## <a name="next-steps"></a>Nästa steg

Du kan köra/redigera de automatiskt genererade ADF-pipelinen med kör SSIS-paket aktiviteter eller skapa nya på ADF-portalen. Mer information finns i [köra SSIS-paket som kör SSIS-paket aktiviteter i ADF-pipeline](./how-to-invoke-ssis-package-ssis-activity.md).