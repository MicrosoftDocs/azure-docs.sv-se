---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cc7e083352ec056af42d38de463a1fe1829ab557
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866127"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[Granska datorer med inställningar för osäker lösenordssäkerhet](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Det här initiativet distribuerar principkraven och granskar datorer med osäkra inställningar för lösenordssäkerhet. Mer information om gästkonfigurationsprinciper finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[\[Förhandsversion: \] Distribuera krav för att aktivera gästkonfigurationsprinciper på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Det här initiativet lägger till en system tilldelad hanterad identitet och distribuerar det plattforms lämpliga gästkonfigurationstillägget till virtuella datorer som kan övervakas av gästkonfigurationsprinciper. Detta är ett krav för alla gästkonfigurationsprinciper och måste tilldelas till principtilldelningsomfånget innan du använder någon gästkonfigurationsprincip. Mer information om gästkonfiguration finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0-förhandsversion |
|[\[Förhandsversion: \] Windows-datorer måste uppfylla kraven för Azure-säkerhetsbaslinjen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Det här initiativet granskar Windows-datorer med inställningar som inte uppfyller Azures säkerhetsbaslinje. Mer information finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-förhandsversion |
