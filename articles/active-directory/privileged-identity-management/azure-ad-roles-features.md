---
title: Roll funktioner i Azure AD i Privileged Identity Management | Microsoft Docs
description: Hantera Azure AD-roller för tilldelning Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4478c9c286c06d5d6c5593195a0e93abd286b8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371519"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Hanterings funktioner för Azure AD-roller i Privileged Identity Management

Hanterings upplevelsen för Azure AD-roller i Privileged Identity Management har uppdaterats för att förena hur Azure AD-roller och Azure-resurs roller hanteras. Tidigare har Privileged Identity Management för Azure Resource roles några viktiga funktioner som inte var tillgängliga för Azure AD-roller.

När uppdateringen för närvarande är distribuerad sammanfogas de två i en enda hanterings miljö och du får samma funktioner för Azure AD-roller som för Azures resurs roller. Den här artikeln innehåller information om de uppdaterade funktionerna och eventuella krav.

## <a name="time-bound-assignments"></a>Tidsbegränsade tilldelningar

Tidigare fanns det två möjliga tillstånd för roll tilldelningar: *berättigade* och *permanenta*. Nu kan du också ange en start-och slut tid för varje typ av tilldelning. Det här tillägget ger dig fyra möjliga tillstånd att placera en uppgift i:

- Berättigad permanent
- Aktiv permanent
- Berättigad, med angivna start-och slutdatum för tilldelning
- Aktiv, med angivna start-och slutdatum för tilldelning

I många fall, även om du inte vill att användarna ska ha en berättigad tilldelning och aktiverar roller varje gång, kan du skydda din Azure AD-organisation genom att ange en förfallo tid för tilldelningar. Om du till exempel har vissa tillfälliga användare som är berättigade bör du överväga att ange förfallo datum så att de automatiskt tas bort från roll tilldelningen när deras arbete har slutförts.

## <a name="new-role-settings"></a>Nya roll inställningar

Vi lägger också till nya inställningar för Azure AD-roller.

- **Tidigare** kunde du bara konfigurera aktiverings inställningar per roll. Det innebär att aktiverings inställningar, till exempel Multi-Factor Authentication-krav och incident-/begäran biljett krav har tillämpats på alla användare som är berättigade till en viss roll.
- **Nu** kan du konfigurera om en enskild användare behöver utföra Multi-Factor Authentication innan de kan aktivera en roll. Du kan också ha avancerad kontroll över dina Privileged Identity Management e-postmeddelanden relaterade till vissa roller.

## <a name="extend-and-renew-assignments"></a>Utöka och förnya tilldelningar

När du avbildar en tidsbegränsad tilldelning kan den första frågan du frågar vad händer om en roll har upphört att gälla? I den här nya versionen tillhandahåller vi två alternativ för det här scenariot:

- **Utöka**: när en roll tilldelning närmar sig förfallo datum kan användaren använda Privileged Identity Management för att begära ett tillägg för roll tilldelningen
- **Förnya**: när en roll tilldelning har upphört att gälla kan användaren använda Privileged Identity Management för att begära en förnyelse för roll tilldelningen

Både åtgärder som initieras av användaren kräver ett godkännande från en global administratör eller en privilegie rad roll administratör. Administratörer behöver inte längre vara i arbetet med att hantera dessa förfallo datum. De behöver bara vänta på begär Anden om förlängning eller förnyelse och godkänna dem om begäran är giltig.

## <a name="api-changes"></a>API-ändringar

När kunderna har den uppdaterade versionen som är distribuerad till sin Azure AD-organisation slutar det befintliga graf-API: et att fungera. Du måste gå över till för att använda [Graph API för Azures resurs roller](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Om du vill hantera Azure AD-roller med det API: t byter du `/azureResources` till `/aadroles` i signaturen och använder katalog-ID: t för `resourceId` .

Vi har försökt med vårt bästa att kontakta alla kunder som använder föregående API för att meddela dem om den här ändringen i förväg. Om din Azure AD-organisation har flyttats till den nya versionen och du fortfarande är beroende av det gamla API: et kan du kontakta teamet på pim_preview@microsoft.com .

## <a name="powershell-change"></a>PowerShell-ändring

För kunder som använder Privileged Identity Management PowerShell-modulen för Azure AD-roller slutar PowerShell att arbeta med uppdateringen. I stället för föregående cmdletar måste du använda Privileged Identity Management-cmdletar i Azure AD Preview PowerShell-modulen. Installera Azure AD PowerShell-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Nu kan du [läsa dokumentationen och exemplen för PIM-åtgärder i den här PowerShell-modulen](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-configure.md)
- [Roll definitioner i Azure AD](../roles/permissions-reference.md)