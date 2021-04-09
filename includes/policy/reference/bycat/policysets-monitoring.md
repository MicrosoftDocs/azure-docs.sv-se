---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1dab7150da65708512872facc9e972811c78cb5d
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090102"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[\[För hands version \] : Distribuera-Konfigurera förutsättningar för att aktivera Azure Monitor och Azure-säkerhetsagenter på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Konfigurera datorer för att automatiskt installera Azure Monitor och Azures säkerhets agenter. Security Center samlar in händelser från agenterna och använder dem för att tillhandahålla säkerhets aviseringar och skräddarsydda härdnings uppgifter (rekommendationer). Skapa en resurs grupp och Log Analytics arbets ytan i samma region som datorn för att lagra gransknings poster. Den här principen gäller bara för virtuella datorer i några regioner. |5 |1.0.0 – för hands version |
|[Aktivera Azure Monitor för Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Aktivera Azure Monitor för Virtual Machine Scale Sets i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som parameter. Obs! Om din skalnings uppsättnings upgradePolicy är inställd på manuell, måste du tillämpa tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta AZ VMSS Update-instances. |6 |1.0.1 |
|[Aktivera Azure Monitor for VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Aktivera Azure Monitor för de virtuella datorerna (VM) i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som parameter. |10 |2.0.0 |
