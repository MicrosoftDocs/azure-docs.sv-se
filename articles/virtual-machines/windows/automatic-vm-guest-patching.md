---
title: Automatisk korrigering av virtuella gäst datorer för virtuella Windows-datorer i Azure
description: Lär dig hur du automatiskt korrigerar virtuella Windows-datorer i Azure
author: mayanknayar
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 12/23/2020
ms.author: manayar
ms.openlocfilehash: e22e8b81382614c2930c72a8150606f859be501d
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762987"
---
# <a name="preview-automatic-vm-guest-patching-for-windows-vms-in-azure"></a>Förhandsversion: Så här konfigurerar du automatisk gästuppdatering för virtuella datorer med Windows i Azure

Genom att aktivera automatisk uppdatering av virtuella datorer för virtuella Windows-datorer kan du under lätta uppdaterings hanteringen genom att säkert och automatiskt uppdatera virtuella datorer för att upprätthålla säkerhetskompatibiliteten.

Automatisk korrigering av virtuella gäst datorer har följande egenskaper:
- Korrigeringar som klassificeras som *kritiska* eller *säkerhet* hämtas automatiskt och tillämpas på den virtuella datorn.
- Uppdateringar tillämpas under låg belastnings tider i den virtuella datorns tidszon.
- Uppdaterings dirigeringen hanteras av Azure och korrigeringarna tillämpas efter principer för tillgänglighets första.
- Hälso tillstånd för virtuella datorer, som fastställs genom plattformens hälso signaler, övervakas för att identifiera korrigerings fel.
- Fungerar för alla VM-storlekar.

> [!IMPORTANT]
> Automatisk uppdatering av virtuella gäst datorer är för närvarande en offentlig för hands version. En opt-in-procedur krävs för att använda den offentliga för hands versions funktionen som beskrivs nedan.
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Hur fungerar automatisk uppdatering av virtuella gäst datorer?

Om automatisk uppdatering av virtuella gäst datorer är aktive rad på en virtuell dator, hämtas de tillgängliga *viktiga* och *säkerhets* korrigeringarna och tillämpas automatiskt på den virtuella datorn. Den här processen inaktive ras automatiskt varje månad när nya korrigeringar släpps via Windows Update. Korrigering av korrigering och installation sker automatiskt, och processen omfattar att starta om den virtuella datorn efter behov.

Den virtuella datorn bedöms regelbundet med några dagars mellanrum och flera gånger under en 30-dagarsperiod för att fastställa de tillämpliga korrigeringarna för den virtuella datorn. Korrigeringarna kan installeras varje dag på den virtuella datorn under låg belastnings tider för den virtuella datorn. Den här automatiska utvärderingen säkerställer att eventuella korrigeringar som saknas upptäcks så snart som möjligt.

Korrigeringsfiler installeras inom 30 dagar från den månatliga Windows Updates versionen, efter den tillgänglighets första dirigering som beskrivs nedan. Korrigeringsfiler installeras bara vid låg belastnings tider för den virtuella datorn, beroende på den virtuella datorns tidszon. Den virtuella datorn måste köras under låg belastnings tid för att korrigerings program ska installeras automatiskt. Om en virtuell dator stängs av under en periodisk utvärdering, kommer den virtuella datorn automatiskt att utvärderas och tillämpliga korrigerings program installeras automatiskt under nästa periodiska bedömning (vanligt vis inom några dagar) när den virtuella datorn påbörjas.

Definitions uppdateringar och andra korrigeringar som inte klassificeras som *kritiska* eller *säkerhet* installeras inte via den automatiska gäst korrigeringen för virtuella datorer. Om du vill installera korrigeringar med andra korrigeringar eller schemalägga korrigerings installation i ett eget anpassat underhålls fönster kan du använda [uppdateringshantering](tutorial-config-management.md#manage-windows-updates).

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

## <a name="supported-os-images"></a>OS-avbildningar som stöds
Det finns för närvarande stöd för virtuella datorer som skapats från vissa OS-plattforms avbildningar i för hands versionen. Anpassade avbildningar stöds för närvarande inte i för hands versionen.

Följande plattforms-SKU: er stöds för närvarande (och fler läggs till regelbundet):

| Publisher               | OS-erbjudande      |  Sku               |
|-------------------------|---------------|--------------------|
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Data Center    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Data Center-Server Core |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Data Center |
| MicrosoftWindowsServer  | WindowsServer | 2019-Data Center-kärna |

## <a name="patch-orchestration-modes"></a>Korrigera Orchestration-lägen
Virtuella Windows-datorer i Azure har nu stöd för följande säkerhets lägen för snabb korrigeringar:

**AutomaticByPlatform:**
- Det här läget aktiverar automatisk uppdatering av virtuella gäst datorer för den virtuella Windows-datorn och efterföljande korrigerings installation dirigeras av Azure.
- Det här läget krävs för första korrigering av tillgänglighet.
- Om du anger det här läget inaktive ras även de inbyggda automatiska uppdateringarna på den virtuella Windows-datorn för att undvika duplicering.
- Det här läget stöds bara för virtuella datorer som skapas med avbildningar som stöds av OS-plattformen ovan.
- Om du vill använda det här läget ställer du in egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=true` och anger egenskapen  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` i mallen för virtuell dator.

**AutomaticByOS:**
- Det här läget aktiverar automatiska uppdateringar på den virtuella Windows-datorn och korrigeringsfiler installeras på den virtuella datorn via automatiska uppdateringar.
- Det här läget stöder inte tillgänglighets-första uppdatering.
- Det här läget anges som standard om inget annat korrigerings läge anges.
- Om du vill använda det här läget ställer du in egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=true` och anger egenskapen  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` i mallen för virtuell dator.

**Bok**
- Det här läget inaktiverar automatiska uppdateringar på den virtuella Windows-datorn.
- Det här läget stöder inte tillgänglighets-första uppdatering.
- Det här läget ska ställas in när du använder anpassade lösningar för uppdatering.
- Om du vill använda det här läget ställer du in egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=false` och anger egenskapen  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` i mallen för virtuell dator.

> [!NOTE]
>Egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates` kan för närvarande endast anges när den virtuella datorn skapas. Att växla från manuell till ett automatiskt läge eller från antingen automatiska lägen till manuellt läge stöds inte för närvarande. Växling från AutomaticByOS-läge till AutomaticByPlatfom-läge stöds

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Krav för att aktivera automatisk uppdatering av virtuella gäst datorer

- [Azure VM-agenten](../extensions/agent-windows.md) måste vara installerad på den virtuella datorn.
- Den Windows Update tjänsten måste köras på den virtuella datorn.
- Den virtuella datorn måste kunna komma åt Windows Update-slutpunkter. Om den virtuella datorn är konfigurerad för att använda Windows Server Update Services (WSUS) måste de relevanta WSUS-Server slut punkterna vara tillgängliga.
- Använd Compute API version 2020-06-01 eller högre.

Att aktivera för hands versions funktionen kräver en engångs anmälan för funktionen **InGuestAutoPatchVMPreview** per prenumeration, enligt beskrivningen nedan.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du aktiverar för hands versionen av din prenumeration:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till Compute-resursens leverantör.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera för hands versionen av din prenumeration.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till Compute-resursens leverantör.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Aktivera förhands granskningen för prenumerationen med hjälp av [AZ-funktionen registrera](/cli/azure/feature#az-feature-register) .

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till Compute-resursens leverantör.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```
## <a name="enable-automatic-vm-guest-patching"></a>Aktivera automatisk uppdatering av VM-gäster
Om du vill aktivera automatisk uppdatering av virtuella gäst datorer kontrollerar du att egenskapen *osProfile. windowsConfiguration. enableAutomaticUpdates* har angetts till *True* i definitionen för VM-mallen. Den här egenskapen kan bara anges när du skapar den virtuella datorn.

### <a name="rest-api"></a>REST-API
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

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) för att aktivera automatisk uppdatering av VM-gäster när du skapar eller uppdaterar en virtuell dator.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
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

När automatisk uppdatering av virtuella gäst datorer är aktive rad för en virtuell dator installeras ett VM-tillägg av typen `Microsoft.CPlat.Core.WindowsPatchExtension` på den virtuella datorn. Det här tillägget behöver inte installeras eller uppdateras manuellt eftersom det här tillägget hanteras av Azure-plattformen som en del av den automatiska korrigerings processen för VM-gäster.

Det kan ta mer än tre timmar att aktivera automatiska uppdateringar av virtuella datorer på en virtuell dator, eftersom aktiveringen slutförs under den virtuella datorns låg belastnings tid. Tillägget installeras och uppdateras också vid låg belastnings tider för den virtuella datorn. Om den virtuella datorns låg belastnings tid upphör innan aktiveringen kan slutföras, återupptas aktiveringen vid nästa tillgängliga tid för låg belastning. Om den virtuella datorn tidigare hade automatiskt Windows Update aktiverat genom AutomaticByOS korrigerings läge inaktive ras automatiskt Windows Update för den virtuella datorn när tillägget har installerats.

Du kan kontrol lera om den automatiska korrigeringen av VM-gäster har slutförts och korrigerings tillägget är installerat på den virtuella datorn genom att granska instans visningen för den virtuella datorn. Om aktiveringen är klar installeras tillägget och utvärderings resultatet för den virtuella datorn är tillgängligt under `patchStatus` . Du kan få åtkomst till den virtuella datorns instans vy på flera sätt enligt beskrivningen nedan.

### <a name="rest-api"></a>REST-API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-06-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [Get-AzVM](/powershell/module/az.compute/get-azvm) med `-Status` parametern för att komma åt instans visningen för den virtuella datorn.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell tillhandahåller för närvarande bara information om korrigerings tillägget. Information om `patchStatus` kommer också att vara tillgänglig snart via PowerShell.

### <a name="azure-cli-20"></a>Azure CLI 2.0
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
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) för att utvärdera de tillgängliga korrigeringarna för den virtuella datorn.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [AZ VM-utvärdering – korrigeringsfiler](/cli/azure/vm#az-vm-assess-patches) för att utvärdera de tillgängliga korrigeringarna för den virtuella datorn.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lär dig mer om att skapa och hantera virtuella Windows-datorer](tutorial-manage-vm.md)
