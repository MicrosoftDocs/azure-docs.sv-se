---
title: Köra SQL Server Integration Services-paket (SSIS) med det Azure-aktiverade Dtexec-verktyget
description: Lär dig hur du kör SQL Server Integration Services-paket (SSIS) med det Azure-aktiverade Dtexec-verktyget.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 90c269435f8df2cb3e7aeedc8ff561693c831a09
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556485"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Köra SQL Server Integration Services-paket med Azure-aktiverat Dtexec-verktyget

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver kommando tolks verktyget för Azure-aktiverad Dtexec (AzureDTExec). Den används för att köra SQL Server Integration Services-paket (SSIS) på Azure-SSIS Integration Runtime (IR) i Azure Data Factory.

Det traditionella Dtexec-verktyget levereras med SQL Server. Mer information finns i [Dtexec-verktyget](/sql/integration-services/packages/dtexec-utility). De anropas ofta av tredje parts dirigering eller Scheduler, till exempel ActiveBatch och Control-M, för att köra SSIS-paket lokalt. 

Det moderna AzureDTExec-verktyget levereras med ett SQL Server Management Studio-verktyg (SSMS). Det kan också anropas av tredje parts Dirigerare eller Scheduler för att köra SSIS-paket i Azure. Det underlättar lyftning och förflyttning eller migrering av SSIS-paket till molnet. När du har migrerat kan du, om du vill fortsätta att använda tredje parts-Dirigerare eller schemaläggare i din dagliga verksamhet, nu anropa AzureDTExec i stället för Dtexec.

AzureDTExec kör paketen som kör SSIS-paket aktiviteter i Data Factory pipelines. Mer information finns i [köra SSIS-paket som Azure Data Factory-aktiviteter](./how-to-invoke-ssis-package-ssis-activity.md). 

AzureDTExec kan konfigureras via SSMS för att använda ett Azure Active Directory-program (Azure AD) som genererar pipelines i din data fabrik. Den kan också konfigureras för att komma åt fil system, fil resurser eller Azure Files där du lagrar dina paket. Baserat på de värden som du anger för sina anrops alternativ genererar AzureDTExec och kör en unik Data Factory pipeline med en aktivitet för att köra SSIS-paket. Att anropa AzureDTExec med samma värden för dess alternativ kör om den befintliga pipelinen.

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda AzureDTExec hämtar och installerar du den senaste versionen av SSMS, som är version 18,3 eller senare. Ladda ned den från [den här webbplatsen](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="configure-the-azuredtexec-utility"></a>Konfigurera AzureDTExec-verktyget
När du installerar SSMS på den lokala datorn installeras även AzureDTExec. Om du vill konfigurera inställningarna startar du SSMS med alternativet **Kör som administratör** . Välj sedan **verktyg**  >  **migrera till Azure**  >  **Konfigurera Azure-aktiverade Dtexec**.

![Konfigurera Azure-aktiverad Dtexec-menyn](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Den här åtgärden öppnar ett **AzureDTExecConfig** -fönster som måste öppnas med administratörs behörighet för att det ska kunna skriva till filen *AzureDTExec. Settings* . Om du inte har kört SSMS som administratör öppnas ett User Account Control-fönster (UAC). Ange ditt administratörs lösen ord för att öka dina privilegier.

![Konfigurera Azure-aktiverade Dtexec-inställningar](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

I fönstret **AzureDTExecConfig** anger du dina konfigurations inställningar enligt följande:

- **ApplicationId**: Ange den unika identifieraren för den Azure AD-app som du skapar med rätt behörigheter för att generera pipelines i din data fabrik. Mer information finns i [skapa en Azure AD-App och tjänstens huvud namn via Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).
- **AuthenticationKey**: Ange autentiseringsnyckel för din Azure AD-App.
- **TenantId**: Ange den unika identifieraren för Azure AD-klienten, under vilken din Azure AD-App skapas.
- **DataFactory**: Ange namnet på din data fabrik där unika pipelines med AKTIVITETEN kör SSIS-paket genereras baserat på värdena för de alternativ som visas när du anropar AzureDTExec.
- **IRName**: Ange namnet på Azure-SSIS IR i din data fabrik, där de paket som anges i deras Universal NAMING Convention (UNC)-sökväg körs när du anropar AzureDTExec.
- **PipelineNameHashStrLen**: ange längden på hash-strängarna som ska genereras från värdena för de alternativ som du anger när du anropar AzureDTExec. Strängarna används för att skapa unika namn för Data Factory pipelines som kör dina paket på Azure-SSIS IR. Det räcker vanligt vis en längd på 32 tecken.
- **ResourceGroup**: Ange namnet på den Azure-resurs grupp som data fabriken skapades i.
- **SubscriptionId**: Ange den unika identifieraren för den Azure-prenumeration som data fabriken skapades under.
- **LogAccessDomain**: Ange domänens autentiseringsuppgifter för att komma åt LOGGMAPPEN i UNC-sökvägen när du skriver loggfiler, vilket krävs när **LogPath** har angetts och **LogLevel** inte är **Null**.
- **LogAccessPassword**: Ange lösen ordets autentiseringsuppgifter för att komma åt LOGGMAPPEN i UNC-sökvägen när du skriver loggfiler, vilket krävs när **LogPath** har angetts och **LogLevel** inte är **Null**.
- **LogAccessUserName**: Ange användar namnet som autentiseringsuppgifter för att komma åt LOGGMAPPEN i UNC-sökvägen när du skriver loggfiler, vilket krävs när **LogPath** har angetts och **LogLevel** inte är **Null**.
- **LogLevel**: Ange det valda omfånget för loggning från fördefinierade **Null**-, **Basic**-, **verbose**-eller **prestanda** alternativ för dina paket körningar på Azure-SSIS IR.
- **LogPath**: ange UNC-sökvägen till loggmappen där loggfiler från paket körningarna på Azure-SSIS IR skrivs.
- **PackageAccessDomain**: Ange domänens autentiseringsuppgifter för att komma åt dina paket i UNC-sökvägen som anges när du anropar AzureDTExec.
- **PackageAccessPassword**: Ange lösen ordets autentiseringsuppgifter för att komma åt dina paket i UNC-sökvägen som anges när du anropar AzureDTExec.
- **PackageAccessUserName**: Ange användar namnets autentiseringsuppgifter för att komma åt dina paket i UNC-sökvägen som anges när du anropar AzureDTExec.

Om du vill lagra dina paket och loggfiler i fil system eller fil resurser lokalt ansluter du Azure-SSIS IR till ett virtuellt nätverk som är anslutet till ditt lokala nätverk så att det kan hämta dina paket och skriva dina loggfiler. Mer information finns i [ansluta ett Azure-SSIS IR till ett virtuellt nätverk](./join-azure-ssis-integration-runtime-virtual-network.md).

För att undvika att Visa känsliga värden som skrivs till filen *AzureDTExec. Settings* i oformaterad text kodar vi dem till strängar av base64-kodning. När du anropar AzureDTExec, avkodas alla base64-kodade strängar tillbaka till sina ursprungliga värden. Du kan skydda filen *AzureDTExec. Settings* ytterligare genom att begränsa de konton som har åtkomst till den.

## <a name="invoke-the-azuredtexec-utility"></a>Anropa verktyget AzureDTExec
Du kan anropa AzureDTExec i kommando tolken och ange relevanta värden för specifika alternativ i användnings fall scenariot.

Verktyget installeras på `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` . Du kan lägga till sökvägen till miljövariabeln PATH för att den ska anropas från var som helst.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

När du anropar AzureDTExec finns liknande alternativ som när du anropar Dtexec. Mer information finns i [Dtexec-verktyget](/sql/integration-services/packages/dtexec-utility). Här följer de alternativ som stöds för närvarande:

- **/F [Il]**: läser in ett paket som lagras i fil system, fil resurs eller Azure Files. Som värde för det här alternativet kan du ange UNC-sökvägen för paket filen i fil system, fil resurs eller Azure Files med tillägget. dtsx. Om UNC-sökvägen som anges innehåller ett blank steg, så omge hela sökvägen med citat tecken.
- **/Conf [igFile]**: anger en konfigurations fil för extrahering av värden från. Med det här alternativet kan du ange en körnings konfiguration för ditt paket som skiljer sig från det som anges i design läge. Du kan lagra olika inställningar i en XML-konfigurationsfil och sedan läsa in dem innan du kör paketet. Mer information finns i [SSIS Package Configurations](/sql/integration-services/packages/package-configurations). Om du vill ange värdet för det här alternativet använder du UNC-sökvägen för konfigurations filen i fil system, fil resurs eller Azure Files med dess dtsConfig-tillägg. Om UNC-sökvägen som anges innehåller ett blank steg, så omge hela sökvägen med citat tecken.
- **/Conn [et]**: anger anslutnings strängar för befintliga anslutnings hanterare i paketet. Med det här alternativet kan du ange körnings anslutnings strängar för befintliga anslutnings hanterare i ditt paket som skiljer sig från de som anges i design läge. Ange värdet för det här alternativet enligt följande: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` .
- **/Set**: åsidosätter konfigurationen av en parameter, variabel, egenskap, container, log Provider, uppräknare för uppräknare eller anslutning i ditt paket. Det här alternativet kan anges flera gånger. Ange värdet för det här alternativet enligt följande: `property_path;value` . `\package.variables[counter].Value;1`Åsidosätter till exempel värdet för `counter` variabeln som 1. Du kan använda guiden **paket konfiguration** för att hitta, kopiera och klistra in värdet `property_path` för objekt i ditt paket vars värde du vill åsidosätta. Mer information finns i [guiden paket konfiguration](/sql/integration-services/packages/legacy-package-deployment-ssis).
- **/De [crypt]**: Anger krypterings lösen ordet för ditt paket som har kon figurer ATS med  / skydds nivån EncryptAllWithPassword **EncryptSensitiveWithPassword** .

> [!NOTE]
> När du anropar AzureDTExec med nya värden för dess alternativ genereras en ny pipeline förutom alternativet **/de [kript]**.

## <a name="next-steps"></a>Nästa steg

När unika pipeliner med aktiviteten kör SSIS-paket genereras och körs när du anropar AzureDTExec, kan de övervakas på Data Factory-portalen. Du kan också tilldela Data Factory-utlösare till dem om du vill dirigera/schemalägga dem med hjälp av Data Factory. Mer information finns i [köra SSIS-paket som Data Factory-aktiviteter](./how-to-invoke-ssis-package-ssis-activity.md).

> [!WARNING]
> Den genererade pipelinen förväntas användas endast av AzureDTExec. Egenskaperna eller parametrarna kan ändras i framtiden, så du behöver inte ändra eller återanvända dem i något annat syfte. Ändringar kan bryta AzureDTExec. Om detta händer tar du bort pipelinen. AzureDTExec genererar en ny pipeline nästa gången den anropas.