---
title: Köra ett SSIS-paket med aktiviteten kör SSIS-paket
description: Den här artikeln beskriver hur du kör ett SQL Server Integration Services-paket (SSIS) i en Azure Data Factory-pipeline med hjälp av aktiviteten kör SSIS-paket.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: de9cd7e77e558c4d1a0aa62af17bc612eee5ec56
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555852"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Kör ett SSIS-paket med aktiviteten Kör SSIS-paket i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du kör ett SQL Server Integration Services-paket (SSIS) i en Azure Data Factory-pipeline med hjälp av aktiviteten kör SSIS-paket. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Skapa en Azure-SSIS integration Runtime (IR) om du inte redan har en genom att följa de stegvisa anvisningarna i [självstudien: etablering Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

## <a name="run-a-package-in-the-azure-portal"></a>Kör ett paket i Azure Portal
I det här avsnittet använder du Data Factory användar gränssnitt (UI) eller appen för att skapa en Data Factory-pipeline med en kör SSIS-paket-aktivitet som kör ditt SSIS-paket.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en EXECUTE SSIS-paket-aktivitet
I det här steget använder du Data Factory gränssnittet eller appen för att skapa en pipeline. Du lägger till en aktivitet för att köra SSIS-paket i pipelinen och konfigurerar den att köra ditt SSIS-paket. 

1. På Data Factory översikt eller start sida på Azure Portal väljer du panelen **författare & övervakare** för att starta Data Factory användar gränssnitt eller app på en separat flik. 

   ![Data Factory start sida](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**. 

   ![Sidan Kom igång](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Expandera **Allmänt** i **aktivitets** verktygs lådan. Dra sedan en aktivitet för att **köra SSIS-paket** till pipelinens designer-yta. 

   ![Dra en aktivitet för att köra SSIS-paket till design ytan](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   Välj aktivitets objekt för att köra SSIS-paket för att konfigurera dess **allmänna**, **Inställningar**, **SSIS-parametrar**, **anslutnings hanterare** och **Property åsidosätter** -flikar.

#### <a name="general-tab"></a>fliken Allmänt

Utför följande steg på fliken **Allmänt** i AKTIVITETEN kör SSIS-paket.

![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. I **namn** anger du namnet på AKTIVITETEN kör SSIS-paket.

   1. För **Beskrivning** anger du en beskrivning av AKTIVITETEN kör SSIS-paket.

   1. För **tids gräns** anger du den längsta tid som AKTIVITETEN kör SSIS-paket kan köras. Standardvärdet är 7 dagar, formatet är D. HH: MM: SS.

   1. För **återförsök** anger du det maximala antalet nya försök för AKTIVITETEN kör SSIS-paket.

   1. För **Återförsöksintervall** anger du antalet sekunder mellan varje nytt försök för AKTIVITETEN kör SSIS-paket. Standardvärdet är 30 sekunder.

   1. Markera kryss rutan **säkra utdata** för att välja om du vill utesluta utdata från AKTIVITETEN kör SSIS-paket från loggning.

   1. Markera kryss rutan **säkra inmatade** för att välja om du vill utesluta indatamängden för AKTIVITETEN kör SSIS-paket från loggning.

#### <a name="settings-tab"></a>Fliken Inställningar

Utför följande steg på fliken **Inställningar** för AKTIVITETEN kör SSIS-paket.

![Ange egenskaper på fliken Inställningar-automatisk](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. För **Azure-SSIS IR** väljer du den angivna Azure-SSIS IR för att köra AKTIVITETEN kör SSIS-paket.

   1. För **Beskrivning** anger du en beskrivning av AKTIVITETEN kör SSIS-paket.

   1. Markera kryss rutan **Windows-autentisering** för att välja om du vill använda Windows-autentisering för att komma åt data lager, till exempel SQL-servrar/fil resurser lokalt eller Azure Files.
   
      Om du markerar den här kryss rutan anger du värdena för dina paket körnings uppgifter i rutorna **domän**, **användar namn** och **lösen ord** . För att till exempel komma åt Azure Files är domänen `Azure` , användar namnet är `<storage account name>` och lösen ordet är `<storage account key>` .

      Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden. Det gör du genom att markera kryss rutan **Azure Key Vault** bredvid dem. Välj eller redigera din befintliga Key Vault-länkade tjänst eller skapa en ny. Välj sedan det hemliga namnet och versionen för ditt värde. När du skapar eller redigerar en länkad nyckel valvs tjänst kan du välja eller redigera ditt befintliga nyckel valv eller skapa ett nytt. Se till att bevilja Data Factory hanterad identitets åtkomst till ditt nyckel valv om du inte redan har gjort det. Du kan också ange hemligheten direkt i följande format: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Markera kryss rutan **32-bitars körning** för att välja om ditt paket behöver 32-bitars körning för att kunna köras.

   1. För **paket plats** väljer du **SSISDB**, **fil system (paket)**, **fil system (projekt)**, **inbäddat paket** eller **paket lager**. 

##### <a name="package-location-ssisdb"></a>Paket plats: SSISDB

**SSISDB** som din paket plats väljs automatiskt om din Azure-SSIS IR etablerades med en SSIS-katalog (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans eller om du kan välja den själv. Om den är markerad utför du följande steg.

   1. Om din Azure-SSIS IR körs och kryss rutan **manuella poster** är avmarkerad bläddrar du och väljer dina befintliga mappar, projekt, paket och miljöer från SSISDB. Välj **Uppdatera** för att hämta nyligen tillagda mappar, projekt, paket eller miljöer från SSISDB, så att de är tillgängliga för bläddring och val. Om du vill bläddra och välja miljöer för dina paket körningar måste du konfigurera dina projekt i förväg för att lägga till dessa miljöer som referenser från samma mappar under SSISDB. Mer information finns i [skapa och MAPPA SSIS miljöer](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

   1. För **loggnings nivå** väljer du ett fördefinierat loggnings område för paket körningen. Markera kryss rutan **anpassad** om du vill ange ett anpassat loggnings namn i stället. 

   1. Om din Azure-SSIS IR inte körs eller om kryss rutan **manuella poster** är markerad, anger du dina paket-och miljö Sök vägar från SSISDB direkt i följande format: `<folder name>/<project name>/<package name>.dtsx` och `<folder name>/<environment name>` .

      ![Ange egenskaper på fliken Inställningar – manuell](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Paket plats: fil system (paket)

**Fil system (paket)** som din paket plats väljs automatiskt om Azure-SSIS IR etablerades utan SSISDB eller om du kan välja det själv. Om den är markerad utför du följande steg.

![Ange egenskaper på fliken Inställningar – fil system (paket)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Ange att paketet ska köras genom att ange en Universal Naming Convention (UNC)-sökväg till paket filen (med `.dtsx` ) i rutan **paket Sök väg** . Du kan bläddra och välja ditt paket genom att välja **Bläddra i fil lagring** eller ange sökvägen manuellt. Om du till exempel lagrar ditt paket i Azure Files, är sökvägen `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. Om du konfigurerar ditt paket i en separat fil måste du också ange en UNC-sökväg till konfigurations filen (med `.dtsConfig` ) i rutan **konfigurations Sök väg** . Du kan bläddra och välja konfigurationen genom att välja **Bläddra i fil lagring** eller ange dess sökväg manuellt. Om du till exempel lagrar konfigurationen i Azure Files, är sökvägen `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Ange autentiseringsuppgifterna för att komma åt ditt paket och konfigurationsfiler. Om du tidigare har angett värdena för paketets körnings uppgifter (för **Windows-autentisering**) kan du återanvända dem genom att markera kryss rutan **samma som autentiseringsuppgifter för paket körning** . Annars anger du värdena för dina paket åtkomst uppgifter i rutorna **domän**, **användar namn** och **lösen ord** . Om du till exempel lagrar paketet och konfigurationen i Azure Files, är domänen `Azure` , användar namnet `<storage account name>` och lösen ordet `<storage account key>` . 

      Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden. Det gör du genom att markera kryss rutan **Azure Key Vault** bredvid dem. Välj eller redigera din befintliga Key Vault-länkade tjänst eller skapa en ny. Välj sedan det hemliga namnet och versionen för ditt värde. När du skapar eller redigerar en länkad nyckel valvs tjänst kan du välja eller redigera ditt befintliga nyckel valv eller skapa ett nytt. Se till att bevilja Data Factory hanterad identitets åtkomst till ditt nyckel valv om du inte redan har gjort det. Du kan också ange hemligheten direkt i följande format: `<key vault linked service name>/<secret name>/<secret version>` . 

      Dessa autentiseringsuppgifter används också för att komma åt dina underordnade paket i åtgärden kör paket som refereras till av en egen sökväg och andra konfigurationer som anges i dina paket. 

   1. Om du använde skydds nivån **EncryptAllWithPassword** eller **EncryptSensitiveWithPassword** när du skapade paketet via SQL Server Data Tools (SSDT) anger du värdet för lösen ordet i rutan **krypterings lösen ord** . Du kan också använda en hemlighet som lagrats i Azure Key Vault som dess värde (se ovan).
      
      Om du har använt skydds nivån **EncryptSensitiveWithUserKey** , ange dina känsliga värden i konfigurationsfiler igen eller på fliken **SSIS-parametrar**, **anslutnings hanterare** eller **egenskaps åsidosättningar** (se nedan).
      
      Om du har använt skydds nivån **EncryptAllWithUserKey** stöds den inte. Du måste konfigurera om ditt paket så att det använder en annan skydds nivå via SSDT eller `dtutil` kommando rads verktyget. 

   1. För **loggnings nivå** väljer du ett fördefinierat loggnings område för paket körningen. Markera kryss rutan **anpassad** om du vill ange ett anpassat loggnings namn i stället. 
   
   1. Om du vill logga dina paket körningar utöver att använda de standard logg leverantörer som kan anges i ditt paket, anger du loggmappen genom att ange dess UNC-sökväg i rutan **loggnings Sök väg** . Du kan bläddra och välja din loggfil genom att välja **Bläddra i fil lagring** eller ange sökvägen manuellt. Om du till exempel lagrar loggarna i Azure Files är loggnings Sök vägen `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . En undermapp skapas i den här sökvägen för varje enskild paket körning, med namnet efter körnings-ID: t för aktiviteten kör SSIS-paket och där loggfilerna genereras var femte minut. 
   
   1. Ange de autentiseringsuppgifter som ska användas för att komma åt loggmappen. Om du tidigare har angett värdena för dina paket åtkomst uppgifter (se ovan) kan du återanvända dem genom att markera kryss rutan **samma som paket åtkomst autentiseringsuppgifter** . Annars anger du värdena för inloggnings uppgifterna för inloggning i rutorna **domän**, **användar namn** och **lösen ord** . Om du till exempel lagrar loggarna i Azure Files är domänen `Azure` , användar namnet `<storage account name>` och lösen ordet `<storage account key>` . Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden (se ovan).
   
För alla UNC-sökvägar som tidigare nämnts måste det fullständigt kvalificerade fil namnet innehålla färre än 260 tecken. Katalog namnet måste innehålla färre än 248 tecken.

##### <a name="package-location-file-system-project"></a>Paket plats: fil system (projekt)

Om du väljer **fil system (projekt)** som paket plats utför du följande steg.

![Ange egenskaper på fliken Inställningar – fil system (projekt)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Ange att paketet ska köras genom att tillhandahålla en UNC-sökväg till projekt filen (med `.ispac` ) i rutan **projekt Sök väg** och en paketfil (med `.dtsx` ) från projektet i rutan **paket namn** . Du kan bläddra och välja ditt projekt genom att välja **Bläddra i fil lagring** eller ange sökvägen manuellt. Om du till exempel lagrar projektet i Azure Files, är sökvägen `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Ange autentiseringsuppgifterna för att komma åt ditt projekt och paketfiler. Om du tidigare har angett värdena för paketets körnings uppgifter (för **Windows-autentisering**) kan du återanvända dem genom att markera kryss rutan **samma som autentiseringsuppgifter för paket körning** . Annars anger du värdena för dina paket åtkomst uppgifter i rutorna **domän**, **användar namn** och **lösen ord** . Om du till exempel lagrar projektet och paketet i Azure Files, är domänen `Azure` , användar namnet `<storage account name>` och lösen ordet `<storage account key>` . 

      Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden. Det gör du genom att markera kryss rutan **Azure Key Vault** bredvid dem. Välj eller redigera din befintliga Key Vault-länkade tjänst eller skapa en ny. Välj sedan det hemliga namnet och versionen för ditt värde. När du skapar eller redigerar en länkad nyckel valvs tjänst kan du välja eller redigera ditt befintliga nyckel valv eller skapa ett nytt. Se till att bevilja Data Factory hanterad identitets åtkomst till ditt nyckel valv om du inte redan har gjort det. Du kan också ange hemligheten direkt i följande format: `<key vault linked service name>/<secret name>/<secret version>` . 

      Dessa autentiseringsuppgifter används också för att komma åt dina underordnade paket i åtgärden kör paket som refereras från samma projekt. 

   1. Om du använde skydds nivån **EncryptAllWithPassword** eller **EncryptSensitiveWithPassword** när du skapade paketet via SSDT anger du värdet för lösen ordet i rutan **krypterings lösen ord** . Du kan också använda en hemlighet som lagrats i Azure Key Vault som dess värde (se ovan).
      
      Om du har använt skydds nivån **EncryptSensitiveWithUserKey** anger du de känsliga värdena på flikarna **SSIS**, **anslutnings hanterare** eller **Property åsidosätter** (se nedan).
      
      Om du har använt skydds nivån **EncryptAllWithUserKey** stöds den inte. Du måste konfigurera om ditt paket så att det använder en annan skydds nivå via SSDT eller `dtutil` kommando rads verktyget. 

   1. För **loggnings nivå** väljer du ett fördefinierat loggnings område för paket körningen. Markera kryss rutan **anpassad** om du vill ange ett anpassat loggnings namn i stället. 
   
   1. Om du vill logga dina paket körningar utöver att använda de standard logg leverantörer som kan anges i ditt paket, anger du loggmappen genom att ange dess UNC-sökväg i rutan **loggnings Sök väg** . Du kan bläddra och välja din loggfil genom att välja **Bläddra i fil lagring** eller ange sökvägen manuellt. Om du till exempel lagrar loggarna i Azure Files är loggnings Sök vägen `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . En undermapp skapas i den här sökvägen för varje enskild paket körning, med namnet efter körnings-ID: t för aktiviteten kör SSIS-paket och där loggfilerna genereras var femte minut. 
   
   1. Ange de autentiseringsuppgifter som ska användas för att komma åt loggmappen. Om du tidigare har angett värdena för dina paket åtkomst uppgifter (se ovan) kan du återanvända dem genom att markera kryss rutan **samma som paket åtkomst autentiseringsuppgifter** . Annars anger du värdena för inloggnings uppgifterna för inloggning i rutorna **domän**, **användar namn** och **lösen ord** . Om du till exempel lagrar loggarna i Azure Files är domänen `Azure` , användar namnet `<storage account name>` och lösen ordet `<storage account key>` . Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden (se ovan).
   
För alla UNC-sökvägar som tidigare nämnts måste det fullständigt kvalificerade fil namnet innehålla färre än 260 tecken. Katalog namnet måste innehålla färre än 248 tecken.

##### <a name="package-location-embedded-package"></a>Paket plats: inbäddat paket

Om du väljer **inbäddat paket** som paket plats utför du följande steg.

![Ange egenskaper på fliken Inställningar – inbäddat paket](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Dra och släpp paket filen (med `.dtsx` ) eller **Ladda upp** den från en mapp i rutan som visas. Paketet komprimeras och bäddas in automatiskt i aktivitetens nytto Last. När du har bäddat in paketet kan du **Ladda ned** det senare för redigering. Du kan också **Parameterisera** ditt inbäddade paket genom att tilldela det till en pipeline-parameter som kan användas i flera aktiviteter och därmed optimera storleken på din pipeline-nyttolast. Det finns för närvarande inte stöd för att bädda in projektfiler (med `.ispac` ), så du kan inte använda SSIS-parametrar/anslutnings hanterare med omfång på projekt nivå i dina inbäddade paket.
   
   1. Om det inbäddade paketet inte är krypterat och vi identifierar användningen av EXECUTE Package Task (EPT), markeras kryss rutan **Kör paket uppgift** automatiskt och dina underordnade paket som refereras till av deras fil system Sök väg läggs till automatiskt, så att du kan bädda in dem.
   
      Om vi inte kan identifiera användningen av EPT, måste du manuellt markera kryss rutan **Kör paket aktivitet** och lägga till dina underordnade paket som refereras till av deras fil system Sök väg en i taget, så att du kan bädda in dem. Om dina underordnade paket lagras i SQL Server Database (MSDB) kan du inte bädda in dem, så du måste se till att Azure-SSIS IR har åtkomst till MSDB för att kunna hämta dem med hjälp av deras SQL Server referenser. Det finns för närvarande inte stöd för att bädda in projektfiler (med `.ispac` ), så du kan inte använda projektbaserade referenser för dina underordnade paket.
   
   1. Om du använde skydds nivån **EncryptAllWithPassword** eller **EncryptSensitiveWithPassword** när du skapade paketet via SSDT anger du värdet för lösen ordet i rutan **krypterings lösen ord** . 
   
      Du kan också använda en hemlighet som lagras i Azure Key Vault som dess värde. Det gör du genom att markera kryss rutan **Azure Key Vault** bredvid den. Välj eller redigera din befintliga Key Vault-länkade tjänst eller skapa en ny. Välj sedan det hemliga namnet och versionen för ditt värde. När du skapar eller redigerar en länkad nyckel valvs tjänst kan du välja eller redigera ditt befintliga nyckel valv eller skapa ett nytt. Se till att bevilja Data Factory hanterad identitets åtkomst till ditt nyckel valv om du inte redan har gjort det. Du kan också ange hemligheten direkt i följande format: `<key vault linked service name>/<secret name>/<secret version>` .
      
      Om du har använt skydds nivån **EncryptSensitiveWithUserKey** , ange dina känsliga värden i konfigurationsfiler igen eller på fliken **SSIS-parametrar**, **anslutnings hanterare** eller **egenskaps åsidosättningar** (se nedan).
      
      Om du har använt skydds nivån **EncryptAllWithUserKey** stöds den inte. Du måste konfigurera om ditt paket så att det använder en annan skydds nivå via SSDT eller `dtutil` kommando rads verktyget.

   1. För **loggnings nivå** väljer du ett fördefinierat loggnings område för paket körningen. Markera kryss rutan **anpassad** om du vill ange ett anpassat loggnings namn i stället. 
   
   1. Om du vill logga dina paket körningar utöver att använda de standard logg leverantörer som kan anges i ditt paket, anger du loggmappen genom att ange dess UNC-sökväg i rutan **loggnings Sök väg** . Du kan bläddra och välja din loggfil genom att välja **Bläddra i fil lagring** eller ange sökvägen manuellt. Om du till exempel lagrar loggarna i Azure Files är loggnings Sök vägen `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . En undermapp skapas i den här sökvägen för varje enskild paket körning, med namnet efter körnings-ID: t för aktiviteten kör SSIS-paket och där loggfilerna genereras var femte minut. 
   
   1. Ange de autentiseringsuppgifter som ska användas för att komma åt loggmappen genom att ange deras värden i rutorna **domän**, **användar namn** och **lösen ord** . Om du till exempel lagrar loggarna i Azure Files är domänen `Azure` , användar namnet `<storage account name>` och lösen ordet `<storage account key>` . Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden (se ovan).
   
För alla UNC-sökvägar som tidigare nämnts måste det fullständigt kvalificerade fil namnet innehålla färre än 260 tecken. Katalog namnet måste innehålla färre än 248 tecken.

##### <a name="package-location-package-store"></a>Paket plats: paket arkiv

Om du väljer **paket arkiv** som paket plats utför du följande steg.

![Ange egenskaper på fliken Inställningar-paket arkiv](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. För **paket arkivets namn** väljer du ett befintligt paket lager som är kopplat till din Azure-SSIS IR.

   1. Ange att paketet ska köras genom att ange dess sökväg (utan `.dtsx` ) från det valda paket arkivet i rutan **paket Sök väg** . Om det valda paket arkivet finns ovanpå fil system/Azure Files kan du bläddra och välja ditt paket genom att välja Bläddra i **fil lagring**, annars kan du ange dess sökväg i formatet `<folder name>\<package name>` . Du kan också importera nya paket till det valda paket arkivet via SQL Server Management Studio (SSMS) som liknar det [äldre SSIS-paket arkivet](/sql/integration-services/service/package-management-ssis-service). Mer information finns i [Hantera SSIS-paket med Azure-SSIS IR paket lager](./azure-ssis-integration-runtime-package-store.md).

   1. Om du konfigurerar ditt paket i en separat fil måste du ange en UNC-sökväg till konfigurations filen (med `.dtsConfig` ) i rutan **konfigurations Sök väg** . Du kan bläddra och välja konfigurationen genom att välja **Bläddra i fil lagring** eller ange dess sökväg manuellt. Om du till exempel lagrar konfigurationen i Azure Files, är sökvägen `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Välj kryss rutan **autentiseringsuppgifter för konfigurations åtkomst** om du vill ange vilka autentiseringsuppgifter som ska användas för att komma åt konfigurations filen separat. Detta krävs när det valda paket arkivet finns ovanpå SQL Server Database (MSDB) som hanteras av din Azure SQL-hanterade instans eller inte också lagrar konfigurations filen.
   
      Om du tidigare har angett värdena för paketets körnings uppgifter (för **Windows-autentisering**) kan du återanvända dem genom att markera kryss rutan **samma som autentiseringsuppgifter för paket körning** . Annars anger du värdena för inloggnings uppgifterna för konfigurationen i rutorna **domän**, **användar namn** och **lösen ord** . Om du till exempel lagrar konfigurationen i Azure Files, är domänen `Azure` , användar namnet `<storage account name>` och lösen ordet `<storage account key>` . 

      Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden. Det gör du genom att markera kryss rutan **Azure Key Vault** bredvid dem. Välj eller redigera din befintliga Key Vault-länkade tjänst eller skapa en ny. Välj sedan det hemliga namnet och versionen för ditt värde. När du skapar eller redigerar en länkad nyckel valvs tjänst kan du välja eller redigera ditt befintliga nyckel valv eller skapa ett nytt. Se till att bevilja Data Factory hanterad identitets åtkomst till ditt nyckel valv om du inte redan har gjort det. Du kan också ange hemligheten direkt i följande format: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Om du använde skydds nivån **EncryptAllWithPassword** eller **EncryptSensitiveWithPassword** när du skapade paketet via SSDT anger du värdet för lösen ordet i rutan **krypterings lösen ord** . Du kan också använda en hemlighet som lagrats i Azure Key Vault som dess värde (se ovan).
      
      Om du har använt skydds nivån **EncryptSensitiveWithUserKey** , ange dina känsliga värden i konfigurationsfiler igen eller på fliken **SSIS-parametrar**, **anslutnings hanterare** eller **egenskaps åsidosättningar** (se nedan).
      
      Om du har använt skydds nivån **EncryptAllWithUserKey** stöds den inte. Du måste konfigurera om ditt paket så att det använder en annan skydds nivå via SSDT eller `dtutil` kommando rads verktyget. 

   1. För **loggnings nivå** väljer du ett fördefinierat loggnings område för paket körningen. Markera kryss rutan **anpassad** om du vill ange ett anpassat loggnings namn i stället. 
   
   1. Om du vill logga dina paket körningar utöver att använda de standard logg leverantörer som kan anges i ditt paket, anger du loggmappen genom att ange dess UNC-sökväg i rutan **loggnings Sök väg** . Du kan bläddra och välja din loggfil genom att välja **Bläddra i fil lagring** eller ange sökvägen manuellt. Om du till exempel lagrar loggarna i Azure Files är loggnings Sök vägen `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . En undermapp skapas i den här sökvägen för varje enskild paket körning, med namnet efter körnings-ID: t för aktiviteten kör SSIS-paket och där loggfilerna genereras var femte minut. 
   
   1. Ange de autentiseringsuppgifter som ska användas för att komma åt loggmappen genom att ange deras värden i rutorna **domän**, **användar namn** och **lösen ord** . Om du till exempel lagrar loggarna i Azure Files är domänen `Azure` , användar namnet `<storage account name>` och lösen ordet `<storage account key>` . Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden (se ovan).
   
För alla UNC-sökvägar som tidigare nämnts måste det fullständigt kvalificerade fil namnet innehålla färre än 260 tecken. Katalog namnet måste innehålla färre än 248 tecken.

#### <a name="ssis-parameters-tab"></a>Fliken SSIS-parametrar

Utför följande steg på fliken **SSIS-parametrar** för AKTIVITETEN kör SSIS-paket.

![Ange egenskaper på fliken SSIS-parametrar](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Om din Azure-SSIS IR körs, väljs **SSISDB** som paket plats och kryss rutan **manuella poster** på fliken **Inställningar** är avmarkerad. de befintliga SSIS-parametrarna i det valda projektet och paketet från SSISDB visas så att du kan tilldela värden till dem. Annars kan du ange dem en i taget för att tilldela dem värden manuellt. Kontrol lera att de finns och att de har angetts korrekt för att paket körningen ska lyckas. 
   
   1. Om du har använt skydds nivån **EncryptSensitiveWithUserKey** när du skapade ditt paket via SSDT och **fil system (paket)**, väljs **fil system (projekt)**, **inbäddat paket** eller **paket arkiv** som din paket plats, du måste också ange de känsliga parametrarna igen för att tilldela värden till dem på den här fliken. 
   
När du tilldelar värden till parametrarna kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, Data Factory systemvariabler och Data Factory-pipeline-parametrar eller variabler.

Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden. Det gör du genom att markera kryss rutan **Azure Key Vault** bredvid dem. Välj eller redigera din befintliga Key Vault-länkade tjänst eller skapa en ny. Välj sedan det hemliga namnet och versionen för ditt värde. När du skapar eller redigerar en länkad nyckel valvs tjänst kan du välja eller redigera ditt befintliga nyckel valv eller skapa ett nytt. Se till att bevilja Data Factory hanterad identitets åtkomst till ditt nyckel valv om du inte redan har gjort det. Du kan också ange hemligheten direkt i följande format: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Fliken anslutnings hanterare

Utför följande steg på fliken **anslutnings hanterare** för AKTIVITETEN kör SSIS-paket.

![Ange egenskaper på fliken anslutnings hanterare](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Om din Azure-SSIS IR körs, väljs **SSISDB** som paket plats och kryss rutan **manuella poster** på fliken **Inställningar** är avmarkerad, befintliga anslutnings hanterare i det valda projektet och paketet från SSISDB visas så att du kan tilldela värden till deras egenskaper. Annars kan du ange dem en i taget för att tilldela värdena till egenskaperna manuellt. Kontrol lera att de finns och att de har angetts korrekt för att paket körningen ska lyckas. 
   
      Du kan hämta rätt **omfång**, **namn** och **egenskaps** namn för anslutnings hanteraren genom att öppna paketet som innehåller det på SSDT. När paketet har öppnats väljer du relevant anslutnings hanterare för att visa namn och värden för alla egenskaper i fönstret **Egenskaper** för SSDT. Med den här informationen kan du åsidosätta värdena för egenskaperna för anslutnings hanteraren i körnings läge. 

      ![Hämta egenskaper för anslutnings hanteraren från SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Utan att ändra ditt ursprungliga paket på SSDT kan du till exempel konvertera lokala data flöden från lokala till lokala data som körs på SQL Server till data flöden från lokala till molnet som körs på SSIS IR i ADF genom att åsidosätta värdena för egenskaperna **ConnectByProxy**, **ConnectionString** och **ConnectUsingManagedIdentity** i befintliga anslutnings hanterare vid körning.
      
      Dessa åsidosättningar för körning kan aktivera Self-Hosted IR (SHIR) som proxy för SSIS IR vid åtkomst till data lokalt, se [Konfigurera SHIR som proxy för SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)och Azure SQL Database/hanterade instans anslutningar med den senaste MSOLEDBSQL-drivrutinen som i sin tur aktiverar Azure Active Directory (AAD)-autentisering med ADM-hanterad identitet, se [Konfigurera AAD-autentisering med ADF-hanterad identitet för OLEDB-anslutningar](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication).

      ![Ange egenskaper från SSDT på fliken anslutnings hanterare](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Om du har använt skydds nivån **EncryptSensitiveWithUserKey** när du skapade ditt paket via SSDT och **fil system (paket)**, väljs **fil system (projekt)**, **inbäddat paket** eller **paket arkiv** som paket plats, du måste också ange känsliga egenskaper för anslutnings hanteraren igen för att tilldela värden till dem på den här fliken. 

När du tilldelar värden till egenskaperna för anslutnings hanteraren kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, Data Factory systemvariabler och Data Factory pipeline-parametrar eller variabler. 

Du kan också använda hemligheter som lagras i Azure Key Vault som deras värden. Det gör du genom att markera kryss rutan **Azure Key Vault** bredvid dem. Välj eller redigera din befintliga Key Vault-länkade tjänst eller skapa en ny. Välj sedan det hemliga namnet och versionen för ditt värde. När du skapar eller redigerar en länkad nyckel valvs tjänst kan du välja eller redigera ditt befintliga nyckel valv eller skapa ett nytt. Se till att bevilja Data Factory hanterad identitets åtkomst till ditt nyckel valv om du inte redan har gjort det. Du kan också ange hemligheten direkt i följande format: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Fliken åsidosättningar för egenskap

Utför följande steg på fliken **åsidosättningar** för att köra SSIS-paket.

![Ange egenskaper på fliken åsidosättningar för egenskap](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Ange Sök vägarna för de befintliga egenskaperna i det valda paketet ett i taget för att tilldela dem värden manuellt. Kontrol lera att de finns och att de har angetts korrekt för att paket körningen ska lyckas. Om du till exempel vill åsidosätta värdet för användar variabeln anger du dess sökväg i följande format: `\Package.Variables[User::<variable name>].Value` . 

      Du kan hämta rätt **egenskaps Sök väg** för alla paket egenskaper genom att öppna paketet som innehåller det på SSDT. När paketet har öppnats väljer du egenskapen kontroll flöde och **konfigurationer** i fönstret **Egenskaper** för SSDT. Välj sedan knappen med tre punkter **(...**) bredvid dess **konfigurations** egenskap för att öppna de **paket konfigurations organisatörer** som normalt används för att [skapa paket konfigurationer i paket distributions modellen](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations). 

      ![Hämta paket egenskaper från SSDT-Configurations-egenskap](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      I **Sorteraren för paket konfiguration** markerar du kryss rutan **Aktivera paket konfigurationer** och knappen **Lägg till...** för att öppna **paket konfigurations guiden**. 
      
      I **guiden paket konfiguration** väljer du alternativet **XML-konfigurationsfil** i list rutan **konfigurations typ** och anger konfigurations **Inställningar direkt** , anger namnet på konfigurations filen och klickar på knappen **Nästa >** . 

      ![Hämta paket egenskaper från SSDT-konfigurationer organisatör](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Slutligen väljer du de paket egenskaper vars sökväg du vill och **nästa >** knapp.  Nu kan du se, kopiera & klistra in de sökvägar för paket egenskaper som du vill ha och spara dem i konfigurations filen. Med den här informationen kan du åsidosätta värdena för alla paket egenskaper i körnings läge. 

      ![Hämta paket egenskaper från guiden SSDT-konfiguration](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Om du har använt skydds nivån **EncryptSensitiveWithUserKey** när du skapade ditt paket via SSDT och **fil system (paket)**, väljs **fil system (projekt)**, **inbäddat paket** eller **paket arkiv** som paket plats, du måste också ange egenskaperna för det känsliga paketet igen för att tilldela värden till dem på den här fliken. 
   
När du tilldelar värden till dina paket egenskaper kan du lägga till dynamiskt innehåll med hjälp av uttryck, funktioner, Data Factory systemvariabler och Data Factory pipeline-parametrar eller variabler.

Värdena som tilldelas i konfigurationsfiler och på fliken **SSIS Parameters** kan åsidosättas med hjälp av flikarna **anslutnings hanterare** eller **Property Overrides** . De värden som tilldelas på fliken **anslutnings hanterare** kan också åsidosättas med hjälp av fliken **åsidosättningar för egenskaper** .

Verifiera pipeline-konfigurationen genom att välja **Verifiera** i verktygsfältet. Om du vill stänga **verifierings rapporten för pipeline** väljer du **>>** .

Om du vill publicera pipelinen till Data Factory väljer du **publicera alla**. 

### <a name="run-the-pipeline"></a>Köra en pipeline
I det här steget utlöser du en pipeline-körning. 

1. Om du vill utlösa en pipeline-körning väljer du **Utlös** i verktygsfältet och väljer **Utlös nu**. 

   ![Utlös nu](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**. 

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du ser pipeline-körningen och dess status tillsammans med annan information, till exempel **körnings start** tiden. Välj **Uppdatera** för att uppdatera vyn.

   ![Pipelinekörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Välj länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). Du kan bara se en aktivitets körning eftersom pipelinen bara har en aktivitet. Det är aktiviteten kör SSIS-paket.

   ![Aktivitetskörningar](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Kör följande fråga mot SSISDB-databasen på SQL-servern för att kontrol lera att paketet körs. 

   ```sql
   select * from catalog.executions
   ```

   ![Verifiera paket körningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Du kan också få körnings-ID: t för SSISDB från utdata från pipeline-aktiviteten och använda ID: t för att kontrol lera utförliga körnings loggar och fel meddelanden i SQL Server Management Studio.

   ![Hämta körnings-ID: t.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägg pipelinen med en utlösare

Du kan också skapa en schemalagd utlösare för din pipeline så att pipelinen körs enligt ett schema, till exempel varje timme eller varje dag. Ett exempel finns i [skapa en data fabrik – Data Factory användar gränssnitt](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Köra ett paket med PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa en Data Factory-pipeline med en kör SSIS-paket-aktivitet som kör ditt SSIS-paket. 

Installera de senaste Azure PowerShell modulerna genom att följa de stegvisa anvisningarna i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Skapa en data fabrik med Azure-SSIS IR
Du kan antingen använda en befintlig data fabrik som redan Azure-SSIS IR etablerad eller skapa en ny data fabrik med Azure-SSIS IR. Följ de stegvisa anvisningarna i [självstudien: Distribuera SSIS-paket till Azure via PowerShell](./tutorial-deploy-ssis-packages-azure-powershell.md).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Skapa en pipeline med en EXECUTE SSIS-paket-aktivitet 
I det här steget skapar du en pipeline med en aktivitet för att köra SSIS-paket. Aktiviteten Kör ditt SSIS-paket. 

1. Skapa en JSON-fil med namnet `RunSSISPackagePipeline.json` i `C:\ADF\RunSSISPackage` mappen med innehåll som liknar följande exempel.

   > [!IMPORTANT]
   > Ersätt objekt namn, beskrivningar och sökvägar, egenskaper, parameter värden, lösen ord och andra variabel värden innan du sparar filen. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Om du vill köra paket som lagras i fil systemet/Azure Files anger du värdena för egenskaperna för ditt paket och logg plats enligt följande:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Om du vill köra paket i projekt som lagras i fil systemet/Azure Files anger du värdena för paket platsens egenskaper enligt följande:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Om du vill köra inbäddade paket anger du värdena för paket platsens egenskaper enligt följande:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Om du vill köra paket som är lagrade i paket arkiv anger du värdena för dina paket-och konfigurations plats egenskaper enligt följande:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. I Azure PowerShell växlar du till `C:\ADF\RunSSISPackage` mappen.

3. Om du vill skapa pipelinen **RunSSISPackagePipeline** kör du cmdleten **set-AzDataFactoryV2Pipeline** .

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Här är exempel på utdata:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Köra en pipeline
Använd cmdleten **Invoke-AzDataFactoryV2Pipeline** för att köra pipelinen. Cmdleten samlar även in pipelinekörningens ID för kommande övervakning.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Övervaka pipeline

Kör följande PowerShell-skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar. Kopiera eller klistra in följande skript i PowerShell-fönstret och välj RETUR. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Du kan också övervaka pipelinen med hjälp av Azure Portal. Stegvisa instruktioner finns i [övervaka pipelinen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Schemalägg pipelinen med en utlösare
I föregående steg körde du pipelinen på begäran. Du kan också skapa en schema utlösare för att köra pipelinen enligt ett schema, till exempel varje timme eller varje dag.

1. Skapa en JSON-fil med namnet `MyTrigger.json` i `C:\ADF\RunSSISPackage` mappen med följande innehåll: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. I Azure PowerShell växlar du till `C:\ADF\RunSSISPackage` mappen.
1. Kör cmdleten **set-AzDataFactoryV2Trigger** som skapar utlösaren. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Som standard är utlösaren i stoppat läge. Starta utlösaren genom att köra cmdleten **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Bekräfta att utlösaren har startats genom att köra cmdleten **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Kör följande kommando efter nästa timma. Om den aktuella tiden till exempel är 3:25 PM, kör du kommandot på 4 PM UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Kör följande fråga mot SSISDB-databasen på SQL-servern för att kontrol lera att paketet körs. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Nästa steg
Se följande blogg inlägg:
- [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i Azure Data Factory pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)