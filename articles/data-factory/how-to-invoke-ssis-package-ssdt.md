---
title: Köra SSIS-paket från SSDT
description: Lär dig hur du kör SSIS-paket i Azure från SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: a455d547fa3db2fd6e963458a29c77f516112e18
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556519"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Köra SSIS-paket i Azure från SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs funktionen för SSIS-projekt (Azure-aktiverade SQL Server Integration Services) på SQL Server Data Tools (SSDT). Det gör att du kan utvärdera SSIS-paketens moln kompatibilitet och köra dem på Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF). Du kan använda den här funktionen för att testa dina befintliga paket innan du lyft & Shift/migrera dem till Azure eller för att utveckla nya paket som ska köras i Azure.

Med den här funktionen kan du koppla en nyligen skapad/befintlig Azure-SSIS IR till SSIS-projekt och sedan köra dina paket på den.  Vi stöder att köra paket som ska distribueras till SSIS-katalogen (SSISDB) som hanteras av din Azure SQL Database Server eller en hanterad instans i projekt distributions modellen. Vi stöder också att köra paket som ska distribueras till fil systemet/Azure Files/SQL Server Database (MSDB) som hanteras av din Azure SQL-hanterade instans i paket distributions modellen. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här funktionen laddar du ned och installerar de senaste SSDT med SSIS-projekts tillägg för Visual Studio (VS) [härifrån.](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) Du kan också hämta och installera de senaste SSDT som ett fristående installations program [härifrån.](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure – aktivera SSIS-projekt

### <a name="creating-new-azure-enabled-ssis-projects"></a>Skapa nya Azure-aktiverade SSIS-projekt

På SSDT kan du skapa nya Azure-aktiverade SSIS-projekt med hjälp av mallen för **Integration Services-projekt (Azure-aktiverad)** .

   ![Nytt Azure-aktiverat SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

När det Azure-aktiverade projektet har skapats uppmanas du att ansluta till SSIS i Azure Data Factory.

   ![Anslut Azure-SSIS IR-prompt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Om du vill ansluta till din Azure-SSIS IR direkt, se [ansluta till Azure-SSIS IR](#connectssisir) för mer information. Du kan också ansluta senare genom att högerklicka på noden projekt i Solution Explorer fönstret i SSDT för att visa en meny. Sedan väljer du kommandot **Anslut till SSIS i Azure Data Factory** objekt på **SSIS i Azure Data Factory** -undermenyn.

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure – aktivera befintliga SSIS-projekt

För befintliga SSIS-projekt kan du aktivera dem med Azure genom att följa dessa steg:

1. Högerklicka på noden projekt i Solution Explorer fönstret i SSDT för att visa en meny. Sedan väljer du det **Azure-aktiverade projekt** objektet i **SSIS i Azure Data Factory** -undermenyn för att starta **guiden Azure-aktiverad projekt**.

   ![Azure – aktivera befintligt SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. På sidan **Välj Visual Studio-konfiguration** väljer du din befintliga vs-konfiguration för att tillämpa paket körnings inställningar i Azure. Du kan också skapa en ny, om du inte redan har gjort det, se [skapa en ny vs-konfiguration](/visualstudio/ide/how-to-create-and-edit-configurations). Vi rekommenderar att du har minst två olika VS-konfigurationer för paket körningar i lokala miljöer och moln miljöer, så att du kan använda Azure för att aktivera ditt projekt mot moln konfigurationen. På så sätt kan du, om du har parameterstyrda ditt projekt eller paket, tilldela olika värden till ditt projekt eller paket parametrar vid körning baserat på de olika körnings miljöerna (antingen på din lokala dator eller i Azure). Se till exempel miljöer för att [Växla paket körning](#switchenvironment).

   ![Välj Visual Studio-konfiguration](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure – aktivera dina befintliga SSIS-projekt kräver att du anger att mål Server versionen ska vara den senaste versionen som stöds av Azure-SSIS IR. Azure-SSIS IR är för närvarande baserat på **SQL Server 2017**. Kontrol lera att dina paket inte innehåller ytterligare komponenter som inte stöds på SQL Server 2017. Se också till att alla kompatibla ytterligare komponenter också har installerats på Azure-SSIS IR via anpassade installationer, se [Anpassa din Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md). Klicka på **Nästa** om du vill fortsätta.

   ![Byt mål Server version](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Se [ansluta till Azure-SSIS IR](#connectssisir) för att slutföra anslutningen av projektet till Azure-SSIS IR.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Ansluta Azure-aktiverade projekt till SSIS i Azure Data Factory

Genom att ansluta dina Azure-aktiverade projekt till SSIS i ADF kan du ladda upp dina paket till Azure Files och köra dem på Azure-SSIS IR. Du kan göra det genom att följa dessa steg:

1. På sidan **SSIS på ADF-introduktionen** granskar du introduktionen och väljer knappen **Nästa** för att fortsätta.

   ![SSIS i ADF introduktion](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. På sidan **Välj SSIS IR på ADF** väljer du den befintliga ADF-filen och Azure-SSIS IR att köra paket. Du kan också skapa nya om du inte har några.
   - Välj den aktuella Azure-prenumerationen och ADF först om du vill välja din befintliga Azure-SSIS IR.
   - Om du väljer din befintliga ADF som inte har några Azure-SSIS IR väljer du knappen **skapa SSIS IR** för att skapa en ny på ADF-portalen. När du har skapat kan du gå tillbaka till den här sidan och välja nya Azure-SSIS IR.
   - Om du väljer din befintliga Azure-prenumeration som saknar ADF, väljer du knappen **skapa SSIS IR** för att starta **guiden skapa integration runtime**. I guiden kan du ange den angivna platsen och prefixet för att vi automatiskt ska skapa en ny Azure-resurs grupp, Data Factory och SSIS IR för din räkning, med namnet i följande mönster: **YourPrefix-RG/DF/IR-YourCreationTime**. När du har skapat kan du gå tillbaka till den här sidan och välja den nya ADF-filen och Azure-SSIS IR.

   ![Välj SSIS IR i ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. På sidan **välj Azure Storage** väljer du ditt befintliga Azure Storage-konto för att ladda upp paket till Azure Files. Du kan också skapa en ny om du inte har några.
   - Välj den aktuella Azure-prenumerationen först om du vill välja ditt befintliga Azure Storage-konto.
   - Om du väljer samma Azure-prenumeration som Azure-SSIS IR som inte har något Azure Storage konto, väljer du knappen **skapa Azure Storage** . Vi skapar automatiskt ett nytt namn för din räkning på samma plats som din Azure-SSIS IR, med namnet genom att kombinera ett prefix för ditt Azure-SSIS IR namn och dess skapelse datum. När du har skapat kan du gå tillbaka till den här sidan och välja ditt nya Azure Storage-konto.
   - Om du väljer en annan Azure-prenumeration som inte har något Azure Storage konto, väljer du knappen **skapa Azure Storage** för att skapa en ny på Azure Portal. När du har skapat kan du gå tillbaka till den här sidan och välja ditt nya Azure Storage-konto.

   ![Välj Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Välj knappen **Anslut** för att slutföra anslutningen av projektet till Azure-SSIS IR. Vi visar den valda Azure-SSIS IR och Azure Storage kontot under noden **länkade Azure-resurser** i Solution Explorer fönstret i SSDT. Vi uppdaterar också regelbundet och visar status för din Azure-SSIS IR där. Du kan hantera din Azure-SSIS IR genom att högerklicka på noden för att öppna en meny och sedan välja det **Start\Stop\Manage** -objekt som tar dig till ADF-portalen.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Utvärdera SSIS-project\packages för körningar i Azure

### <a name="assessing-single-or-multiple-packages"></a>Utvärdera enstaka eller flera paket

Innan du kör dina paket i Azure kan du utvärdera dem för att få fram möjliga problem med moln kompatibilitet. Detta inkluderar migration-Blocker och ytterligare information som du bör vara medveten om. 
-  Du har möjlighet att utvärdera enskilda paket en i taget eller alla paket på samma gång under ditt projekt.

   ![Utvärdera paket](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Utvärdera projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  I fönstret **utvärderings rapport** för SSDT kan du hitta alla potentiella kompatibilitetsproblem med molnet, var och en med en egen detaljerad beskrivning och rekommendation. Du kan också exportera utvärderings rapporten till en CSV-fil som kan delas med alla som bör minimera problemen. 

   ![Utvärderingsrapport](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Undertrycker utvärderings regler

När du är säker på att vissa potentiella problem med molnet inte är tillämpliga eller har lösts ordentligt i dina paket, kan du utelämna de relevanta bedömnings reglerna som ligger på ytan. Detta minskar bruset i de efterföljande utvärderings rapporterna.
-  Välj länken **Konfigurera undertrycks regel för utvärderings regel** i fönstret **utvärderings rapport** i SSDT för att visa fönstret **utvärderings regler för utvärderings regel** där du kan välja vilka bedömnings regler som ska undertryckas.

   ![Inställningar för undervisning av utvärderings regel](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Du kan också högerklicka på noden projekt i Solution Explorer fönstret i SSDT för att popup-menyn. Välj det **Azure-aktiverade Settings-** objektet i **SSIS i Azure Data Factory** -undermenyn för att visa ett fönster som innehåller dina projekt egenskaps sidor. Välj egenskapen för **ignorerade bedömnings regel-ID: n** i avsnittet **Azure-aktiverade inställningar** . Välj slutligen knappen med tre punkter (**...**) för att visa fönstret för att visa **undervisnings inställningar för utvärderings regel** där du kan välja vilka bedömnings regler som ska undertryckas.

   ![Azure-aktiverade inställningar](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Inställningar för undervisning av utvärderings regel via Azure-aktiverade inställningar](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Köra SSIS-paket i Azure

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Konfigurera Azure-aktiverade inställningar

Innan du kör dina paket i Azure kan du konfigurera dina Azure-aktiverade inställningar för dem. Du kan till exempel aktivera Windows-autentisering på Azure-SSIS IR för att komma åt lokala/molnbaserade data lager genom att följa dessa steg:

1. Högerklicka på noden projekt i Solution Explorer fönstret i SSDT för att visa en meny. Sedan väljer du det **Azure-aktiverade Settings-** objektet i **SSIS i Azure Data Factory** -menyn för att visa ett fönster som innehåller dina projekt egenskaps sidor.

   ![Azure-aktiverade inställningar](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Markera avsnittet **Aktivera Windows-autentisering** i **Azure-aktiverade inställningar** och välj sedan **True** i list menyn. Sedan väljer du egenskapen **autentiseringsuppgifter för Windows-autentisering** och väljer sedan knappen med tre punkter (**...**) för att popup-fönstret **autentiseringsuppgifter för Windows-autentisering** ska visas.

   ![Aktivera Windows-autentisering](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Ange dina autentiseringsuppgifter för Windows-autentisering. Om du till exempel vill komma åt Azure Files kan du ange `Azure` , `YourStorageAccountName` och `YourStorageAccountKey` för **domän**, **användar namn** och **lösen ord**.

   ![Autentiseringsuppgifter för Windows-autentisering](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Starta paket körningar

När du har anslutit dina Azure-aktiverade projekt till SSIS i ADF, bedömer deras moln kompatibilitet och minimerar eventuella problem kan du köra/testa dina paket på Azure-SSIS IR.
-  Välj **Start** -knappen i SSDT-verktygsfältet för att ta en meny. Välj sedan **Kör i Azure** -objektet.

   ![Kör i Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Du kan också högerklicka på noden paket i Solution Explorer fönstret i SSDT för att öppna en meny. Välj sedan **Kör paket i Azure** -objektet.

   ![Kör paket i Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Om du ska kunna köra dina paket i Azure måste du ha en Azure-SSIS IR, så om Azure-SSIS IR stoppas öppnas ett fönster i dialog rutan där du kan starta det. Om du exkluderar en anpassad konfigurations tid bör den här processen slutföras inom 5 minuter, men det kan ta cirka 20-30 minuter för Azure-SSIS IR att ansluta till ett virtuellt nätverk. När du har kört dina paket i Azure kan du stoppa Azure-SSIS IR för att hantera dess löpande kostnad genom att högerklicka på noden i Solution Explorers fönstret för SSDT för att öppna en meny och sedan välja **Start\Stop\Manage** -objektet som tar dig till ADF-portalen.

### <a name="using-execute-package-task"></a>Använda uppgiften Kör paket

Om dina paket innehåller uppgifter för att köra paket som refererar till underordnade paket som lagras på lokala fil system, följer du dessa ytterligare steg:

1. Ladda upp underordnade paket till Azure Files under samma Azure Storage konto som är anslutet till dina projekt och hämta sin nya Universal Naming Convention-sökväg (UNC), t. ex. `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. Ersätt fil Sök vägen för de underordnade paketen i fil anslutnings hanteraren för utföra paket aktiviteter med sin nya UNC-sökväg
   - Om din lokala dator som kör SSDT inte kan komma åt den nya UNC-sökvägen kan du ange den på panelen egenskaper i fil anslutnings hanteraren
   - Du kan också använda en variabel för fil Sök vägen för att tilldela rätt värde vid körning

Om dina paket innehåller uppgifter för att köra paket som refererar till underordnade paket i samma projekt krävs inga ytterligare steg.

### <a name="switching-package-protection-level"></a>Växlar paket skydds nivå

Körning av SSIS-paket i Azure stöder inte **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** -skydds nivåer. Om dina paket har kon figurer ATS för att använda dem kommer vi därför att konvertera dem tillfälligt till att använda **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** -skydds nivåer. Vi kommer också att generera krypterings lösen ord slumpmässigt när vi laddar upp dina paket till Azure Files för körningar på din Azure-SSIS IR.

> [!NOTE]
> Om dina paket innehåller uppgifter för att köra paket som refererar till underordnade paket som kon figurer ATS för att använda **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** -skydds nivåer, måste du manuellt konfigurera om de underordnade paketen för att använda **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** -skydds nivåer innan du kör dina paket.

Om dina paket redan har kon figurer ATS för att använda **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** -skydds nivåer behåller vi dem oförändrade. Vi kommer fortfarande att slumpmässigt generera krypterings lösen ord när vi laddar upp dina paket till Azure Files för körningar på din Azure-SSIS IR.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Växla paket körnings miljöer

Om du Parameterisera ditt projekt/paket i projekt distributions modellen kan du skapa flera VS-konfigurationer för att växla paket körnings miljöer. På så sätt kan du tilldela miljöbaserade värden till dina projekt-/paket parametrar vid körning. Vi rekommenderar att du har minst två olika VS-konfigurationer för paket körningar i lokala miljöer och moln miljöer, så att du kan använda Azure för att aktivera dina projekt mot moln konfigurationen. Här är ett steg-för-steg-exempel på hur du växlar paket körnings miljöer mellan din lokala dator och Azure:

1. Låt oss säga att paketet innehåller en fil system aktivitet som anger attributen för en fil. När du kör den på den lokala datorn anges attributen för en fil som lagras i det lokala fil systemet. När du kör den på din Azure-SSIS IR vill du att den ska ange attributen för en fil som lagras i Azure Files. Börja med att skapa en paket parameter av sträng typ och ge den **namnet fil Sök väg för att lagra** värdet för mål filens sökväg.

   ![Skapa paket parameter](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. På sidan **Allmänt** i fönstret för **Åtgärds hanteraren i fil systemet** Parameterisera du till **SourceVariable** -egenskapen i **käll anslutningen** med parametern **fil Sök väg** . 

   ![Anslutning till Parameterisera-källa](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Som standard har du en befintlig VS-konfiguration för paket körningar i den lokala miljön som heter **Development**. Skapa en ny VS-konfiguration för paket körningar i moln miljön med namnet **Azure**, se [skapa en ny vs-konfiguration](/visualstudio/ide/how-to-create-and-edit-configurations), om du inte redan har gjort det.

4. När du visar parametrarna för ditt paket väljer du knappen **Lägg till parametrar till konfigurationer** för att öppna fönstret **Hantera parameter värden** för ditt paket. Därefter tilldelar du olika värden för mål filens sökväg till parametern sökväg för fil **Sök** väg under **utvecklings** -och **Azure** -konfigurationerna.

   ![Tilldela parameter värden](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure – aktivera ditt projekt mot moln konfigurationen, se [Azure – aktivera befintliga SSIS-projekt](#azureenableproject), om du inte redan har gjort det. Konfigurera sedan Azure-aktiverade inställningar för att aktivera Windows-autentisering för din Azure-SSIS IR att få åtkomst till Azure Files, se [Konfigurera Azure-aktiverade inställningar](#azureenabledsettings)om du inte redan har gjort det.

6. Kör ditt paket i Azure. Du kan byta tillbaka din paket körnings miljö till den lokala datorn genom att välja **utvecklings** konfigurationen.

   ![Växla Visual Studio-konfiguration](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Använda paket konfigurations fil

Om du använder paketets konfigurationsfiler i paket distributions modellen kan du tilldela miljö egenskaper till dina paket egenskaper vid körning. Vi överför filerna automatiskt med dina paket till Azure Files för körningar på din Azure-SSIS IR.

### <a name="checking-package-execution-logs"></a>Kontrollerar paket körnings loggar

När du har startat paket körningen formaterar vi och visar loggen i **förlopps** fönstret för SSDT. För ett långvarigt paket uppdaterar vi regelbundet sina loggar med minuterna. Du kan omedelbart avbryta paket körningen genom att välja **stopp** knappen i SSDT-verktygsfältet. Du kan också tillfälligt hitta rå data för sina loggar i följande UNC-sökväg: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , men vi rensar det efter en dag.

## <a name="current-limitations"></a>Aktuella begränsningar

-  Azure-aktiverade SSDT stöder bara kommersiella/globala moln regioner och har inte stöd för statliga/nationella moln regioner för tillfället.

## <a name="next-steps"></a>Nästa steg

När du är nöjd med att köra dina paket i Azure från SSDT kan du distribuera och köra dem som kör SSIS-paket aktiviteter i ADF-pipeline, se [köra SSIS-paket som EXECUTE SSIS-paket aktiviteter i ADF-pipeline](./how-to-invoke-ssis-package-ssis-activity.md).