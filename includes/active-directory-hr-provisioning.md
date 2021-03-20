---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93135397"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Molnet HR Application to Azure Active Directory användar etablering

Tidigare har IT-personalen förlitat sig på manuella metoder för att skapa, uppdatera och ta bort medarbetare. De har använt metoder som att ladda upp CSV-filer eller anpassade skript för att synkronisera medarbetar data. Dessa etablerings processer är fel känsliga, oskyddade och svåra att hantera.

Om du vill hantera livs cykeln för identiteter för anställda, leverantörer eller eventualtillgångar, [Azure Active Directory (Azure AD) användar etablerings tjänsten](../articles/active-directory/app-provisioning/user-provisioning.md) erbjuder integrering med MOLNbaserade HR-program (personal). Exempel på program är Workday eller SuccessFactors.

Azure AD använder denna integrering för att aktivera följande moln-arbets flöden för HR (app):

- **Etablera användare för att Active Directory:** Etablera valda uppsättningar av användare från en Cloud HR-app till en eller flera Active Directory domäner.
- **Tillhandahåll enbart moln användare till Azure AD:** I scenarier där Active Directory inte används etablerar du användare direkt från Cloud HR-appen till Azure AD.
- **Skriv tillbaka till Cloud HR-appen:** Skriv e-postadresserna och attributen för användar namn från Azure AD tillbaka till Cloud HR-appen.


## <a name="enabled-hr-scenarios"></a>Aktiverade HR-scenarier

Azure AD-tjänsten för användar etablering möjliggör automatisering av följande scenarier för HR-baserade identitets livs cykel hantering:

- **Ny anställds anställning:** När en ny medarbetare läggs till i Cloud HR-appen skapas ett användar konto automatiskt i Active Directory och Azure AD med alternativet att skriva tillbaka attributen e-postadress och användar namn till Cloud HR-appen.
- **Uppdateringar av anställdas attribut och profiler:** När en anställds post, till exempel namn, titel eller chef uppdateras i Cloud HR-appen, uppdateras deras användar konto automatiskt i Active Directory och Azure AD.
- **Anställdas uppsägningar:** När en medarbetare avslutas i Cloud HR-appen inaktive ras deras användar konto automatiskt i Active Directory och Azure AD.
- **Anställdas återställningar:** När en medarbetare återställs i Cloud HR-appen kan deras gamla konto automatiskt återaktiveras eller etableras om till Active Directory och Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Vem är den här integreringen bäst lämpad för?

Cloud HR app-integrering med Azure AD-användar etablering passar utmärkt för organisationer som:

- Vill ha en förbyggd, molnbaserad lösning för användar etablering i molnet.
- Kräv direkt användar etablering från Cloud HR-appen till Active Directory eller Azure AD.
- Kräv att användare ska tillhandahållas genom att använda data som hämtats från Cloud HR-appen.
- Kräv anslutning till, flytta och lämna användare för att synkroniseras till en eller flera Active Directory skogar, domäner och organisationsenheter enbart baserat på ändrings information som identifieras i Cloud HR-appen.
- Använd Office 365 för e-post.


### <a name="key-benefits"></a>Viktiga fördelar

Den här funktionen för HR-drivna IT-etablering ger följande betydande affärs förmåner:

- **Öka produktiviteten:** Nu kan du automatisera tilldelningen av användar konton och Office 365-licenser och ge åtkomst till nyckel grupper. Automatiserade tilldelningar ger nya anställda omedelbar till gång till sina jobb verktyg och ökar produktiviteten.
- **Hantera risk:** Du kan öka säkerheten genom att automatisera ändringar baserat på medarbetarnas status eller grupp medlemskap med data som flödar in från Cloud HR-appen. Automatisering av ändringar säkerställer att användar identiteter och åtkomst till viktiga appar uppdateras automatiskt när användare övergår till eller lämnar organisationen.
- **Hantera efterlevnad och styrning:** Azure AD stöder interna gransknings loggar för användar etablerings begär Anden som utförs av appar av både käll-och mål system. Med granskning kan du spåra vem som har åtkomst till apparna från en enda skärm.
- **Hantera kostnad:** Automatisk etablering minskar kostnaderna genom att undvika ineffektivitet och mänskligt fel som är kopplat till manuell etablering. Det minskar behovet av anpassade användar etablerings lösningar som skapats med tiden genom att använda äldre och inaktuella plattformar.
