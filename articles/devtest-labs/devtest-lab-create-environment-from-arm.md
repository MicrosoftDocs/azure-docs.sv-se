---
title: Skapa miljöer med flera virtuella datorer och PaaS-resurser med mallar
description: Lär dig hur du skapar miljöer med flera virtuella datorer och PaaS-resurser i Azure DevTest Labs en Azure Resource Manager mall
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: f285acffe642a85fa27792ee51ea67a57f6d35a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790121"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar

Azure DevTest Labs miljöer gör det möjligt för användare att enkelt distribuera komplexa infrastrukturer på ett konsekvent sätt inom labbets begränsade delar. Du kan använda [Azure Resource Manager för](../azure-resource-manager/templates/template-syntax.md) att skapa miljöer med uppsättningar resurser i DevTest Labs. Dessa miljöer kan innehålla alla Azure-resurser som Resource Manager-mallar kan skapa.

Du kan enkelt [lägga till en virtuell dator (VM) i taget](devtest-lab-add-vm.md) i ett labb med hjälp av [Azure Portal](https://portal.azure.com). Scenarier som webbappar med flera nivåer eller en SharePoint-servergrupp behöver dock en mekanism för att skapa flera virtuella datorer i ett enda steg. Med hjälp Azure Resource Manager-mallar kan du definiera infrastrukturen och konfigurationen för din Azure-lösning och upprepade gånger distribuera flera virtuella datorer i ett konsekvent tillstånd.

Azure Resource Manager ger också följande fördelar:

- Azure Resource Manager-mallar läses in direkt från din GitHub- eller Azure Repos-källkontrolllagringsplats.
- Användarna kan skapa en miljö genom att välja en konfigurerad Azure Resource Manager mall från Azure Portal, precis som med andra typer av [VM-baser.](devtest-lab-comparing-vm-base-image-types.md)
- Du kan etablera Azure PaaS-resurser och virtuella IaaS-datorer i en miljö från en Azure Resource Manager mall.
- Du kan spåra kostnaden för miljöer i labbet, förutom enskilda virtuella datorer som skapats av andra typer av baser. PaaS-resurser skapas och visas i kostnadsspårningen. Automatisk avstängning av virtuella datorer gäller dock inte för PaaS-resurser.

Mer information om fördelarna med att använda Resource Manager-mallar för att distribuera, uppdatera eller ta bort många labbresurser i en enda åtgärd finns i Fördelar med att [använda Resource Manager mallar](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> När du använder en Resource Manager mall som bas för att skapa virtuella labbdatorn finns det några skillnader mellan att skapa flera virtuella datorer eller en enskild virtuell dator. Mer information finns i [Använda en virtuell dators mall Azure Resource Manager .](devtest-lab-use-resource-manager-template.md)
>

## <a name="use-devtest-labs-public-environments"></a>Använda offentliga DevTest Labs-miljöer
Azure DevTest Labs har en offentlig lagringsplats [med Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/Environments) som du kan använda för att skapa miljöer utan att behöva ansluta till en extern GitHub-källa själv. Den här offentliga lagringsplatsen liknar den offentliga lagringsplatsen för artefakter som är tillgängliga i Azure Portal för varje labb som du skapar. Med miljödatabasen kan du snabbt komma igång med förskapade miljömallar som har få indataparametrar. Dessa mallar ger dig en smidig kom igång-upplevelse för PaaS-resurser i labb.

På den offentliga lagringsplatsen har DevTest Labs-teamet och andra skapat och delat mallar som används ofta, till exempel Azure Web Apps, Service Fabric Cluster och en SharePoint Farm-utvecklingsmiljö. Du kan använda dessa mallar direkt eller anpassa dem efter dina behov. Mer information finns i [Konfigurera och använda offentliga miljöer i DevTest Labs.](devtest-lab-configure-use-public-environments.md) När du har skapat egna mallar kan du lagra dem på den här lagringsplatsen för att dela dem med andra eller konfigurera en egen Git-lagringsplats.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Skapa egna malldatabaser

Som en av de bästa metoderna med infrastruktur som kod och konfiguration som kod bör du hantera miljömallar i källkontroll. Azure DevTest Labs följer den här praxis och läser in alla Azure Resource Manager mallar direkt från dina GitHub- eller Azure Repos-lagringsplatsen. Därför kan du använda Resource Manager mallar i hela lanseringscykeln, från testmiljön till produktionsmiljön.

Det finns flera regler att följa för att organisera Azure Resource Manager mallar på en lagringsplats:

- Du måste ge huvudmallfilen *namnetazuredeploy.jspå*.

- Om du vill använda parametervärden som definierats i en parameterfil måste parameterfilen ha *namnetazuredeploy.parameters.jspå*.

  Du kan använda parametrarna `_artifactsLocation` och för att konstruera värdet `_artifactsLocationSasToken` parametersLink URI, så att DevTest Labs automatiskt kan hantera kapslade mallar. Mer information finns i [Distribuera kapslade Azure Resource Manager för testningsmiljöer.](deploy-nested-template-environments.md)

- Du kan definiera metadata för att ange mallens visningsnamn och beskrivning i en *filmetadata.jspå* enligt följande:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Viktiga Azure Resource Manager mallfiler](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Lägga till malldatabaser i labbet

När du har skapat och konfigurerat din lagringsplats kan du lägga till den i labbet med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** och sedan **DevTest Labs** i listan.
1. I listan över labb väljer du det labb du vill ha.
1. I labbets **översiktsfönster** väljer du **Konfiguration och principer**.

   ![Konfiguration och principer](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. I listan **Konfigurations-** och policyinställningar väljer **du Lagringsplatsen.** **Lagringsplatsen För offentliga** artefakter genereras automatiskt för alla labb och ansluter till den offentliga GitHub-lagringsplatsen [DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Om du vill lägga Azure Resource Manager malldatabasen väljer du **Lägg till**.

   ![Offentlig lagringsplatsen](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. I **fönstret Lagringsplatsen** anger du följande information:

   - **Namn:** Ange ett lagringsplatsnamn som ska användas i labbet.
   - **URL för Git-klon:** Ange URL:en för Git HTTPS-klonen från GitHub eller Azure Repos.
   - **Gren** (valfritt): Ange grennamnet för att komma åt Azure Resource Manager malldefinitioner.
   - **Personlig åtkomsttoken:** Ange den personliga åtkomsttoken som används för att få säker åtkomst till din lagringsplats.
     - Om du vill hämta din token från Azure Repos går du till din profil och **väljer**  >  **Användarinställningar**  >  **Säkerhetstoken för personlig åtkomst.**
     - Om du vill hämta din token från GitHub går du till din profil och väljer **Inställningar**  >  **Utvecklarinställningar**  >  **Personliga åtkomsttoken.**
   - **Mappsökvägar:** Ange mappsökvägen som är relativ till din Git-klonade URI för antingen artefaktdefinitionerna eller Azure Resource Manager malldefinitionerna.

1. Välj **Spara**.

   ![Lägg till ny lagringsplats](./media/devtest-lab-create-environment-from-arm/repo-values.png)

När du har lagt Azure Resource Manager en mall i labbet kan labbanvändarna skapa miljöer med hjälp av mallen.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurera åtkomstbehörigheter för labbanvändare

**Labbanvändarna** har rollen Läsare som standard, så de kan inte ändra resurserna i en miljöresursgrupp. De kan till exempel inte stoppa eller starta sina resurser.

Följ dessa steg om du **vill ge** labbanvändarna deltagarrollen så att de kan redigera resurserna i sina miljöer:

1. I fönstret [Azure Portal](https://portal.azure.com)väljer du Konfiguration **och principer** i fönstret Översikt för labbet **och** väljer sedan **Labbinställningar.**

1. I fönstret **Labbinställningar** väljer du **Deltagare** och sedan Spara **för** att bevilja skrivbehörighet till labbanvändare.

   ![Konfigurera åtkomstbehörighet för labbanvändare](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Nästa avsnitt går igenom hur du skapar miljöer från en Azure Resource Manager mall.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Skapa miljöer från mallar i Azure Portal

När du har lagt Azure Resource Manager en mall i labbet kan labbanvändarna skapa miljöer i Azure Portal följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster** och sedan **DevTest Labs** i listan.

1. I listan över labb väljer du det labb du vill ha.

1. På labbsidan väljer du Lägg **till**.

1. I **fönstret Välj en bas** visas de basavbildningar som du kan använda, Azure Resource Manager mallarna visas först. Välj den Azure Resource Manager mall som du vill använda.

   ![Välja en bas](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. I fönstret **Lägg till** anger du ett värde **för Miljönamn** som ska visas för miljöanvändare.

   Mallen Azure Resource Manager definierar resten av indatafälten. Om mallen som *azuredeploy.parameter.jspå* filen definierar standardvärden, visar indatafälten dessa värden.

   För parametrar av typen *säker sträng* kan du använda hemligheter från din Azure Key Vault. Mer information om hur du lagrar hemligheter i ett nyckelvalv och använder dem när du skapar labbresurser finns i [Lagra hemligheter i Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

   ![Lägg till fönster](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Följande parametervärden visas inte i indatafälten, även om mallen anger dem. I stället visar formuläret tomma indatafält där labbanvändarna måste ange värden när de skapar miljön.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Välj **Lägg** till för att skapa miljön.

   Miljön börjar etableras omedelbart, med statusen som visas i **listan Mina virtuella** datorer. Labbet skapar automatiskt en ny resursgrupp för att etablera alla resurser som definierats i Azure Resource Manager mallen.

1. När miljön har skapats väljer du  miljön i listan Mina virtuella datorer för att öppna resursgruppsfönstret och bläddra bland alla resurser som miljön har etablerat.

   ![Miljöresurser](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Du kan också expandera miljön om du bara vill visa listan över virtuella datorer som miljön har etablerat.

   ![Lista över mina virtuella datorer](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Välj någon av miljöerna för att visa tillgängliga åtgärder, till exempel tillämpa artefakter, koppla datadiskar, ändra tiden för automatisk avstängning med mera.

   ![Miljöåtgärder](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatisera miljöskapande med PowerShell

Det är möjligt att använda Azure Portal för att lägga till en enda miljö i ett labb, men när ett utvecklings- eller testscenario måste skapa flera miljöer är automatiserad distribution en bättre upplevelse.

Innan du fortsätter kontrollerar du att du har en Azure Resource Manager som definierar de resurser som ska skapas. [Lägg till och konfigurera mallen på en Git-lagringsplats](#configure-your-own-template-repositories)och [lägg till lagringsplatsen i labbet](#add-template-repositories-to-the-lab).

Följande exempelskript skapar en miljö i labbet. Kommentarerna hjälper dig att förstå skriptet bättre.

1. Spara följande PowerShell-exempelskript på hårddisken som *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Kör skriptet på följande sätt med dina specifika värden för SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (mapp i Git-lagringsplatsen) och EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Du kan också använda Azure CLI för att distribuera resurser med Resource Manager mallar. Mer information finns i Distribuera [resurser med Resource Manager-mallar och Azure CLI.](../azure-resource-manager/templates/deploy-cli.md)

> [!NOTE]
> Endast en användare med behörighet som labbägare kan skapa virtuella datorer från en Resource Manager mall med hjälp av Azure PowerShell. Om du vill automatisera skapandet av virtuella datorer med Resource Manager en mall och du bara har användarbehörigheter kan du använda CLI-kommandot [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Resource Manager begränsningar i DevTest Labs

Överväg följande begränsningar när du använder Resource Manager i DevTest Labs:

- Du kan inte skapa formler eller anpassade avbildningar från virtuella labb datorer som har skapats från en Resource Manager mall.

- De flesta principer utvärderas inte när du distribuerar Resource Manager mallar.

Du kan till exempel ha en labbprincip som en användare bara kan skapa fem virtuella datorer. En användare kan dock distribuera en Resource Manager som skapar dussintals virtuella datorer. Principer som inte utvärderas är:

  - Antal virtuella datorer per användare

  - Antal virtuella Premium-datorer per labbanvändare

  - Antal premiumdiskar per labbanvändare

## <a name="next-steps"></a>Nästa steg
- När du har skapat en virtuell dator kan du ansluta till den virtuella datorn **genom att välja** Anslut i den virtuella datorns hanteringsfönster.
- Visa och hantera resurser i en miljö genom att välja miljön i **listan Mina virtuella datorer** i labbet.
- Utforska de [Azure Resource Manager från Azure-snabbstartsmallgalleriet](https://github.com/Azure/azure-quickstart-templates).
