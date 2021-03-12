---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: aebc296493192026dac64d22be6f28aa6fc94fe7
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613816"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[Granska datorer med säkerhets inställningar för oskyddade lösen ord](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Det här initiativet distribuerar princip kraven och granskar datorer med osäkra lösen ords säkerhets inställningar. Mer information om principer för gäst konfiguration finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[\[För hands version \] : Distribuera krav för att aktivera principer för gäst konfiguration på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Det här initiativet lägger till en systemtilldelad hanterad identitet och distribuerar den plattforms lämpliga gäst konfigurations tillägget till virtuella datorer som är berättigade att övervakas av principer för gäst konfiguration. Detta är en förutsättning för alla principer för gäst konfiguration och måste tilldelas princip tilldelnings omfånget innan du använder någon princip för gäst konfiguration. Mer information om gäst konfiguration finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0 – för hands version |
|[\[För hands version \] : Windows-datorer uppfyller kraven för Azures säkerhets bas linje](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Detta initiativ granskar Windows-datorer med inställningar som inte uppfyller Azures säkerhets bas linje. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0 – för hands version |
