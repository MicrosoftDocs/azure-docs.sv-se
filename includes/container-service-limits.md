---
title: ta med fil
description: ta med fil
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: da22991b9a1c4b69d3a3d6eb6f76b0925a6ad3d4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800130"
---
| Resurs                                                                                                           | Gräns                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maximalt antal kluster per prenumeration                                                                                  | 5000                                                                                                                                                                                                        |
| Maximalt antal noder per kluster med tillgänglighetsuppsättningar för virtuella datorer och Basic Load Balancer SKU                       | 100                                                                                                                                                                                                         |
| Maximalt antal noder per kluster med Virtual Machine Scale Sets [och Standard Load Balancer SKU][standard-load-balancer] | 1 000 (över alla [nodpooler)][node-pool]                                            |
| Maximalt antal nodpooler per kluster                                                                                     | 100                                                                                  |
| Maximalt antal poddar per nod: [Grundläggande nätverk][basic-networking] med Kubenet                                           | Max: 250 <br /> Standard för Azure CLI: 110 <br /> Azure Resource Manager standardmall: 110 <br /> Azure Portal standarddistribution: 30          |
| Maximalt antal poddar per [nod: Avancerade nätverk][advanced-networking] med Azure Container Networking Interface        | Max: 250 <br /> Standard: 30                                                      |
| Förhandsversion av Open Service Mesh (OSM) AKS-tillägg                                                                          | Kubernetes-klusterversion: 1.19+<sup>1</sup><br />OSM-styrenheter per kluster: 1<sup>1</sup><br />Poddar per OSM-styrenhet: 500<sup>1</sup><br />Kubernetes-tjänstkonton som hanteras av OSM: 50<sup>1</sup> |

<sup>1</sup> OSM-tillägget för AKS är i förhandsversion och kommer att genomgå ytterligare förbättringar före allmän tillgänglighet (GA). Under förhandsversionsfasen rekommenderar vi att du inte överskrider de gränser som visas.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
