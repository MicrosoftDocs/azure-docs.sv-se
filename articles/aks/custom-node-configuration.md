---
title: Anpassa Node-konfigurationen för AKS (Azure Kubernetes service) Node-pooler (för hands version)
description: Lär dig hur du anpassar konfigurationen på klusternoder i Azure Kubernetes service (AKS) och Node-pooler.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 589081149d08983d3cd5a4a8822873f5a6cfca0e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559439"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Anpassa Node-konfigurationen för Azure Kubernetes service (AKS) Node-pooler (för hands version)

Genom att anpassa din Node-konfiguration kan du konfigurera eller justera inställningarna för operativ systemet (OS) eller kubelet-parametrarna för att matcha arbets Belastningens behov. När du skapar ett AKS-kluster eller lägger till en resurspool i klustret, kan du anpassa en delmängd av vanliga inställningar för operativ system och kubelet. Om du vill konfigurera inställningar utöver den här del mängden [använder du en daemon uppsättning för att anpassa dina konfigurationer som behövs utan ÅTERFICK AKS-stöd för noderna](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Registrera `CustomNodeConfigPreview` förhands gransknings funktionen

Om du vill skapa ett AKS-kluster eller en noduppsättning som kan anpassa kubelet-parametrarna eller OS-inställningarna måste du aktivera `CustomNodeConfigPreview` funktions flaggan i din prenumeration.

Registrera `CustomNodeConfigPreview` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] , som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Det tar några minuter för statusen att visa *registrerad*. Verifiera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av kommandot [AZ Provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster eller en Node-pool som kan anpassa kubelet-parametrarna eller OS-inställningarna behöver du det senaste *AKS-förhands visnings* tillägget Azure CLI. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] . Eller installera eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Använd konfiguration av anpassad nod

### <a name="kubelet-custom-configuration"></a>Anpassad konfiguration för Kubelet

De Kubelet-parametrar som stöds och godkända värden visas nedan.

| Parameter | Tillåtna värden/intervall | Standardvärde | Description |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | ingen, statisk | inget | Den statiska principen tillåter behållare i [garanterad poddar](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) med heltal CPU begär åtkomst till exklusiva CPU: er på noden. |
| `cpuCfsQuota` | SANT, FALSKT | true |  Aktivera/inaktivera CPU-CFS kvot tillämpning för behållare som anger processor gränser. | 
| `cpuCfsQuotaPeriod` | Intervall i millisekunder (MS) | `100ms` | Anger värdet för PROCESSORns CFS-kvot period. | 
| `imageGcHighThreshold` | 0-100 | 85 | Procent av disk användningen efter vilken avbildnings-skräp insamling alltid körs. Minsta disk **användning som** utlöser skräp insamling. Om du vill inaktivera skräp insamling av bilder anger du 100. | 
| `imageGcLowThreshold` | 0-100, inte högre än `imageGcHighThreshold` | 80 | Procent av disk användningen före vilken avbildnings-skräp insamling aldrig körs. Minsta disk användning som **kan** utlösa skräp insamling. |
| `topologyManagerPolicy` | ingen, god ansträngning, begränsad, enkel NUMA-nod | inget | Optimera NUMA-nodens justering, se mer [här](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/). Endast Kubernetes v-1.18 +. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Inget | Lista över tillåtna osäkra sysctls eller osäkra sysctl-mönster. | 

### <a name="linux-os-custom-configuration"></a>Anpassad konfiguration för Linux OS

De OS-inställningar som stöds och godkända värden visas nedan.

#### <a name="file-handle-limits"></a>Fil hanterings gränser

När du betjänar mycket trafik är det vanligt att den trafik du betjänar kommer från ett stort antal lokala filer. Du kan justera de lägre kernel-inställningarna och inbyggda gränser så att du kan hantera mer, med kostnaden för viss system minne.

| Inställning | Tillåtna värden/intervall | Standardvärde | Description |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 – 12000500 | 709620 | Maximalt antal fil referenser som Linux-kerneln ska allokera, genom att öka det här värdet kan du öka det maximala antalet öppna filer som tillåts. |
| `fs.inotify.max_user_watches` | 781250 – 2097152 | 1048576 | Maximalt antal fil bevakningar som tillåts av systemet. Varje *klocka* är ungefär 90 byte på en 32-bitars kernel och ungefär 160 byte på en 64-bitars kernel. | 
| `fs.aio-max-nr` | 65536 – 6553500 | 65536 | AIO-nr visar det aktuella hela system antalet asynkrona IO-begäranden. AIO-Max-# låter dig ändra det maximala värdet AIO-nr kan växa till. |
| `fs.nr_open` | 8192 – 20000500 | 1048576 | Det maximala antalet fil hanterar en process kan allokeras. |


#### <a name="socket-and-network-tuning"></a>Inställning av socket och nätverk

För-agent-noder, som förväntas hantera ett stort antal samtidiga sessioner, kan du använda en delmängd av TCP-och nätverks alternativen nedan, som du kan använda för att justera per Node-pool. 

| Inställning | Tillåtna värden/intervall | Standardvärde | Description |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 – 3240000 | 16384 | Maximalt antal anslutnings begär Anden som kan placeras i kö för alla lyssnings uttag. En övre gräns för värdet för parametern efter släpning som överförts till funktionen [Listener (2)](http://man7.org/linux/man-pages/man2/listen.2.html) . Om argumentet efter släpning är större än `somaxconn` , trunkeras det tyst till den här gränsen.
| `net.core.netdev_max_backlog` | 1000 – 3240000 | 1000 | Maximalt antal paket, köade på ingångs sidan, när gränssnittet tar emot paket snabbare än kärnan kan bearbeta dem. |
| `net.core.rmem_max` | 212992 – 134217728 | 212992 | Maximal buffertstorlek för mottagnings socket i byte. |
| `net.core.wmem_max` | 212992 – 134217728 | 212992 | Maximal buffertstorlek för sändning av socket i byte. | 
| `net.core.optmem_max` | 20480 – 4194304 | 20480 | Maximal extra buffertstorlek (alternativ Memory buffer) tillåts per socket. Minnes alternativ minne används i några fall för att lagra extra strukturer som rör socketens användning. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 – 3240000 | 16384 | Det maximala antalet köade anslutnings begär Anden som fortfarande inte har fått en bekräftelse från den anslutande klienten. Om antalet överskrids börjar kerneln att släppa förfrågningar. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 – 1440000 | 32768 | Maximalt antal `timewait` Sockets som innehas av systemet samtidigt. Om det här antalet överskrids, förstörs Time-wait socket omedelbart och varningen skrivs ut. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Den tid som en överblivna anslutning (som inte längre refereras till av någon program) blir kvar i FIN_WAIT_2 tillstånd innan den avbryts vid det lokala slutet. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Hur ofta TCP skickar ut `keepalive` meddelanden när `keepalive` är aktiverat. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Hur många `keepalive` avsökningar TCP skickar ut, tills den bestämmer att anslutningen är bruten. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Hur ofta avsökningarna skickas ut. Multiplicerat med `tcp_keepalive_probes` det tar upp till tiden att avsluta en anslutning som inte svarar, efter att avsökningarna har startats. | 
| `net.ipv4.tcp_tw_reuse` | 0 eller 1 | 0 | Tillåt åter användning `TIME-WAIT` av socketar för nya anslutningar när det är säkert från protokoll synpunkt. | 
| `net.ipv4.ip_local_port_range` | Första: 1024-60999 och senaste: 32768-65000] | Första: 32768 och senaste: 60999 | Det lokala port intervallet som används av TCP-och UDP-trafik för att välja den lokala porten. Består av två tal: det första värdet är den första lokala port som tillåts för TCP-och UDP-trafik på agent-noden, det andra är det sista lokala port numret. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 – 80000 | 4096 | Minsta antal poster som kan finnas i ARP-cachen. Skräp insamling utlöses inte om antalet poster är under den här inställningen. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 – 90000 | 8192 | Mjuk maximalt antal poster som kan finnas i ARP-cachen. Den här inställningen är utan tvekan som är viktigast, medan ARP-skräp insamling utlöses om 5 sekunder efter det att den här mjuka gränsen har uppnåtts. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 – 100000 | 16384 | Maximalt antal poster i ARP-cachen. |
| `net.netfilter.nf_conntrack_max` | 131072 – 589824 | 131072 | `nf_conntrack` är en modul som spårar anslutnings poster för NAT i Linux. `nf_conntrack`Modulen använder en hash-tabell för att registrera den *upprättade anslutnings* posten för TCP-protokollet. `nf_conntrack_max` är det maximala antalet noder i hash-tabellen, det vill säga det maximala antalet anslutningar som stöds av `nf_conntrack` modulen eller storleken på anslutnings spårnings tabellen. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 – 147456 | 65536 | `nf_conntrack` är en modul som spårar anslutnings poster för NAT i Linux. `nf_conntrack`Modulen använder en hash-tabell för att registrera den *upprättade anslutnings* posten för TCP-protokollet. `nf_conntrack_buckets` är storleken på hash-tabellen. | 

#### <a name="worker-limits"></a>Arbetsgränser

Precis som fil beskrivnings gränser är antalet arbetare eller trådar som en process kan skapa begränsas av både en kernel-inställning och användar begränsningar. Gränsen för användare på AKS är obegränsad. 

| Inställning | Tillåtna värden/intervall | Standardvärde | Description |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Processer kan skapa arbets trådar. Det maximala antalet trådar som kan skapas anges med kernel-inställningen `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Virtuellt minne

Inställningarna nedan kan användas för att finjustera driften av del systemet för det virtuella minnet (VM) i Linux-kerneln och `writeout` av felaktiga data till disk.

| Inställning | Tillåtna värden/intervall | Standardvärde | Description |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 – 262144 | 65530 | Den här filen innehåller det maximala antalet minnes mappnings områden som en process kan ha. Minnes mappnings områden används som en sido effekt av anrop `malloc` , direkt av `mmap` , och och `mprotect` `madvise` även vid inläsning av delade bibliotek. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Detta procentuella värde styr tendensen i kärnan för att frigöra minne, som används för cachelagring av katalog-och inode-objekt. |
| `vm.swappiness` | 0 - 100 | 60 | Den här kontrollen används för att definiera hur aggressivt kernel ska växla minnes sidor. Högre värden ökar aggressivheten, lägre värden minskar mängden växling. Värdet 0 instruerar kärnan att inte initiera växling tills mängden lediga och filåterställda sidor är mindre än det övre vatten märket i en zon. | 
| `swapFileSizeMB` | 1 MB – storleken på den [temporära disken](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/SDB) | Inget | SwapFileSizeMB anger storlek i MB på en växlings fil som skapas på agent-noderna från den här noden. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) är en Linux-kernel som är avsedd att förbättra prestandan genom att utnyttja processorns minnes mappnings maskin vara effektivare. När den är aktive rad försöker kerneln allokera `hugepages` närhelst det är möjligt och Linux-processen får 2 MB sidor om `mmap` regionen är 2 MB naturligt justerad. I vissa fall när `hugepages` har Aktiver ATS i systemet kan det hända att fler minnes resurser allokeras i program. Ett program kan `mmap` vara en stor region men bara touch 1 byte, i så fall kan en sida på 2 MB fördelas i stället för en 4K-sida utan anledning. Det här scenariot är anledningen till att det är möjligt att inaktivera `hugepages` hela systemet eller att bara ha dem i `MADV_HUGEPAGE madvise` regioner. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Det här värdet styr om kärnan ska göra aggressiv användning av minnes komprimering för att göra mer `hugepages` tillgängligt. | 

> [!IMPORTANT]
> För enkel sökning och läsbarhet visas operativ system inställningarna i det här dokumentet med deras namn, men de ska läggas till i konfigurations-JSON-filen eller AKS-API: et med [camelCase](/dotnet/standard/design-guidelines/capitalization-conventions).

Skapa en `kubeletconfig.json` fil med följande innehåll:

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
Skapa en `linuxosconfig.json` fil med följande innehåll:

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

Skapa ett nytt kluster som anger kubelet och OS-konfigurationerna med hjälp av JSON-filerna som skapades i föregående steg. 

> [!NOTE]
> När du skapar ett kluster kan du ange kubelet-konfiguration, OS-konfiguration eller båda. Om du anger en konfiguration när du skapar ett kluster kommer bara noderna i den första noden att ha den konfigurationen att tillämpas. Alla inställningar som inte är konfigurerade i JSON-filen behåller standardvärdet.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Lägg till en ny resurspool som anger parametrarna för Kubelet med hjälp av JSON-filen som du skapade.

> [!NOTE]
> När du lägger till en nod i ett befintligt kluster, kan du ange kubelet-konfiguration, OS-konfiguration eller båda. Om du anger en konfiguration när du lägger till en Node-pool, kommer endast noderna i den nya noden att ha den konfigurationen att tillämpas. Alla inställningar som inte är konfigurerade i JSON-filen behåller standardvärdet.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du konfigurerar ditt AKS-kluster](cluster-configuration.md).
- Lär dig hur [du uppgraderar nodens avbildningar](node-image-upgrade.md) i klustret.
- Se [uppgradera ett Azure Kubernetes service (AKS)-kluster](upgrade-cluster.md) för att lära dig hur du uppgraderar klustret till den senaste versionen av Kubernetes.
- I listan med [vanliga frågor och svar om AKS](faq.md) hittar du svar på några vanliga frågor om AKS.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
