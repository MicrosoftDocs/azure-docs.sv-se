---
title: Skydda tjänstens huvud namn i Azure Active Directory
description: Hitta, utvärdera och skydda tjänstens huvud namn.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 297c0a53fb2ab4ee0b2c5291cabf5a63c8841664
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604710"
---
# <a name="securing-service-principals"></a>Skydda tjänstens huvud namn

En Azure Active Directory (Azure AD) [tjänstens huvud namn](../develop/app-objects-and-service-principals.md) är den lokala åter givningen av ett program objekt i en enda klient eller katalog.  Den fungerar som identitet för program instansen. Tjänstens huvud namn definierar vem som kan komma åt programmet och vilka resurser som programmet kan komma åt. Ett huvud namn för tjänsten skapas i varje klient där programmet används och refererar till objektet globalt unikt program. Klienten skyddar tjänstens huvud namn för inloggning och åtkomst till resurser.  

### <a name="tenant-service-principal-relationships"></a>Innehavarens huvud relationer för tjänsten
Ett program med en enda klient organisation har bara ett huvud namn för tjänsten i hem klienten. Ett webb program eller API för flera innehavare kräver ett huvud namn för tjänsten i varje klient organisation. Ett tjänst objekt skapas när en användare från den klienten har samtyckt till programmets eller API: s användning. Detta medgivande skapar en en-till-många-relation mellan programmet för flera klienter och dess associerade tjänst huvud namn.

Ett program med flera klienter är lokalt i en enda klient organisation och har utformats för att ha instanser i andra klienter. De flesta SaaS-program (program vara som en tjänst) är utformade för flera innehavare. Använd tjänstens huvud namn för att säkerställa rätt säkerhets position för programmet och dess användare i både en klient och flera klient användnings fall.

## <a name="applicationid-and-objectid"></a>ApplicationID och ObjectID

En viss program instans har två distinkta egenskaper: ApplicationID (kallas även ClientID) och ObjectID.

> [!NOTE] 
> Du kanske upptäcker att villkoren för program-och tjänstens huvud namn används utbytbart när de refereras till i ett program i kontexten för autentisering av relaterade uppgifter. De är dock två olika representationer av program i Azure AD.
 

ApplicationID representerar det globala programmet och är detsamma för alla program instanser mellan klienter. ObjectID är ett unikt värde för ett program objekt och representerar tjänstens huvud namn. Precis som med användare, grupper och andra resurser hjälper ObjectID unikt att identifiera en program instans i Azure AD.

Mer detaljerad information om det här ämnet finns i [relation till program-och tjänst objekt](../develop/app-objects-and-service-principals.md).

Du kan också skapa ett program och dess tjänst huvud objekt (ObjectID) i en klient med hjälp av Azure PowerShell, Azure CLI, Microsoft Graph, Azure Portal och andra verktyg. 

![Skärm bild som visar en ny program registrering med fälten program-ID och objekt-ID markerade.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Det finns två mekanismer för autentisering med tjänstens huvud namn – klient certifikat och klient hemligheter. 

![ Skärm bild som visar sidan för nya appar där områdena certifikat och klient hemligheter är markerade.](./media/securing-service-accounts/secure-principal-certificates.png)

Certifikat är säkrare: Använd klient certifikat om möjligt. Till skillnad från klient hemligheter kan klient certifikat inte oavsiktligt bäddas in i kod. Använd Azure Key Vault för certifikat-och hemlighets hantering när det är möjligt att kryptera följande till gångar genom att använda nycklar som skyddas av säkerhetsmoduler för maskin vara:

* nycklar för autentisering

* lagrings konto nycklar

* data krypterings nycklar

* . PFX-filer

* lösenord 

Mer information om Azure Key Vault och hur du använder den för certifikat-och hemlighets hantering finns i [om Azure Key Vault](../../key-vault/general/overview.md) och [tilldela en Key Vault åtkomst princip med hjälp av Azure Portal](../../key-vault/general/assign-access-policy-portal.md). 

 ### <a name="challenges-and-mitigations"></a>Utmaningar och begränsningar
I följande tabell visas åtgärder för utmaningar som du kan stöta på när du använder tjänstens huvud namn.


| Utmaningar| Åtgärder |
| - | - |
| Åtkomst granskningar för tjänstens huvud namn som tilldelats privilegierade roller.| Den här funktionen är i för hands version och ännu inte tillgänglig. |
| Granskar tjänstens huvud namn åtkomst| Manuell kontroll av resursens åtkomst kontrol lista med hjälp av Azure Portal. |
| Över behöriga tjänst huvud namn| När du skapar Automation service-konton eller tjänstens huvud namn, anger du bara de behörigheter som krävs för uppgiften. Utvärdera befintliga huvud namn för tjänsten för att se om du kan minska behörigheterna. |
|Identifiera ändringar av autentiseringsuppgifter för tjänstens huvud namn eller autentiseringsmetoder |Använd rapport arbets boken för känsliga åtgärder, som kan hjälpa dig att undvika det här problemet. [Se förklaringen i det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>Hitta konton med tjänstens huvud namn
Kör följande kommandon för att hitta konton med hjälp av tjänstens huvud namn.

Använda Azure CLI


`az ad sp list`

Använda PowerShell

`Get-AzureADServicePrincipal -All:$true` 


Mer information finns i [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="assess-service-principal-security"></a>Utvärdera tjänstens huvud namn säkerhet

För att utvärdera säkerheten för dina tjänst huvud namn, se till att du utvärderar behörigheter och lagring av autentiseringsuppgifter.

Minska potentiella utmaningar med följande information.

|Utmaningar | Åtgärder|
| - | - |
| Identifiera den användare som samtyckt till en app för flera klienter och identifiera illegala medgivande bidrag till en app med flera klient organisationer | Kör följande PowerShell för att hitta appar för flera klienter.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Inaktivera användar medgivande. <br>Tillåt användar medgivande från verifierade utgivare, för valda behörigheter (rekommenderas) <br> Använd villkorlig åtkomst för att blockera tjänstens huvud namn från ej betrodda platser. Konfigurera dem under användar kontexten och deras token ska användas för att aktivera tjänstens huvud namn.|
|Använda en hårdkodad delad hemlighet i ett skript med hjälp av ett huvud namn för tjänsten.|Använd ett certifikat eller en Azure Key Vault.|
|Spåra vem som använder certifikatet eller hemligheten| Övervaka inloggnings tillägg för tjänstens huvud namn med hjälp av inloggnings loggarna för Azure AD.|
Det går inte att hantera tjänstens huvud namn med villkorlig åtkomst.| Övervaka inloggningarna med inloggnings loggarna för Azure AD
| Standard rollen för Azure RBAC är deltagare. |Utvärdera behoven och tillämpa rollen med minsta möjliga behörighet för att uppfylla det behovet.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Flytta från ett användar konto till ett huvud namn för tjänsten  
Om du använder ett Azure-användarkonto som tjänstens huvud namn kan du utvärdera om du kan flytta till en [hanterad identitet](../../app-service/overview-managed-identity.md?tabs=dotnet) eller ett huvud namn för tjänsten. Om du inte kan använda en hanterad identitet, etablerar du ett huvud namn för tjänsten som bara har tillräcklig behörighet och omfattning för att köra de uppgifter som krävs. Du kan skapa ett huvud namn för tjänsten genom att [Registrera ett program](../develop/howto-create-service-principal-portal.md)eller med [PowerShell](../develop/howto-authenticate-service-principal-powershell.md).

När du använder Microsoft Graph bör du kontrol lera dokumentationen för det specifika API: t, [som i det här exemplet](/powershell/azure/create-azure-service-principal-azureps), och se till att behörighets typen för programmet visas som stöds.

## <a name="next-steps"></a>Nästa steg

**Läs mer om tjänstens huvud namn:**

[Skapa ett huvudnamn för tjänsten](../develop/howto-create-service-principal-portal.md)

 [Övervaka inloggningar för tjänstens huvud namn](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**Lär dig mer om att skydda tjänst konton:**

[Introduktion till Azures tjänst konton](service-accounts-introduction-azure.md)

[Skydda hanterade identiteter](service-accounts-managed-identities.md)

[Styrande Azure-tjänstekonton](service-accounts-governing-azure.md)

[Introduktion till lokala tjänst konton](service-accounts-on-premises.md)
