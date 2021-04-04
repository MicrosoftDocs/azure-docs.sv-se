---
title: Skapa en Azure Load Balancer regel för ett kluster
description: Konfigurera en Azure Load Balancer för att öppna portar för ditt Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 7e09c7b0b3e2bfa5a5ff834e243f5098cbbd947b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319890"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Öppna portar för ett Service Fabric-kluster

Belastningsutjämnaren som distribueras med ditt Azure Service Fabric-kluster dirigerar trafik till din app som körs på en nod. Om du ändrar appen så att den använder en annan port måste du exponera den porten (eller dirigera en annan port) i Azure Load Balancer.

När du har distribuerat Service Fabric-klustret till Azure skapades en belastningsutjämnare automatiskt åt dig. Om du inte har en belastningsutjämnare kan du läsa [Konfigurera en Internetbaserad belastningsutjämnare](../load-balancer/quickstart-load-balancer-standard-public-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurera Service Fabric

Konfigurations filen för Service Fabric program **ServiceManifest.xml** definierar de slut punkter som ska användas i programmet. När konfigurations filen har uppdaterats för att definiera en slut punkt måste belastningsutjämnaren uppdateras för att exponera den (eller en annan) port. Mer information om hur du skapar Service Fabric-slutpunkten finns i [Konfigurera en slut punkt](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En Load Balancer regel öppnar en port som riktar sig mot Internet och vidarebefordrar trafik till den interna nodens port som används av ditt program. Om du inte har en belastningsutjämnare kan du läsa [Konfigurera en Internetbaserad belastningsutjämnare](../load-balancer/quickstart-load-balancer-standard-public-portal.md).

Om du vill skapa en Load Balancer regel måste du samla in följande information:

- Namn på belastnings utjämning.
- Resurs gruppen för belastningsutjämnaren och Service Fabric-klustret.
- Extern port.
- Intern port.

## <a name="azure-cli"></a>Azure CLI
Det tar bara ett enda kommando att skapa en belastnings Utjämnings regel med **Azure CLI**. Du behöver bara känna till både namnet på belastningsutjämnaren och resurs gruppen för att skapa en ny regel.

>[!NOTE]
>Om du behöver identifiera namnet på belastningsutjämnaren använder du det här kommandot för att snabbt få en lista över alla belastningsutjämnare och de associerade resurs grupperna.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Azure CLI-kommandot har några parametrar som beskrivs i följande tabell:

| Parameter | Beskrivning |
| --------- | ----------- |
| `--backend-port`  | Porten som Service Fabric programmet lyssnar på. |
| `--frontend-port` | Porten som belastningsutjämnaren exponerar för externa anslutningar. |
| `-lb-name` | Namnet på den belastningsutjämnare som ska ändras. |
| `-g`       | Resurs gruppen som har både belastningsutjämnaren och Service Fabric klustret. |
| `-n`       | Regelns önskade namn. |


>[!NOTE]
>Mer information om hur du skapar en belastningsutjämnare med Azure CLI finns i [skapa en belastningsutjämnare med Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell är lite mer komplicerat än Azure CLI. Följ de här konceptuella stegen för att skapa en regel:

1. Hämta belastningsutjämnaren från Azure.
2. Skapa en regel.
3. Lägg till regeln i belastningsutjämnaren.
4. Uppdatera belastningsutjämnaren.

>[!NOTE]
>Om du behöver identifiera namnet på belastningsutjämnaren använder du det här kommandot för att snabbt få en lista över alla belastningsutjämnare och tillhör ande resurs grupper.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

För `New-AzLoadBalancerRuleConfig` kommandot `-FrontendPort` representerar den port som belastningsutjämnaren exponerar för externa anslutningar och `-BackendPort` representerar den port som Service Fabric-appen lyssnar på.

>[!NOTE]
>Mer information om hur du skapar en belastningsutjämnare med PowerShell finns i [skapa en belastningsutjämnare med PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [nätverk i Service Fabric](service-fabric-patterns-networking.md).