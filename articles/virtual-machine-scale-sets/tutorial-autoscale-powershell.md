---
title: Självstudie – Autoskala en skalnings uppsättning med Azure PowerShell
description: Läs hur du automatiskt skalar en VM-skalningsuppsättning med Azure PowerShell allteftersom CPU-kraven varierar
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 8ee124f866a5241620671ff84c24f3713f62efe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078477"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Självstudie: Skala en VM-skalningsuppsättning automatiskt med Azure PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

När du skapar en skalningsuppsättning, definierar du antalet virtuella datorinstanser som du vill köra. När ditt program behöver ändras, kan du automatiskt öka eller minska antalet virtuella datorinstanser. Möjligheten att skala automatiskt låter dig hålla dig uppdaterad med kundernas behov eller svara på ändringar i programprestandan under hela livscykeln för din app. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Använd automatisk skalning med en skalningsuppsättning
> * Skapa och använd regler för automatisk skalning
> * Belastningstesta virtuella datorinstanser och utlös regler för automatisk skalning
> * Skala tillbaka automatiskt när efterfrågan minskar

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Det finns ett känt problem som påverkar Azure PowerShell-modulversion 6.8.1 eller senare, inklusive den aktuella versionen av Azure Cloud Shell. Den här självstudien kan endast köras med Azure PowerShell-modulversion 6.0.0 till 6.8.0. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Definiera vissa variabler för din skalningsuppsättning för att göra det enklare att skapa automatiska regler. I följande exempel definieras variabler för skalningsuppsättningen *myScaleSet* i resursgruppen *myResourceGroup* och i regionen *USA, östra*. Ditt prenumerations-ID hämtas med [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Om du har flera prenumerationer som är kopplade till ditt konto, returneras endast den första prenumerationen. Justera namn och prenumerations-ID på följande sätt:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Skapa nu en skalningsuppsättning för en virtuell dator med [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). För att distribuera trafik till flera virtuella datorinstanser så skapas även en lastbalanserare. Lastbalanseraren innehåller regler för att distribuera trafik på TCP-port 80 och för att tillåta trafik för fjärrskrivbordet på TCP-port 3389 och PowerShell-fjärrkommunikation på TCP-port 5985. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

## <a name="create-a-rule-to-autoscale-out"></a>Skapa en regel för att automatiskt skala ut
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du kan styra vilka mått som ska övervakas, som CPU eller disk, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många virtuella datorinstanser som ska läggas till skalningsuppsättningen.

Nu ska vi skapa en regel med [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) som ökar antalet virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen är större än 70 % över en 5 minutersperiod. När regeln utlöses, ökar antalet virtuella datorinstanser med tre.

Följande parametrar används för den här regeln:

| Parameter               | Förklaring                                                                                                         | Värde          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *– MetricName*           | Prestandamått för att övervaka och tillämpa åtgärder för skalningsuppsättningar på.                                                   | Processorprocentandel |
| *-TimeGrain*            | Hur ofta måtten samlas in för analys.                                                                   | 1 minut       |
| *-MetricStatistic*      | Definierar hur de insamlade mätvärdena ska aggregeras för analys.                                                | Genomsnitt        |
| *-TimeWindow*           | Tidsperioden som övervakas innan värdena för måttet och tröskelvärdet jämförs.                                   | 5 minuter      |
| *– Operatör*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än   |
| *– Tröskel*            | Det värde som får regeln för automatisk skalning att utlösa en åtgärd.                                                      | 70 %            |
| *-ScaleActionDirection* | Anger om skalningsuppsättningen ska skala upp eller ner när regeln gäller.                                             | Öka       |
| *-ScaleActionScaleType* | Anger att antalet virtuella datorinstanser ska ändras med ett specifikt värde.                                    | Ändra antal   |
| *-ScaleActionValue*     | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                            | 3              |
| *-ScaleActionCooldown*  | Hur lång tid ska gå innan regeln tillämpas igen så att de automatiska skalningsåtgärderna har tid att börja gälla. | 5 minuter      |

I följande exempel skapas ett objekt med namnet *myRuleScaleOut* som innehåller den här regeln för att skapa upp. *-MetricResourceId* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppnamn och namn på skalningsuppsättning:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Skapa en regel för att automatiskt skala in
På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.

Skapa en annan regel med [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) som minskar antalet virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen faller under 30 % över en 5 minutersperiod. När regeln utlöses minskar antalet virtuella dator instanser med ett. I följande exempel skapas ett objekt med namnet *myRuleScaleDown* som innehåller den här skalnings regeln. *-MetricResourceId* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppnamn och namn på skalningsuppsättning:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Definiera en autoskalningsprofil
Skapa en profil för att associera automatiska skalningsregler med en skalningsuppsättning. Autoskalningsprofilen definierar standard-, minsta och största kapacitet för skalningsuppsättning och associerar automatiska skalningsregler. Skapa en autoskalningsprofil med [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). Följande exempel anger standard- och minimumkapacitet på *2* virtuella datorinstanser och högst *10*. De regler för att skala ut och skala in som har skapats i föregående steg appliceras:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>Använd autoskalning-profil i en skalnings uppsättning
Det sista steget är att använda profilen för automatisk skalning på din skalningsuppsättning. Din skalningsuppsättning kan därefter automatiskt skala in eller ut baserat på programmets efterfrågan. Använda autoskalningsprofilen med [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) på följande sätt:

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Generera CPU-belastning på skalningsuppsättningen
Om du vill testa reglerna för automatisk skalning kan du generera lite CPU-belastning på de virtuella datorinstanserna i skalningsuppsättningen. Den här simulerade CPU-belastningen gör att reglerna för automatisk skalning skalar ut och ökar antalet virtuella datorinstanser. När den simulerade CPU-belastningen sedan minskar så skalar reglerna för automatisk skalning in och minskar antalet virtuella datorinstanser.

Om du vill visa en lista med NAT-portar för anslutning till VM-instanser i en skalningsuppsättning, hämta först lastbalanseringsobjektet med [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Visa sedan de ingående NAT-reglerna med [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Följande exempelutdata visar instansnamnet, den offentliga IP-adressen för lastbalanseraren och portnummer som NAT-regler vidarebefordrar trafik till:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

*Namnet* på regeln överensstämmer med namnet på den virtuella datorinstansen som det visas i ett tidigare [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm)-kommando. Till exempel för att ansluta till VM-instans *0*, använder du *myRDPRule.0* och ansluter till porten *50001*. För att ansluta till VM-instans *1*, använder du värdet från *myRDPRule.1* och ansluter till porten *50002*.

Visa den offentliga IP-adressen för lastbalanseraren med [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Exempel på utdata:

```powershell
IpAddress
---------
52.168.121.216
```

Skapa en fjärranslutning till din första virtuella datorinstans. Ange din egna offentliga IP-adress och ditt portnummer för nödvändig VM-instans såsom visas i föregående kommandon. När du uppmanas till det anger du de autentiseringsuppgifter som användes när du skapade skalnings uppsättningen (som standard i exempel kommandona, de är *azureuser* och *P \@ Ssw0Rd!*). Om du använder Azure Cloud Shell, utför du den här åtgärden från en lokal PowerShell-kommandotolk eller klienten för fjärrskrivbord. Följande exempel ansluter till VM-instans *0*:

```powershell
mstsc /v 52.168.121.216:50001
```

Efter att du loggat in kan du öppna Internet Explorer från aktivitetsfältet.

- Välj **OK** för att acceptera uppmaningen *Använd rekommenderade inställningar för säkerhet, sekretess och kompatibilitet*
- Skriv *http://download.sysinternals.com/files/CPUSTRES.zip* i adress fältet.
- Eftersom Förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat, välj **Lägg till***http://download.sysinternals.com* domänen i listan över betrodda platser.
- När du tillfrågas om filhämtning, välj **Öppna** och välj och **Kör** verktyget *CPUSTRES. EXE*.

Om du vill generera viss CPU-belastning markerar du två kryssrutor för **Aktiva** trådar. Från den nedrullningsbara menyn **Aktivitet** för båda trådarna väljer du *Maximal*. Du kan öppna Aktivitetshanteraren för att bekräfta att CPU-belastningen på den virtuella datorn är på 100 %.

![CPU-belastningsverktyget genererar belastningen på VM-instansen](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Lämna anslutningen till fjärrskrivbordssessionen öppen och öppna en annan anslutning till fjärrskrivbordssession. Anslut till den andra VM-instansen med det portnummer som anges från den tidigare [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig)-cmdlet:en:

```powershell
mstsc /v 52.168.121.216:50002
```

När du är inloggad på den andra VM-instansen, upprepa föregående steg om du vill hämta och köra *CPUSTRES. EXE*. Starta två **Aktiva** trådar igen och ange aktiviteten till *Maximal*.

För att tillåta att verktyget **CPU Stress** fortsätter att köras, lämna båda fjärrskrivbordssessionerna öppna.


## <a name="monitor-the-active-autoscale-rules"></a>Övervaka de aktiva reglerna för automatisk skalning
Du övervakar antalet virtuella datorinstanser i din skalningsuppsättning med **while**. Det tar 5 minuter för skalan för automatisk skalning att påbörja processen för skalbarhet som svar på den CPU-belastning som genereras av **CPUStress** på var och en av de virtuella dator instanserna:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

När CPU-tröskelvärdet har uppnåtts, ökar reglerna för automatisk skalning antalet virtuella datorinstanser i skalningsuppsättningen. Följande utdata visar tre virtuella datorer som skapats när skalningsuppsättningen skalar ut automatiskt:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

I anslutningssessionen till fjärrskrivbordet för varje VM-instans, stänger du verktyget **CPU Stress**. Genomsnittlig CPU-belastning över skalningsuppsättningen återgår till normal. Efter 5 minuter till, skalar reglerna för automatisk skalning in antalet virtuella datorinstanser. Skalan in-åtgärder tar först bort de virtuella datorinstanserna med de högsta ID. När en skalningsuppsättning använder tillgänglighetsuppsättningar eller tillgänglighetszoner fördelas skala in-åtgärder jämnt över dessa VM-instanser. Följande exempelutdata visar en VM-instans som tas bort eftersom skalningsuppsättningen skalar in automatiskt:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Avsluta *while* med `Ctrl-c`. Skalningsuppsättningen fortsätter att skala in var 5:e minut och tar bort en VM-instans tills att det lägsta instansantalet på två har uppnåtts.


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser så tar du bort resursgruppen och alla dess resurser med [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Parametern `-Force` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång. Parametern `-AsJob` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien läste du om hur du automatiskt kan skala in eller ut en skalningsuppsättning med Azure PowerShell:

> [!div class="checklist"]
> * Använd automatisk skalning med en skalningsuppsättning
> * Skapa och använd regler för automatisk skalning
> * Belastningstesta virtuella datorinstanser och utlös regler för automatisk skalning
> * Skala tillbaka automatiskt när efterfrågan minskar
