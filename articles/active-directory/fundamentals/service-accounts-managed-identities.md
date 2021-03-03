---
title: Skydda hanterade identiteter i Azure Active Directory
description: Beskrivning av hur du hittar, bedömer och ökar säkerheten för hanterade identiteter.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32ba630d5e2f8e3e581f394af2ee687a971d3a7a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693284"
---
# <a name="securing-managed-identities"></a>Skydda hanterade identiteter

Utvecklare utmanas ofta av hanteringen av hemligheter och autentiseringsuppgifter som används för att skydda kommunikationen mellan olika tjänster. Hanterade identiteter är säkra Azure Active Directory (Azure AD)-identiteter som skapas för att tillhandahålla identiteter för Azure-resurser.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Fördelar med att använda hanterade identiteter för Azure-resurser

Följande är fördelarna med att använda hanterade identiteter:

* Du behöver inte hantera autentiseringsuppgifter. Med hanterade identiteter hanteras autentiseringsuppgifterna fullständigt, roteras och skyddas av Azure. Identiteter tillhandahålls och tas bort automatiskt med Azure-resurser. Hanterade identiteter gör det möjligt för Azure-resurser att kommunicera med alla tjänster som stöder Azure AD-autentisering.

* Ingen (inklusive en global administratör) har åtkomst till autentiseringsuppgifterna, så de kan inte skadas av misstag av, till exempel att de ingår i koden.

## <a name="when-to-use-managed-identities"></a>När ska du använda hanterade identiteter?

Hanterade identiteter används bäst för kommunikation mellan tjänster som stöder Azure AD-autentisering. 

Ett käll system begär åtkomst till en mål tjänst. Alla Azure-resurser kan vara ett käll system. Till exempel har en virtuell Azure-dator, Azure Function-instans och Azure App Services-instanser stöd för hanterade identiteter.

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Hur autentisering och auktorisering fungerar

Med hanterade identiteter kan käll systemet hämta en token från Azure AD utan att käll ägaren måste hantera autentiseringsuppgifter. Azure hanterar autentiseringsuppgifterna. Den token som hämtas av käll systemet presenteras för mål systemet för autentisering. 

Mål systemet måste autentisera (identifiera) och auktorisera käll systemet innan åtkomst tillåts. När mål tjänsten har stöd för Azure AD-baserad autentisering, accepterar den en åtkomsttoken som utfärdats av Azure AD. 

Azure har ett kontroll plan och ett data plan. I kontroll planet skapar du resurser och i det data plan som du har åtkomst till dem. Du kan till exempel skapa en Cosmos-databas i kontroll planet, men fråga den i data planet.

När mål systemet accepterar token för autentisering, kan det stödja olika mekanismer för auktorisering för kontroll planet och data planet.

Alla kontroll Plans åtgärder i Azure hanteras av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) och använder [Azure-rollbaserad Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview). I data planet har varje mål system en egen mekanism för auktorisering. Azure Storage stöder Azure RBAC på data planet. Till exempel kan program som använder Azure App-tjänster läsa data från Azure Storage och program som använder Azure Kubernetes-tjänsten kan läsa hemligheter lagrade i Azure Key Vault.

Mer information om kontroll och data plan finns i [kontroll plan och data plan åtgärder-Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/control-plane-and-data-plane).

Alla Azure-tjänster kommer slutligen att ha stöd för hanterade identiteter. Mer information finns i [tjänster som stöder hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

##  

## <a name="types-of-managed-identities"></a>Typer av hanterade identiteter

Det finns två typer av hanterade identiteter – tilldelade system och tilldelade användare.

Systemtilldelad hanterad identitet har följande egenskaper:

* De har 1:1-relationer med Azure-resursen. Det finns till exempel en unik hanterad identitet som är kopplad till varje virtuell dator.

* De är knutna till livs cykeln för Azure-resurser. När resursen tas bort tas den hanterade identitet som är kopplad till den bort automatiskt, vilket eliminerar risken som är kopplad till överblivna konton. 

Användare som tilldelats hanterade identiteter har följande egenskaper:

* Livs cykeln för dessa identiteter är oberoende av en Azure-resurs och du måste hantera livs cykeln. När Azure-resursen tas bort, tas inte den tilldelade användarens hanterade identitet automatiskt bort åt dig.

* En enskild användare som tilldelats en hanterad identitet kan tilldelas till noll eller flera Azure-resurser.

* De kan skapas i förväg och sedan tilldelas till en resurs.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Hitta hanterade identitets tjänstens huvud namn i Azure AD

Du kan hitta hanterade identiteter på flera sätt:

* Använda sidan företags program i Azure Portal

* Använda Microsoft Graph

### <a name="using-the-azure-portal"></a>Använda Azure Portal

1. I Azure AD väljer du företags program.

2. Välj filtret för "hanterade identiteter" 

   ![Bild av skärmen alla program med list rutan program typ med markering av hanterade identiteter.](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Använda Microsoft Graph

Du kan hämta en lista över alla hanterade identiteter i din klient organisation med följande GET-begäran till Microsoft Graph:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

Du kan filtrera dessa förfrågningar. Mer information finns i diagram dokumentationen för [Get servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-get?view=graph-rest-1.0&tabs=http).

## <a name="assess-the-security-of-managed-identities"></a>Utvärdera säkerheten för hanterade identiteter 

Du kan utvärdera säkerheten för hanterade identiteter på följande sätt:

* Granska behörigheter och se till att den lägsta privilegierade modellen är markerad. Använd följande PowerShell-cmdlet för att hämta de behörigheter som tilldelats dina hanterade identiteter.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Se till att den hanterade identiteten inte ingår i några privilegierade grupper, till exempel en administratörs grupp.  
Du kan göra detta genom att räkna upp medlemmarna i dina privilegierade grupper med PowerShell.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Se till att du vet vilka resurser som den hanterade identiteten har åtkomst till](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell).

## <a name="move-to-managed-identities"></a>Flytta till hanterade identiteter

Om du använder ett huvud namn för tjänsten eller ett Azure AD-användarkonto kan du utvärdera om du i stället kan använda en hanterad för att eliminera behovet av att skydda, rotera och hantera autentiseringsuppgifter. 

## <a name="next-steps"></a>Nästa steg

**Information om hur du skapar hanterade identiteter finns i:** 

[Skapa en användare som tilldelats en hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal). 

[Aktivera en systemtilldelad hanterad identitet när en resurs skapas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

[Aktivera systemtilldelad hanterad identitet för en befintlig resurs](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Mer information om tjänst konton finns i:**

[Introduktion till Azure Active Directory tjänst konton](service-accounts-introduction-azure.md)

[Skydda tjänstens huvud namn](service-accounts-principal.md)

[Styrande Azure-tjänstekonton](service-accounts-governing-azure.md)

[Introduktion till lokala tjänst konton](service-accounts-on-poremises.md)

 

 

 
