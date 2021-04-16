---
title: Automatiska uppgraderingar av operativsystemavbildningar med skalningsuppsättningar för virtuella Azure-datorer
description: Lär dig hur du automatiskt uppgraderar OS-avbildningen på VM-instanser i en skalningsuppsättning
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: automatic-os-upgrade
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 1e32ff4bc1c39e8a3385f8037f88cedbdc17d3a6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375754"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatiska uppgraderingar av OS-avbildningar i skalningsuppsättningar med virtuella Azure-datorer

Genom att aktivera automatiska uppgraderingar av os-avbildningar på din skalningsuppsättning kan du underlätta uppdateringshanteringen genom att uppgradera OS-disken automatiskt för alla instanser i skalningsuppsättningen.

Automatisk uppgradering av operativsystemet har följande egenskaper:

- När den har konfigurerats tillämpas den senaste OS-avbildningen som publicerats av avbildningsutgivare automatiskt på skalningsuppsättningen utan att användaren behöver göra något.
- Uppgraderar batchar av instanser på rullande sätt varje gång en ny avbildning publiceras av utgivaren.
- Integrerar med programhälsoavsökningar och [tillägget För programhälsa.](virtual-machine-scale-sets-health-extension.md)
- Fungerar för alla VM-storlekar och för både Windows- och Linux-avbildningar.
- Du kan välja bort automatiska uppgraderingar när som helst (OS-uppgraderingar kan också initieras manuellt).
- OS-disken för en virtuell dator ersätts med den nya OS-disken som skapats med den senaste avbildningsversionen. Konfigurerade tillägg och anpassade dataskript körs, medan beständiga datadiskar bevaras.
- [Tilläggssekvensering](virtual-machine-scale-sets-extension-sequencing.md) stöds.
- Automatisk uppgradering av os-avbildning kan aktiveras på en skalningsuppsättning av valfri storlek.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hur fungerar automatisk uppgradering av OS-avbildningar?

En uppgradering fungerar genom att ersätta OS-disken för en virtuell dator med en ny disk som skapats med den senaste avbildningsversionen. Alla konfigurerade tillägg och anpassade dataskript körs på OS-disken, medan beständiga datadiskar bevaras. För att minimera programmets driftstopp sker uppgraderingar i batchar, och högst 20 % av skalningsuppsättningen uppgraderas när som helst. Du kan också integrera en Azure Load Balancer programhälsoavsökning eller [programhälsotillägget](virtual-machine-scale-sets-health-extension.md). Vi rekommenderar att du införlivar ett program pulsslag och verifierar att uppgraderingen lyckas för varje batch i uppgraderingsprocessen.

Uppgraderingsprocessen fungerar på följande sätt:
1. Innan du påbörjar uppgraderingsprocessen ser initieraren till att högst 20 % av instanserna i hela skalningsuppsättningen är felaktiga (av någon anledning).
2. Uppgraderingsdirigeringen identifierar batchen med VM-instanser som ska uppgraderas, där en batch har högst 20 % av det totala instansantalet, med en minsta batchstorlek på en virtuell dator.
3. OS-disken för den valda batchen med VM-instanser ersätts med en ny OS-disk som skapats från den senaste avbildningen. Alla angivna tillägg och konfigurationer i skalningsuppsättningsmodellen tillämpas på den uppgraderade instansen.
4. För skalningsuppsättningar med konfigurerade programhälsoavsökningar eller tillägget för programhälsa väntar uppgraderingen i upp till 5 minuter på att instansen ska bli felfri innan nästa batch uppgraderas. Om en instans inte återställer sitt hälsotillstånd inom 5 minuter efter en uppgradering återställs som standard den tidigare OS-disken för instansen.
5. Uppgraderingsdirigeringen spårar också procentandelen instanser som blir felaktiga efter en uppgradering. Uppgraderingen avbryts om mer än 20 % av de uppgraderade instanserna blir felaktiga under uppgraderingen.
6. Ovanstående process fortsätter tills alla instanser i skalningsuppsättningen har uppgraderats.

Orkestratorn för skalningsuppsättningens OS-uppgradering kontrollerar den övergripande skalningsuppsättningens hälsa innan varje batch uppgraderas. När du uppgraderar en batch kan det finnas andra samtidiga planerade eller oplanerade underhållsaktiviteter som kan påverka hälsotillståndet för dina skalningsuppsättningsinstanser. I sådana fall om mer än 20 % av skalningsuppsättningens instanser blir felaktiga stoppas uppgraderingen av skalningsuppsättningen i slutet av den aktuella batchen.

> [!NOTE]
>Automatisk uppgradering av operativsystemet uppgraderar inte referensavbildningens SKU på skalningsuppsättningen. Om du vill ändra SKU:n (till exempel Ubuntu 16.04-LTS till 18.04-LTS) måste du uppdatera skalningsuppsättningsmodellen direkt med önskad bild-SKU. [](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) Utgivare och erbjudande för avbildningar kan inte ändras för en befintlig skalningsuppsättning.  

## <a name="supported-os-images"></a>Operativsystemavbildningar som stöds
Endast vissa operativsystemplattformsavbildningar stöds för närvarande. Anpassade [avbildningar stöds](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) om skalningsuppsättningen använder anpassade avbildningar [via Shared Image Gallery](../virtual-machines/shared-image-galleries.md).

Följande plattforms-SKU:er stöds för närvarande (och fler läggs till regelbundet):

| Publisher               | OS-erbjudande      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| OpenLogic               | CentOS        | 7.5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Krav för att konfigurera automatisk uppgradering av OS-avbildning

- Versionsegenskapen för avbildningen måste vara inställd på *senaste*. 
- Använd programhälsoavsökningar eller [programhälsotillägg för](virtual-machine-scale-sets-health-extension.md) icke-Service Fabric skalningsuppsättningar.
- Använd Compute API version 2018-10-01 eller senare.
- Se till att externa resurser som anges i skalningsuppsättningsmodellen är tillgängliga och uppdaterade. Exempel är SAS-URI för startnyttolast i egenskaper för VM-tillägg, nyttolast i lagringskonto, referens till hemligheter i modellen med mera.
- För skalningsuppsättningar som använder virtuella Windows-datorer och börjar med Compute API-version 2019-03-01 måste egenskapen *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* anges till *falskt* i skalningsuppsättningsmodelldefinitionen. Egenskapen ovan aktiverar uppgraderingar av virtuella datorer där "Windows Update" tillämpar operativsystemkorrigeringar utan att ersätta OS-disken. När automatiska os-avbildningsuppgraderingar är aktiverade på din skalningsuppsättning krävs ingen ytterligare uppdatering Windows Update ".

### <a name="service-fabric-requirements"></a>Service Fabric krav

Om du använder Service Fabric måste du kontrollera att följande villkor är uppfyllda:
-   Service Fabric [hållbarhetsnivån](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) är Silver eller Guld och inte Brons (förutom tillståndslösa nodtyper, som har stöd för automatiska OS-uppgraderingar).
-   Tillägget Service Fabric på modelldefinitionen för skalningsuppsättningen måste ha TypeHandlerVersion 1.1 eller senare.
-   Hållbarhetsnivån ska vara samma i Service Fabric och Service Fabric på modelldefinitionen för skalningsuppsättningen.
- Ytterligare en hälsoavsökning eller användning av tillägget för programhälsa krävs inte.

Se till att hållbarhetsinställningarna inte är felmatchade på Service Fabric och Service Fabric tillägg, eftersom ett matchningsfel leder till uppgraderingsfel. Hållbarhetsnivåer kan ändras enligt riktlinjerna som beskrivs på den [här sidan.](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)


## <a name="automatic-os-image-upgrade-for-custom-images"></a>Automatisk uppgradering av OS-avbildning för anpassade avbildningar

Automatisk uppgradering av os-avbildning stöds för anpassade avbildningar som distribueras [via Shared Image Gallery](../virtual-machines/shared-image-galleries.md). Andra anpassade avbildningar stöds inte för automatiska uppgraderingar av operativsystemavbildningar.

### <a name="additional-requirements-for-custom-images"></a>Ytterligare krav för anpassade avbildningar
- Installations- och konfigurationsprocessen för automatisk uppgradering av os-avbildningar är densamma för alla skalningsuppsättningar som beskrivs i [konfigurationsavsnittet](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) på den här sidan.
- Skalningsuppsättningar som konfigurerats för automatiska uppgraderingar av os-avbildningar uppgraderas till den senaste versionen av [](../virtual-machines/shared-image-galleries.md#replication) Shared Image Gallery-avbildningen när en ny version av avbildningen publiceras och replikeras till den regionen för skalningsuppsättningen. Om den nya avbildningen inte replikeras till den region där skalan distribueras uppgraderas inte skalningsuppsättningsinstanserna till den senaste versionen. Med regional avbildningsreplikering kan du styra hur den nya avbildningen distribueras för dina skalningsuppsättningar.
- Den nya avbildningsversionen bör inte undantas från den senaste versionen för galleriavbildningen. Avbildningsversioner som undantas från galleriavbildningens senaste version distribueras inte till skalningsuppsättningen via automatisk uppgradering av OPERATIVSYSTEMavbildningen.

> [!NOTE]
>Det kan ta upp till 3 timmar för en skalningsuppsättning att utlösa den första avbildningsuppgraderingsutgraderingen när skalningsuppsättningen först har konfigurerats för automatiska OS-uppgraderingar. Det här är en tidsfördröjning per skalningsuppsättning. Efterföljande bildutrullningar utlöses på skalningsuppsättningen inom 30–60 minuter.


## <a name="configure-automatic-os-image-upgrade"></a>Konfigurera automatisk uppgradering av OS-avbildning
Om du vill konfigurera automatisk uppgradering av operativsystemavbildningar, se till att egenskapen *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* är inställd på *sant* i skalningsuppsättningsmodelldefinitionen.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du ställer in automatiska os-uppgraderingar på en skalningsuppsättningsmodell:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att konfigurera automatiska uppgraderingar av OS-avbildningar för din skalningsuppsättning. I följande exempel konfigureras automatiska uppgraderingar för skalningsuppsättningen med namnet *myScaleSet* i resursgruppen med namnet *myResourceGroup:*

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss update för att](/cli/azure/vmss#az-vmss-update) konfigurera automatiska uppgraderingar av OS-avbildningar för din skalningsuppsättning. Använd Azure CLI 2.0.47 eller högre. I följande exempel konfigureras automatiska uppgraderingar för skalningsuppsättningen med namnet *myScaleSet* i resursgruppen med namnet *myResourceGroup:*

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>När du har konfigurerat automatiska uppgraderingar av os-avbildningar för din skalningsuppsättning måste du även ta de virtuella datorerna med skalningsuppsättningen till den senaste skalningsuppsättningsmodellen om skalningsuppsättningen använder uppgraderingsprincipen ["Manuell".](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

## <a name="using-application-health-probes"></a>Använda hälsoavsökningar för program

Under en uppgradering av operativsystemet uppgraderas VM-instanser i en skalningsuppsättning en batch i taget. Uppgraderingen bör endast fortsätta om kundprogrammet är felfritt på de uppgraderade VM-instanserna. Vi rekommenderar att programmet ger hälsosignaler till skalningsuppsättningens OS-uppgraderingsmotor. Som standard tar plattformen under OS-uppgraderingar hänsyn till VM-energitillstånd och tilläggsetableringstillstånd för att avgöra om en VM-instans är felfri efter en uppgradering. Under os-uppgraderingen av en virtuell datorinstans ersätts OS-disken på en VM-instans med en ny disk baserat på den senaste avbildningsversionen. När uppgraderingen av operativsystemet har slutförts körs de konfigurerade tilläggen på dessa virtuella datorer. Programmet anses vara felfritt endast när alla tillägg på instansen har etablerats.

En skalningsuppsättning kan också konfigureras med Programhälsoavsökningar för att ge plattformen korrekt information om programmets pågående tillstånd. Programhälsoavsökningar är anpassade Load Balancer avsökningar som används som en hälsosignal. Programmet som körs på en skalningsuppsättnings-VM-instans kan svara på externa HTTP- eller TCP-begäranden som anger om det är felfritt. Mer information om hur Custom Load Balancer-avsökningar fungerar finns i Förstå [lastbalanseringsavsökningar.](../load-balancer/load-balancer-custom-probe-overview.md) Programhälsoavsökningar stöds inte för Service Fabric skalningsuppsättningar. Icke-Service Fabric skalningsuppsättningar kräver antingen Load Balancer programhälsoavsökningar eller [tillägget för programhälsa.](virtual-machine-scale-sets-health-extension.md)

Om skalningsuppsättningen är konfigurerad för att använda flera placeringsgrupper måste [avsökningar med Standard Load Balancer](../load-balancer/load-balancer-overview.md) användas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurera en anpassad Load Balancer som programhälsoavsökning på en skalningsuppsättning
Som bästa praxis bör du uttryckligen skapa en lastbalanseringsavsökning för skalningsuppsättningens hälsa. Samma slutpunkt för en befintlig HTTP-avsökning eller TCP-avsökning kan användas, men en hälsoavsökning kan kräva ett annat beteende än en traditionell lastbalanseringsavsökning. En traditionell lastbalanseringsavsökning kan till exempel returnera ett feltillstånd om belastningen på instansen är för hög, men det skulle inte vara lämpligt för att fastställa instanshälsan under en automatisk uppgradering av operativsystemet. Konfigurera avsökningen så att den har en hög avsökningshastighet på mindre än två minuter.

Lastbalanseringsavsökningen kan refereras till i *networkProfile* för skalningsuppsättningen och kan associeras med antingen en intern eller offentlig lastbalanserare på följande sätt:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> När du använder automatiska OS-Service Fabric distribueras den nya operativsystemavbildningen Uppdateringsdomän av uppdateringsdomän för att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Om du vill använda automatiska OS-uppgraderingar Service Fabric din klusternodtyp konfigureras för att använda Silver-hållbarhetsnivån eller högre. För nivån bronsbeständighet stöds automatisk uppgradering av operativsystemet endast för tillståndslösa nodtyper. Mer information om hållbarhetsegenskaperna för Service Fabric kluster finns i den [här dokumentationen.](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)

### <a name="keep-credentials-up-to-date"></a>Håll autentiseringsuppgifterna uppdaterade
Om din skalningsuppsättning använder autentiseringsuppgifter för att få åtkomst till externa resurser, till exempel ett VM-tillägg som konfigurerats att använda en SAS-token för lagringskontot, ser du till att autentiseringsuppgifterna uppdateras. Om några autentiseringsuppgifter, inklusive certifikat och token, har upphört att gälla, misslyckas uppgraderingen och den första batchen med virtuella datorer lämnas i ett misslyckat tillstånd.

De rekommenderade stegen för att återställa virtuella datorer och återaktivera automatisk uppgradering av operativsystemet om det uppstår ett resursautentiseringsfel är:

* Återskapa token (eller andra autentiseringsuppgifter) som skickas till dina tillägg.
* Se till att alla autentiseringsuppgifter som används inifrån den virtuella datorn för att prata med externa entiteter är uppdaterade.
* Uppdatera tillägg i skalningsuppsättningsmodellen med eventuella nya token.
* Distribuera den uppdaterade skalningsuppsättningen, vilket uppdaterar alla VM-instanser, inklusive de misslyckade.

## <a name="using-application-health-extension"></a>Använda tillägget för programhälsa
Tillägget För programhälsa distribueras i en instans av en VM-skalningsuppsättning och rapporterar om VM-hälsan inifrån skalningsuppsättningsinstansen. Du kan konfigurera tillägget för att söka efter en programslutpunkt och uppdatera status för programmet på den instansen. Den här instansstatusen kontrolleras av Azure för att avgöra om en instans är berättigad till uppgraderingsåtgärder.

Eftersom tillägget rapporterar hälsotillstånd inifrån en virtuell dator kan tillägget användas i situationer där externa avsökningar, till exempel programhälsoavsökningar (som använder anpassade [Azure Load Balancer-avsökningar)](../load-balancer/load-balancer-custom-probe-overview.md)inte kan användas.

Det finns flera sätt att distribuera Application Health-tillägget till dina skalningsuppsättningar som beskrivs i exemplen i den [här artikeln.](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Hämta historik över automatiska uppgraderingar av os-avbildningar
Du kan kontrollera historiken för den senaste os-uppgraderingen som utförts på din skalningsuppsättning med Azure PowerShell, Azure CLI 2.0 eller REST-API:erna. Du kan hämta historik för de senaste fem os-uppgraderingsförsöken under de senaste två månaderna.

### <a name="rest-api"></a>REST-API
I följande exempel [används REST API för att](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) kontrollera status för skalningsuppsättningen med namnet *myScaleSet* i resursgruppen med namnet *myResourceGroup:*

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

GET-anropet returnerar egenskaper som liknar följande exempelutdata:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Get-AzVmss](/powershell/module/az.compute/get-azvmss) för att kontrollera historiken för OS-uppgradering för din skalningsuppsättning. I följande exempel visas hur du granskar uppgraderingsstatusen för operativsystemet för en skalningsuppsättning med namnet *myScaleSet* i resursgruppen med namnet *myResourceGroup:*

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) för att kontrollera historiken för OS-uppgradering för din skalningsuppsättning. Använd Azure CLI 2.0.47 eller högre. I följande exempel visas hur du granskar uppgraderingsstatusen för operativsystemet för en skalningsuppsättning med namnet *myScaleSet* i resursgruppen med namnet *myResourceGroup:*

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hur hämtar jag den senaste versionen av en plattformsoperativsystemavbildning?

Du kan hämta tillgängliga avbildningsversioner för SKU:er som stöds för automatisk uppgradering av operativsystem med hjälp av följande exempel:

### <a name="rest-api"></a>REST-API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Utlösa os-avbildningsuppgraderingar manuellt
När automatisk uppgradering av OS-avbildningar är aktiverat på din skalningsuppsättning behöver du inte utlösa avbildningsuppdateringar manuellt på din skalningsuppsättning. Orchestrator för operativsystemuppgradering tillämpar automatiskt den senaste tillgängliga avbildningsversionen på dina skalningsuppsättningsinstanser utan manuella åtgärder.

I vissa fall där du inte vill vänta tills initieraren har tillämpat den senaste avbildningen kan du utlösa en uppgradering av operativsystemavbildningen manuellt med hjälp av exemplen nedan.

> [!NOTE]
> Manuell utlösare av uppgraderingar av OPERATIVSYSTEMavbildningar ger inte automatiska återställningsfunktioner. Om en instans inte återställer sitt hälsotillstånd efter en uppgraderingsåtgärd går det inte att återställa den tidigare OS-disken.

### <a name="rest-api"></a>REST-API
Använd [API-anropet Starta](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) uppgradering av operativsystem för att starta en löpande uppgradering för att flytta alla vm-skalningsuppsättningsinstanser till den senaste tillgängliga os-avbildningsversionen. Instanser som redan kör den senaste tillgängliga versionen av operativsystemet påverkas inte. I följande exempel visas hur du kan starta en rullande os-uppgradering på en skalningsuppsättning med namnet *myScaleSet* i resursgruppen med namnet *myResourceGroup:*

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) för att kontrollera os-uppgraderingshistoriken för din skalningsuppsättning. I följande exempel visas hur du kan starta en rullande os-uppgradering på en skalningsuppsättning med namnet *myScaleSet* i resursgruppen med namnet *myResourceGroup:*

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss rolling-upgrade start för](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) att kontrollera historiken för OS-uppgradering för din skalningsuppsättning. Använd Azure CLI 2.0.47 eller högre. I följande exempel visas hur du kan starta en rullande os-uppgradering på en skalningsuppsättning med namnet *myScaleSet* i resursgruppen med namnet *myResourceGroup:*

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda mallar för att distribuera en skalningsuppsättning med automatiska OS-uppgraderingar för avbildningar som stöds, till exempel [Ubuntu 16.04-LTS.](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>Nästa steg
Fler exempel på hur du använder automatiska OS-uppgraderingar med skalningsuppsättningar finns på [GitHub-lagringsplatsen](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).