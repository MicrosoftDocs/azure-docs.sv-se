---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0e5edcfad636c76107adc7e72b50ea61249aa00a
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807168"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[Distribuera – konfigurera krav för att aktivera Azure Monitor och Azure-säkerhetsagenter på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Konfigurera datorer för att automatiskt installera Azure Monitor och Azures säkerhets agenter. Security Center samlar in händelser från agenterna och använder dem för att tillhandahålla säkerhets aviseringar och skräddarsydda härdnings uppgifter (rekommendationer). Skapa en resurs grupp och Log Analytics arbets ytan i samma region som datorn för att lagra gransknings poster. Den här principen gäller bara för virtuella datorer i några regioner. |5 |1.0.0 – för hands version |
|[Aktivera Azure Monitor för Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Aktivera Azure Monitor för Virtual Machine Scale Sets i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som parameter. Obs! Om din skalnings uppsättnings upgradePolicy är inställd på manuell, måste du tillämpa tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta AZ VMSS Update-instances. |6 |1.0.1 |
|[Aktivera Azure Monitor for VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Aktivera Azure Monitor för de virtuella datorerna (VM) i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som parameter. |10 |2.0.0 |
