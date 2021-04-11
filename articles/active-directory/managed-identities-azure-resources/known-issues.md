---
title: Kända problem med hanterade identiteter – Azure Active Directory
description: Kända problem med hanterade identiteter för Azure-resurser.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226930"
---
# <a name="known-issues-with-managed-identities"></a>Kända problem med hanterade identiteter

I den här artikeln beskrivs några problem med hanterade identiteter och hur du kan åtgärda dem. Vanliga frågor om hanterade identiteter finns dokumenterade i artikeln [vanliga frågor och svar](managed-identities-faq.md) .
## <a name="vm-fails-to-start-after-being-moved"></a>Det går inte att starta den virtuella datorn efter flyttning 

Om du flyttar en virtuell dator i ett körnings tillstånd från en resurs grupp eller prenumeration fortsätter den att köras under flytten. Om den virtuella datorn stoppas och startas om när den har flyttats, kommer den dock inte att starta. Det här problemet uppstår eftersom den virtuella datorn inte uppdaterar referensen till de hanterade identiteterna för Azure-resursers identitet och fortsätter att peka på den i den gamla resurs gruppen.

**Lösning** 
 
Utlös en uppdatering på den virtuella datorn så att den kan hämta korrekta värden för hanterade identiteter för Azure-resurser. Du kan göra en ändring av en VM-egenskap för att uppdatera referensen till de hanterade identiteterna för Azures resurs identitet. Du kan till exempel ange ett nytt tagg värde på den virtuella datorn med följande kommando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Det här kommandot anger en ny tagg "fixVM" med värdet 1 på den virtuella datorn. 
 
Genom att ange den här egenskapen uppdateras VM med rätt hanterade identiteter för resurs-URI för Azure-resurser och sedan bör du kunna starta den virtuella datorn. 
 
När den virtuella datorn har startats kan taggen tas bort med hjälp av följande kommando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Överföra en prenumeration mellan Azure AD-kataloger

Hanterade identiteter uppdateras inte när en prenumeration flyttas/överförs till en annan katalog. Det innebär att alla befintliga systemtilldelade eller användarspecifika hanterade identiteter bryts. 

Lösning för hanterade identiteter i en prenumeration som har flyttats till en annan katalog:

 - För systemtilldelade hanterade identiteter: inaktivera och återaktivera. 
 - För användare som tilldelats hanterade identiteter: ta bort, återskapa och koppla dem igen till nödvändiga resurser (till exempel virtuella datorer)

Mer information finns i [Överföra en Azure-prenumeration till en annan Azure AD-katalog](../../role-based-access-control/transfer-subscription.md).


## <a name="next-steps"></a>Nästa steg

Du kan läsa vår artikel om de [tjänster som stöder hanterade identiteter](services-support-managed-identities.md) och våra [vanliga frågor och svar](managed-identities-faq.md)
