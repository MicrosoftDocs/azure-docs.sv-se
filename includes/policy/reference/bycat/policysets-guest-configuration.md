---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 281ad78418150bf4cb458e4bf02acd9446fa01dd
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704021"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[Granska datorer med säkerhets inställningar för oskyddade lösen ord](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Det här initiativet distribuerar princip kraven och granskar datorer med osäkra lösen ords säkerhets inställningar. Mer information om principer för gäst konfiguration finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[Distribuera krav för att aktivera principer för gäst konfiguration på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Det här initiativet lägger till en systemtilldelad hanterad identitet och distribuerar den plattforms lämpliga gäst konfigurations tillägget till virtuella datorer som är berättigade att övervakas av principer för gäst konfiguration. Detta är en förutsättning för alla principer för gäst konfiguration och måste tilldelas princip tilldelnings omfånget innan du använder någon princip för gäst konfiguration. Mer information om gäst konfiguration finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0 – för hands version |
|[Windows-datorer måste uppfylla kraven för Azures säkerhets bas linje](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Detta initiativ granskar Windows-datorer med inställningar som inte uppfyller Azures säkerhets bas linje. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0 – för hands version |
