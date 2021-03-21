---
title: Automatisk korrigering av virtuella gäst datorer för virtuella Azure-datorer
description: Lär dig hur du automatiskt korrigerar virtuella datorer i Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101680318"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>För hands version: automatisk korrigering av virtuella gäst datorer för virtuella Azure-datorer

Om du aktiverar automatisk uppdatering av virtuella gäst datorer för dina virtuella Azure-datorer kan du under lätta uppdaterings hanteringen genom att säkert och automatiskt uppdatera virtuella datorer för att upprätthålla säkerhetskompatibilitet.

Automatisk korrigering av virtuella gäst datorer har följande egenskaper:
- Korrigeringar som klassificeras som *kritiska* eller *säkerhet* hämtas automatiskt och tillämpas på den virtuella datorn.
- Uppdateringar tillämpas under låg belastnings tider i den virtuella datorns tidszon.
- Uppdaterings dirigeringen hanteras av Azure och korrigeringarna tillämpas efter [principer för tillgänglighets första](#availability-first-patching).
- Hälso tillstånd för virtuella datorer, som fastställs genom plattformens hälso signaler, övervakas för att identifiera korrigerings fel.
- Fungerar för alla VM-storlekar.

> [!IMPORTANT]
> Automatisk uppdatering av virtuella gäst datorer är för närvarande en offentlig för hands version. En opt-in-procedur krävs för att använda den offentliga för hands versions funktionen som beskrivs nedan.
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Hur fungerar automatisk uppdatering av virtuella gäst datorer?

Om automatisk uppdatering av virtuella gäst datorer är aktive rad på en virtuell dator, hämtas de tillgängliga *viktiga* och *säkerhets* korrigeringarna och tillämpas automatiskt på den virtuella datorn. Den här processen startar automatiskt varje månad när nya korrigeringar lanseras. Korrigering av korrigering och installation sker automatiskt, och processen omfattar att starta om den virtuella datorn efter behov.

Den virtuella datorn bedöms regelbundet med några dagars mellanrum och flera gånger under en 30-dagarsperiod för att fastställa de tillämpliga korrigeringarna för den virtuella datorn. Korrigeringarna kan installeras varje dag på den virtuella datorn under låg belastnings tider för den virtuella datorn. Den här automatiska utvärderingen säkerställer att eventuella korrigeringar som saknas upptäcks så snart som möjligt.

Korrigeringsfiler installeras inom 30 dagar från de månatliga korrigerings versionerna, efter den tillgänglighets första dirigering som beskrivs nedan. Korrigeringsfiler installeras bara vid låg belastnings tider för den virtuella datorn, beroende på den virtuella datorns tidszon. Den virtuella datorn måste köras under låg belastnings tid för att korrigerings program ska installeras automatiskt. Om en virtuell dator stängs av under en periodisk utvärdering, kommer den virtuella datorn automatiskt att utvärderas och tillämpliga korrigerings program installeras automatiskt under nästa periodiska bedömning (vanligt vis inom några dagar) när den virtuella datorn påbörjas.

Definitions uppdateringar och andra korrigeringar som inte klassificeras som *kritiska* eller *säkerhet* kommer inte att installeras via automatisk uppdatering av gäst datorer. Om du vill installera korrigeringar med andra korrigeringar eller schemalägga korrigerings installation i ett eget anpassat underhålls fönster kan du använda [uppdateringshantering](./windows/tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Tillgänglighet – första korrigering

Installations processen för installationen dirigeras globalt av Azure för alla virtuella datorer där automatisk uppdatering av VM-gäster är aktiverat. Den här dirigeringen följer tillgänglighets de första principerna för olika nivåer av tillgänglighet från Azure.

För en grupp virtuella datorer som genomgår en uppdatering kommer Azure-plattformen att dirigera uppdateringar:

**Mellan regioner:**
- En månatlig uppdatering dirigeras över Azure globalt i ett stegvist sätt för att förhindra globala distributions problem.
- En fas kan ha en eller flera regioner, och en uppdatering flyttas bara till nästa faser om kvalificerade virtuella datorer i en fas uppdateras.
- Geo-kopplade regioner uppdateras inte samtidigt och får inte finnas i samma regionala fas.
- En uppdaterings lyckad uppdatering mäts genom spårning av den virtuella datorns hälso tillstånd efter uppdatering. VM-hälsan spåras via plattforms hälso indikatorer för den virtuella datorn.

**Inom en region:**
- Virtuella datorer i olika Tillgänglighetszoner uppdateras inte samtidigt.
- Virtuella datorer som inte ingår i en tillgänglighets uppsättning är grupperade på bästa möjliga sätt för att undvika samtidiga uppdateringar för alla virtuella datorer i en prenumeration.

**Inom en tillgänglighets uppsättning:**
- Alla virtuella datorer i en gemensam tillgänglighets uppsättning uppdateras inte samtidigt.
-   Virtuella datorer i en gemensam tillgänglighets uppsättning uppdateras inom gränserna för uppdaterings domänen och virtuella datorer över flera uppdaterings domäner uppdateras inte samtidigt.

Installations datumet för korrigerings filen för en angiven virtuell dator kan variera månad till månad, eftersom en specifik virtuell dator kan hämtas i en annan batch mellan månads uppdaterings cykler.

### <a name="which-patches-are-installed"></a>Vilka korrigeringar är installerade?
De installerade korrigeringarna beror på distributions fasen för den virtuella datorn. Varje månad startas en ny global distribution där alla säkerhets-och viktiga korrigeringar som utvärderas för en enskild virtuell dator har installerats för den virtuella datorn. Distributionen dirigeras över alla Azure-regioner i batchar (beskrivs i avsnittet tillgänglighets-första uppdatering ovan).

Den exakta uppsättningen korrigeringar som ska installeras varierar beroende på VM-konfigurationen, inklusive OS-typ och utvärderings tid. Det går att använda två identiska virtuella datorer i olika regioner för att få olika korrigeringar installerade om det finns mer eller mindre korrigeringar som är tillgängliga när uppdaterings dirigeringen når olika regioner vid olika tidpunkter. På samma sätt kan virtuella datorer i samma region men utvärderas vid olika tidpunkter (på grund av olika tillgänglighets zoner eller batchar med tillgänglighets uppsättningar) kan få olika korrigeringar.

Eftersom den automatiska korrigeringen av gästa virtuella datorer inte konfigurerar korrigerings källan, kan två liknande virtuella datorer som har kon figurer ATS för olika uppdaterings källor, t. ex. offentligt lager och privat lagrings plats, också se en skillnad i den exakta uppsättningen korrigeringar som är installerade

För OS-typer som publicerar korrigeringar på en fast takt, kan virtuella datorer som är konfigurerade för den offentliga lagrings platsen för operativ systemet ta emot samma uppsättning korrigeringar i olika distributions faser under en månad. Windows-VM: ar som kon figurer ATS för den offentliga Windows Update-lagringsplatsen.

När en ny distribution aktive ras varje månad får en virtuell dator minst en korrigerings distribution varje månad om den virtuella datorn är påslagen under låg belastnings tider. Detta säkerställer att den virtuella datorn korrigeras med de senaste tillgängliga säkerhets-och viktiga korrigeringarna varje månad. För att säkerställa konsekvens i en uppsättning korrigeringar som är installerade kan du konfigurera dina virtuella datorer för att utvärdera och hämta korrigeringar från dina egna privata databaser.

## <a name="supported-os-images"></a>OS-avbildningar som stöds
Det finns för närvarande stöd för virtuella datorer som skapats från vissa OS-plattforms avbildningar i för hands versionen. Anpassade avbildningar stöds för närvarande inte i för hands versionen.

Följande plattforms-SKU: er stöds för närvarande (och fler läggs till regelbundet):

| Publisher               | OS-erbjudande      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18,04 – LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Data Center    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Data Center-Server Core |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Data Center |
| MicrosoftWindowsServer  | WindowsServer | 2019-Data Center-kärna |

## <a name="patch-orchestration-modes"></a>Korrigera Orchestration-lägen
Virtuella datorer på Azure har nu stöd för följande säkerhets lägen för snabb korrigeringar:

**AutomaticByPlatform:**
- Det här läget stöds för både virtuella Linux-och Windows-datorer.
- Med det här läget aktive ras automatisk korrigering av virtuella gäst datorer för den virtuella datorn och efterföljande korrigerings installationer dirigeras av Azure.
- Det här läget krävs för första korrigering av tillgänglighet.
- Det här läget stöds bara för virtuella datorer som skapas med avbildningar som stöds av OS-plattformen ovan.
- För virtuella Windows-datorer inaktiverar inställningen det här läget även de inbyggda automatiska uppdateringarna på den virtuella Windows-datorn för att undvika duplicering.
- Om du vill använda det här läget på virtuella Linux-datorer anger du egenskapen `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` i mallen för virtuell dator.
- Om du vill använda det här läget på virtuella Windows-datorer anger du egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=true` och anger egenskapen  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` i mallen för virtuell dator.

**AutomaticByOS:**
- Det här läget stöds bara för virtuella Windows-datorer.
- Det här läget aktiverar automatiska uppdateringar på den virtuella Windows-datorn och korrigeringsfiler installeras på den virtuella datorn via automatiska uppdateringar.
- Det här läget stöder inte tillgänglighets-första uppdatering.
- Det här läget anges som standard om inget annat korrigerings läge har angetts för en virtuell Windows-dator.
- Om du vill använda det här läget på virtuella Windows-datorer anger du egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=true` och anger egenskapen  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` i mallen för virtuell dator.

**Bok**
- Det här läget stöds bara för virtuella Windows-datorer.
- Det här läget inaktiverar automatiska uppdateringar på den virtuella Windows-datorn.
- Det här läget stöder inte tillgänglighets-första uppdatering.
- Det här läget ska ställas in när du använder anpassade lösningar för uppdatering.
- Om du vill använda det här läget på virtuella Windows-datorer anger du egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=false` och anger egenskapen  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` i mallen för virtuell dator.

**ImageDefault:**
- Det här läget stöds bara för virtuella Linux-datorer.
- Det här läget stöder inte tillgänglighets-första uppdatering.
- Det här läget följer standard konfigurationen för uppdatering i den avbildning som används för att skapa den virtuella datorn.
- Det här läget anges som standard om inget annat korrigerings läge har angetts för en virtuell Linux-dator.
- Om du vill använda det här läget på virtuella Linux-datorer anger du egenskapen `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` i mallen för virtuell dator.

> [!NOTE]
>För virtuella Windows-datorer `osProfile.windowsConfiguration.enableAutomaticUpdates` kan egenskapen för närvarande endast anges när den virtuella datorn skapas. Att växla från manuell till ett automatiskt läge eller från antingen automatiska lägen till manuellt läge stöds inte för närvarande. Växling från AutomaticByOS-läge till AutomaticByPlatfom-läge stöds.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Krav för att aktivera automatisk uppdatering av virtuella gäst datorer

- Azure VM-agenten för [Windows](./extensions/agent-windows.md) eller [Linux](./extensions/agent-linux.md) måste vara installerad på den virtuella datorn.
- För virtuella Linux-datorer måste Azure Linux-agenten vara version 2.2.53.1 eller högre. [Uppdatera Linux-agenten](./extensions/update-linux-agent.md) om den aktuella versionen är lägre än den version som krävs.
- För virtuella Windows-datorer måste Windows Updates tjänsten köras på den virtuella datorn.
- Den virtuella datorn måste kunna komma åt de konfigurerade uppdaterings slut punkterna. Om din virtuella dator har kon figurer ATS för att använda privata lagrings platser för Linux eller Windows Server Update Services (WSUS) för virtuella Windows-datorer, måste relevanta uppdaterings slut punkter vara tillgängliga.
- Använd Compute API version 2020-12-01 eller högre. Compute API version 2020-06-01 kan användas för virtuella Windows-datorer med begränsade funktioner.

Att aktivera för hands versions funktionen kräver en engångs anmälan för funktionerna **InGuestAutoPatchVMPreview** och **InGuestPatchVMPreview** per prenumeration, enligt beskrivningen i följande avsnitt.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du aktiverar för hands versionen av din prenumeration:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till Compute-resursens leverantör.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera för hands versionen av din prenumeration.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till Compute-resursens leverantör.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Aktivera förhands granskningen för prenumerationen med hjälp av [AZ-funktionen registrera](/cli/azure/feature#az-feature-register) .

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till Compute-resursens leverantör.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Aktivera automatisk uppdatering av VM-gäster
Om du vill aktivera automatisk uppdatering av virtuella gäst datorer på en virtuell Windows-dator kontrollerar du att egenskapen *osProfile. windowsConfiguration. enableAutomaticUpdates* har angetts till *True* i definitionen för VM-mallen. Den här egenskapen kan bara anges när du skapar den virtuella datorn. Den här ytterligare egenskapen gäller inte för virtuella Linux-datorer.

### <a name="rest-api-for-linux-vms"></a>REST API för virtuella Linux-datorer
I följande exempel beskrivs hur du aktiverar automatisk uppdatering av virtuella gäst datorer:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>REST API för virtuella Windows-datorer
I följande exempel beskrivs hur du aktiverar automatisk uppdatering av virtuella gäst datorer:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell för virtuella Windows-datorer
Använd cmdleten [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) för att aktivera automatisk uppdatering av VM-gäster när du skapar eller uppdaterar en virtuell dator.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Azure CLI för virtuella Windows-datorer
Använd [AZ VM Create](/cli/azure/vm#az-vm-create) för att aktivera automatisk korrigering av VM-gäster när du skapar en ny virtuell dator. I följande exempel konfigureras automatisk uppdatering av virtuella gäst datorer för en virtuell dator med namnet *myVM* i resurs gruppen med namnet *myResourceGroup*:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Om du vill ändra en befintlig virtuell dator använder du [AZ VM Update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Aktivering och bedömning

> [!NOTE]
>Det kan ta mer än tre timmar att aktivera automatiska uppdateringar av virtuella datorer på en virtuell dator, eftersom aktiveringen slutförs under den virtuella datorns låg belastnings tid. Eftersom utvärderings-och korrigerings installation endast sker under låg belastnings tider, måste den virtuella datorn också köras vid låg belastnings tider för att tillämpa korrigeringar.

När automatisk uppdatering av virtuella gäst datorer är aktive rad för en virtuell dator installeras ett VM-tillägg av typen `Microsoft.CPlat.Core.LinuxPatchExtension` på en virtuell Linux-dator eller ett VM-tillägg av typen `Microsoft.CPlat.Core.WindowsPatchExtension` som installeras på en virtuell Windows-dator. Det här tillägget behöver inte installeras eller uppdateras manuellt eftersom det här tillägget hanteras av Azure-plattformen som en del av den automatiska korrigerings processen för VM-gäster.

Det kan ta mer än tre timmar att aktivera automatiska uppdateringar av virtuella datorer på en virtuell dator, eftersom aktiveringen slutförs under den virtuella datorns låg belastnings tid. Tillägget installeras och uppdateras också vid låg belastnings tider för den virtuella datorn. Om den virtuella datorns låg belastnings tid upphör innan aktiveringen kan slutföras, återupptas aktiveringen vid nästa tillgängliga tid för låg belastning.

Automatiska uppdateringar inaktive ras i de flesta fall och korrigerings installationen görs genom att tillägget skickas framåt. Följande villkor gäller.
- Om en virtuell Windows-dator tidigare hade automatiskt Windows Update aktive ras genom AutomaticByOS korrigerings läge inaktive ras automatiskt Windows Update för den virtuella datorn när tillägget har installerats.
- För virtuella Ubuntu-datorer inaktive ras standard automatiska uppdateringar automatiskt när automatisk korrigering av VM-gäster slutfört aktivering.
- Automatiska uppdateringar måste inaktive ras manuellt (detta är en för hands versions begränsning) för RHEL. Köra

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Du kan kontrol lera om den automatiska korrigeringen av VM-gäster har slutförts och korrigerings tillägget är installerat på den virtuella datorn genom att granska instans visningen för den virtuella datorn. Om aktiveringen är klar installeras tillägget och utvärderings resultatet för den virtuella datorn är tillgängligt under `patchStatus` . Du kan få åtkomst till den virtuella datorns instans vy på flera sätt enligt beskrivningen nedan.

### <a name="rest-api"></a>REST-API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [Get-AzVM](/powershell/module/az.compute/get-azvm) med `-Status` parametern för att komma åt instans visningen för den virtuella datorn.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell tillhandahåller för närvarande bara information om korrigerings tillägget. Information om `patchStatus` kommer också att vara tillgänglig snart via PowerShell.

### <a name="azure-cli"></a>Azure CLI
Använd [AZ VM get-instance-View](/cli/azure/vm#az-vm-get-instance-view) för att komma åt instans visningen för den virtuella datorn.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Förstå uppdaterings statusen för din virtuella dator

`patchStatus`Avsnittet i svars visnings svaret innehåller information om den senaste utvärderingen och den senaste korrigerings installationen för den virtuella datorn.

Utvärderings resultatet för din virtuella dator kan granskas i `availablePatchSummary` avsnittet. En utvärdering utförs regelbundet för en virtuell dator där automatisk uppdatering av VM-gäster är aktiverat. Antalet tillgängliga korrigeringar efter en utvärdering anges under `criticalAndSecurityPatchCount` och `otherPatchCount` resultat. Den automatiska korrigeringen av den virtuella gäst datorn kommer att installera alla korrigeringsfiler som utvärderas i klassificeringarna *kritiska* och *säkerhets* korrigeringar. Alla andra utvärderade korrigeringar hoppas över.

Installations resultatet för korrigerings filen för din virtuella dator kan granskas under `lastPatchInstallationSummary` avsnittet. Det här avsnittet innehåller information om det senaste försöket att installera uppdateringen på den virtuella datorn, inklusive antalet korrigeringsfiler som har installerats, väntar, misslyckades eller hoppades över. Korrigeringsfiler installeras bara under underhålls perioden för låg belastnings tid för den virtuella datorn. Väntande och misslyckade korrigeringar görs automatiskt om vid nästa underhålls period för tids perioder.

## <a name="on-demand-patch-assessment"></a>Utvärdering av korrigering på begäran
Om automatisk uppdatering av virtuella gäst datorer redan har Aktiver ATS för den virtuella datorn, utförs en periodisk korrigering av korrigering på den virtuella datorn under den tid då den virtuella datorn låg belastning. Den här processen är automatisk och resultatet av den senaste utvärderingen kan granskas via den virtuella datorns instans vy enligt beskrivningen ovan i det här dokumentet. Du kan också utlösa en utvärdering på begäran för din virtuella dator när som helst. Det kan ta några minuter att slutföra korrigerings utvärderingen och status för den senaste utvärderingen uppdateras i instans visningen för den virtuella datorn.

Att aktivera för hands versions funktionen kräver en engångs anmälan för funktionen **InGuestPatchVMPreview** per prenumeration. Den här förhands granskningen skiljer sig från den automatiska korrigeringen av gäst uppdaterings funktionen för virtuella datorer som gjorts tidigare för **InGuestAutoPatchVMPreview**. Att aktivera ytterligare förhands granskning av funktioner är ett separat och ytterligare krav. För hands versionen av funktionen för uppdatering på begäran kan aktive ras enligt den [förhands gransknings process](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) som beskrivs tidigare för automatisk uppdatering av virtuella gäst datorer.

> [!NOTE]
>Utvärdering av korrigering på begäran utlöser inte automatiskt korrigerings installation. Om du har aktiverat automatisk korrigering av virtuella gäst datorer, kommer de utvärderade och tillämpliga korrigeringarna för den virtuella datorn att installeras under den virtuella datorns låg belastnings tid, efter den tillgänglighets uppdaterings process som beskrivs tidigare i det här dokumentet.

### <a name="rest-api"></a>REST-API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) för att utvärdera de tillgängliga korrigeringarna för den virtuella datorn.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Använd [AZ VM-utvärdering – korrigeringsfiler](/cli/azure/vm#az-vm-assess-patches) för att utvärdera de tillgängliga korrigeringarna för den virtuella datorn.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lär dig mer om att skapa och hantera virtuella Windows-datorer](./windows/tutorial-manage-vm.md)
