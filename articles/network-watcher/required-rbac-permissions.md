---
title: Azure RBAC-behörigheter som krävs för att använda funktioner
titleSuffix: Azure Network Watcher
description: Lär dig vilka Azure-rollbaserade åtkomst kontroll behörigheter som krävs för att kunna arbeta med Network Watcher-funktioner.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 47418b9c5235255ff7dbf4a1a151e51e4c9aba58
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019677"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Azure rollbaserad åtkomst kontroll behörigheter som krävs för att använda Network Watcher-funktioner

Med rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du bara tilldela de åtgärder som medlemmarna i din organisation behöver för att kunna slutföra deras tilldelade ansvar. För att kunna använda Network Watcher-funktioner måste det konto som du loggar in på Azure med, tilldelas till de inbyggda rollerna [ägare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)eller [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) , eller tilldelas till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) som tilldelas de åtgärder som anges för varje Network Watcher-funktion i följande avsnitt. Mer information om Network Watchers funktioner finns i [Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/Read                              | Hämta en nätverks övervakare                                          |
| Microsoft. Network/networkWatchers/Write                             | Skapa eller uppdatera en nätverks övervakare                             |
| Microsoft. Network/networkWatchers/Delete                            | Ta bort en nätverks övervakare                                       |

## <a name="nsg-flow-logs"></a>NSG flödesloggar

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/configureFlowLog/Action           | Konfigurera en flödes logg                                           |
| Microsoft. Network/networkWatchers/queryFlowLogStatus/Action         | Fråga efter status för en flödes logg                                    |

## <a name="connection-troubleshoot"></a>Felsökning av anslutning

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectivityCheck/Action          | Starta en anslutning felsöka testet
| Microsoft. Network/networkWatchers/queryTroubleshootResult/Action    | Fråga resultatet av en anslutning felsöka testet                |
| Microsoft. Network/networkWatchers/Felsök/åtgärd               | Köra en anslutning felsöka test                             |

## <a name="connection-monitor"></a>Anslutnings övervakare

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectionMonitors/start/Action   | Starta en anslutnings övervakare                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/stop/action    | Stoppa en anslutnings övervakare                                      |
| Microsoft. Network/networkWatchers/connectionMonitors/fråga/åtgärd   | Fråga en anslutnings övervakare                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/Read           | Hämta en anslutnings övervakare                                       |
| Microsoft. Network/networkWatchers/connectionMonitors/Write          | Skapa en anslutningsövervakare                                    |
| Microsoft. Network/networkWatchers/connectionMonitors/Delete         | Ta bort en anslutnings övervakare                                    |

## <a name="packet-capture"></a>Paketfångst

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/packetCaptures/queryStatus/Action | Fråga om status för en paket fångst                           |
| Microsoft. Network/networkWatchers/packetCaptures/stop/action        | Stoppa en paket fångst                                          |
| Microsoft. Network/networkWatchers/packetCaptures/Read               | Hämta en paket fångst                                           |
| Microsoft. Network/networkWatchers/packetCaptures/Write              | Skapa en paket fångst                                        |
| Microsoft. Network/networkWatchers/packetCaptures/Delete             | Ta bort en paket avbildning                                        |

## <a name="ip-flow-verify"></a>Kontrollera IP-flöde

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/ipFlowVerify/Action               | Verifiera ett IP-flöde                                              |

## <a name="next-hop"></a>Nästa hopp

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/nextHop/Action                    | Hämta nästa hopp från en virtuell dator                                     |

## <a name="network-security-group-view"></a>Vy för nätverkssäkerhetsgrupp

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/securityGroupView/Action          | Visa säkerhets grupper                                           |

## <a name="topology"></a>Topologi

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/topologi/åtgärd                   | Hämta topologi                                                   |
| Microsoft. Network/networkWatchers/Topology/Read                     | Samma som ovan                                                  |

## <a name="reachability-report"></a>Rapport om nåbarhet

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/azureReachabilityReport/Action    | Hämta en tillgänglighets rapport för Azure                               |


## <a name="additional-actions"></a>Ytterligare åtgärder

Network Watcher funktioner kräver också följande åtgärder:

| Åtgärd (er)                                                           | Beskrivning                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/ \* /Read                                     | Används för att hämta roll tilldelningar och princip definitioner i Azure          |
| Microsoft. Resources/Subscriptions/resourceGroups/Read               | Används för att räkna upp alla resurs grupper i en prenumeration    |
| Microsoft. Storage/storageAccounts/Read                              | Används för att hämta egenskaperna för det angivna lagrings kontot   |
| Microsoft. Storage/storageAccounts/listServiceSas/Action, </br> Microsoft. Storage/storageAccounts/listAccountSas/Action, <br> Microsoft. Storage/storageAccounts/Listnycklar/åtgärd| Används för att hämta signaturer för delad åtkomst (SAS) som aktiverar [säker åtkomst till lagrings kontot](../storage/common/storage-sas-overview.md) och skriver till lagrings kontot |
| Microsoft. Compute/virtualMachines/Read, </br> Microsoft. Compute/virtualMachines/Write| Används för att logga in på den virtuella datorn, göra en paket fångst och ladda upp den till lagrings kontot|
| Microsoft. Compute/virtualMachines/Extensions/Read </br> Microsoft. Compute/virtualMachines/tillägg/Skriv| Används för att kontrol lera om Network Watcher tillägget finns och installera vid behov |
| Microsoft. Compute/virtualMachineScaleSets/Read, </br> Microsoft. Compute/virtualMachineScaleSets/Write| Används för att komma åt skalnings uppsättningar för virtuella datorer, göra paket insamlade och överföra dem till lagrings kontot|
| Microsoft. Compute/virtualMachineScaleSets/Extensions/Read, </br> Microsoft. Compute/virtualMachineScaleSets/tillägg/Skriv| Används för att kontrol lera om Network Watcher tillägget finns och installera vid behov |
| Microsoft. Insights/alertRules/*                                     | Används för att ställa in mått varningar                                     |
| Microsoft. support/*                                                 | Används för att skapa och uppdatera support biljetter från Network Watcher |