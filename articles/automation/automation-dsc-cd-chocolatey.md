---
title: Konfigurera Azure Automation kontinuerlig distribution med choklad
description: Den här artikeln beskriver hur du konfigurerar kontinuerlig distribution med tillstånds konfiguration och choklad paket hanteraren.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 955e6b22c22d9cbe5891bcd0109806cb9270a456
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168662"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Konfigurera kontinuerlig distribution med Chocolatey

I en DevOps-värld finns det många verktyg för att hjälpa till med olika punkter i den kontinuerliga integrerings pipelinen. Azure Automation [tillstånds konfiguration](automation-dsc-overview.md) är ett välkomst tillägg till de alternativ som DevOps team kan använda. 

Azure Automation är en hanterad tjänst i Microsoft Azure som gör att du kan automatisera olika uppgifter med hjälp av Runbooks, noder och delade resurser, till exempel autentiseringsuppgifter, scheman och globala variabler. Azure Automation tillstånds konfiguration utökar denna automatiserings funktion som inkluderar DSC-verktyg (Desired State Configuration). Här är en bra [Översikt](automation-dsc-overview.md).

Den här artikeln visar hur du konfigurerar kontinuerlig distribution (CD) för en Windows-dator. Du kan enkelt utöka tekniken för att inkludera så många Windows-datorer som behövs i rollen, till exempel en webbplats och gå därifrån till ytterligare roller.

![Kontinuerlig distribution för virtuella IaaS-datorer](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>På hög nivå

Det är ganska lite här, men lyckligt vis kan den delas upp i två huvud processer:

- Skriva kod och testa den och sedan skapa och publicera installations paket för högre och lägre versioner av systemet.
- Skapa och hantera virtuella datorer som installerar och kör koden i paketen.  

När båda dessa kärn processer är på plats är det enkelt att automatiskt uppdatera paketet på dina virtuella datorer när nya versioner skapas och distribueras.

## <a name="component-overview"></a>Komponent översikt

Paket hanterare som [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) är välkända i Linux-världen, men inte så mycket i Windows-världen.
[Choklad](https://chocolatey.org/) är en sådan sak och Scott Hanselmans [blogg](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) i avsnittet är en bra introduktion. Med hjälp av en kortfattat så Jenkins kan du använda kommando raden för att installera paket från en central lagrings plats på ett Windows-operativsystem. Du kan skapa och hantera din egen lagrings plats och choklad kan installera paket från valfritt antal databaser som du anger.

[POWERSHELL DSC](/powershell/scripting/dsc/overview/overview) är ett PowerShell-verktyg som gör att du kan deklarera den konfiguration som du vill använda för en dator. Om du till exempel vill installera choklad, IIS installerat, port 80 öppnat och version 1.0.0 av din webbplats, implementerar den lokala DSC-Configuration Manager (LCM) konfigurationen. En DSC-pull-server innehåller en databas med konfigurationer för dina datorer. LCM på varje dator kontrollerar regelbundet för att se om konfigurationen matchar den lagrade konfigurationen. Den kan antingen rapportera status eller försöka återställa datorn till en justering med den lagrade konfigurationen. Du kan redigera den lagrade konfigurationen på hämtnings servern för att få en dator eller en uppsättning datorer att komma i justering med den ändrade konfigurationen.

En DSC-resurs är en modul med kod som har vissa funktioner, till exempel hantering av nätverk, Active Directory eller SQL Server. Den choklad DSC-resursen vet hur man kommer åt en NuGet-Server (bland annat), laddar ned paket, installerar paket och så vidare. Det finns många andra DSC-resurser i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Du installerar dessa moduler på din Azure Automation hämtnings Server för tillstånds konfiguration för användning av dina konfigurationer.

Resource Manager-mallar ger ett deklarativ sätt att skapa din infrastruktur, till exempel nätverk, undernät, nätverks säkerhet och routning, belastningsutjämnare, nätverkskort, virtuella datorer och så vidare. Här är en [artikel](../azure-resource-manager/management/deployment-models.md) som jämför Resource Manager-distributions modellen (deklarativ) med distributions modellen för Azure Service Management (ASM eller klassisk) (tvingande). Den här artikeln innehåller en diskussion om huvud resurs leverantörer: beräkning, lagring och nätverk.

En viktig funktion i en Resource Manager-mall är möjligheten att installera ett VM-tillägg i den virtuella datorn när det har tillhandahållits. Ett VM-tillägg har vissa funktioner, till exempel att köra ett anpassat skript, installera antivirus program och köra ett konfigurations skript för DSC. Det finns många andra typer av VM-tillägg.

## <a name="quick-trip-around-the-diagram"></a>Snabb resa runt diagrammet

Börja överst, Skriv koden, skapa den, testa den och skapa sedan ett installations paket. Choklad kan hantera olika typer av installations paket, till exempel MSI, MSU, ZIP. Och du har full kraften hos PowerShell för att utföra den faktiska installationen om det inte finns några inbyggda funktioner för choklad. Placera paketet på en plats som kan kommas åt – en paket lagrings plats. I det här användnings exemplet används en offentlig mapp i ett Azure Blob Storage-konto, men det kan vara var som helst. Choklad fungerar internt med NuGet-servrar och några andra för hantering av paketets metadata. I [den här artikeln](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) beskrivs alternativen. I användnings exemplet används NuGet. En nuspec är metadata om dina paket. Nuspec-informationen kompileras till en NuPkg som lagras på en NuGet-Server. När din konfiguration begär ett paket efter namn och refererar till en NuGet-Server, tar den choklad DSC-resursen på den virtuella datorn paketet och installerar det. Du kan också begära en speciell version av ett paket.

I det nedre vänstra hörnet av bilden finns en Azure Resource Manager-mall. I det här användnings exemplet registrerar VM-tillägget den virtuella datorn med hämtnings servern för Azure Automation tillstånds konfiguration som en nod. Konfigurationen lagras två gånger i pull-servern: en gång som oformaterad text och kompileras som en MOF-fil. I Azure Portal representerar MOF en Node-konfiguration, i stället för en enkel konfiguration. Det är den artefakt som är associerad med en nod så att noden känner till konfigurationen. Information nedan visar hur du tilldelar nodens konfiguration till noden.

Att skapa nuspec, kompilera den och lagra den i en NuGet-Server är en liten sak. Och du redan hanterar virtuella datorer. 

Om du tar nästa steg i kontinuerlig distribution måste du konfigurera pull-servern en gång, registrera dina noder med en gång och skapa och lagra den inledande konfigurationen på servern. När paket uppgraderas och distribueras till lagrings platsen behöver du bara uppdatera konfigurationen och nodens konfiguration på hämtnings servern vid behov.

Det är bra om du inte börjar med en Resource Manager-mall. Det finns PowerShell-kommandon som hjälper dig att registrera dina virtuella datorer med hämtnings servern. Mer information finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Om användnings exemplet

Användnings exemplet i den här artikeln börjar med en virtuell dator från en allmän Windows Server 2012 R2-avbildning från Azure-galleriet. Du kan starta från en lagrad avbildning och sedan ändra från DSC-konfigurationen.
Att ändra konfigurationen för bakade till en avbildning är dock mycket svårare än att dynamiskt uppdatera konfigurationen med DSC.

Du behöver inte använda en Resource Manager-mall och VM-tillägget för att använda den här tekniken med dina virtuella datorer. Och de virtuella datorerna behöver inte vara på Azure för att vara under CD-hantering. Allt som krävs är att choklad installeras och att LCM har kon figurer ATS på den virtuella datorn så att den vet var hämtnings servern är.

När du uppdaterar ett paket på en virtuell dator som är i produktion måste du ta den virtuella datorn ur rotationen medan uppdateringen är installerad. Hur du gör detta varierar mycket. Med en virtuell dator bakom en Azure Load Balancer kan du till exempel lägga till en anpassad avsökning. När du uppdaterar den virtuella datorn måste avsöknings slut punkten returnera en 400. Den justeringar som krävs för att göra den här ändringen kan finnas i konfigurationen, så att du kan ändra tillbaka till att returnera en 200 när uppdateringen är klar.

Fullständig källa för det här användnings exemplet är i [det här Visual Studio-projektet](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) på GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Steg 1: Konfigurera pull-servern och automation-kontot

På en autentiserad ( `Connect-AzAccount` ) PowerShell-kommando rad: (kan ta några minuter medan hämtnings servern har kon figurer ATS)

```azurepowershell-interactive
New-AzResourceGroup -Name MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount -ResourceGroupName MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES -Name MY-AUTOMATION-ACCOUNT
```

Du kan flytta ditt Automation-konto till någon av följande regioner (kallas även platser): östra USA 2, södra centrala USA, US Gov, Virginia, Västeuropa, Sydostasien, Östra Japan, centrala Indien och Australien, sydöstra, centrala Kanada, norra Europa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Steg 2: gör VM-tillägget till Resource Manager-mallen

Information om VM-registrering (med PowerShell DSC VM-tillägg) som tillhandahålls i den här [Azure snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Det här steget registrerar din nya virtuella dator med hämtnings servern i listan över noder för tillstånds konfiguration. En del av den här registreringen anger den Node-konfiguration som ska tillämpas på noden. Den här noden behöver inte finnas ännu i hämtnings servern, så det är bra att steg 4 är där detta görs för första gången. Men här i steg 2 måste du ha valt namnet på noden och namnet på konfigurationen. I det här användnings exemplet är noden "isvbox" och konfigurationen är "ISVBoxConfig". Konfigurations namnet för noden (som ska anges i DeploymentTemplate.jspå) är "ISVBoxConfig. isvbox".

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Steg 3: Lägg till nödvändiga DSC-resurser till hämtnings servern

PowerShell-galleriet instrumenteras för att installera DSC-resurser i ditt Azure Automation-konto.
Navigera till den resurs du vill använda och klicka på knappen "distribuera till Azure Automation".

![PowerShell-galleriet exempel](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

En annan teknik som nyligen lagts till i Azure Portal gör att du kan hämta nya moduler eller uppdatera befintliga moduler. Klicka via kontot för Automation-kontot, panelen till gångar och slutligen panelen moduler. Med ikonen Bläddra i galleriet kan du se en lista över moduler i galleriet, öka detalj nivån till information och slutligen importera till ditt Automation-konto. Detta är ett bra sätt att hålla dina moduler uppdaterade från tid till gång. Dessutom kontrollerar import funktionen beroenden med andra moduler för att se till att ingen blir osynkroniserade.

Det finns också en manuell metod som bara används en gång per resurs, om du inte vill uppgradera den senare. Mer information om hur du redigerar PowerShell-integrations moduler finns i [Redigera integrations moduler för Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Mappstrukturen för en PowerShell-integrering för en Windows-dator skiljer sig lite från mappstrukturen som förväntas av Azure Automation. 

1. Installera [Windows Management Framework V5](https://aka.ms/wmf5latest) (behövs inte för Windows 10).

2. Installera integrations modulen.

    ```azurepowershell-interactive
    Install-Module -Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Kopiera mappen module från **C:\Program Files\WindowsPowerShell\Modules\MODULE-Name** till en tillfällig mapp.

4. Ta bort exempel och dokumentation från huvudmappen.

5. Zippa huvudmappen, namnge ZIP-filen med namnet på mappen.

6. Sätt ZIP-filen i en nåbar HTTP-plats, till exempel Blob Storage i ett Azure Storage konto.

7. Kör följande kommando.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME -ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

Det inkluderade exemplet implementerar de här stegen för cChoco och xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Steg 4: Lägg till nodens konfiguration på hämtnings servern

Det finns inget särskilt om första gången du importerar konfigurationen till pull-servern och kompilerar. Alla senare importer eller kompileringar av samma konfiguration ser exakt likadana ut. Varje gång du uppdaterar ditt paket och behöver skicka ut det till produktion kan du göra det här steget när du har säkerställt att konfigurations filen är korrekt, inklusive den nya versionen av paketet. Här är konfigurations filen **ISVBoxConfig.ps1**:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Här är **New-ConfigurationScript.ps1** skriptet (ändrat för att använda modulen AZ):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published -Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

De här stegen resulterar i en ny Node-konfiguration med namnet **ISVBoxConfig. isvbox** som placeras på hämtnings servern. Konfigurations namnet för noden skapas som `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Steg 5: skapa och underhålla paketets metadata

För varje paket som du infogar i paket lagrings platsen behöver du en nuspec som beskriver det. Den måste kompileras och lagras på NuGet-servern. Den här processen beskrivs [här](https://docs.nuget.org/create/creating-and-publishing-a-package). 

Du kan använda **MyGet.org** som en NuGet-Server. Du kan köpa den här tjänsten, men Thee är en kostnads fri start-SKU. På [NuGet](https://www.nuget.org/)hittar du instruktioner om hur du installerar din egen NuGet-Server för dina privata paket.

## <a name="step-6-tie-it-all-together"></a>Steg 6: knyt ihop allt

Varje gången en version passerar frågor och svar och har godkänts för distribution, skapas paketet och nuspec och nupkg uppdateras och distribueras till NuGet-servern. Konfigurationen (steg 4) måste också uppdateras för att komma överens med det nya versions numret. Den måste sedan skickas till pull-servern och kompileras.

Från den tidpunkten är det upp till de virtuella datorer som är beroende av den konfigurationen för att hämta uppdateringen och installera den. Var och en av dessa uppdateringar är enkla – bara en rad eller två av PowerShell. För Azure DevOps är en del av dem inkapslade i bygg aktiviteter som kan sammanställas tillsammans i en version. Den här [artikeln](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) innehåller mer information. Den här [GitHub-lagrings platsen](https://github.com/Microsoft/vso-agent-tasks) innehåller information om tillgängliga build-uppgifter.

## <a name="related-articles"></a>Relaterade artiklar
* [Översikt över Azure Automation DSC](automation-dsc-overview.md)
* [Onboarding Machines for Management by Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Översikt över Azure Automation tillstånds konfiguration](automation-dsc-overview.md).
- För att komma igång med funktionen, se [Kom igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
