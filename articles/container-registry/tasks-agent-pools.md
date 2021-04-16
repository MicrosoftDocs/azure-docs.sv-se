---
title: Använda dedikerad pool för att köra uppgift – uppgifter
description: Konfigurera en dedikerad beräkningspool (agentpool) i registret för att köra en Azure Container Registry uppgift.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 21db066b3f18106938d11fbd8e2cfe688c1ef276
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389561"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Köra en ACR-uppgift på en dedikerad agentpool

Konfigurera en Azure-hanterad VM-pool *(agentpool)* för att aktivera [körning av Azure Container Registry i][acr-tasks] en dedikerad beräkningsmiljö. När du har konfigurerat en eller flera pooler i registret kan du välja en pool för att köra en uppgift i stället för tjänstens standardmiljö för beräkning.

En agentpool tillhandahåller:

- **Stöd för virtuella nätverk** – Tilldela en agentpool till ett virtuellt Azure-nätverk, vilket ger åtkomst till resurser i det virtuella nätverket, till exempel ett containerregister, nyckelvalv eller lagring.
- **Skala efter behov** – Öka antalet instanser i en agentpool för beräkningsintensiva aktiviteter eller skala till noll. Faktureringen baseras på poolallokering. Mer information finns i [Prissättning.](https://azure.microsoft.com/pricing/details/container-registry/)
- **Flexibla alternativ** – Välj mellan olika [poolnivåer och skalningsalternativ](#pool-tiers) som uppfyller dina uppgiftsarbetsbelastningsbehov.
- **Azure-hantering** – Uppgiftspooler korrigeras och underhålls av Azure, vilket ger reserverad allokering utan att de enskilda virtuella datorerna behöver underhållas.

Den här funktionen är tillgänglig på **tjänstnivån** premiumcontainerregister. Information om registertjänstnivåer och begränsningar finns i Azure Container Registry [SKU:er][acr-tiers].

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

- Uppgiftsagentpooler stöder för närvarande Linux-noder. Windows-noder stöds inte för närvarande.
- Uppgiftsagentpooler är tillgängliga som förhandsversion i följande regioner: USA, västra 2, USA, södra centrala, USA, östra 2, USA, östra, USA, centrala, Europa, västra, Europa, norra, Kanada, centrala, USGov Arizona, USGov Texas och USGov Virginia.
- För varje register är den totala standardkvoten för vCPU (kärna) 16 för alla standardagentpooler och är 0 för isolerade agentpooler. Öppna en [supportbegäran om][open-support-ticket] ytterligare allokering.
- Du kan för närvarande inte avbryta en uppgiftskörning på en agentpool.

## <a name="prerequisites"></a>Förutsättningar

* Om du vill använda Azure CLI-stegen i den här artikeln krävs Azure CLI version 2.3.1 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli]. Eller kör i [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Om du inte redan har ett containerregister skapar du ett [(Premium-nivå][create-reg-cli] krävs) i en förhandsversionsregion.

## <a name="pool-tiers"></a>Poolnivåer

Agentpoolnivåer tillhandahåller följande resurser per instans i poolen.

|Nivå    | Typ  |  Processor  |Minne (GB)  |
|---------|---------|---------|---------|
|S1     |  standard    | 2       |    3     |
|S2     |  standard    | 4       |    8     |
|S3     |  standard    | 8       |   16     |
|I6     |  Isolerade    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Skapa och hantera en uppgiftsagentpool

### <a name="set-default-registry-optional"></a>Ange standardregister (valfritt)

För att förenkla Azure CLI-kommandon som följer anger du standardregistret genom att köra [kommandot az configure:][az-configure]

```azurecli
az configure --defaults acr=<registryName>
```

I följande exempel förutsätts att du har angett standardregistret. Om inte skickar du en `--registry <registryName>` parameter i varje `az acr` kommando.

### <a name="create-agent-pool"></a>Skapa agentpool

Skapa en agentpool med hjälp av [kommandot az acr agentpool][az-acr-agentpool-create] create. I följande exempel skapas en nivå S2-pool (4 CPU/instans). Som standard innehåller poolen 1 instans.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Det tar flera minuter att skapa en agentpool och andra poolhanteringsåtgärder.

### <a name="scale-pool"></a>Skalningspool

Skala upp eller ned poolstorleken med [kommandot az acr agentpool update.][az-acr-agentpool-update] I följande exempel skalas poolen till 2 instanser. Du kan skala till 0 instanser.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Skapa pool i ett virtuellt nätverk

### <a name="add-firewall-rules"></a>Lägga till brandväggsregler

Uppgiftsagentpooler kräver åtkomst till följande Azure-tjänster. Följande brandväggsregler måste läggas till i befintliga nätverkssäkerhetsgrupper eller användardefinierade vägar.

| Riktning | Protokoll | Källa         | Källport | Mål          | Dest-port | Använt    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Utgående  | TCP      | VirtualNetwork | Valfri         | AzureKeyVault        | 443       | Standardvärde |
| Utgående  | TCP      | VirtualNetwork | Valfri         | Storage              | 443       | Standardvärde |
| Utgående  | TCP      | VirtualNetwork | Valfri         | EventHub             | 443       | Standardvärde |
| Utgående  | TCP      | VirtualNetwork | Valfri         | AzureActiveDirectory | 443       | Standardvärde |
| Utgående  | TCP      | VirtualNetwork | Valfri         | AzureMonitor         | 443       | Standardvärde |

> [!NOTE]
> Om dina uppgifter kräver ytterligare resurser från det offentliga Internet lägger du till motsvarande regler. Ytterligare regler krävs till exempel för att köra en docker-build-uppgift som hämtar basavbildningarna från Docker Hub, eller återställer ett NuGet-paket.

### <a name="create-pool-in-vnet"></a>Skapa pool i VNet

I följande exempel skapas en agentpool i *undernätet mysubnet* i nätverket *myvnet*:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Köra uppgiften på agentpoolen

I följande exempel visas hur du anger en agentpool när du köar en uppgift.

> [!NOTE]
> Om du vill använda en agentpool i en ACR-uppgift ser du till att poolen innehåller minst 1 instans.
>

### <a name="quick-task"></a>Snabbuppgift

Köa en snabbuppgift i agentpoolen med hjälp av [kommandot az acr build][az-acr-build] och skicka `--agent-pool` parametern :

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Automatiskt utlöst uppgift

Du kan till exempel skapa en schemalagd aktivitet i agentpoolen [med az acr task create][az-acr-task-create]och skicka `--agent-pool` parametern .

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

Kontrollera aktivitetskonfigurationen genom att [köra az acr task run][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Frågepoolstatus

Om du vill hitta antalet körningar som är schemalagda i agentpoolen kör [du az acr agentpool show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Nästa steg

Fler exempel på containeravbildningsbyggen och underhåll i molnet finns i ACR-uppgifter [självstudieserien](container-registry-tutorial-quick-task.md).



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
