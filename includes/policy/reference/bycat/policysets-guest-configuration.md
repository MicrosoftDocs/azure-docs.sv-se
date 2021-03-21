---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 654a7f18d7b06756a6073b4203ea7b6042d1c747
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604943"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[Granska datorer med säkerhets inställningar för oskyddade lösen ord](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Det här initiativet distribuerar princip kraven och granskar datorer med osäkra lösen ords säkerhets inställningar. Mer information om principer för gäst konfiguration finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[\[För hands version \] : Distribuera krav för att aktivera principer för gäst konfiguration på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Det här initiativet lägger till en systemtilldelad hanterad identitet och distribuerar den plattforms lämpliga gäst konfigurations tillägget till virtuella datorer som är berättigade att övervakas av principer för gäst konfiguration. Detta är en förutsättning för alla principer för gäst konfiguration och måste tilldelas princip tilldelnings omfånget innan du använder någon princip för gäst konfiguration. Mer information om gäst konfiguration finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0 – för hands version |
|[\[För hands version \] : Windows-datorer uppfyller kraven för Azures säkerhets bas linje](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Detta initiativ granskar Windows-datorer med inställningar som inte uppfyller Azures säkerhets bas linje. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0 – för hands version |
