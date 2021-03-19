---
title: Integrera Azure DevTest Labs i dina Azure-pipeliner
description: Lär dig hur du integrerar Azure DevTest Labs i dina Azure-pipeliner kontinuerlig integrering och leverans pipelines
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 96f99d41d0a7ea07bf3854292f9c3bd6245414b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288934"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integrera Azure DevTest Labs i din Azure-pipeline CI/CD-pipeline

Du kan använda tillägget *Azure DevTest Labs uppgifter* för att integrera dina Azure pipelines kontinuerlig integrering och kontinuerlig leverans (CI/CD) för att bygga och lansera pipelines med Azure DevTest Labs. Tillägget installerar flera aktiviteter, inklusive: 

- Skapa en virtuell dator (VM)
- Skapa en anpassad avbildning från en virtuell dator
- Ta bort en virtuell dator 

De här uppgifterna gör det enkelt att snabbt distribuera en virtuell dator med *en virtuell dator för en speciell* test aktivitet och sedan ta bort den virtuella datorn när testet är klart.

Den här artikeln visar hur du använder Azure DevTest Labs uppgifter för att skapa och distribuera en virtuell dator, skapa en anpassad avbildning och sedan ta bort den virtuella datorn, allt som en versions pipeline. Du utför vanligt vis uppgifterna individuellt i dina egna anpassade bygge-, test-och distributions pipeliner.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Förutsättningar

- Registrera dig eller logga in i din [Azure DevOps](https://dev.azure.com) -organisation och [skapa ett projekt](/vsts/organizations/projects/create-project) i organisationen. 
  
- Installera Azure DevTest Labs uppgifter-tillägget från Visual Studio Marketplace.
  
  1. Gå till [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Välj **Hämta utan kostnad**.
  1. Välj din Azure DevOps-organisation i list rutan och välj **Installera**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Skapa mallen för att skapa en virtuell Azure-dator 

I det här avsnittet beskrivs hur du skapar den Azure Resource Manager-mall som du använder för att skapa en virtuell Azure-dator på begäran.

1. Om du vill skapa en Resource Manager-mall i din prenumeration följer du proceduren i [använda en Resource Manager-mall](devtest-lab-use-resource-manager-template.md).
   
1. Innan du genererar Resource Manager-mallen lägger du till [WinRM-artefakten](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) som en del av att skapa den virtuella datorn. Distributions uppgifter som *Azure File Copy* och *PowerShell på mål datorer* behöver WinRM-åtkomst. WinRM-artefakten kräver ett värdnamn som parameter, vilket bör vara det fullständigt kvalificerade domän namnet (FQDN) för den virtuella datorn. 
   
   > [!NOTE]
   > När du använder WinRM med en delad IP-adress måste du lägga till en NAT-regel för att mappa en extern port till WinRM-porten. Du behöver inte NAT-regeln om du skapar den virtuella datorn med en offentlig IP-adress.
   
   
1. Spara mallen på datorn som en fil med namnet *CreateVMTemplate.jspå*.
   
1. Checka in mallen i käll kontroll systemet.

## <a name="create-a-script-to-get-vm-properties"></a>Skapa ett skript för att hämta VM-egenskaper

När du kör uppgifts steg som *Azure File Copy* eller *PowerShell på mål datorerna* i versions pipelinen samlar följande skript in de värden som du behöver för att distribuera en app till en virtuell dator. Du använder vanligt vis dessa uppgifter för att distribuera din app till en virtuell Azure-dator. Aktiviteterna kräver värden, t. ex. resurs grupps namn, IP-adress och fullständigt domän namn för den virtuella datorn.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Så här skapar du skript filen:

1. Öppna en text redigerare och klistra in följande skript i det.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Spara filen med ett namn som *GetLabVMParams.ps1* och kontrol lera den i käll kontroll systemet. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Skapa en versionspipeline i Azure Pipelines

Så här skapar du en ny versions pipeline:

1. På sidan för ditt Azure DevOps-projekt väljer du **pipeline**-  >  **utgåvor** från det vänstra navigerings fältet.
1. Välj **Ny pipeline**.
1. Under **Välj en mall** rullar du ned och väljer **Tom jobb** och väljer sedan **Använd**.

### <a name="add-and-set-variables"></a>Lägg till och ange variabler

I pipeline-uppgifterna används de värden som du har tilldelat till den virtuella datorn när du skapade Resource Manager-mallen i Azure Portal. 

Så här lägger du till variabler för värdena: 

1. På sidan pipelines väljer du fliken **variabler** .
   
1. För varje variabel väljer du **Lägg till** och anger namn och värde:
   
   |Name|Värde|
   |---|---|
   |*vmName*|VM-namn som du har tilldelat i Resource Manager-mallen|
   |*Användar*|Användar namn för att komma åt den virtuella datorn|
   |*lösenord*|Lösen ord för användar namnet. Välj Lås ikonen för att dölja och skydda lösen ordet.

### <a name="create-a-devtest-labs-vm"></a>Skapa en virtuell DevTest Labs-dator

Nästa steg är att skapa en virtuell dator i den gyllene avbildningen som ska användas för framtida distributioner. Du skapar den virtuella datorn i Azure DevTest Labs-instansen med hjälp av aktiviteten *Azure DevTest Labs Skapa virtuell dator* .

1. På fliken release pipeline för **pipeline väljer** du den hyperlänkade texten i **steg 1** för att **Visa steg uppgifter** och väljer sedan plus tecknet **+** bredvid **Agent jobb**. 
   
1. Under **Lägg till aktiviteter** väljer du **Azure DevTest Labs Skapa virtuell dator** och väljer **Lägg till**. 
   
1. Välj **skapa Azure DevTest Labs virtuell dator** i det vänstra fönstret. 

1. I den högra rutan fyller du i formuläret enligt följande:
   
   |Fält|Värde|
   |---|---|
   |**Azure RM-prenumeration**|Välj en tjänst anslutning eller prenumeration från **tillgängliga Azure-tjänst anslutningar** eller **tillgängliga Azure-prenumerationer** i list rutan och välj **auktorisera** vid behov.<br /><br />**Obs:** Information om hur du skapar en mer begränsad behörighets anslutning till din Azure-prenumeration finns i [Azure Resource Manager tjänstens slut punkt](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Labb namn**|Välj namnet på ett befintligt labb där den virtuella labb datorn ska skapas.|
   |**Mallnamn**|Ange den fullständiga sökvägen till och namnet på mallfilen som du sparade i din käll kods lagrings plats. Du kan använda inbyggda egenskaper för att förenkla sökvägen, till exempel:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Mallparametrar**|Ange parametrarna för de variabler som du definierade tidigare:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Variabler**  >  för utdata **ID för labb-VM**|Ange variabeln för det skapade Labbets VM-ID. Om du använder standard- **labVMId** kan du referera till variabeln i efterföljande uppgifter som *$ (labVMId)*.<br /><br />Du kan skapa ett annat namn än standardvärdet, men kom ihåg att använda rätt namn i efterföljande uppgifter. Du kan skriva ID: t för den virtuella datorns labb i följande format:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Samla in information om den virtuella datorn DevTest Labs

Kör skriptet som du skapade tidigare för att samla in information om den virtuella datorn DevTest Labs. 

1. På fliken release pipeline för **pipeline väljer** du den hyperlänkade texten i **steg 1** för att **Visa steg uppgifter** och väljer sedan plus tecknet **+** bredvid **Agent jobb**. 
   
1. Under **Lägg till aktiviteter** väljer du **Azure PowerShell** och väljer **Lägg till**. 
   
1. Välj **Azure PowerShell skript: sökväg** i det vänstra fönstret. 
   
1. I den högra rutan fyller du i formuläret enligt följande:
   
   |Fält|Värde|
   |---|---|
   |**Typ av Azure-anslutning**|Välj **Azure Resource Manager**.|
   |**Azure Subscription (Azure-prenumeration)**|Välj din tjänst anslutning eller prenumeration.| 
   |**Skript typ**|Välj **sökväg till skript fil**.|
   |**Skript Sök väg**|Ange den fullständiga sökvägen och namnet på det PowerShell-skript som du sparade i din käll kods lagrings plats. Du kan använda inbyggda egenskaper för att förenkla sökvägen, till exempel:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Skript argument**|Ange namnet på *labVmId* -variabeln som har fyllts i med föregående uppgift, till exempel:<br /><br />`-labVmId '$(labVMId)'`|

Skriptet samlar in de nödvändiga värdena och lagrar dem i miljövariabler i versions pipeline, så att du enkelt kan referera till dem i efterföljande steg.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Skapa en avbildning av en virtuell dator från DevTest Labs VM

Nästa uppgift är att skapa en avbildning av den nyligen distribuerade virtuella datorn i Azure DevTest Labs-instansen. Du kan sedan använda avbildningen för att skapa kopior av den virtuella datorn på begäran när du vill köra en dev-uppgift eller köra vissa tester. 

1. På fliken release pipeline för **pipeline väljer** du den hyperlänkade texten i **steg 1** för att **Visa steg uppgifter** och väljer sedan plus tecknet **+** bredvid **Agent jobb**. 
   
1. Under **Lägg till aktiviteter** väljer du **Azure DevTest Labs Skapa anpassad avbildning** och väljer **Lägg till**. 
   
1. Konfigurera uppgiften enligt följande:
   
   |Fält|Värde|
   |---|---|
   |**Azure RM-prenumeration**|Välj din tjänst anslutning eller prenumeration.|
   |**Labb namn**|Välj namnet på ett befintligt labb där bilden ska skapas.|
   |**Namn på anpassad avbildning**|Ange ett namn för den anpassade avbildningen.|
   |**Beskrivning** (valfritt)|Ange en beskrivning som gör det enkelt att välja rätt avbildning senare.|
   |**Käll labb VM**  >  **ID för käll labb VM**|Om du har ändrat standard namnet för LabVMId-variabeln anger du den här. Standardvärdet är **$ (labVMId)**.|
   |**Variabler**  >  för utdata **Anpassat bild-ID**|Du kan redigera standard namnet på variabeln om det behövs.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Distribuera din app till den virtuella DevTest Labs-datorn (valfritt)

Du kan lägga till uppgifter för att distribuera din app till den nya virtuella DevTest Labs-datorn. De uppgifter som du vanligt vis använder för att distribuera appen är *Azure File Copy* och *PowerShell på mål datorerna*.

Den VM-information som du behöver för parametrarna för dessa uppgifter lagras i tre konfigurationsfiler som heter **labVmRgName**, **labVMIpAddress** och **labVMFqdn** i versions pipelinen. Om du bara vill experimentera med att skapa en virtuell dator med DevTest Labs och en anpassad avbildning, utan att distribuera en app till den, kan du hoppa över det här steget.

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Den sista aktiviteten är att ta bort den virtuella dator som du har distribuerat i Azure DevTest Labs-instansen. Du skulle vanligt vis ta bort den virtuella datorn när du har kört dev-aktiviteterna eller kört de tester som du behöver på den distribuerade virtuella datorn. 

1. På fliken release pipeline för **pipeline väljer** du den hyperlänkade texten i **steg 1** för att **Visa steg uppgifter** och väljer sedan plus tecknet **+** bredvid **Agent jobb**. 
   
1. Under **Lägg till aktiviteter** väljer du **Azure DevTest Labs ta bort virtuell dator** och väljer **Lägg till**. 
   
1. Konfigurera uppgiften enligt följande:
   
   - Under **Azure RM-prenumeration** väljer du tjänst anslutningen eller prenumerationen. 
   - För **Labbets VM-ID**, om du har ändrat standard namnet för LabVMId-variabeln, anger du den här. Standardvärdet är **$ (labVMId)**.
   
### <a name="save-the-release-pipeline"></a>Spara versions pipelinen

Så här sparar du den nya versions pipelinen:

1. Välj den **nya pipeline-pipeline** på sidan release pipeline och ange ett nytt namn för pipelinen. 
   
1. Välj ikonen **Spara** längst upp till höger. 

## <a name="create-and-run-a-release"></a>Skapa och köra en version

Så här skapar och kör du en version med den nya pipelinen:

1. Välj **Skapa version** längst upp till höger på sidan Frisläpp pipelines. 
   
1. Under **artefakter** väljer du den senaste versionen och väljer sedan **skapa**.
   
1. I varje versions steg uppdaterar du vyn av DevTest Labs-instansen i Azure Portal för att Visa skapande av virtuella datorer, avbildnings skapande och borttagning av virtuella datorer.

Du kan använda den anpassade avbildningen för att skapa virtuella datorer när du behöver dem.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [skapar miljöer för flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
- Utforska fler starter Resource Manager-mallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub lagrings platsen](https://github.com/Azure/azure-quickstart-templates).
- Om det behövs går du till [fel söknings sidan för Azure DevOps](/azure/devops/pipelines/troubleshooting) .
 
