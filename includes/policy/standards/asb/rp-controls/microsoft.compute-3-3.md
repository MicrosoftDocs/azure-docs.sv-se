---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3680c9c80716cc5e074f370d20a1a5929de0b699
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867958"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Granska Windows-datorer som saknar någon av de angivna medlemmarna i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Kräver att krav distribueras till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen Administratörer inte innehåller en eller flera medlemmar som anges i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Granska Windows-datorer som har extra konton i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Kräver att krav distribueras till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen Administratörer innehåller medlemmar som inte finns med i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[Granska Windows-datorer som har de angivna medlemmarna i gruppen Administratörer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Kräver att krav distribueras till principtilldelningsomfånget. Mer information finns i [https://aka.ms/gcpol](https://aka.ms/gcpol) . Datorer är icke-kompatibla om den lokala gruppen Administratörer innehåller en eller flera av de medlemmar som anges i principparametern. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
