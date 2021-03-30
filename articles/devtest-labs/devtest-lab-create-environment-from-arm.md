---
title: Skapa miljöer med flera virtuella datorer och PaaS-resurser med mallar
description: Lär dig att skapa miljöer med flera virtuella datorer och PaaS-resurser i Azure DevTest Labs från en Azure Resource Manager mall
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 97659d4ab95fdbe75460161d0ceed71a1cb5cf82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88182416"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar

Azure DevTest Labs miljöer gör det möjligt för användarna att enkelt distribuera komplexa infrastrukturer på ett konsekvent sätt i Labbets befinta förhållanden. Du kan använda [Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md) för att skapa miljöer med resurs uppsättningar i DevTest Labs. De här miljöerna kan innehålla alla Azure-resurser som Resource Manager-mallar kan skapa.

Du kan enkelt [lägga till en virtuell dator (VM) i](devtest-lab-add-vm.md) taget till ett labb med hjälp av [Azure Portal](https://portal.azure.com). Scenarier som webbappar med flera nivåer eller en SharePoint-grupp behöver dock en mekanism för att skapa flera virtuella datorer i ett enda steg. Genom att använda Azure Resource Manager-mallar kan du definiera infrastrukturen och konfigurationen för din Azure-lösning och flera gånger distribuera flera virtuella datorer i ett konsekvent tillstånd.

Azure Resource Manager mallar ger också följande fördelar:

- Azure Resource Manager mallar läses in direkt från din lagrings plats för GitHub eller Azure databaser-käll kontroll.
- Användarna kan skapa en miljö genom att välja en konfigurerad Azure Resource Manager-mall från Azure Portal, precis som med andra typer av [virtuella dator baser](devtest-lab-comparing-vm-base-image-types.md).
- Du kan etablera Azure PaaS-resurser samt virtuella IaaS-datorer i en miljö från en Azure Resource Manager-mall.
- Du kan spåra kostnaden för miljöer i labbet, förutom enskilda virtuella datorer som skapats av andra typer av baser. PaaS-resurser skapas och visas i kostnads uppföljning. Automatisk avstängning av den virtuella datorn gäller dock inte för PaaS-resurser.

Mer information om fördelarna med att använda Resource Manager-mallar för att distribuera, uppdatera eller ta bort många labb resurser i en enda åtgärd finns i [fördelar med att använda Resource Manager-mallar](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> När du använder en Resource Manager-mall som bas för att skapa virtuella labb datorer, finns det vissa skillnader mellan att skapa flera virtuella datorer eller en enskild virtuell dator. Mer information finns i [använda en virtuell dators Azure Resource Manager mall](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Använda DevTest Labs offentliga miljöer
Azure DevTest Labs har ett [offentligt lager av Azure Resource Manager mallar](https://github.com/Azure/azure-devtestlab/tree/master/Environments) som du kan använda för att skapa miljöer utan att behöva ansluta till en extern GitHub-källa själv. Den här offentliga lagrings platsen liknar den offentliga lagrings platsen för artefakter som är tillgängliga i Azure Portal för varje labb som du skapar. Med miljö lagringen kan du snabbt komma igång med förredigerade miljömallar som har några indataparametrar. Dessa mallar ger dig en smidig upplevelse för att komma igång med PaaS-resurser inom labb.

I det offentliga lagret har DevTest Labs-teamet och andra har skapat och delat mallar som används ofta, till exempel Azure Web Apps, Service Fabric kluster och en utvecklings miljö för SharePoint-grupper. Du kan använda dessa mallar direkt eller anpassa dem efter dina behov. Mer information finns i [Konfigurera och använda offentliga miljöer i DevTest Labs](devtest-lab-configure-use-public-environments.md). När du har skapat dina egna mallar kan du lagra dem i den här lagrings platsen för att dela dem med andra eller konfigurera en egen git-lagringsplats.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Skapa dina egna mall-databaser

Som en av de bästa metoderna med infrastruktur som kod och konfigurations-som-kod bör du hantera miljömallar i käll kontrollen. Azure DevTest Labs följer den här övningen och läser in alla Azure Resource Manager-mallar direkt från dina GitHub-eller Azure databaser-databaser. Därför kan du använda Resource Manager-mallar över hela versions cykeln, från test miljön till produktions miljön.

Det finns flera regler att följa för att organisera Azure Resource Manager mallar i en lagrings plats:

- Du måste ge Master Template-filen *azuredeploy.jspå*.

- Om du vill använda parameter värden som definierats i en parameter fil måste parameter filen ha namnet *azuredeploy.parameters.jspå*.

  Du kan använda parametrarna `_artifactsLocation` och `_artifactsLocationSasToken` för att skapa parametersLink URI-värdet, så att DevTest Labs automatiskt hanterar kapslade mallar. Mer information finns i [distribuera kapslade Azure Resource Manager mallar för test miljöer](deploy-nested-template-environments.md).

- Du kan definiera metadata för att ange mallens visnings namn och beskrivning i en fil med namnet *metadata.jspå*, enligt följande:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Nyckel Azure Resource Manager mallfiler](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Lägg till mallar i arbets Arkiv i labbet

När du har skapat och konfigurerat din lagrings plats kan du lägga till den i labbet med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
1. I listan med labb väljer du det labb du vill ha.
1. I labb **översikts** fönstret väljer du **konfiguration och principer**.

   ![Konfiguration och principer](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. I listan **konfiguration och principer** inställningar väljer du **Arkiv**. Den **offentliga artefakt lagrings platsen** -lagringsplatsen genereras automatiskt för alla labb och ansluter till den [offentliga GitHub-lagringsplatsen för DevTest Labs](https://github.com/Azure/azure-devtestlab).

1. Välj **Lägg till** för att lägga till din Azure Resource Manager mall-lagringsplats.

   ![Offentlig lagrings platsen](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. I fönstret **databaser** anger du följande information:

   - **Namn**: Ange ett namn på databasen som ska användas i labbet.
   - **Git-klon-URL**: Ange git https-klonings-URL: en från GitHub eller Azure databaser.
   - **Gren** (valfritt): Ange namnet på grenen för att komma åt dina definitioner för Azure Resource Manager mal len.
   - **Personlig** åtkomsttoken: Ange den personliga åtkomsttoken som används för säker åtkomst till lagrings platsen.
     - Om du vill hämta din token från Azure databaser väljer du **användar inställningar**  >  **säkerhet**  >  **personliga** åtkomsttoken under din profil.
     - För att hämta din token från GitHub, under din profil, väljer du **Inställningar inställningar** för  >  **utvecklare**  >  **personliga åtkomsttoken**.
   - **Mappsökvägar: Ange** den mappsökväg som är relativ till din git-klon-URI för antingen dina artefakt definitioner eller dina definitioner för Azure Resource Manager mal len.

1. Välj **Spara**.

   ![Lägg till ny lagrings plats](./media/devtest-lab-create-environment-from-arm/repo-values.png)

När du lägger till en Azure Resource Manager-mall i labbet kan dina labb användare skapa miljöer med hjälp av mallen.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurera åtkomst behörigheter för labb användare

Labb användare har rollen **läsare** som standard, så att de inte kan ändra resurserna i en miljö resurs grupp. De kan till exempel inte stoppa eller starta sina resurser.

Följ dessa steg om du vill ge dina labb användare **deltagar** rollen så att de kan redigera resurserna i sina miljöer:

1. I rutan [Azure Portal](https://portal.azure.com)väljer du **konfiguration och principer** i din labb **Översikt** och väljer sedan **labb inställningar**.

1. I rutan **labb inställningar** väljer du **deltagare** och väljer sedan **Spara** för att bevilja Skriv behörighet till labb användare.

   ![Konfigurera åtkomst behörighet för labb användare](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Nästa avsnitt beskriver hur du skapar miljöer från en Azure Resource Manager-mall.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Skapa miljöer från mallar i Azure Portal

När du lägger till en Azure Resource Manager-mall i labbet kan dina labb användare skapa miljöer i Azure Portal genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.

1. I listan med labb väljer du det labb du vill ha.

1. På labb sidan väljer du **Lägg till**.

1. I fönstret **Välj en bas** visas de grundläggande avbildningarna som du kan använda, med de Azure Resource Manager mallarna först. Välj den Azure Resource Manager mall du vill använda.

   ![Välja en bas](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. I fönstret **Lägg till** anger du ett **miljö namn** värde som visas för miljö användare.

   Azure Resource Manager mal len definierar resten av inmatade fält. Om mallen *azuredeploy.parameter.jsi* filen definierar standardvärden, visar indata fälten dessa värden.

   För parametrar av typen *säker sträng* kan du använda hemligheter från Azure Key Vault. Information om hur du lagrar hemligheter i ett nyckel valv och använder dem när du skapar labb resurser finns i [lagra hemligheter i Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

   ![Lägg till fönster](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Följande parameter värden visas inte i inmatade fält, även om mallen anger dem. I stället visar formuläret tomma indatatyper där labb användare måste ange värden när de skapar miljön.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB – NYCKEL
   > - GEN-PASSWORD

1. Välj **Lägg till** för att skapa miljön.

   Miljön startar etableringen direkt med statusen som visas i listan **mina virtuella datorer** . Labbet skapar automatiskt en ny resurs grupp för att etablera alla de resurser som definierats i Azure Resource Manager-mallen.

1. När miljön har skapats väljer du miljön i listan **mina virtuella datorer** för att öppna rutan resurs grupp och bläddrar till alla resurser som miljön har etablerad.

   ![Miljö resurser](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Du kan också expandera miljön om du bara vill visa listan över virtuella datorer som miljön har etablerad.

   ![Listan Mina virtuella datorer](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Välj någon av miljöerna för att visa tillgängliga åtgärder, till exempel att tillämpa artefakter, koppla data diskar, ändra tid för automatisk avstängning och mycket annat.

   ![Miljö åtgärder](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatisera miljö skapande med PowerShell

Det är möjligt att använda Azure Portal för att lägga till en enda miljö till ett labb, men när ett utvecklings-eller testnings scenario måste skapa flera miljöer är automatisk distribution en bättre upplevelse.

Innan du fortsätter kontrollerar du att du har en Azure Resource Manager-mall som definierar vilka resurser som ska skapas. [Lägg till och konfigurera mallen i en git-lagringsplats](#configure-your-own-template-repositories)och [Lägg till lagrings platsen i labbet](#add-template-repositories-to-the-lab).

Följande exempel skript skapar en miljö i labbet. Kommentarerna hjälper dig att förstå skriptet bättre.

1. Spara följande exempel på PowerShell-skript på hård disken som *deployenv.ps1*.

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

1. Kör skriptet på följande sätt med hjälp av dina speciella värden för SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (mapp i git lagrings platsen) och EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Du kan också använda Azure CLI för att distribuera resurser med Resource Manager-mallar. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Endast en användare med labb ägar behörighet kan skapa virtuella datorer från en Resource Manager-mall med hjälp av Azure PowerShell. Om du vill automatisera skapandet av virtuella datorer med en Resource Manager-mall och du bara har användar behörigheter kan du använda CLI-kommandot [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Begränsningar i Resource Manager-mallar i DevTest Labs

Tänk på följande begränsningar när du använder Resource Manager-mallar i DevTest Labs:

- Du kan inte skapa formler eller anpassade bilder från virtuella labb datorer som skapats från en Resource Manager-mall.

- De flesta principer utvärderas inte när du distribuerar Resource Manager-mallar.

Du kan till exempel ha en labb princip som en användare bara kan skapa fem virtuella datorer. En användare kan dock distribuera en Resource Manager-mall som skapar dussin tals virtuella datorer. Principer som inte utvärderas är:

  - Antal virtuella datorer per användare

  - Antal virtuella Premium-datorer per labb användare

  - Antal Premium diskar per labb användare

## <a name="next-steps"></a>Nästa steg
- När du har skapat en virtuell dator kan du ansluta till den virtuella datorn genom att välja **Anslut** i hanterings fönstret för den virtuella datorn.
- Visa och hantera resurser i en miljö genom att välja miljön i listan **mina virtuella datorer** i labbet.
- Utforska [Azure Resource Manager mallar från Azure snabb starts galleriet](https://github.com/Azure/azure-quickstart-templates).
