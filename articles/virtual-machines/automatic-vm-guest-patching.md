---
title: Automatisk uppdatering av VM-gäst för virtuella Azure-datorer
description: Lär dig hur du automatiskt korrigerar virtuella datorer i Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 1a6a67fe43d4e0a6086154d71e61fe51680dbcd0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762595"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Förhandsversion: Automatisk uppdatering av VM-gäst för virtuella Azure-datorer

Genom att aktivera automatisk uppdatering av vm-gäster för dina virtuella Azure-datorer kan du underlätta uppdateringshanteringen genom att på ett säkert och automatiskt sätt korrigera virtuella datorer för att upprätthålla säkerhetsefterlevnaden.

Automatisk gästkorrigering av virtuella datorer har följande egenskaper:
- Korrigeringar som klassificeras som *kritiska eller* *säkerhet hämtas* automatiskt och tillämpas på den virtuella datorn.
- Korrigeringar tillämpas under tider med låg belastning i den virtuella datorns tidszon.
- Korrigeringsorkestrering hanteras av Azure och korrigeringar tillämpas enligt [tillgänglighetsprinciperna](#availability-first-patching).
- Hälsan för virtuella datorer, som bestäms via plattformens hälsosignaler, övervakas för att identifiera felkorrigeringar.
- Fungerar för alla VM-storlekar.

> [!IMPORTANT]
> Automatisk uppdatering av VM-gäst är för närvarande i allmänt tillgänglig förhandsversion. En procedur för att delta krävs för att använda funktionerna i den offentliga förhandsversionen som beskrivs nedan.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Hur fungerar automatisk gästkorrigering av virtuella datorer?

Om automatisk uppdatering av vm-gäster är aktiverat  på  en virtuell dator laddas de tillgängliga korrigeringarna Kritisk och Säkerhet ned och tillämpas automatiskt på den virtuella datorn. Den här processen startar automatiskt varje månad när nya korrigeringar släpps. Utvärdering och installation av korrigeringar görs automatiskt och processen omfattar att starta om den virtuella datorn efter behov.

Den virtuella datorn utvärderas regelbundet med några dagars och flera gånger inom en 30-dagars period för att fastställa tillämpliga korrigeringar för den virtuella datorn. Korrigeringarna kan installeras vilken dag som helst på den virtuella datorn under tider med låg belastning för den virtuella datorn. Den här automatiska utvärderingen säkerställer att eventuella korrigeringar som saknas identifieras så snart som möjligt.

Korrigeringar installeras inom 30 dagar efter de månatliga korrigeringsutgåderna, efter den tillgänglighetsbaserade orkestrering som beskrivs nedan. Korrigeringar installeras endast under tider med låg belastning för den virtuella datorn, beroende på den virtuella datorns tidszon. Den virtuella datorn måste köras under tider med låg belastning för att korrigeringar ska installeras automatiskt. Om en virtuell dator stängs av under en regelbunden utvärdering utvärderas den virtuella datorn automatiskt och tillämpliga korrigeringar installeras automatiskt under nästa periodiska utvärdering (vanligtvis inom några dagar) när den virtuella datorn är påslagen.

Definitionsuppdateringar och andra korrigeringar som inte klassificeras som *kritiska eller* *säkerhet installeras* inte via automatisk gästkorrigering av virtuella datorer. Om du vill installera korrigeringar med andra korrigeringsklassificeringar eller schemalägga installationen av korrigeringar i din egen [anpassade underhållsfönstret](./windows/tutorial-config-management.md#manage-windows-updates)kan du använda Uppdateringshantering .

### <a name="availability-first-patching"></a>Tillgänglighetskorrigering först

Korrigeringsinstallationsprocessen samordnas globalt av Azure för alla virtuella datorer som har automatisk gästkorrigering av virtuella datorer aktiverat. Den här orkestrering följer tillgänglighetsprinciper för olika tillgänglighetsnivåer som tillhandahålls av Azure.

För en grupp virtuella datorer som genomgår en uppdatering dirigerar Azure-plattformen uppdateringar:

**Mellan regioner:**
- En månatlig uppdatering samordnas över Azure globalt i en fas för att förhindra globala distributionsfel.
- En fas kan ha en eller flera regioner, och en uppdatering flyttas bara till nästa faser om berättigade virtuella datorer i en fasuppdatering lyckas.
- Geo-parkopplade regioner uppdateras inte samtidigt och kan inte vara i samma regionala fas.
- En uppdaterings framgång mäts genom att spåra den virtuella datorns hälsotillstånd efter uppdateringen. VM-hälsa spåras via hälsoindikatorer för plattformar för den virtuella datorn.

**Inom en region:**
- Virtuella datorer i Tillgänglighetszoner uppdateras inte samtidigt.
- Virtuella datorer som inte ingår i en tillgänglighetsuppsättning batchas efter bästa försök för att undvika samtidiga uppdateringar för alla virtuella datorer i en prenumeration.

**Inom en tillgänglighetsuppsättning:**
- Alla virtuella datorer i en gemensam tillgänglighetsuppsättning uppdateras inte samtidigt.
-   Virtuella datorer i en gemensam tillgänglighetsuppsättning uppdateras inom gränserna för uppdateringsdomänen och virtuella datorer över flera uppdateringsdomäner uppdateras inte samtidigt.

Uppdateringsinstallationsdatumet för en viss virtuell dator kan variera månad för månad, eftersom en specifik virtuell dator kan hämtas i en annan batch mellan de månatliga uppdateringscyklerna.

### <a name="which-patches-are-installed"></a>Vilka korrigeringar är installerade?
Vilka korrigeringar som installeras beror på hur den virtuella datorn distribueras. Varje månad startas en ny global installation där alla säkerhets- och kritiska korrigeringar som utvärderats för en enskild virtuell dator installeras för den virtuella datorn. Sammanslagningen samordnas i alla Azure-regioner i batchar (beskrivs i avsnittet tillgänglighetsuppdatering ovan).

Den exakta uppsättningen korrigeringar som ska installeras varierar beroende på den virtuella datorns konfiguration, inklusive operativsystemtyp och tidsinställning för utvärdering. Det är möjligt för två identiska virtuella datorer i olika regioner att installera olika korrigeringar om det finns fler eller färre korrigeringar tillgängliga när korrigeringsorkestrering når olika regioner vid olika tidpunkter. På samma sätt, men mindre ofta, kan virtuella datorer inom samma region men som utvärderas vid olika tidpunkter (på grund av olika tillgänglighetszoner eller tillgänglighetsuppsättningsbatchar) få olika korrigeringar.

Eftersom automatisk gästkorrigering av virtuella datorer inte konfigurerar korrigeringskällan kan två liknande virtuella datorer som konfigurerats för olika korrigeringskällor, till exempel offentlig lagringsplats jämfört med privat lagringsplats, också se en skillnad i den exakta uppsättningen installerade korrigeringar.

För os-typer som släpper korrigeringar i fast takt kan virtuella datorer som konfigurerats på den offentliga lagringsplatsen för operativsystemet förvänta sig att få samma uppsättning korrigeringar i de olika lanseringsfaserna under en månad. Till exempel virtuella Windows-datorer som konfigurerats för den offentliga Windows Update lagringsplatsen.

När en ny utlösning utlöses varje månad får en virtuell dator minst en korrigeringsutformning varje månad om den virtuella datorn är påslagen under tider med låg belastning. Detta säkerställer att den virtuella datorn korrigeras med de senaste tillgängliga säkerhets- och kritiska korrigeringarna varje månad. För att säkerställa konsekvens i uppsättningen korrigeringar som installerats kan du konfigurera dina virtuella datorer för att utvärdera och ladda ned korrigeringar från dina egna privata lagringsplatsen.

## <a name="supported-os-images"></a>Operativsystemavbildningar som stöds
Endast virtuella datorer som skapats från vissa os-plattformsavbildningar stöds för närvarande i förhandsversionen. Anpassade avbildningar stöds för närvarande inte i förhandsversionen.

Följande plattforms-SKU:er stöds för närvarande (och fler läggs till regelbundet):

| Publisher               | OS-erbjudande      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Korrigera orkestreringslägen
Virtuella datorer på Azure har nu stöd för följande läge för korrigeringsorkestrering:

**AutomaticByPlatform:**
- Det här läget stöds för både virtuella Linux- och Windows-datorer.
- Det här läget möjliggör automatisk gästkorrigering av virtuella datorer för den virtuella datorn och efterföljande korrigeringsinstallationer samordnas av Azure.
- Det här läget krävs för tillgänglighetskorrigering först.
- Det här läget stöds endast för virtuella datorer som skapas med hjälp av operativsystemplattformsavbildningar som stöds ovan.
- För virtuella Windows-datorer inaktiverar inställningen det här läget även de inbyggda automatiska uppdateringarna på den virtuella Windows-datorn för att undvika duplicering.
- Om du vill använda det här läget på virtuella Linux-datorer anger du egenskapen `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` i VM-mallen.
- Om du vill använda det här läget på virtuella Windows-datorer anger du egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=true` och anger egenskapen i  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` VM-mallen.

**AutomaticByOS:**
- Det här läget stöds endast för virtuella Windows-datorer.
- Det här läget aktiverar automatiska uppdateringar på den virtuella Windows-datorn och korrigeringar installeras på den virtuella datorn via automatiska uppdateringar.
- Det här läget stöder inte tillgänglighetskorrigering först.
- Det här läget är inställt som standard om inget annat korrigeringsläge har angetts för en virtuell Windows-dator.
- Om du vill använda det här läget på virtuella Windows-datorer anger du egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=true` och anger egenskapen i  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` VM-mallen.

**Manuell:**
- Det här läget stöds endast för virtuella Windows-datorer.
- Det här läget inaktiverar automatiska uppdateringar på den virtuella Windows-datorn.
- Det här läget stöder inte tillgänglighetskorrigering först.
- Det här läget bör anges när du använder anpassade korrigeringslösningar.
- Om du vill använda det här läget på virtuella Windows-datorer anger du egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates=false` och anger egenskapen i  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` VM-mallen.

**ImageDefault:**
- Det här läget stöds endast för virtuella Linux-datorer.
- Det här läget stöder inte tillgänglighetskorrigering först.
- Det här läget använder standardkonfigurationen för korrigering i den avbildning som används för att skapa den virtuella datorn.
- Det här läget anges som standard om inget annat korrigeringsläge har angetts för en virtuell Linux-dator.
- Om du vill använda det här läget på virtuella Linux-datorer anger du egenskapen `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` i vm-mallen.

> [!NOTE]
>För virtuella Windows-datorer kan egenskapen `osProfile.windowsConfiguration.enableAutomaticUpdates` för närvarande bara anges när den virtuella datorn först skapas. Det finns för närvarande inte stöd för att växla från manuellt till automatiskt läge eller från automatiska lägen till manuellt läge. Det finns stöd för att växla från AutomaticByOS-läge till AutomaticByPlatfom-läge.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Krav för att aktivera automatisk gästkorrigering av virtuella datorer

- Den virtuella datorn måste ha Azure VM-agenten för [Windows](./extensions/agent-windows.md) eller [Linux](./extensions/agent-linux.md) installerad.
- För virtuella Linux-datorer måste Azure Linux-agenten vara version 2.2.53.1 eller senare. [Uppdatera Linux-agenten](./extensions/update-linux-agent.md) om den aktuella versionen är lägre än den version som krävs.
- För virtuella Windows-datorer Windows Update-tjänsten köras på den virtuella datorn.
- Den virtuella datorn måste kunna komma åt de konfigurerade uppdateringsslutpunkterna. Om den virtuella datorn är konfigurerad för att använda privata lagringsplatsen för Linux eller Windows Server Update Services (WSUS) för virtuella Windows-datorer, måste relevanta uppdateringsslutpunkter vara tillgängliga.
- Använd Compute API version 2020-12-01 eller senare. Compute API-version 2020-06-01 kan användas för virtuella Windows-datorer med begränsad funktionalitet.

För att aktivera förhandsversionsfunktionen krävs en enda gång för funktionerna **InGuestAutoPatchVMPreview** och **InGuestPatchVMPreview** per prenumeration, enligt beskrivningen i följande avsnitt.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du aktiverar förhandsversionen för din prenumeration:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera förhandsversionen för din prenumeration.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az feature register](/cli/azure/feature#az_feature_register) för att aktivera förhandsversionen för din prenumeration.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Aktivera automatisk uppdatering av VM-gäster
Om du vill aktivera automatisk uppdatering av vm-gäster på en virtuell Windows-dator, se till att egenskapen *osProfile.windowsConfiguration.enableAutomaticUpdates* är inställd på *sant* i malldefinitionen för den virtuella datorn. Den här egenskapen kan bara anges när du skapar den virtuella datorn. Den här ytterligare egenskapen gäller inte för virtuella Linux-datorer.

### <a name="rest-api-for-linux-vms"></a>REST API för virtuella Linux-datorer
I följande exempel beskrivs hur du aktiverar automatisk gästkorrigering av virtuella datorer:

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
I följande exempel beskrivs hur du aktiverar automatisk gästkorrigering av virtuella datorer:

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
Använd [cmdleten Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) för att aktivera automatisk uppdatering av VM-gäst när du skapar eller uppdaterar en virtuell dator.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Azure CLI för virtuella Windows-datorer
Använd [az vm create för](/cli/azure/vm#az_vm_create) att aktivera automatisk gästkorrigering av virtuella datorer när du skapar en ny virtuell dator. I följande exempel konfigureras automatisk vm-gästkorrigering för en virtuell dator med namnet *myVM* i resursgruppen med namnet *myResourceGroup:*

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Om du vill ändra en befintlig virtuell dator använder [du az vm update](/cli/azure/vm#az_vm_update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Aktivera och bedöma

> [!NOTE]
>Det kan ta mer än tre timmar att aktivera automatiska VM-gästuppdateringar på en virtuell dator, eftersom det är klart under den virtuella datorns lågbelastningstider. Eftersom utvärderings- och korrigeringsinstallationen endast sker under tider med låg belastning måste den virtuella datorn också köras under tider med låg belastning för att tillämpa korrigeringar.

När automatisk gästkorrigering av virtuella datorer är aktiverat för en virtuell dator installeras ett VM-tillägg av typen på en virtuell Linux-dator eller ett VM-tillägg av typen installeras på en `Microsoft.CPlat.Core.LinuxPatchExtension` `Microsoft.CPlat.Core.WindowsPatchExtension` virtuell Windows-dator. Det här tillägget behöver inte installeras eller uppdateras manuellt, eftersom det här tillägget hanteras av Azure-plattformen som en del av den automatiska gästkorrigeringsprocessen för virtuella datorer.

Det kan ta mer än tre timmar att aktivera automatiska VM-gästuppdateringar på en virtuell dator, eftersom det är klart under den virtuella datorns lågbelastningstider. Tillägget installeras och uppdateras även under tider med låg belastning för den virtuella datorn. Om den virtuella datorns timmar med låg belastning slutar innan det går att slutföra funktionen återupptas aktiverandeprocessen vid nästa tillgängliga tidpunkt vid låg belastning.

Automatiska uppdateringar inaktiveras i de flesta fall och korrigeringsinstallationen görs via tillägget framöver. Följande villkor gäller.
- Om en virtuell Windows-dator tidigare hade automatisk Windows Update aktiverat via AutomaticByOS-korrigeringsläget stängs Automatisk Windows Update av för den virtuella datorn när tillägget installeras.
- För virtuella Ubuntu-datorer inaktiveras de automatiska standarduppdateringarna automatiskt när automatisk uppdatering av gäst för virtuella datorer har slutförts.
- För RHEL måste automatiska uppdateringar inaktiveras manuellt (detta är en begränsning i förhandsversionen). Utföra:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Om du vill kontrollera om den automatiska gästkorrigeringen av virtuella datorer har slutförts och uppdateringstillägget är installerat på den virtuella datorn kan du granska den virtuella datorns instansvy. Om processen är klar installeras tillägget och utvärderingsresultaten för den virtuella datorn blir tillgängliga under `patchStatus` . Den virtuella datorns instansvy kan nås via flera olika sätt enligt beskrivningen nedan.

### <a name="rest-api"></a>REST-API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Get-AzVM](/powershell/module/az.compute/get-azvm) med `-Status` parametern för att få åtkomst till instansvyn för den virtuella datorn.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell tillhandahåller för närvarande endast information om korrigeringstillägget. Information om `patchStatus` kommer också att vara tillgänglig snart via PowerShell.

### <a name="azure-cli"></a>Azure CLI
Använd [az vm get-instance-view för](/cli/azure/vm#az_vm_get_instance_view) att få åtkomst till instansvyn för den virtuella datorn.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Förstå korrigeringsstatusen för den virtuella datorn

Avsnittet `patchStatus` i instansvisningssvaret innehåller information om den senaste utvärderingen och den senaste korrigeringsinstallationen för den virtuella datorn.

Utvärderingsresultaten för den virtuella datorn kan granskas i `availablePatchSummary` avsnittet . En utvärdering utförs regelbundet för en virtuell dator som har automatisk gästkorrigering av virtuella datorer aktiverat. Antalet tillgängliga korrigeringar efter en utvärdering anges under `criticalAndSecurityPatchCount` och `otherPatchCount` resultat. Automatisk gästkorrigering av virtuella datorer installerar alla  korrigeringar som utvärderas under klassificeringarna *kritisk och* säkerhetskorrigering. Alla andra utvärderade korrigeringar hoppas över.

Installationsresultaten för korrigeringen för den virtuella datorn kan granskas under `lastPatchInstallationSummary` avsnittet . Det här avsnittet innehåller information om det senaste installationsförsöket för korrigeringen på den virtuella datorn, inklusive antalet korrigeringar som har installerats, väntar, misslyckats eller hoppas över. Korrigeringar installeras endast under underhållsfönstret vid låg belastning för den virtuella datorn. Väntande och misslyckade korrigeringar försöks automatiskt igen under underhållsfönstret vid låg belastning.

## <a name="on-demand-patch-assessment"></a>Utvärdering av korrigering på begäran
Om automatisk gästkorrigering av virtuella datorer redan har aktiverats för den virtuella datorn utförs en regelbunden korrigeringsbedömning på den virtuella datorn under den virtuella datorns lågbelastningstid. Den här processen är automatisk och resultatet av den senaste utvärderingen kan granskas via den virtuella datorns instansvy enligt beskrivningen tidigare i det här dokumentet. Du kan också utlösa en korrigeringsbedömning på begäran för den virtuella datorn när som helst. Det kan ta några minuter att slutföra korrigeringsutvärderingen och statusen för den senaste utvärderingen uppdateras i den virtuella datorns instansvy.

För att aktivera förhandsgranskningsfunktionen krävs en enda gång för funktionen **InGuestPatchVMPreview** per prenumeration. Den här funktionsförhandsvisningen skiljer sig från den automatiska funktionsregistreringen för gästkorrigering av virtuella datorer som gjordes tidigare för **InGuestAutoPatchVMPreview**. Att aktivera den ytterligare funktionsförhandsvisningen är ett separat och ytterligare krav. Funktionsförhandsvisningen för korrigeringsbedömning på [](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) begäran kan aktiveras efter förhandsgranskningsaktiveringsprocessen som beskrevs tidigare för automatisk gästkorrigering av virtuella datorer.

> [!NOTE]
>Utvärdering av korrigeringar på begäran utlöser inte korrigeringsinstallation automatiskt. Om du har aktiverat automatisk uppdatering av VM-gäst kommer de utvärderade och tillämpliga korrigeringarna för den virtuella datorn att installeras under den virtuella datorns lågbelastningstid, efter den uppdateringsprocess för tillgänglighet först som beskrivs tidigare i det här dokumentet.

### <a name="rest-api"></a>REST-API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) för att utvärdera tillgängliga korrigeringar för den virtuella datorn.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Använd [az vm assess-patches](/cli/azure/vm#az_vm_assess_patches) för att utvärdera tillgängliga korrigeringar för den virtuella datorn.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Läs mer om att skapa och hantera virtuella Windows-datorer](./windows/tutorial-manage-vm.md)
