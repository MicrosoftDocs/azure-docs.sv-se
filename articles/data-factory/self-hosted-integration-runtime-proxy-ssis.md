---
title: Konfigurera en integration runtime med egen värd som proxy för SSIS
description: Lär dig hur du konfigurerar en integration runtime med egen värd som en proxy för en Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/19/2020
ms.openlocfilehash: 5f393e95a7d468ea2c6130077e42b25b038e839d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376232"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Konfigurera en egen värd-IR som en proxy för en Azure-SSIS IR i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du kör SQL Server Integration Services-paket (SSIS) på en Azure-SSIS Integration Runtime (Azure-SSIS IR) i Azure Data Factory med en egen värd för integration Runtime (lokal IR) som kon figurer ATS som proxy. 

Med den här funktionen kan du komma åt data lokalt utan att behöva [ansluta dina Azure-SSIS IR till ett virtuellt nätverk](./join-azure-ssis-integration-runtime-virtual-network.md). Funktionen är användbar när företags nätverket har en konfiguration för komplex eller en princip som är för begränsad så att du kan mata in Azure-SSIS IR i den.

Den här funktionen delar upp din SSIS Data Flow-uppgift i två mellanlagrings uppgifter när så är tillämpligt: 
* **Lokal mellanlagrings uppgift**: den här aktiviteten kör data flödes komponenten som ansluter till ett lokalt data lager på din egen värd-IR. Den flyttar data från det lokala data lagret till ett mellanlagringsområde i Azure Blob Storage eller vice versa.
* **Cloud Staging-aktivitet**: den här aktiviteten kör data flödes komponenten som inte ansluter till ett lokalt data lager på din Azure-SSIS IR. Den flyttar data från mellanlagringsområdet i Azure Blob Storage till ett moln data lager eller vice versa.

Om din data flödes uppgift flyttar data från lokalt till molnet, kommer de första och andra mellanlagrings aktiviteterna att vara både lokala och mellanlagrings aktiviteter. Om din data flödes uppgift flyttar data från molnet till lokalt, kommer de första och andra mellanlagrings aktiviteterna att vara moln och de lokala mellanlagrings aktiviteterna. Om din data flödes uppgift flyttar data från lokalt till lokalt, kommer de första och andra mellanlagrings aktiviteterna att vara både lokala mellanlagrings aktiviteter. Om din data flödes uppgift flyttar data från molnet till molnet gäller inte den här funktionen.

Andra förmåner och funktioner för den här funktionen gör att du kan till exempel konfigurera din egen värd-IR i regioner som ännu inte stöds av en Azure-SSIS IR och tillåta den offentliga statiska IP-adressen för din egen värd-IR i brand väggen för dina data källor.

## <a name="prepare-the-self-hosted-ir"></a>Förbered IR med egen värd

Om du vill använda den här funktionen skapar du först en data fabrik och konfigurerar en Azure-SSIS IR. Om du inte redan har gjort det följer du anvisningarna i [Konfigurera en Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

Sedan konfigurerar du din egen värd-IR i samma data fabrik där Azure-SSIS IR har kon figurer ATS. För att göra det, se [skapa en IR med egen värd](./create-self-hosted-integration-runtime.md).

Slutligen kan du hämta och installera den senaste versionen av den lokala IR-versionen, samt ytterligare driv rutiner och körnings program, på din lokala dator eller virtuella Azure-dator (VM) enligt följande:
- Hämta och installera den senaste versionen av [IR med egen värd](https://www.microsoft.com/download/details.aspx?id=39717).
- Om du använder Object Linking and Embedding Database (OLEDB), Open Database Connectivity (ODBC) eller ADO.NET-anslutningar i dina paket kan du ladda ned och installera de relevanta driv rutinerna på samma dator där din egen värd-IR är installerad, om du inte redan har gjort det.  

  Om du använder den tidigare versionen av OLEDB-drivrutinen för SQL Server (SQL Server Native Client [SQLNCLI]) [hämtar du 64-bitars versionen](https://www.microsoft.com/download/details.aspx?id=50402).  

  Om du använder den senaste versionen av OLEDB-drivrutinen för SQL Server (MSOLEDBSQL) [hämtar du 64-bitars versionen](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Om du använder OLEDB/ODBC/ADO. NET-drivrutiner för andra databas system, till exempel PostgreSQL, MySQL, Oracle och så vidare, kan du hämta 64-bitars versionerna från sina webbplatser.
- Om du inte redan har gjort det kan du [Hämta och installera 64-bitars versionen av Visual C++ (VC) runtime](https://www.microsoft.com/download/details.aspx?id=40784) på samma dator där din egen värd för IR är installerad.

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>Aktivera Windows-autentisering för lokala mellanlagrings uppgifter

Om lokala mellanlagrings uppgifter på din egen värd-IR kräver Windows-autentisering [konfigurerar du SSIS-paketen så att de använder samma Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). 

Dina lokala mellanlagrings uppgifter kommer att anropas med det lokala IR-tjänstkontot (*NT SERVICE\DIAHostService*, som standard) och dina data lager kommer att få åtkomst till Windows Authentication-kontot. Båda kontona kräver att vissa säkerhets principer tilldelas till dem. På den lokala IR-datorn går du till lokala **säkerhets principer**  >  **lokala principer**  >  **tilldelning av användar rättigheter** och gör sedan följande:

1. Tilldela de *Justera minnes kvoterna för en process* och *Ersätt en token för processnivå* till det egna IR-tjänstkontot. Detta bör ske automatiskt när du installerar din egen värd-IR med standard tjänst kontot. Om den inte gör det tilldelar du dessa principer manuellt. Om du använder ett annat tjänst konto tilldelar du samma principer.

1. Tilldela Windows Authentication *-kontot inloggningen logga in som en tjänst* princip.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Förbereda Azure Blob Storage länkade tjänsten för mellanlagring

Om du inte redan har gjort det skapar du en länkad Azure Blob Storage-tjänst i samma data fabrik där Azure-SSIS IR har kon figurer ATS. Det gör du i [skapa en Azure Data Factory-länkad tjänst](./quickstart-create-data-factory-portal.md#create-a-linked-service). Se till att göra följande:
- För **data lager** väljer du **Azure Blob Storage**.  
- För **Anslut via integration runtime** väljer du **AutoResolveIntegrationRuntime** (inte din Azure-SSIS IR eller din egen IR-anslutning), eftersom vi använder standard Azure IR för att hämta autentiseringsuppgifter för Azure-Blob Storage.
- För **autentiseringsmetod** väljer du **konto nyckel**, **SAS-URI**, **tjänstens huvud namn** eller **hanterad identitet**.  

>[!TIP]
>Om du väljer **tjänstens huvud namns** metod ger du tjänstens huvud namn minst en roll för *Storage BLOB data Contributor* . Mer information finns i [Azure Blob Storage Connector](connector-azure-blob-storage.md#linked-service-properties). Om du väljer metoden för **hanterad identitet** ger du rätt roller för din ADM-hanterade identitet för att få åtkomst till Azure Blob Storage. Mer information finns i [komma åt Azure-Blob Storage att använda Azure Active Directory autentisering med ADM-hanterad identitet](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication).

![Förbereda Azure Blob Storage-länkad tjänst för mellanlagring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurera en Azure-SSIS IR med din egen värd-IR som proxy

När du har för berett din egen värd för IR och Azure Blob Storage-länkad tjänst för mellanlagring kan du nu konfigurera nya eller befintliga Azure-SSIS IR med den infraröda IR som en proxy i Data Factory-portalen eller-appen. Innan du gör det, om det befintliga Azure-SSIS IR redan körs, stoppar du det och startar sedan om det.

1. I installations fönstret för **integration runtime** hoppar du över avsnitten **allmänna inställningar** och **SQL-inställningar** genom att välja **Nästa**. 

1. I avsnittet **Avancerade inställningar** gör du följande:

   1. Markera kryss rutan **konfigurera Self-Hosted integration runtime som proxy för din Azure-SSIS integration runtime** . 

   1. I list rutan **egen värd integration runtime** väljer du den befintliga IR-filen med egen värd som proxy för Azure-SSIS IR.

   1. I list rutan för den **länkade tjänsten för mellanlagring** väljer du din befintliga Azure Blob Storage-länkade tjänst eller skapar en ny för mellanlagring.

   1. I rutan **mellanlagringsplats** anger du en BLOB-behållare i det valda Azure Blob Storage-kontot eller låter det vara tomt om du vill använda en standard för mellanlagring.

   1. Välj **Fortsätt**.

   ![Avancerade inställningar med en egen värd-IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Du kan också konfigurera din nya eller befintliga Azure-SSIS IR med den egen värdbaserade IR-filen som en proxy med PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Aktivera SSIS-paket för att ansluta via proxy

Genom att använda de senaste SSDT som antingen SSIS-projektets tillägg för Visual Studio eller ett fristående installations program kan du hitta en ny `ConnectByProxy` egenskap som har lagts till i anslutnings hanteraren för data flödes komponenter som stöds.
* [Ladda ned SSIS Projects-tillägget för Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Hämta det fristående installations programmet](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)   

När du skapar nya paket som innehåller data flödes aktiviteter med komponenter som ansluter till data lokalt, kan du aktivera den här egenskapen genom att ange den som *True* i rutan **Egenskaper** för relevanta anslutnings hanterare.

![Aktivera egenskapen ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Du kan också aktivera den här egenskapen när du kör befintliga paket, utan att behöva ändra dem manuellt en i taget.  Det finns två alternativ:
- **Alternativ A**: öppna, återskapa och distribuera om projektet som innehåller paketen med de senaste SSDT som ska köras på din Azure-SSIS IR. Du kan sedan aktivera egenskapen genom att ange den som *True* för relevanta anslutnings hanterare. När du kör paket från SSMS visas dessa anslutnings hanterare på fliken **anslutnings hanterare** i popup-fönstret **Kör paket** .

  ![Aktivera ConnectByProxy Egenskap2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Du kan också aktivera egenskapen genom att ställa in den på *Sant* för relevanta anslutnings hanterare som visas på fliken **anslutnings hanterare** i [kör SSIS-paket aktivitet](./how-to-invoke-ssis-package-ssis-activity.md) när du kör paket i Data Factory pipelines.
  
  ![Aktivera ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Alternativ B:** Distribuera om projektet som innehåller de paket som ska köras på din SSIS IR. Du kan sedan aktivera egenskapen genom att ange dess egenskaps Sök väg, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` och ange den till *True* som en åsidosättande egenskap på fliken **Avancerat** i popup-fönstret **Kör paket** när du kör paket från SSMS.

  ![Aktivera ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Du kan också aktivera egenskapen genom att ange dess egenskaps Sök väg, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` och ange den till *True* som en åsidosättande egenskap på fliken **åsidosättningar** för att [köra SSIS-paket](./how-to-invoke-ssis-package-ssis-activity.md) när du kör paket i Data Factory pipeliner.
  
  ![Aktivera ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Felsöka de lokala och molnbaserade mellanlagrings uppgifterna

På din egen värd-IR kan du hitta körnings loggarna i mappen *C:\ProgramData\SSISTelemetry* och körnings loggarna för lokala mellanlagrings uppgifter i mappen *C:\ProgramData\SSISTelemetry\ExecutionLog* .  Du hittar körnings loggarna för mellanlagrings aktiviteter i din SSISDB, angivna sökvägar för loggning eller Azure Monitor beroende på om du lagrar dina paket i SSISDB, aktiverar [Azure Monitor-integrering](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)osv. Du kan också hitta unika ID: n för lokala mellanlagrings uppgifter i körnings loggarna för aktiviteter i molnet. 

![Unikt ID för den första mellanlagrings uppgiften](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

Om du har höjt kund support ärenden kan du välja knappen **skicka loggar** på fliken **diagnostik** i **Microsoft integration runtime Configuration Manager** som är installerad på din egen värd-IR för att skicka de senaste drift-och körnings loggarna för oss att undersöka.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Fakturering för aktiviteter för lokal och molnbaserad mellanlagring

De lokala mellanlagrings aktiviteter som körs på din lokala IR-överföring faktureras separat, precis som alla data förflyttnings aktiviteter som körs på en IR med egen värd debiteras. Detta anges i artikeln om [prissättning för Azure Data Factory data pipelinen](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

De mellanlagrings aktiviteter som körs på dina Azure-SSIS IR faktureras inte separat, utan att Azure-SSIS IR som körs faktureras enligt vad som anges i artikeln om [Azure-SSIS IR prissättning](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="enable-custom3rd-party-components"></a>Aktivera komponenter för anpassade/tredje parter 

Följ dessa anvisningar om du vill aktivera dina anpassade/tredje parts komponenter för att komma åt data lokalt med hjälp av IR som proxy för Azure-SSIS IR:

1. Installera dina anpassade/tredje parts komponenter som riktar SQL Server 2017 på Azure-SSIS IR via [standard-/Express-inställningar](./how-to-configure-azure-ssis-ir-custom-setup.md).

1. Skapa följande register nycklar för DTSPath på IR med egen värd, om de inte redan finns:
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` Ange till `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` Ange till `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. Installera dina anpassade/tredje parts komponenter riktade mot SQL Server 2017 på egen värd-IR under DTSPath ovan och kontrol lera att installations processen:

   1. Skapar `<DTSPath>` , `<DTSPath>/Connections` , `<DTSPath>/PipelineComponents` och `<DTSPath>/UpgradeMappings` mappar om de inte redan finns.
   
   1. Skapar en egen XML-fil för tilläggs mappningar i `<DTSPath>/UpgradeMappings` mappen.
   
   1. Installerar alla sammansättningar som refereras av dina komponenter för anpassade/tredje parts komponenter i Global Assembly Cache (GAC).

Här följer några exempel på våra partner, [Theobald Software](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) och [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir), som har anpassat sina komponenter för att använda vår Express anpassade installation och egen värd-IR som proxy för Azure-SSIS IR.

## <a name="enforce-tls-12"></a>Aktivera TLS 1.2

Om du behöver använda starkt kryptografiskt/mer säkert nätverks protokoll (TLS 1,2) och inaktivera äldre SSL/TLS-versioner på din egen värd-IR kan du hämta och köra det *main. cmd* -skript som finns i mappen *CustomSetupScript/UserScenarios/TLS 1,2* i vår offentliga för hands versions behållare.  Med hjälp av [Azure Storage Explorer](https://storageexplorer.com/)kan du ansluta till vår offentliga för hands versions behållare genom att ange följande SAS-URI:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Aktuella begränsningar

- Endast data flödes komponenter som är inbyggda/förinstallerade på Azure-SSIS IR Standard Edition, förutom Hadoop/HDFS/DQS-komponenter, stöds för närvarande, se [alla inbyggda/förinstallerade komponenter på Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).
- Endast anpassade/tredje parts data flödes komponenter som är skrivna i förvaltad kod (.NET Framework) stöds för närvarande – de som skrivs i intern kod (C++) stöds inte för närvarande.
- Det finns för närvarande inte stöd för att ändra variabel värden i både lokala och molnbaserade mellanlagrings uppgifter.
- Att ändra variabel värden av typen Object i lokala mellanlagringsplatser visas inte i andra aktiviteter.
- *ParameterMapping* i OLEDB-källan stöds inte för tillfället. Som en lösning kan du använda *SQL-kommandot från variabeln* som *AccessMode* och använda *uttrycket* för att infoga variabler/parametrar i ett SQL-kommando. Som en illustration kan du se paketet *ParameterMappingSample. dtsx* som finns i mappen *SelfHostedIRProxy/begränsningar* i vår offentliga för hands versions behållare. Med hjälp av Azure Storage Explorer kan du ansluta till vår offentliga för hands versions behållare genom att ange SAS-URI: t ovan.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din egen värd-IR som proxy för din Azure-SSIS IR kan du distribuera och köra dina paket för att komma åt data lokalt som kör SSIS-paket aktiviteter i Data Factory pipelines. Mer information finns i [köra SSIS-paket som kör SSIS-paket aktiviteter i Data Factory pipelines](./how-to-invoke-ssis-package-ssis-activity.md).