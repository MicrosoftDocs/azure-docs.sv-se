---
title: Anpassa installations programmet för en Azure-SSIS Integration Runtime
description: Den här artikeln beskriver hur du använder det anpassade installations gränssnittet för en Azure-SSIS Integration Runtime för att installera ytterligare komponenter eller ändra inställningar
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387741"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Anpassa installations programmet för en Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Du kan anpassa Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) i Azure Data Factory (ADF) via anpassade inställningar. Du kan lägga till egna steg under etableringen eller omkonfigurationen av din Azure-SSIS IR. 

Genom att använda anpassade installations program kan du ändra standard operativ konfigurationen eller miljön för Azure-SSIS IR. Om du till exempel vill starta ytterligare Windows-tjänster, spara autentiseringsuppgifter för fil resurser eller använda starkt kryptografiskt/mer säkert nätverks protokoll (TLS 1,2). Du kan också installera ytterligare komponenter, till exempel sammansättningar, driv rutiner eller tillägg, på varje nod i Azure-SSIS IR. De kan vara egna, öppen källkod eller komponenter från tredje part. Mer information om inbyggda/förinstallerade komponenter finns i [inbyggda/förinstallerade komponenter på Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).

Du kan göra anpassade inställningar på Azure-SSIS IR på något av två sätt: 
* **Standard anpassad installation med ett skript**: Förbered ett skript och dess associerade filer och ladda upp dem tillsammans till en BLOB-behållare i ditt Azure Storage-konto. Du anger sedan en signatur för delad åtkomst (SAS) Uniform Resource Identifier (URI) för din behållare när du konfigurerar eller konfigurerar om Azure-SSIS IR. Varje nod i Azure-SSIS IR laddar sedan ned skriptet och dess tillhör ande filer från din behållare och kör den anpassade installationen med förhöjd behörighet. När den anpassade installationen är slutförd laddar varje nod standardutdata för körning och andra loggar till din behållare.
* **Express anpassad installation utan skript**: kör några vanliga system konfigurationer och Windows-kommandon eller installera några populära eller rekommenderade ytterligare komponenter utan att använda några skript.

Du kan installera både kostnads fria (olicensierade) och betalda (licensierade) komponenter med standard-och Express-anpassade installationer. Om du är en oberoende program varu leverantör (ISV) kan du läsa mer i [utveckla betalda eller licensierade komponenter för Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> För att kunna dra nytta av framtida förbättringar rekommenderar vi att du använder v3 eller en senare serie av noder för Azure-SSIS IR med anpassad installation.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller endast för anpassade standardinställningar:

- Om du vill använda *gacutil.exe* i skriptet för att installera sammansättningar i GAC (Global Assembly Cache) måste du ange *gacutil.exe* som en del av din anpassade installation. Eller så kan du använda kopian som finns i *exempel* mappen i vår *offentliga för hands versions* behållare, se avsnittet **standard anpassade installations exempel** nedan.

- Om du vill referera till en undermapp i skriptet kan *msiexec.exe* inte använda `.\` notationen för att referera till rotmappen. Använd ett kommando som `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` i stället för `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- Administrativa resurser eller dolda nätverks resurser som skapas automatiskt av Windows stöds för närvarande inte på Azure-SSIS IR.

- IBM iSeries Access ODBC-drivrutinen stöds inte på Azure-SSIS IR. Du kan se installations fel under den anpassade installationen. Kontakta IBM-supporten om du behöver hjälp.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill anpassa din Azure-SSIS IR behöver du följande objekt:

- [En Azure-prenumeration](https://azure.microsoft.com/)

- [Etablera Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)

- [Ett Azure Storage-konto](https://azure.microsoft.com/services/storage/). Krävs inte för Express-anpassade installationer. För standard anpassade installations program laddar du upp och lagrar det anpassade installations skriptet och dess associerade filer i en BLOB-behållare. Den anpassade installations processen överför även körnings loggarna till samma BLOB-behållare.

## <a name="instructions"></a>Instruktioner

Du kan etablera eller konfigurera om Azure-SSIS IR med anpassade inställningar i ADF UI. Om du vill göra samma användning av PowerShell kan du hämta och installera [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Anpassad standard installation

Utför följande steg för att etablera eller konfigurera om din Azure-SSIS IR med standard anpassade inställningar i ADF-gränssnittet.

1. Förbered det anpassade installations skriptet och dess associerade filer (till exempel. bat,. cmd,. exe,. dll,. msi eller. ps1-filer).

   * Du måste ha en skript fil med namnet *main. cmd*, vilket är start punkten för den anpassade installationen.  
   * För att säkerställa att skriptet kan köras obevakat bör du testa det på den lokala datorn först.  
   * Om du vill att ytterligare loggar som genereras av andra verktyg (till exempel *msiexec.exe*) ska överföras till din behållare anger du den fördefinierade miljövariabeln, `CUSTOM_SETUP_SCRIPT_LOG_DIR` som loggmappen i dina skript (till exempel *msiexec/i xxx.msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Ladda ned, installera och öppna [Azure Storage Explorer](https://storageexplorer.com/).

   a. Under **(lokal och ansluten)** högerklickar du på **lagrings konton** och väljer sedan **Anslut till Azure Storage**.

      ![Ansluta till Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Välj **Använd ett lagrings konto namn och nyckel** och välj sedan **Nästa**.

      ![Använda lagringskontots namn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Ange namnet och nyckeln för ditt Azure Storage-konto, Välj **Nästa** och välj sedan **Anslut**.

      ![Ange lagrings kontots namn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Under ditt anslutna Azure Storage-konto högerklickar du på **BLOB-behållare**, väljer **skapa BLOB-behållare** och namnger den nya behållaren.

      ![Skapa en blobcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Välj den nya behållaren och ladda upp det anpassade installations skriptet och dess tillhör ande filer. Se till att ladda upp *main. cmd* på den översta nivån i din behållare, inte i någon mapp. Din behållare får bara innehålla nödvändiga anpassade installationsfiler, så det tar längre tid att ladda ned dem till din Azure-SSIS IR. Maximal varaktighet för en anpassad installation är för närvarande 45 minuter innan tids gränsen uppnåddes. Detta inkluderar tiden för att ladda ned alla filer från behållaren och installera dem på Azure-SSIS IR. Om installations programmet kräver mer tid kan du generera ett support ärende.

      ![Ladda upp filer till BLOB-behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Högerklicka på behållaren och välj sedan **Hämta signatur för delad åtkomst**.

      ![Hämta signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   ex. Skapa SAS-URI: n för din behållare med tillräckligt lång förfallo tid och med behörigheten Läs/skriv/lista. Du behöver SAS-URI: n för att ladda ned och köra det anpassade installations skriptet och dess tillhör ande filer. Detta inträffar när en nod i Azure-SSIS IR återställs eller startas om. Du måste också ha Skriv behörighet för att ladda upp konfigurations körnings loggar.

      > [!IMPORTANT]
      > Se till att SAS-URI: n inte upphör att gälla och att de anpassade installations resurserna alltid är tillgängliga under hela livs cykeln för din Azure-SSIS IR, från att skapas till borttagning, särskilt om du regelbundet stoppar och startar Azure-SSIS IR under den här perioden.

      ![Generera signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopiera och spara SAS-URI: n för din behållare.

      ![Kopiera och spara signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Markera kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** på sidan **Avancerade inställningar** i installations fönstret för **integration runtime** . Ange sedan SAS-URI: n för din behållare i text rutan **SAS-URI för anpassad installations behållare** .

   ![Avancerade inställningar med anpassade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

När den anpassade standard installationen slutförs och din Azure-SSIS IR startas kan du hitta alla anpassade installations loggar i mappen *main. cmd. log* i din behållare. De innehåller standardutdata av *main. cmd* och andra körnings loggar.

### <a name="express-custom-setup"></a>Express anpassad installation

Utför följande steg för att etablera eller konfigurera om Azure-SSIS IR med anpassade installations alternativ för ADF-gränssnittet.

1. Markera kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** på sidan **Avancerade inställningar** i installations fönstret för **integration runtime** . 

1. Välj **nytt** för att öppna fönstret **Lägg till Express anpassad installation** och välj sedan en typ i list rutan **snabb installation av snabb inställning** . Vi erbjuder för närvarande Express anpassade inställningar för att köra cmdkey-kommando, lägga till miljövariabler, installera Azure PowerShell och installera licensierade komponenter.

#### <a name="running-cmdkey-command"></a>Kör cmdkey-kommando

Om du väljer **Kör cmdkey-kommando** typen för den anpassade snabb installationen kan du köra Windows cmdkey-kommandot på Azure-SSIS IR. Det gör du genom att ange namnet på din dator eller ditt domän namn, användar namn, konto namn och lösen ord eller konto nyckel i text rutorna **/Add**, **/User** och **/pass** . Detta gör att du kan spara autentiseringsuppgifter för SQL-servrar, fil resurser eller Azure Files på Azure-SSIS IR. Om du till exempel vill komma åt Azure Files kan du ange `YourAzureStorageAccountName.file.core.windows.net` , `azure\YourAzureStorageAccountName` och `YourAzureStorageAccountKey` för **/Add**, **/User** respektive **/pass**. Detta påminner om att köra Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) -kommandot på den lokala datorn. Endast en Express anpassad installation för att köra cmdkey-kommandot stöds för tillfället. Om du vill köra flera cmdkey-kommandon använder du i stället en standard anpassad installation.

#### <a name="adding-environment-variables"></a>Lägga till miljövariabler

Om du väljer alternativet för att **lägga till miljövariabeln** för den anpassade snabb installationen kan du lägga till en Windows-miljövariabel på din Azure-SSIS IR. Det gör du genom att ange ditt namn och värde för din miljö variabel i text rutorna **variabel namn** och **variabel värde** . Detta gör att du kan använda miljövariabeln i dina paket som körs på Azure-SSIS IR, till exempel i skript komponenter/uppgifter. Detta påminner om att köra Windows [set](/windows-server/administration/windows-commands/set_1) -kommandot på den lokala datorn.

#### <a name="installing-azure-powershell"></a>Installerar Azure PowerShell

Om du väljer **installations Azure PowerShell** typen för den anpassade snabb installationen kan du installera AZ-modulen för PowerShell på din Azure-SSIS IR. Det gör du genom att ange AZ-modulens versions nummer (x. y. z) som du vill använda från en [lista över de som stöds](https://www.powershellgallery.com/packages/az). Detta gör att du kan köra Azure PowerShell-cmdletar/-skript i dina paket för att hantera Azure-resurser, till exempel [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md).

#### <a name="installing-licensed-components"></a>Installera licensierade komponenter

Om du väljer alternativet **Installera licensierad komponent** för den anpassade snabb installationen kan du välja en integrerad komponent från våra ISV-partner i list rutan **komponent namn** :

   * Om du väljer **uppgifts fabriks komponenten SentryOne** kan du installera [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) Suite-komponenter från SentryOne på din Azure-SSIS IR. Det gör du genom att ange den produkt licens nyckel som du har köpt från dem i förväg i text rutan **licens nyckel** . Den aktuella integrerade versionen är **2020.1.3**.

   * Om du väljer **oh22's-Hedda. I/o** -komponenten kan du installera [Hedda. IO](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) -datakvalitet/rengörings komponent från oh22 på din Azure-SSIS IR. För att göra det måste du köpa sin tjänst i förväg. Den aktuella integrerade versionen är **1.0.14**.

   * Om du väljer komponenten **oh22's SQLPhonetics.net** kan du installera [SQLPhonetics.net](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) Data Quality/Matching-komponenten från oh22 på din Azure-SSIS IR. Det gör du genom att ange den produkt licens nyckel som du har köpt från dem i förväg i text rutan **licens nyckel** . Den aktuella integrerade versionen är **1.0.45**.

   * Om du väljer komponenten **SSIS Integration Toolkit för KingswaySoft** kan du installera [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) Suite för kopplingar för CRM/ERP/marknadsföring/samarbets program, till exempel Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce marknadsförings moln osv. från KingswaySoft på din Azure-SSIS IR. Det gör du genom att ange den produkt licens nyckel som du har köpt från dem i förväg i text rutan **licens nyckel** . Den aktuella integrerade versionen är **2020,1**.

   * Om du väljer **KINGSWAYSOFT SSIS Productivity Pack** -komponenten kan du installera [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) Suite-komponenter från KingswaySoft på din Azure-SSIS IR. Det gör du genom att ange den produkt licens nyckel som du har köpt från dem i förväg i text rutan **licens nyckel** . Den aktuella integrerade versionen är **20,1**.

   * Om du väljer **Theobald program varans xtract** -komponent kan du installera [xtract](https://theobald-software.com/en/xtract-is/) -serien för SAP-system (ERP, s/4HANA, BW) från Theobald program vara på din Azure-SSIS IR. Det gör du genom att dra & släpp/Ladda upp den produkt licens fil som du har köpt från dem i förväg i indata-rutan för **licens filen** . Den aktuella integrerade versionen är **6.1.1.3**.

   * Om du väljer **integration service** -komponenten för AecorSoft kan du installera [integration service](https://www.aecorsoft.com/en/products/integrationservice) Suite för anslutnings program för SAP-och Salesforce-system från AecorSoft på din Azure-SSIS IR. Det gör du genom att ange den produkt licens nyckel som du har köpt från dem i förväg i text rutan **licens nyckel** . Den aktuella integrerade versionen är **3.0.00**.

   * Om du väljer SSIS-komponenten för **CDATA-standarden** kan du installera SSIS- [standardpaketet](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) för de mest populära komponenterna från CDATA, till exempel Microsoft SharePoint-kopplingar, på din Azure-SSIS IR. Det gör du genom att ange den produkt licens nyckel som du har köpt från dem i förväg i text rutan **licens nyckel** . Den aktuella integrerade versionen är **19,7354**.

   * Om du väljer **SSIS Extended Package** -komponenten för CDATA kan du installera [SSIS Extended Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) Suite för alla komponenter från CDATA, till exempel Microsoft Dynamics 365 Business Central-anslutningar och andra komponenter i deras **SSIS standard-paket**, på din Azure-SSIS IR. Det gör du genom att ange den produkt licens nyckel som du har köpt från dem i förväg i text rutan **licens nyckel** . Den aktuella integrerade versionen är **19,7354**. På grund av dess stora storlek, för att undvika installations tids gräns, kontrollerar du att Azure-SSIS IR har minst fyra processor kärnor per nod.

Dina anpassade installations alternativ kommer att visas på sidan **Avancerade inställningar** . Om du vill ta bort dem markerar du kryss rutorna och väljer sedan **ta bort**.

### <a name="azure-powershell"></a>Azure PowerShell

Utför följande steg för att etablera eller konfigurera om Azure-SSIS IR med anpassade inställningar med Azure PowerShell.

1. Om din Azure-SSIS IR redan har startat/körs stoppar du det först.

1. Du kan sedan lägga till eller ta bort anpassade inställningar genom att köra `Set-AzDataFactoryV2IntegrationRuntime` cmdleten innan du startar Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Vanliga anpassade installations exempel

Utför följande steg om du vill visa och återanvända några exempel på vanliga anpassade inställningar.

1. Anslut till vår offentliga för hands versions behållare med hjälp av Azure Storage Explorer.

   a. Under **(lokal och ansluten)** högerklickar du på **lagrings konton**, väljer **Anslut till Azure Storage**, väljer **Använd en anslutnings sträng eller en signatur-URI för delad åtkomst** och välj sedan **Nästa**.

      ![Ansluta till Azure Storage med signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Välj **Använd en SAS-URI** och ange följande SAS-URI i text rutan **URI** :

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Ange signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Välj **Nästa** och välj sedan **Anslut**.

   d. I den vänstra rutan väljer du den anslutna **publicpreview** -behållaren och dubbelklickar sedan på mappen *CustomSetupScript* . I den här mappen finns följande objekt:

      * En *exempel* mapp som innehåller en anpassad installation för att installera en grundläggande aktivitet på varje nod i Azure-SSIS IR. Aktiviteten gör ingenting men väntar i ett par sekunder. Mappen innehåller också en *Gacutil* -mapp vars hela innehåll (*gacutil.exe*, *gacutil.exe.config* och *1033\gacutlrc.dll*) kan kopieras som är till din behållare.

      * En *UserScenarios* -mapp som innehåller flera anpassade installations exempel från verkliga användar scenarier. Om du vill installera flera exempel på Azure-SSIS IR kan du kombinera deras anpassade Setup Script-filer (*main. cmd*) till en enda och ladda upp den med alla tillhör ande filer i din behållare.

        ![Innehåll i offentlig för hands versions behållare](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dubbelklicka på mappen *UserScenarios* för att hitta följande objekt:

      * En *.net FRAMEWORK 3,5* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera en tidigare version av .NET Framework på varje nod i Azure-SSIS IR. Den här versionen kan krävas av vissa anpassade komponenter.

      * En *BCP* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera SQL Server kommando rads verktyg (*MsSqlCmdLnUtils.msi*) på varje nod i Azure-SSIS IR. Ett av dessa verktyg är *BCP*(Mass kopierings program).

      * En mapp för *DNS-suffix* som innehåller ett anpassat installations skript (*main. cmd*) för att lägga till ditt eget DNS-suffix (till exempel *test.com*) till valfritt okvalificerat domän namn och omvandla det till ett fullständigt kvalificerat domän namn (FQDN) innan det används i DNS-frågor från din Azure-SSIS IR.

      * En *Excel* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera vissa C#-sammansättningar och-bibliotek på varje nod i Azure-SSIS IR. Du kan använda dem i skript uppgifter för att dynamiskt läsa och skriva Excel-filer. 
      
        Först laddar du ned [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) och [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)och laddar sedan upp dem tillsammans med *main. cmd* till din behållare. Alternativt, om du bara vill använda standard kopplingarna för Excel (anslutnings hanteraren, källa och mål), är den Access Redistributable som innehåller dem redan förinstallerad på din Azure-SSIS IR, så du behöver inte någon anpassad installation.
      
      * En *MySQL ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera MySQL ODBC-drivrutinerna på varje nod i Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutningar (anslutnings hanteraren, källa och mål) för att ansluta till MySQL-servern. 
     
        Börja [med att hämta de senaste 64-bitars-och 32-bitars versionerna av installations program för MySQL ODBC-drivrutinen](https://dev.mysql.com/downloads/connector/odbc/) (till exempel *mysql-connector-odbc-8.0.13-winx64.msi* och *mysql-connector-odbc-8.0.13-win32.msi*) och överför dem sedan tillsammans med *main. cmd* till din behållare.

      * En *Oracle Enterprise* -mapp som innehåller ett anpassat installations skript (*main. cmd*) och tyst installations konfigurations fil (*client. rsp*) för att installera Oracle-kopplingarna och OCI-drivrutinen på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda anslutnings hanteraren för Oracle, källa och mål för att ansluta till Oracle-servern. 
      
        Först laddar du ned Microsoft Connector v 5.0 för Oracle (*AttunitySSISOraAdaptersSetup.msi* och *AttunitySSISOraAdaptersSetup64.msi*) från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) och den senaste oracle-klienten (till exempel *winx64_12102_client.zip*) från [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html). Sedan laddar du upp dem tillsammans med *main. cmd* och *client. rsp* till din behållare. Om du använder TNS för att ansluta till Oracle måste du också hämta *Tnsnames. ora*, redigera det och ladda upp det till din behållare. På så sätt kan den kopieras till mappen Oracle-installation under installationen.

      * En *Oracle STANDARD ADO.net* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle ODP.net-drivrutinen på varje nod i Azure-SSIS IR. Med den här installationen kan du använda anslutnings hanteraren för ADO.NET, källa och mål för att ansluta till Oracle-servern. 
      
        Först [laddar du ned den senaste Oracle ODP.net-drivrutinen](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (till exempel *ODP.NET_Managed_ODAC122cR1.zip*) och laddar sedan upp den tillsammans med *main. cmd* i din behållare.
       
      * En *Oracle standard-ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle ODBC-drivrutinen på varje nod i Azure-SSIS IR. Skriptet konfigurerar också data källans namn (DSN). Med den här installationen kan du använda ODBC-anslutnings hanteraren, källa och mål eller Power Query anslutnings hanteraren och källa med ODBC-datakälla för att ansluta till Oracle-servern. 
      
        Börja med att hämta den senaste Oracle Instant-klienten (Basic-paket eller Basic lite-paket) och ODBC-paket och ladda sedan upp dem tillsammans med *main. cmd* till din behållare:
        * [Hämta 64-bitars paket](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-paket: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Basic lite-paket: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC-paket: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Hämta 32-bitars paket](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic-paket: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Basic lite-paket: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; ODBC-paket: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * En *Oracle standard OLEDB* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle OLEDB-drivrutinen på varje nod i Azure-SSIS IR. Med den här installationen kan du använda OLEDB anslutnings hanteraren, källa och mål för att ansluta till Oracle-servern. 
     
        Först [laddar du ned den senaste Oracle OLEDB-drivrutinen](https://www.oracle.com/partners/campaign/index-090165.html) (till exempel *ODAC122010Xcopy_x64.zip*) och laddar sedan upp den tillsammans med *main. cmd* i din behållare.

      * En *POSTGRESQL ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera POSTGRESQL ODBC-drivrutinerna på varje nod i Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren, källan och målet för att ansluta till PostgreSQL-servern. 
     
        Börja [med att hämta de senaste 64-bitars-och 32-bitars versionerna av POSTGRESQL ODBC-drivrutin installations](https://www.postgresql.org/ftp/odbc/versions/msi/) program (till exempel *psqlodbc_x64.msi* och *psqlodbc_x86.msi*) och ladda upp dem tillsammans med *main. cmd* till din behållare.

      * En *SAP BW* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera SAP .net connector-sammansättningen (*librfc32.dll*) på varje nod i Azure-SSIS IR Enterprise Edition. Med den här installationen kan du använda SAP BW anslutnings hanteraren, källan och målet för att ansluta till SAP BW-servern. 
      
        Först överför du 64-bitars-eller 32-bitars versionen av *librfc32.dll* från SAP-installationsmappen tillsammans med *main. cmd* till din behållare. Skriptet kopierar sedan SAP-sammansättningen till mappen *%windir%\syswow64* eller mappen *%windir%\System32* under installationen.

      * En *lagringsmapp* som innehåller ett anpassat installations skript (*main. cmd*) för att installera Azure PowerShell på varje nod i Azure-SSIS IR. Med den här installationen kan du distribuera och köra SSIS-paket som kör [Azure PowerShell-cmdlets/skript för att hantera Azure Storage](../storage/blobs/storage-quickstart-blobs-powershell.md). 
      
        Kopiera *main. cmd*, ett exempel *AzurePowerShell.msi* (eller Använd den senaste versionen) och *storage.ps1* till din behållare. Använd *PowerShell. dtsx* som en mall för dina paket. Paket mal len kombinerar en [hämtnings uppgift för Azure-Blob](/sql/integration-services/control-flow/azure-blob-download-task), som laddar ned ett ändrings Bart PowerShell-skript (*storage.ps1*) och en process för att [köra processer](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), som kör skriptet på varje nod.

      * En *TERADATA* -mapp som innehåller ett anpassat installations skript (*main. cmd*), dess associerade fil (*install. cmd*) och installations paket (*. msi*). De här filerna installerar Teradata-kopplingarna, Teradata Parallel transporter (TPT) API och ODBC-drivrutinen på varje nod i Azure-SSIS IR Enterprise Edition. Med den här installationen kan du använda Teradata anslutnings hanteraren, källa och mål för att ansluta till Teradata-servern. 
      
        Först [laddar du ned Teradata-verktyg och-verktyg 15. x zip-filen](http://partnerintelligence.teradata.com) (till exempel  *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) och laddar sedan upp den tillsammans med de tidigare nämnda *. cmd* -och *MSI* -filerna till din behållare.

      * En *TLS 1,2* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att använda stark kryptering och säkrare nätverks protokoll (TLS 1,2) på varje nod i Azure-SSIS IR. Skriptet inaktiverar även äldre SSL/TLS-versioner.

      * En *ZULU OPENJDK* -mapp som innehåller ett anpassat installations skript (*main. cmd*) och PowerShell-fil (*install_openjdk.ps1*) för att installera ZULU-OPENJDK på varje nod i Azure-SSIS IR. Med den här installationen kan du använda Azure Data Lake Store och flexibla fil anslutningar för att bearbeta ORC-och Parquet-filer. Mer information finns i [Azure Feature Pack för integration Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java). 
      
        Hämta först [de senaste Zulu-openjdk](https://www.azul.com/downloads/zulu/zulu-windows/) (till exempel *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) och ladda upp den tillsammans med *main. cmd* och *install_openjdk.ps1* till din behållare.

        ![Mappar i mappen användar scenarier](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Om du vill återanvända de här standard anpassade installations exemplen kopierar du innehållet i den valda mappen till din behållare.

1. När du etablerar eller omkonfigurerar ditt Azure-SSIS IR på ADF-gränssnittet, markerar du kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** på sidan **Avancerade inställningar** i installations fönstret för **integration runtime** . Ange sedan SAS-URI: n för din behållare i text rutan **SAS-URI för anpassad installations behållare** .
   
1. När du etablerar eller konfigurerar om din Azure-SSIS IR att använda Azure PowerShell stoppar du den om den redan har startats/körs, kör `Set-AzDataFactoryV2IntegrationRuntime` cmdleten med SAS-URI: n för din behållare som `SetupScriptContainerSasUri` parameter värde och startar sedan Azure-SSIS IR.

1. När den anpassade standard installationen slutförs och din Azure-SSIS IR startas kan du hitta alla anpassade installations loggar i mappen *main. cmd. log* i din behållare. De innehåller standardutdata av *main. cmd* och andra körnings loggar.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Enterprise-versionen av Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Utveckla betalda eller licensierade komponenter för Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
