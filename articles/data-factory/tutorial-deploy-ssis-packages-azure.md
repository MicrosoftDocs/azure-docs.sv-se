---
title: Etablera Azure-SSIS integration runtime
description: Lär dig hur du etablerar Azure SSIS-integreringskörning i Azure Data Factory så att du kan distribuera och köra SSIS-paket i Azure.
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: e0b84bb65e71b023121a5deae8295ddaf8879311
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391362"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Etablera Azure-SSIS integration runtime i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här självstudien innehåller steg Azure Portal för att tillhandahålla en Azure-SQL Server Integration Services (SSIS) integration Runtime (IR) i Azure Data Factory (ADF). En Azure-SSIS IR stöder:

- Köra paket som distribuerats i SSIS-katalogen (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans (projekt distributions modell)
- Köra paket som distribuerats i fil systemet, Azure Files eller SQL Server databas (MSDB) som hanteras av Azure SQL-hanterad instans (paket distributions modell)

När en Azure-SSIS IR har tillhandahållits kan du använda välbekanta verktyg för att distribuera och köra dina paket i Azure. De här verktygen är redan Azure-aktiverade och innehåller SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) och kommando rads verktyg som [dtutil](/sql/integration-services/dtutil-utility) och [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

I den här självstudien går du igenom följande steg:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Etablering av en Azure-SSIS-integreringskörning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

- **Azure SQL Database Server (valfritt)**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Data Factory skapar i sin tur en SSISDB-instans på den här databas servern. 

  Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Med den här konfigurationen kan integrerings körningens Skriv körnings loggar i SSISDB utan att korsa Azure-regioner.

  Tänk på följande saker:

  - Baserat på den valda databas servern kan SSISDB-instansen skapas för din räkning som en enda databas, som en del av en elastisk pool eller i en hanterad instans. Det kan vara tillgängligt i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i [jämför SQL Database och SQL-hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Om du använder en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en IR med egen värd, måste du ansluta din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [skapa en Azure-SSIS IR i ett virtuellt nätverk](./create-azure-ssis-integration-runtime.md).

  - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Den här inställningen gäller inte när du använder en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB. Mer information finns i [skydda Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Om du vill aktivera den här inställningen med hjälp av PowerShell, se [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Lägg till IP-adressen för klient datorn eller ett intervall med IP-adresser som innehåller IP-adressen för klient datorn till klientens IP-adress lista i brand Väggs inställningarna för databas servern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../azure-sql/database/firewall-configure.md).

  - Du kan ansluta till databas servern genom att använda SQL-autentisering med dina autentiseringsuppgifter för Server administratören eller genom att använda Azure AD-autentisering med den hanterade identiteten för din data fabrik. För den senare måste du lägga till den hanterade identiteten för din data fabrik i en Azure AD-grupp med åtkomst behörighet till databas servern. Mer information finns i [skapa en Azure-SSIS IR med Azure AD-autentisering](./create-azure-ssis-integration-runtime.md).

  - Bekräfta att databas servern inte har någon SSISDB-instans redan. Etableringen av en Azure-SSIS IR stöder inte användning av en befintlig SSISDB-instans.

> [!NOTE]
> En lista över Azure-regioner där Data Factory och en Azure-SSIS IR för närvarande är tillgängliga finns i [Data Factory och SSIS IR-tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Om du vill skapa din data fabrik via Azure Portal följer du de stegvisa anvisningarna i [skapa en data fabrik via användar gränssnittet](./quickstart-create-data-factory-portal.md#create-a-data-factory). Välj **Fäst på instrument panelen** när du gör det, för att tillåta snabb åtkomst efter att den har skapats. 

När data fabriken har skapats öppnar du dess översikts sida i Azure Portal. Välj panelen **författare & Monitor** för att öppna sidan för att **komma igång** på en separat flik. Där kan du fortsätta att skapa Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Översikt från Data Factory

1. På sidan **Nu börjar vi** väljer du ikonen för att **konfigurera en SSIS-integreringskörning**. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

### <a name="from-the-authoring-ui"></a>Från redigerings gränssnittet

1. I Azure Data Factory användar gränssnitt växlar du till fliken **Redigera** och väljer **anslutningar**. Växla sedan till fliken **integrerings körningar** för att visa befintliga integrerings körningar i din data fabrik. 

   ![Alternativ för att visa befintliga integreringskörningar](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Välj **ny** för att skapa en Azure-SSIS IR och öppna **installations fönstret för integration runtime** . 

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. I installations fönstret för **integration runtime** väljer du de **befintliga SSIS-paketen lyft och Shift som ska köras i Azure** panel och väljer sedan **Nästa**.

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Etablering av en Azure-SSIS-integreringskörning

Installations fönstret för **integration runtime** har tre sidor där du konfigurerar allmänna, distribuera och avancerade inställningar i förväg.

### <a name="general-settings-page"></a>Sidan allmänna inställningar

Utför följande steg på sidan **allmänna inställningar** i installations fönstret för **integration runtime** . 

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. För **Namn**, ange namnet på din integreringsruntime. 

   1. För **beskrivning** anger du en beskrivning av integrationskörningen. 

   1. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas. Vi rekommenderar att du väljer samma plats för din databasserver som värd för SSISDB. 

   1. För **Nodstorlek** välj storleken på noderna i ditt integreringskörningskluster. Endast nodstorlekar som stöds visas. Välj en stor Node-storlek (skala upp) om du vill köra många beräknings intensiva eller minnes intensiva paket. 

   1. För **nodantal** välj antalet noder i ditt integreringskörningskluster. Endast nodantal som stöds visas. Välj ett stort kluster med många noder (skala ut) om du vill köra många paket parallellt. 

   1. För **utgåva/licens** väljer du SQL Servers versionen för integration Runtime: standard eller Enterprise. Välj företag om du vill använda avancerade funktioner i integrerings körningen. 

   1. För **Spara pengar** väljer du alternativet Azure Hybrid-förmån för integration Runtime: **Ja** eller **Nej**. Välj **Ja** om du vill ta med din egen SQL Server-licens med Software Assurance för att dra nytta av kostnads besparingar med hybrid användning. 

   1. Välj **Nästa**. 

### <a name="deployment-settings-page"></a>Sidan distributions inställningar

På sidan **distributions inställningar** i **installations fönstret för integration runtime** har du möjlighet att skapa SSISDB och eller Azure-SSIS IR paket arkiv.

#### <a name="creating-ssisdb"></a>Skapar SSISDB

På sidan **distributions inställningar** i **installations fönstret för integration runtime** , om du vill distribuera paketen till SSISDB (projekt distributions modell), markerar du kryss rutan **skapa SSIS-katalog (SSISDB) som är värd för Azure SQL Database Server/hanterad instans för att lagra dina projekt/paket/miljöer/körnings loggar** . Alternativt kan du, om du vill distribuera paketen till fil systemet, Azure Files eller SQL Server databas (MSDB) som hanteras av Azure SQL-hanterad instans (paket distributions modell), behöver inte skapa SSISDB eller markera kryss rutan.

Oavsett distributions modell kan du, om du vill använda SQL Server Agent som hanteras av en Azure SQL-hanterad instans för att dirigera/schemalägga dina paket körningar, aktive ras av SSISDB, så Markera kryss rutan ändå. Mer information finns i [Schemalägga SSIS-paket körningar via Azure SQL-hanterad instans agent](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Om du markerar kryss rutan utför du följande steg för att ta med din egen databas server som värd för SSISDB som vi ska skapa och hantera för din räkning.

   ![Distributions inställningar för SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

   1. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning.

   1. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. 
   
      Baserat på den valda databas servern kan SSISDB-instansen skapas för din räkning som en enda databas, som en del av en elastisk pool eller i en hanterad instans. Det kan vara tillgängligt i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i [jämför SQL Database och SQL-hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Om du väljer en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en IR med egen värd, måste du ansluta din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [skapa en Azure-SSIS IR i ett virtuellt nätverk](./create-azure-ssis-integration-runtime.md).

   1. Markera kryss rutan **Använd Azure AD-autentisering med den hanterade identiteten för din ADF** för att välja autentiseringsmetod för din databas server som värd för SSISDB. Du väljer antingen SQL-autentisering eller Azure AD-autentisering med den hanterade identiteten för din data fabrik.

      Om du markerar kryss rutan måste du lägga till den hanterade identiteten för din data fabrik i en Azure AD-grupp med åtkomst behörighet till din databas server. Mer information finns i [skapa en Azure-SSIS IR med Azure AD-autentisering](./create-azure-ssis-integration-runtime.md).
   
   1. För **Administratörs användar namn** anger du användar namnet för SQL-autentisering för din databas server som värd för SSISDB. 

   1. För **Administratörs lösen ord** anger du lösen ordet för SQL-autentisering för din databas server som värd för SSISDB. 

   1. För **tjänst nivån katalog databas** väljer du tjänst nivå för din databas server som värd för SSISDB. Välj nivån Basic, standard eller Premium eller Välj ett namn för elastisk pool.

Välj **Testa anslutning** när det är tillämpligt och om det lyckas väljer du **Nästa**.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Skapa Azure-SSIS IR paket arkiv

På sidan **distributions inställningar** i **installations fönstret för integration runtime** , om du vill hantera dina paket som har distribuerats till msdb, File System eller Azure Files (paket distributions modell) med Azure-SSIS IR paket lager, väljer du kryss rutan **skapa paket lager för att hantera dina paket som har distribuerats i fil systemet/Azure Files/SQL Server databas (msdb) som hanteras av en Azure SQL-hanterad instans** .
   
Med Azure-SSIS IR Package Store kan du importera/exportera/ta bort/köra paket och övervaka/stoppa paket som körs via SSMS som liknar det [äldre SSIS-paket lagret](/sql/integration-services/service/package-management-ssis-service). Mer information finns i [Hantera SSIS-paket med Azure-SSIS IR paket lager](./azure-ssis-integration-runtime-package-store.md).
   
Om du markerar den här kryss rutan kan du lägga till flera paket lager i Azure-SSIS IR genom att välja **ny**. Ett paket lager kan däremot delas av flera Azure-SSIS IRs.

![Distributions inställningar för MSDB/File System/Azure Files](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

Utför följande steg i fönstret **Lägg till paket arkiv** .
   
   1. För **paket arkivets namn** anger du namnet på paket lagret. 

   1. För **länkad tjänst för paket arkiv** väljer du den befintliga länkade tjänsten som lagrar åtkomst informationen för fil system/Azure Files/Azure SQL-hanterad instans där dina paket distribueras eller skapar en ny genom att välja **ny**. Utför följande steg i fönstret **ny länkad tjänst** . 

      > [!NOTE]
      > Du kan använda antingen **Azure File Storage** -eller **fil system** länkade tjänster för att komma åt Azure Files. Om du använder **Azure File Storage** länkade tjänsten stöder Azure-SSIS IR paket lagret bara autentiseringsmetoden **Basic** (inte **konto nyckel** eller **SAS-URI**) för tillfället. Om du vill använda **grundläggande** autentisering på **Azure File Storage** länkade tjänsten kan du lägga till i `?feature.upgradeAzureFileStorage=false` URL: en för ADF-portalen i webbläsaren. Alternativt kan du använda **fil systemets** länkade tjänst för att komma åt Azure Files i stället. 

      ![Distributions inställningar för länkade tjänster](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. I **namn** anger du namnet på den länkade tjänsten. 
         
      1. För **Beskrivning** anger du en beskrivning av den länkade tjänsten. 
         
      1. För **typ** väljer du **Azure File Storage**, **Azure SQL-hanterad instans** eller **fil system**.

      1. Du kan ignorera **Connect via integration runtime** eftersom vi alltid använder Azure-SSIS IR för att hämta åtkomst informationen för paket arkiv.

      1. Om du väljer **Azure File Storage** slutför du följande steg. 

         1. För **Val av konto** väljer du **från Azure-prenumeration** eller **anger manuellt**.
         
         1. Om du väljer **från Azure-prenumeration** väljer du den **aktuella Azure-prenumerationen**, **lagrings konto namnet** och **fil resursen**.
            
         1. Om du väljer **ange manuellt** anger du `\\<storage account name>.file.core.windows.net\<file share name>` för **värd**, `Azure\<storage account name>` för **användar namn** och `<storage account key>` **lösen ord** eller väljer din **Azure Key Vault** där den lagras som en hemlighet.

      1. Om du väljer en **hanterad Azure SQL-instans** utför du följande steg. 

         1. Välj **anslutnings sträng** för att ange den manuellt eller **Azure Key Vault** där den lagras som en hemlighet.
         
         1. Om du väljer **anslutnings sträng** utför du följande steg. 

            1. För **fullständigt kvalificerat domän namn** anger `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342` du eller som den privata eller offentliga slut punkten för din Azure SQL-hanterade instans. Om du anger den privata slut punkten gäller inte **test anslutningen** eftersom ADF-gränssnittet inte kan komma åt den.

            1. För **databas namn** anger du `msdb` .
               
            1. För **Autentiseringstyp** väljer du **SQL-autentisering**, **hanterad identitet** eller **tjänstens huvud namn**.

            1. Om du väljer **SQL-autentisering** anger du relevant **användar namn** och **lösen ord** , eller så väljer du **Azure Key Vault** där det lagras som en hemlighet.

            1. Om du väljer **hanterad identitet** ger du din ADF-hanterade identitets åtkomst till din Azure SQL-hanterade instans.

            1. Om du väljer **tjänstens huvud namn** anger du det relevanta **tjänstens huvud namn-ID** och **tjänstens huvud** namns nyckel eller väljer din **Azure Key Vault** där den lagras som en hemlighet.

      1. Om du väljer **fil system** anger du UNC-sökvägen till mappen där dina paket distribueras för **värden**, samt det relevanta **användar namnet** och **lösen ordet** eller väljer din **Azure Key Vault** där den lagras som en hemlighet.

      1. Välj **Testa anslutning** när det är tillämpligt och om det lyckas väljer du **skapa**.

   1. De tillagda paket arkiven visas på sidan **distributions inställningar** . Om du vill ta bort dem markerar du kryss rutorna och väljer sedan **ta bort**.

Välj **Testa anslutning** när det är tillämpligt och om det lyckas väljer du **Nästa**.

### <a name="advanced-settings-page"></a>Sida för avancerade inställningar

Utför följande steg på sidan **Avancerade inställningar** i installations fönstret för **integration runtime** . 

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. För **maximala parallella körningar per nod** väljer du det maximala antalet paket som ska köras samtidigt per nod i integration runtime-klustret. Endast paketnummer som stöds visas. Välj ett lågt nummer om du vill använda mer än en kärna för att köra ett enda stort paket som är beräknings-eller minnes krävande. Välj ett högt värde om du vill köra ett eller flera små paket i en enda kärna. 

   1. Markera kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** för att välja om du vill lägga till standard-/Express-anpassade inställningar på din Azure-SSIS IR. Mer information finns i [anpassad installation för en Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).
   
   1. Välj alternativet **Välj ett VNet för Azure-SSIS integration runtime att ansluta till, Tillåt ADF att skapa vissa nätverks resurser och om du vill ta med egna statiska offentliga IP-adresser** väljer du om du vill ansluta till Azure-SSIS IR till ett virtuellt nätverk.

      Välj den om du använder en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en lokal IR. Mer information finns i [skapa en Azure-SSIS IR i ett virtuellt nätverk](./create-azure-ssis-integration-runtime.md). 
   
   1. Markera kryss rutan **konfigurera Self-Hosted integration runtime som proxy för din Azure-SSIS integration runtime** för att välja om du vill konfigurera en egen IR som proxy för din Azure-SSIS IR. Mer information finns i [Konfigurera en egen värd för IR som proxy](./self-hosted-integration-runtime-proxy-ssis.md).   

   1. Välj **Fortsätt**. 

På sidan **Sammanfattning** i installations fönstret för **integration runtime** granskar du alla etablerings inställningar, bok märken rekommenderade dokumentations länkar och väljer **Slutför** för att starta skapandet av integration Runtime. 

   > [!NOTE]
   > Om du exkluderar en anpassad konfigurations tid bör den här processen avslutas inom 5 minuter.
   >
   > Om du använder SSISDB kommer tjänsten Data Factory att ansluta till din databas server för att förbereda SSISDB. 
   > 
   > När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, förutom de data källor som inbyggda komponenter redan stöder. Mer information om inbyggda/förinstallerade komponenter finns i [inbyggda/förinstallerade komponenter på Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Mer information om ytterligare komponenter som du kan installera finns i [anpassade inställningar för Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="connections-pane"></a>Fönstret anslutningar

I fönstret **anslutningar** i **Hantera** hubb växlar du till sidan **integrerings körningar** och väljer **Uppdatera**. 

   ![Fönstret anslutningar](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Du kan redigera/konfigurera om din Azure-SSIS IR genom att välja dess namn. Du kan också välja relevanta knappar för att övervaka/starta/stoppa/ta bort Azure-SSIS IR, generera en ADF-pipeline automatiskt med kör SSIS-paket-aktivitet för att köra på din Azure-SSIS IR och Visa JSON-koden/nytto lasten för dina Azure-SSIS IR.  Det går bara att redigera eller ta bort Azure-SSIS IR när den har stoppats.

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket

Om du använder SSISDB kan du distribuera dina paket till den och köra dem på din Azure-SSIS IR med hjälp av de Azure-aktiverade SSDT-eller SSMS-verktygen. Dessa verktyg ansluter till din databas server via dess server slut punkt: 

- För en Azure SQL Database Server är Server slut punktens format `<server name>.database.windows.net` .
- För en hanterad instans med privat slut punkt är Server slut punktens format `<server name>.<dns prefix>.database.windows.net` .
- För en hanterad instans med offentlig slut punkt är Server slut punktens format `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Om du inte använder SSISDB kan du distribuera dina paket i fil system, Azure Files eller MSDB som hanteras av din Azure SQL-hanterade instans och köra dem på Azure-SSIS IR med hjälp av kommando rads verktygen [dtutil](/sql/integration-services/dtutil-utility) och [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) . 

Mer information finns i [distribuera SSIS-projekt/-paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

I båda fallen kan du också köra dina distribuerade paket på Azure-SSIS IR med hjälp av aktiviteten kör SSIS-paket i Data Factory pipeliner. Mer information finns i [anropa SSIS-paket körning som en första klass Data Factory aktivitet](./how-to-invoke-ssis-package-ssis-activity.md).

Se även följande SSIS-dokumentation: 

- [Distribuera, köra och övervaka SSIS-paket i Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Ansluta till SSISDB i Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Schemalägga paket körningar i Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Nästa steg

Läs om hur du anpassar din Azure-SSIS-integreringskörning i följande artikel: 

> [!div class="nextstepaction"]
> [Anpassa en Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)