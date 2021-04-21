---
title: Konfigurera Azure Automation kontinuerlig distribution med Chocolatey
description: Den här artikeln beskriver hur du ställer in kontinuerlig distribution med State Configuration och Chocolatey-pakethanteraren.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 717561614a3e42995bbce6746839fd9b7cbca37e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834866"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Konfigurera kontinuerlig distribution med Chocolatey

I en DevOps-värld finns det många verktyg som hjälper till med olika punkter i pipelinen för kontinuerlig integrering. Azure Automation [State Configuration](automation-dsc-overview.md) är ett välkommen nytt tillägg till de alternativ som DevOps-team kan använda. 

Azure Automation är en hanterad tjänst i Microsoft Azure som gör att du kan automatisera olika uppgifter med hjälp av runbooks, noder och delade resurser, till exempel autentiseringsuppgifter, scheman och globala variabler. Azure Automation State Configuration utökar den här automatiseringsfunktionerna till att omfatta Verktyg för PowerShell Desired State Configuration (DSC). Här är en bra [översikt.](automation-dsc-overview.md)

Den här artikeln visar hur du ställer in kontinuerlig distribution (CD) för en Windows-dator. Du kan enkelt utöka tekniken till att inkludera så många Windows-datorer som behövs i rollen, till exempel en webbplats, och gå vidare till ytterligare roller.

![Kontinuerlig distribution för virtuella IaaS-datorer](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>På en hög nivå

Det är ganska mycket som händer här, men som tur är kan den delas upp i två huvudprocesser:

- Skriva kod och testa den och sedan skapa och publicera installationspaket för större och mindre versioner av systemet.
- Skapa och hantera virtuella datorer som installerar och kör koden i paketen.  

När båda dessa kärnprocesser är på plats är det enkelt att automatiskt uppdatera paketet på dina virtuella datorer när nya versioner skapas och distribueras.

## <a name="component-overview"></a>Komponentöversikt

Pakethanterare som [apt-get är](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) välkända i Linux-världen, men inte så mycket i Windows-världen.
[Chocolatey](https://chocolatey.org/) är något sådant, och Scott Hanselman har [en](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) bra introduktion till ämnet. Med Chocolatey kan du använda kommandoraden för att installera paket från en central lagringsplats på ett Windows-operativsystem. Du kan skapa och hantera din egen lagringsplats och Chocolatey kan installera paket från val annat antal databaser som du anger.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) är ett PowerShell-verktyg som gör att du kan deklarera den konfiguration som du vill använda för en dator. Om du till exempel vill installera Chocolatey, installera IIS, öppna port 80 och installera version 1.0.0 av din webbplats, implementerar DSC Local Konfigurationshanteraren (LCM) den konfigurationen. En DSC-pullserver innehåller en lagringsplats med konfigurationer för dina datorer. LCM på varje dator checkar in regelbundet för att se om dess konfiguration matchar den lagrade konfigurationen. Den kan antingen rapportera status eller försöka anpassa datorn till den lagrade konfigurationen igen. Du kan redigera den lagrade konfigurationen på pull-servern för att få en dator eller uppsättning datorer att anpassa sig till den ändrade konfigurationen.

En DSC-resurs är en kodmodul som har specifika funktioner, till exempel hantering av nätverk, Active Directory eller SQL Server. Chocolatey DSC-resursen vet hur du kommer åt en NuGet-server (bland annat), laddar ned paket, installerar paket och så vidare. Det finns många andra DSC-resurser i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Du installerar de här modulerna på Azure Automation State Configuration pull-servern för användning av dina konfigurationer.

Resource Manager-mallar är ett deklarativt sätt att generera din infrastruktur, till exempel nätverk, undernät, nätverkssäkerhet och routning, lastbalanserare, nätverkskort, virtuella datorer och så vidare. Här är en [artikel](../azure-resource-manager/management/deployment-models.md) som jämför distributionsmodellen Resource Manager (deklarativ) med Azure Service Management-distributionsmodellen (ASM eller klassisk) (imperativ). Den här artikeln innehåller en diskussion om kärnresursproviders: beräkning, lagring och nätverk.

En viktig funktion i en Resource Manager är möjligheten att installera ett VM-tillägg på den virtuella datorn när det etableras. Ett VM-tillägg har specifika funktioner, till exempel att köra ett anpassat skript, installera antivirusprogram och köra ett DSC-konfigurationsskript. Det finns många andra typer av VM-tillägg.

## <a name="quick-trip-around-the-diagram"></a>Snabb resa runt i diagrammet

Från och med längst upp skriver du koden, skapar den, testar den och skapar sedan ett installationspaket. Chocolatey kan hantera olika typer av installationspaket, till exempel MSI, MSU och ZIP. Och du har hela kraften hos PowerShell för att göra den faktiska installationen om Chocolateys inbyggda funktioner inte räcker till. Placera paketet på en plats som kan nås – en paketdatabas. Det här användningsexempel använder en offentlig mapp i ett Azure Blob Storage-konto, men det kan vara var som helst. Chocolatey fungerar inbyggt med NuGet-servrar och några andra för hantering av paketmetadata. [I den](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) här artikeln beskrivs alternativen. I användningsexempel används NuGet. En Nuspec är metadata om dina paket. Nuspec-informationen kompileras till en NuPkg och lagras på en NuGet-server. När konfigurationen begär ett paket efter namn och refererar till en NuGet-server hämtar Chocolatey DSC-resursen på den virtuella datorn paketet och installerar det. Du kan också begära en specifik version av ett paket.

Längst ned till vänster på bilden finns det en Azure Resource Manager mall. I det här användningsexempel registrerar VM-tillägget den virtuella datorn med Azure Automation State Configuration pull-server som en nod. Konfigurationen lagras två gånger på pull-servern: en gång som oformaterad text och när den kompileras som en MOF-fil. I Azure Portal representerar MOF en nodkonfiguration i stället för en enkel konfiguration. Det är artefakten som är associerad med en nod så att noden vet dess konfiguration. Informationen nedan visar hur du tilldelar nodkonfigurationen till noden.

Det är en liten sak att skapa Nuspec, kompilera den och lagra den på en NuGet-server. Och du hanterar redan virtuella datorer. 

För att ta nästa steg till kontinuerlig distribution måste du konfigurera pull-servern en gång, registrera noderna med den en gång och skapa och lagra den inledande konfigurationen på servern. När paketen uppgraderas och distribueras till lagringsplatsen behöver du bara uppdatera konfigurationen och nodkonfigurationen på pull-servern efter behov.

Om du inte börjar med en Resource Manager mall är det ok. Det finns PowerShell-kommandon som hjälper dig att registrera dina virtuella datorer med pull-servern. Mer information finns i [Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Om användningsexempel

Användningsexe exemplet i den här artikeln börjar med en virtuell dator från en generisk Windows Server 2012 R2-avbildning från Azure-galleriet. Du kan börja från valfri lagrad bild och sedan justera därifrån med DSC-konfigurationen.
Det är dock mycket svårare att ändra konfigurationen som ingår i en avbildning än att dynamiskt uppdatera konfigurationen med DSC.

Du behöver inte använda en mall Resource Manager VM-tillägget för att använda den här tekniken med dina virtuella datorer. Och dina virtuella datorer behöver inte finnas i Azure för cd-hantering. Allt som behövs är att Chocolatey installeras och LCM konfigureras på den virtuella datorn så att den vet var pull-servern finns.

När du uppdaterar ett paket på en virtuell dator som är i produktion måste du ta bort den virtuella datorn från rotationen medan uppdateringen installeras. Hur du gör detta varierar mycket. Om du till exempel har en virtuell dator bakom Azure Load Balancer kan du lägga till en anpassad avsökning. När du uppdaterar den virtuella datorn ska avsökningsslutpunkten returnera en 400. Anpassningen som krävs för att göra den här ändringen kan finnas i din konfiguration, och även anpassningen för att växla tillbaka den till att returnera 200 när uppdateringen är klar.

Fullständig källa för det här användningsexempel finns [i det här Visual Studio-projektet](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) på GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Steg 1: Konfigurera pull-servern och Automation-kontot

Vid en autentiserad ( `Connect-AzAccount` ) PowerShell-kommandorad: (kan ta några minuter medan pull-servern har ställts in)

```azurepowershell-interactive
New-AzResourceGroup -Name MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount -ResourceGroupName MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES -Name MY-AUTOMATION-ACCOUNT
```

Du kan placera ditt Automation-konto i någon av följande regioner (även kallat platser): USA, östra 2, USA, södra centrala, US Gov, Virginia, Europa, västra, Sydostasien, Japan, östra, Indien, centrala och Australien, sydöstra, Kanada, centrala och Europa, norra.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Steg 2: Gör justeringar i tillägget för virtuella datorer Resource Manager mallen

Information om VM-registrering (med hjälp av Tillägget för virtuella PowerShell DSC-datorer) som anges i den här [Azure-snabbstartsmallen.](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)
Det här steget registrerar den nya virtuella datorn med pull-servern i listan över State Configuration noder. En del av den här registreringen är att ange nodkonfigurationen som ska tillämpas på noden. Den här nodkonfigurationen behöver inte finnas ännu i pull-servern, så det är ok att steg 4 är där det görs för första gången. Men här i steg 2 behöver du ha bestämt namnet på noden och namnet på konfigurationen. I det här användningsexempel är noden "isvbox" och konfigurationen är "ISVBoxConfig". Därför är nodkonfigurationsnamnet (som ska anges DeploymentTemplate.jspå) ISVBoxConfig.isvbox.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Steg 3: Lägg till nödvändiga DSC-resurser till pull-servern

Den PowerShell-galleriet instrumenteras för att installera DSC-resurser i ditt Azure Automation konto.
Gå till den resurs du vill använda och klicka på knappen "Distribuera till Azure Automation".

![PowerShell-galleriet exempel](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

En annan teknik som nyligen lagts till i Azure Portal gör att du kan hämta in nya moduler eller uppdatera befintliga moduler. Klicka dig igenom Automation-kontoresursen, panelen Tillgångar och slutligen panelen Moduler. Med bläddringsgalleriikonen kan du se listan över moduler i galleriet, öka detaljgranskningen och slutligen importera dem till ditt Automation-konto. Det här är ett bra sätt att hålla dina moduler uppdaterade då och då. Importfunktionen kontrollerar även beroenden med andra moduler för att säkerställa att inget blir osynkront.

Det finns också en manuell metod som endast används en gång per resurs, såvida du inte vill uppgradera den senare. Mer information om hur du skapar PowerShell-integreringsmoduler finns [i Authoring Integration Modules for Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Mappstrukturen för en PowerShell-integreringsmodul för en Windows-dator skiljer sig lite från mappstrukturen som förväntas av Azure Automation. 

1. Installera [Windows Management Framework v5](https://aka.ms/wmf5latest) (behövs inte för Windows 10).

2. Installera integreringsmodulen.

    ```azurepowershell-interactive
    Install-Module -Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Kopiera modulmappen från **c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** till en tillfällig mapp.

4. Ta bort exempel och dokumentation från huvudmappen.

5. Zippa upp huvudmappen och namnge ZIP-filen med namnet på mappen.

6. Placera ZIP-filen på en nåbar HTTP-plats, till exempel bloblagring i ett Azure Storage konto.

7. Kör följande kommando.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME -ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

I det inkluderade exemplet implementeras de här stegen för cChoco och xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Steg 4: Lägg till nodkonfigurationen till pull-servern

Det är inget särskilt med första gången du importerar konfigurationen till pull-servern och kompilerar den. Alla senare importer eller kompileringar av samma konfiguration ser exakt likadana ut. Varje gång du uppdaterar paketet och behöver skicka det till produktion gör du det här steget när du har säkerställt att konfigurationsfilen är korrekt – inklusive den nya versionen av paketet. Här är konfigurationsfilen **somISVBoxConfig.ps1**:

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

Här är **New-ConfigurationScript.ps1** (har ändrats för att använda Az-modulen):

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

De här stegen resulterar i att en ny **nodkonfiguration med namnet ISVBoxConfig.isvbox** placeras på pull-servern. Nodkonfigurationsnamnet har skapats som `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Steg 5: Skapa och underhålla paketmetadata

För varje paket som du lägger till i paketdatabasen behöver du en Nuspec som beskriver det. Den måste kompileras och lagras på NuGet-servern. Den här processen beskrivs [här.](https://docs.nuget.org/create/creating-and-publishing-a-package) 

Du kan använda **MyGet.org** som en NuGet-server. Du kan köpa den här tjänsten, men du är en kostnadsfri start-SKU. På [NuGet](https://www.nuget.org/)hittar du anvisningar om hur du installerar din egen NuGet-server för dina privata paket.

## <a name="step-6-tie-it-all-together"></a>Steg 6: Koppla ihop allt

Varje gång en version passerar QA och godkänns för distribution skapas paketet och nuspec och nupkg uppdateras och distribueras till NuGet-servern. Konfigurationen (steg 4) måste också uppdateras för att godkänna det nya versionsnumret. Den måste sedan skickas till pull-servern och kompileras.

Från och med nu är det upp till de virtuella datorer som är beroende av den konfigurationen att hämta uppdateringen och installera den. Var och en av dessa uppdateringar är enkla – bara en rad eller två av PowerShell. För Azure DevOps är vissa av dem inkapslade i bygguppgifter som kan länkas ihop i en version. Den [här artikeln](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) innehåller mer information. Den [här GitHub-lagringsplatsen](https://github.com/Microsoft/vso-agent-tasks) innehåller information om tillgängliga bygguppgifter.

## <a name="related-articles"></a>Relaterade artiklar
* [Azure Automation DSC-översikt](automation-dsc-overview.md)
* [Registrering av datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Azure Automation State Configuration översikt.](automation-dsc-overview.md)
- Information om hur du kommer igång med funktionen finns [i Kom igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns i [Kompilera DSC-konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
- Prisinformation finns i [Azure Automation State Configuration priser.](https://azure.microsoft.com/pricing/details/automation/)
