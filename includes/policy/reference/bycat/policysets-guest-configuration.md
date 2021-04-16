---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 88d459140be43306f5f75dd96a7800b45b28d74b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107504905"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[Granska datorer med inställningar för osäker lösenordssäkerhet](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Det här initiativet distribuerar principkraven och granskar datorer med osäkra inställningar för lösenordssäkerhet. Mer information om gästkonfigurationsprinciper finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[\[Förhandsversion: \] Distribuera förhandskrav för att aktivera gästkonfigurationsprinciper på virtuella datorer](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Det här initiativet lägger till en system tilldelad hanterad identitet och distribuerar plattforms lämpligt gästkonfigurationstillägg till virtuella datorer som kan övervakas av gästkonfigurationsprinciper. Detta är ett krav för alla gästkonfigurationsprinciper och måste tilldelas till principtilldelningsomfånget innan du använder en gästkonfigurationsprincip. Mer information om gästkonfiguration finns på [https://aka.ms/gcpol](https://aka.ms/gcpol) . |4 |1.0.0-preview |
|[\[Förhandsversion: \] Windows-datorer bör uppfylla kraven för Azure-säkerhetsbaslinjen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Det här initiativet granskar Windows-datorer med inställningar som inte uppfyller Azures säkerhetsbaslinje. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-preview |
