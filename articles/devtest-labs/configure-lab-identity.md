---
title: Konfigurera en labb identitet i Azure DevTest Labs
description: Lär dig hur du konfigurerar en labb identitet i Azure DevTest.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719737"
---
# <a name="configure-a-lab-identity"></a>Konfigurera en labb identitet

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Det är viktigt att dessa autentiseringsuppgifter skyddas. Helst bör autentiseringsuppgifterna aldrig visas på utvecklarnas arbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men koden måste autentiseras för att Key Vault ska kunna hämta dem. 

Funktionen hanterade identiteter för Azure-resurser i Azure Active Directory (Azure AD) löser det här problemet. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod. Läs mer om [hanterade identiteter på Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Det finns två typer av hanterade identiteter: 

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet  

En **systemtilldelad hanterad identitet**   aktive ras direkt på en Azure-tjänstinstans. När identiteten har aktiverats skapar Azure en identitet för instansen i den Azure AD-klientorganisation som är betrodd av prenumerationen för instansen. När identiteten har skapats etableras autentiseringsuppgifterna till instansen. Livs cykeln för en tilldelad identitet är direkt bunden till den Azure-tjänstinstans som den är aktive rad på. Om instansen tas bort rensar Azure automatiskt autentiseringsuppgifterna och identiteten i Azure AD. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>Scenarier för att använda Labbets tilldelade identitet  

Varje DevTest Labs skapas med en tilldelad identitet som är giltig för labbets livs längd. Den tilldelade identiteten används i följande syfte:  

- Alla [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) baserade distributioner som används för att skapa flera virtuella datorer och/eller plattform som en tjänst miljö körs med Labbets tilldelade identitet  
- Disk kryptering för labb diskar som använder en kundhanterad nyckel stöds via Labbets tilldelade identitet. Genom att ge uttryckligen åtkomst till Labbets identitet för att komma åt din disk krypterings uppsättning kan labbet kryptera alla virtuella dator diskar åt dig. Lär dig mer om [hur du aktiverar disk kryptering](encrypt-disks-customer-managed-keys.md) för dina labb diskar med en kundhanterad nyckel.  

### <a name="configure-identity"></a>Konfigurera identitet

Det här avsnittet visar hur du konfigurerar Labbets identitets princip.

> [!NOTE]
> För labb som skapats före 8/10/2020 är systemtilldelad identitet inställd på av. Som labb ägare kan du aktivera den, om du vill använda labb i de syfte som anges i föregående avsnitt.  
>
> För nya labb som skapats efter 8/10/2020 är Labbets tilldelade identitet inställd på som standard och labb ägaren kommer inte att kunna stänga av den i livs cykeln för labbet.  

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **DevTest Labs**.
1. I listan med labb väljer du det labb du vill ha.
1. Välj **konfiguration och principer**  ->  **identitet (för hands version)**. 

> [!div class="mx-imgBorder"]
> ![Konfigurera identitet](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet  

En användare som tilldelats en hanterad identitet skapas som en fristående Azure-resurs. När den skapas skapar Azure en identitet i den Azure AD-klientorganisation som är betrodd av den prenumeration som används. När identiteten har skapats kan den tilldelas till en eller flera tjänstinstanser i Azure. Livs cykeln för en användardefinierad identitet hanteras separat från livs cykeln för de Azure-tjänsteinstanser som den är tilldelad till. 

DevTest Labs stöder användar tilldelning av identiteter för både virtuella datorer och Azure Resource Manager baserade miljöer.  Mer information finns i följande avsnitt:

- [Lägg till en användardefinierad identitet för att distribuera labb Azure Resource Manager miljöer](use-managed-identities-environments.md)
- [Lägg till en användare tilldelade identiteter för att distribuera virtuella labb datorer](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Nästa steg

Granska [Konfigurera kostnads hantering](devtest-lab-configure-cost-management.md)