---
title: Anpassa nodkonfigurationen för Azure Kubernetes Service (AKS)-nodpooler (förhandsversion)
description: Lär dig hur du anpassar konfigurationen på Azure Kubernetes Service(AKS)-klusternoder och nodpooler.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c2173118b58ca92d69286fb36014872c19058bd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779983"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Anpassa nodkonfiguration för AKS Azure Kubernetes Service nodpooler (förhandsversion)

Genom att anpassa nodkonfigurationen kan du konfigurera eller justera inställningarna för operativsystemet eller kubelet-parametrarna så att de matchar behoven för arbetsbelastningarna. När du skapar ett AKS-kluster eller lägger till en nodpool i klustret kan du anpassa en delmängd av de vanliga os- och kubelet-inställningarna. Om du vill konfigurera inställningar utöver den här delmängden använder du en daemonuppsättning för att anpassa dina konfigurationer utan att förlora [AKS-stöd för noderna](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Registrera `CustomNodeConfigPreview` förhandsgranskningsfunktionen

Om du vill skapa ett AKS-kluster eller en nodpool som kan anpassa kubelet-parametrarna eller OS-inställningarna måste du aktivera `CustomNodeConfigPreview` funktionsflaggan för din prenumeration.

Registrera `CustomNodeConfigPreview` funktionsflaggan med [kommandot az feature register,][az-feature-register] som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Det tar några minuter för statusen att visa *Registrerad.* Kontrollera registreringsstatusen med kommandot [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster eller en nodpool som kan anpassa kubelet-parametrarna eller OS-inställningarna behöver du det senaste *Azure CLI-tillägget aks-preview.* Installera Azure *CLI-tillägget aks-preview* med kommandot [az extension add.][az-extension-add] Eller installera eventuella tillgängliga uppdateringar med hjälp av [kommandot az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Använda anpassad nodkonfiguration

### <a name="kubelet-custom-configuration"></a>Anpassad kubelet-konfiguration

De Kubelet-parametrar och godkända värden som stöds visas nedan.

| Parameter | Tillåtna värden/intervall | Standardvärde | Description |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | none, static | inget | Den statiska principen tillåter att [containrar i garanterade poddar](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) med heltals-CPU-begäranden får åtkomst till exklusiva processorer på noden. |
| `cpuCfsQuota` | SANT, FALSKT | true |  Aktivera/inaktivera tvingande CPU CFS-kvoter för containrar som anger CPU-gränser. | 
| `cpuCfsQuotaPeriod` | Intervall i millisekunder (ms) | `100ms` | Anger värdet för CPU CFS-kvotperioden. | 
| `imageGcHighThreshold` | 0-100 | 85 | Den procentandel av diskanvändningen som skräpinsamling av avbildningar alltid körs efter. Minsta diskanvändning som **utlöser** skräpinsamling. Om du vill inaktivera skräpinsamling av avbildningar anger du till 100. | 
| `imageGcLowThreshold` | 0-100, inte högre än `imageGcHighThreshold` | 80 | Den procentandel av diskanvändningen som skräpinsamling av avbildningar aldrig körs före. Minsta diskanvändning som **kan utlösa** skräpinsamling. |
| `topologyManagerPolicy` | none, best-effort, restricted, single-numa-node | inget | Optimera NUMA-nodjusteringen. Mer information [finns här.](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/) Endast kubernetes v1.18+. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Inget | Tillåten lista över osäkra sysctls eller osäkra sysctl-mönster. | 

### <a name="linux-os-custom-configuration"></a>Anpassad konfiguration för Linux-operativsystem

De os-inställningar och godkända värden som stöds visas nedan.

#### <a name="file-handle-limits"></a>Filhandtagsgränser

När du betjänar mycket trafik är det vanligt att trafiken du betjänar kommer från ett stort antal lokala filer. Du kan justera kernelinställningarna nedan och de inbyggda gränserna så att du kan hantera mer, på bekostnad av lite systemminne.

| Inställning | Tillåtna värden/intervall | Standardvärde | Description |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | Maximalt antal filreferenser som Linux-kerneln ska allokera. Genom att öka det här värdet kan du öka det maximala antalet tillåtna öppna filer. |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | Maximalt antal filklockor som tillåts av systemet. Varje *klocka* är ungefär 90 byte på en 32-bitars kernel och ungefär 160 byte på en 64-bitars kernel. | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | Aio-nr visar det aktuella systemomfattande antalet asynkrona io-begäranden. Med aio-max-nr kan du ändra det högsta värdet som aio-nr kan växa till. |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | Det maximala antalet filreferenser som en process kan allokera. |


#### <a name="socket-and-network-tuning"></a>Socket- och nätverksjustering

För agentnoder, som förväntas hantera ett mycket stort antal samtidiga sessioner, kan du använda den delmängd av TCP- och nätverksalternativen nedan som du kan justera per nodpool. 

| Inställning | Tillåtna värden/intervall | Standardvärde | Description |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | Maximalt antal anslutningsbegäranden som kan köas för en viss lyssningssocket. En övre gräns för värdet för parametern för begränsning som skickas till [funktionen listen(2).](http://man7.org/linux/man-pages/man2/listen.2.html) Om argumentet för stilleståndslogg är större än `somaxconn` trunkeras det tyst till den här gränsen.
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | Maximalt antal paket i kö på INDATA-sidan när gränssnittet tar emot paket snabbare än kernel kan bearbeta dem. |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | Den maximala buffertstorleken för mottagningssocket i byte. |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | Maximal storlek för skicka socketbuffert i byte. | 
| `net.core.optmem_max` | 20480 - 4194304 | 20480 | Maximal tilläggsbuffertstorlek (alternativminnesbuffert) tillåts per socket. Minne för socketalternativ används i några fall för att lagra extra strukturer som rör användning av socketen. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | Det maximala antalet köade anslutningsbegäranden som fortfarande inte har tagit emot en bekräftelse från den anslutande klienten. Om det här antalet överskrids börjar kerneln släppa begäranden. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | Maximalt antal `timewait` sockets som finns i systemet samtidigt. Om det här antalet överskrids förstörs väntetidssocketen omedelbart och varningen skrivs ut. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Hur lång tid en överbliven anslutning (som inte längre refereras till av något program) förblir i FIN_WAIT_2 tillstånd innan den avbryts på den lokala änden. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Hur ofta TCP skickar ut `keepalive` meddelanden när `keepalive` är aktiverat. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Hur många `keepalive` avsökningar SOM TCP skickar ut tills den bestämmer att anslutningen är bruten. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Hur ofta avsökningarna skickas ut. Multiplicerat `tcp_keepalive_probes` med det utgör den tid det tar att avsöka en anslutning som inte svarar när avsökningarna har startats. | 
| `net.ipv4.tcp_tw_reuse` | 0 eller 1 | 0 | Tillåt återanvändning av `TIME-WAIT` sockets för nya anslutningar när det är säkert ur protokollperspektiv. | 
| `net.ipv4.ip_local_port_range` | Första: 1024–60999 och sista: 32768 – 65000] | Första: 32768 och sista: 60999 | Det lokala portintervall som används av TCP- och UDP-trafik för att välja den lokala porten. Består av två tal: Det första talet är den första lokala porten som tillåts för TCP- och UDP-trafik på agentnoden, och det andra är det sista lokala portnumret. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | Minsta antal poster som kan finnas i ARP-cachen. Skräpinsamling utlöses inte om antalet poster ligger under den här inställningen. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | Mjukt maximalt antal poster som kan finnas i ARP-cachen. Den här inställningen är utan tvekan den viktigaste eftersom ARP-skräpinsamling utlöses cirka 5 sekunder efter att den här mjuka maxvärdet har nåtts. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | Hård maximalt antal poster i ARP-cachen. |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack` är en modul som spårar anslutningsposter för NAT i Linux. Modulen `nf_conntrack` använder en hash-tabell för att registrera den *etablerade anslutningsposten* för TCP-protokollet. `nf_conntrack_max` är det maximala antalet noder i hash-tabellen, det vill säga det maximala antalet anslutningar som stöds av modulen eller `nf_conntrack` storleken på anslutningsspårningstabellen. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack` är en modul som spårar anslutningsposter för NAT i Linux. Modulen `nf_conntrack` använder en hash-tabell för att registrera den *etablerade anslutningsposten* för TCP-protokollet. `nf_conntrack_buckets` är storleken på hash-tabellen. | 

#### <a name="worker-limits"></a>Arbetsgränser

Precis som begränsningar för filbeskrivning begränsas antalet arbetare eller trådar som en process kan skapa av både en kernelinställning och användarbegränsningar. Användargränsen för AKS är obegränsad. 

| Inställning | Tillåtna värden/intervall | Standardvärde | Description |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Processer kan skapa arbetstrådar. Det maximala antalet trådar som kan skapas anges med kernelinställningen `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Virtuellt minne

Inställningarna nedan kan användas för att justera driften av undersystemet för virtuellt minne (VM) för Linux-kerneln och för `writeout` data med fel på disk.

| Inställning | Tillåtna värden/intervall | Standardvärde | Description |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | Den här filen innehåller det maximala antalet minnesmappningsområden som en process kan ha. Minneskartområden används som en sidoeffekt av att anropa `malloc` , direkt av , och , och även vid `mmap` `mprotect` `madvise` inläsning av delade bibliotek. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Det här procentvärdet styr kernelts tendens att återta minnet, som används för cachelagring av katalog- och innodobjekt. |
| `vm.swappiness` | 0 - 100 | 60 | Den här kontrollen används för att definiera hur aggressiv kerneln ska växla minnessidor. Högre värden ökar aggressiviteten, lägre värden minskar mängden byte. Värdet 0 instruerar kerneln att inte initiera växlingen förrän mängden kostnadsfria och filbaserade sidor är mindre än vattenmärket i en zon. | 
| `swapFileSizeMB` | 1 MB – storleken på den [tillfälliga disken](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Inget | SwapFileSizeMB anger storleken i MB för en växlingsfil som skapas på agentnoderna från den här nodpoolen. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparenta Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) är en Linux-kernelfunktion som är avsedd att förbättra prestanda genom att använda processorns maskinvara för minnesmappning effektivare. När den här funktionen är aktiverad försöker kerneln allokera när det är möjligt och en Linux-process får sidor på 2 MB om regionen är `hugepages` `mmap` 2 MB naturligt justerad. I vissa fall när `hugepages` är aktiverade för hela systemet kan program till slut allokera fler minnesresurser. Ett program kan vara en stor region men bara röra 1 byte av den, i så fall kan en sida på 2 MB allokeras i stället för `mmap` en 4k-sida utan någon bra anledning. Det här scenariot är anledningen till att du kan inaktivera `hugepages` systemomfattande eller bara ha dem i `MADV_HUGEPAGE madvise` regioner. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Det här värdet styr om kerneln ska använda minneskomprimering aggressivt för att göra mer `hugepages` tillgänglig. | 

> [!IMPORTANT]
> För enkel sökning och läsbarhet visas OS-inställningarna i det här dokumentet efter deras namn, men bör läggas till i JSON-konfigurationsfilen eller AKS-API:et med [kamelcase-konventionen](/dotnet/standard/design-guidelines/capitalization-conventions).

Skapa `kubeletconfig.json` en fil med följande innehåll:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Skapa `linuxosconfig.json` en fil med följande innehåll:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Skapa ett nytt kluster som anger kubelet- och OS-konfigurationerna med hjälp av JSON-filerna som skapades i föregående steg. 

> [!NOTE]
> När du skapar ett kluster kan du ange kubelet-konfigurationen, OS-konfigurationen eller båda. Om du anger en konfiguration när du skapar ett kluster är det bara noderna i den ursprungliga nodpoolen som har den konfigurationen tillämpad. Alla inställningar som inte har konfigurerats i JSON-filen behåller standardvärdet.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Lägg till en ny nodpool som anger Kubelet-parametrarna med hjälp av JSON-filen som du skapade.

> [!NOTE]
> När du lägger till en nodpool i ett befintligt kluster kan du ange kubelet-konfiguration, OS-konfiguration eller både och. Om du anger en konfiguration när du lägger till en nodpool tillämpas bara noderna i den nya nodpoolen. Alla inställningar som inte har konfigurerats i JSON-filen behåller standardvärdet.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Nästa steg

- Lär [dig hur du konfigurerar ditt AKS-kluster.](cluster-configuration.md)
- Lär dig [hur du uppgraderar nodavbildningarna](node-image-upgrade.md) i klustret.
- Se [Uppgradera ett Azure Kubernetes Service(AKS)-kluster](upgrade-cluster.md) för att lära dig hur du uppgraderar klustret till den senaste versionen av Kubernetes.
- Se listan med vanliga [frågor om AKS för att](faq.md) få svar på några vanliga AKS-frågor.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why