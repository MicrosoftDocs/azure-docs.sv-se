---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 02118ff7bf6568ba928ecea3658db1aa23e5015a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504902"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[\[Förhandsversion: \] Distribuera – Konfigurera krav för att aktivera Azure Monitor azure-säkerhetsagenter på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Konfigurera datorer för att automatiskt installera Azure Monitor och Azure Security-agenter. Security Center samlar in händelser från agenterna och använder dem för att tillhandahålla säkerhetsaviseringar och skräddarsydda härdningsuppgifter (rekommendationer). Skapa en resursgrupp och En Log Analytics-arbetsyta i samma region som datorn för att lagra granskningsposter. Den här principen gäller endast för virtuella datorer i ett fåtal regioner. |5 |1.0.0-förhandsversion |
|[Aktivera Azure Monitor för Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Aktivera Azure Monitor för Virtual Machine Scale Sets i det angivna omfånget (hanteringsgrupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsytan som parameter. Obs! Om din upgradePolicy för skalningsuppsättningen är inställd på Manuell måste du tillämpa tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgradering på dem. I CLI är detta az vmss update-instances. |6 |1.0.1 |
|[Aktivera Azure Monitor for VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Aktivera Azure Monitor virtuella datorer (VM) i det angivna omfånget (hanteringsgrupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsytan som parameter. |10 |2.0.0 |
