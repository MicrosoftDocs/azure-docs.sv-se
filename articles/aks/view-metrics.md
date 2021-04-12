---
title: Visa kluster mått för Azure Kubernetes service (AKS)
description: Visa kluster mått för Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969510"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Visa kluster mått för Azure Kubernetes service (AKS)

AKS tillhandahåller en uppsättning mått för kontroll planet, inklusive API-servern och kluster autoskalning och klusternoder. Med dessa mått kan du övervaka hälso tillståndet för klustret och felsöka problem. Du kan visa måtten för klustret med hjälp av Azure Portal.

> [!NOTE]
> Dessa AKS-kluster mått överlappar en delmängd av [måtten som tillhandahålls av Kubernetes][kubernetes-metrics].

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Visa mått för ditt AKS-kluster med hjälp av Azure Portal

Så här visar du måtten för ditt AKS-kluster:

1. Logga in på [Azure Portal][azure-portal] och navigera till ditt AKS-kluster.
1. Välj *mått* på vänster sida under *övervakning*.
1. Skapa ett diagram för de mått som du vill visa. Skapa till exempel ett diagram:
    1. För *omfång* väljer du klustret.
    1. I *mått namn området* väljer du *behållar tjänst (hanterade) standard mått*.
    1. För *mått*, under *poddar* väljer du *antal poddar per fas*.
    1. För *agg regering* väljer du *AVG*.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

Exemplet ovan visar måtten för det genomsnittliga antalet poddar för *myAKSCluster*.

## <a name="available-metrics"></a>Tillgängliga mått

Följande kluster mått är tillgängliga:

| Name | Group | ID | Beskrivning |
| --- | --- | --- | ---- |
| Synlighetssekvensnummer-begäranden | API-Server (för hands version) |apiserver_current_inflight_requests | Maximalt antal aktiva synlighetssekvensnummer-begäranden på API-servern per typ av begäran. |
| Klusterhälsa | Kluster autoskalning (för hands version) | cluster_autoscaler_cluster_safe_to_autoscale | Anger om en kluster autoskalning ska vidta åtgärder i klustret. |
| Skala ned cooldown | Kluster autoskalning (för hands version) | cluster_autoscaler_scale_down_in_cooldown | Anger om nedskalning är i cooldown-inga noder kommer att tas bort under denna tidsram. |
| Onödiga noder | Kluster autoskalning (för hands version) | cluster_autoscaler_unneeded_nodes_count | Klustret auotscaler markerar noderna som kandidater för borttagning och tas slutligen bort. |
| Unschedulable poddar | Kluster autoskalning (för hands version) | cluster_autoscaler_unschedulable_pods_count | Antalet poddar som för närvarande är unschedulable i klustret. |
| Totalt antal tillgängliga processor kärnor i ett hanterat kluster | Noder | kube_node_status_allocatable_cpu_cores | Totalt antal tillgängliga processor kärnor i ett hanterat kluster. |
| Total mängd tillgängligt minne i ett hanterat kluster | Noder | kube_node_status_allocatable_memory_bytes | Total mängd tillgängligt minne i ett hanterat kluster. |
| Status för olika nod villkor | Noder | kube_node_status_condition | Status för olika nod villkor |
| Millicores för processor användning | Noder (för hands version) | node_cpu_usage_millicores | Aggregerad mätning av processor användning i millicores över klustret. |
| Procent andel CPU-användning | Noder (för hands version) | node_cpu_usage_percentage | Aggregerad genomsnittlig processor användning mätt i procent över klustret. |
| RSS-byte för minne | Noder (för hands version) | node_memory_rss_bytes | RSS-minne för behållare som används i byte. |
| RSS-procent för minne | Noder (för hands version) | node_memory_rss_percentage | RSS-minne för behållare som används i procent. |
| Byte för arbets minne | Noder (för hands version) | node_memory_working_set_bytes | Arbets minne för behållare som används i byte. |
| Procent andel ledigt minne | Noder (för hands version) | node_memory_working_set_percentage | Arbets minne för behållare som används i procent. |
| Använda byte på disk | Noder (för hands version) | node_disk_usage_bytes | Disk utrymme som används i byte av enhet. |
| Procent andel använt disk utrymme | Noder (för hands version) | node_disk_usage_percentage | Disk utrymme som används i procent av enheten. |
| Nätverk i byte | Noder (för hands version) | node_network_in_bytes | Mottagna byte i nätverket. |
| Utgående byte för nätverk | Noder (för hands version) | node_network_out_bytes | Överförda byte i nätverket. |
| Antal poddar i klart läge | Poddar | kube_pod_status_ready | Antalet poddar i *klar* läge. |
| Antal poddar per fas | Poddar | kube_pod_status_phase | Antal poddar per fas. |

> [!IMPORTANT]
> Mått i för hands versionen kan uppdateras eller ändras, inklusive namn och beskrivningar, i för hands versionen.

## <a name="next-steps"></a>Nästa steg

Förutom kluster måtten för AKS kan du också använda Azure Monitor med ditt AKS-kluster. Mer information om hur du använder Azure Monitor med AKS finns i [Azure Monitor for containers][aks-azure-monitory].

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/