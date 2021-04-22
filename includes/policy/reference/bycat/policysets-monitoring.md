---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f8095fc7c9be6ea23a573de8a5e871965d6c2a66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866114"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[\[Förhandsversion: \] Distribuera – Konfigurera förutsättningar för att Azure Monitor och Azure Security-agenter på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Konfigurera datorer för att automatiskt installera Azure Monitor och Azure Security-agenter. Security Center samlar in händelser från agenterna och använder dem för att tillhandahålla säkerhetsaviseringar och skräddarsydda härdningsuppgifter (rekommendationer). Skapa en resursgrupp och En Log Analytics-arbetsyta i samma region som datorn för att lagra granskningsposter. Den här principen gäller endast för virtuella datorer i ett fåtal regioner. |5 |1.0.0-preview |
|[Aktivera Azure Monitor för Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Aktivera Azure Monitor för Virtual Machine Scale Sets i det angivna omfånget (hanteringsgrupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsytan som parameter. Obs! Om din upgradePolicy för skalningsuppsättningen är inställd på Manuell måste du tillämpa tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgradering på dem. I CLI är detta az vmss update-instances. |6 |1.0.1 |
|[Aktivera Azure Monitor for VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Aktivera Azure Monitor för de virtuella datorerna i det angivna omfånget (hanteringsgrupp, prenumeration eller resursgrupp). Tar Log Analytics-arbetsytan som parameter. |10 |2.0.0 |
