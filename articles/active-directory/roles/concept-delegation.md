---
title: Förstå delegering av administratörs roll – Azure Active Directory | Microsoft Docs
description: Delegerings modeller, exempel och roll säkerhet i Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77ba93b9017fa75f7247bda639880415eda1d280
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740320"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegera administration i Azure Active Directory

Med organisations tillväxten är komplexitet. Ett vanligt svar är att minska en del av arbets belastningen för åtkomst hantering med Azure Active Directory (AD)-administratörs roller. Du kan tilldela de minst möjliga behörigheterna för användare att komma åt sina appar och utföra sina uppgifter. Även om du inte tilldelar rollen global administratör till varje program ägare, placerar du program hanterings ansvar på befintliga globala administratörer. Det finns många orsaker till att en organisation flyttar till en mer decentraliserad administration. Den här artikeln kan hjälpa dig att planera för delegering i din organisation.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Centraliserad respektive delegerad behörighet

När en organisation växer kan det vara svårt att hålla reda på vilka användare som har olika administratörs roller. Om en medarbetare har administratörs rättigheter som de inte behöver kan din organisation vara mer sårbar för säkerhets överträdelser. I allmänhet beror det på hur många administratörer som du stöder och hur detaljerad deras behörigheter är beroende av hur stor din distribution är och hur komplext den är.

* I små eller proof-of-Concept-distributioner gör en eller flera administratörer allt, Det finns ingen delegering. I det här fallet skapar du varje administratör med rollen global administratör.
* I större distributioner med fler datorer, program och skriv bord krävs mer delegering. Flera administratörer kan ha mer speciella funktions ansvars områden (roller). Vissa kan till exempel vara privilegierade identitets administratörer och andra kan vara program administratörer. Dessutom kan en administratör endast hantera vissa grupper av objekt, till exempel enheter.
* Även större distributioner kan kräva ännu mer detaljerade behörigheter, samt eventuellt administratörer med inkonventionella eller hybrid roller.

I Azure AD-portalen kan du [Visa alla medlemmar i en roll](manage-roles-portal.md), vilket kan hjälpa dig att snabbt kontrol lera dina distributions-och ombuds behörigheter.

Om du är intresse rad av att delegera åtkomst till Azure-resurser i stället för administrativ åtkomst i Azure AD, se [tilldela en Azure-roll](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Delegerings planering

Det fungerar för att utveckla en Delegerings modell som passar dina behov. Att utveckla en Delegerings modell är en repetitiv design process och vi rekommenderar att du följer dessa steg:

* Definiera de roller du behöver
* Delegera app-administration
* Ge möjligheten att registrera program
* Delegera appens ägarskap
* Utveckla en säkerhets plan
* Upprätta nöd konton
* Skydda dina administratörs roller
* Gör privilegie rad höjning temporärt

## <a name="define-roles"></a>Definiera roller

Ta reda på Active Directory uppgifter som utförs av administratörer och hur de mappar till roller. Du kan [Visa detaljerade roll beskrivningar](manage-roles-portal.md) i Azure Portal.

Varje uppgift bör utvärderas för frekvens, betydelse och svårighet. Dessa kriterier är viktiga aspekter av aktivitets definitionen eftersom de bestämmer om en behörighet ska delegeras:

* Uppgifter som du utför rutinmässigt, har begränsad risk och är enkla att slutföra är utmärkta kandidater för delegering.
* Aktiviteter som du sällan använder, men som har stor inverkan på organisationen och kräver höga skicklighets nivåer bör övervägas noggrant före delegering. I stället kan du [tillfälligt öka ett konto till den roll som krävs](../privileged-identity-management/pim-configure.md) eller tilldela om uppgiften.

## <a name="delegate-app-administration"></a>Delegera app-administration

Spridningen av appar i din organisation kan belasta Delegerings modellen. Om du placerar belastningen på program åtkomst hantering på den globala administratören är det troligt att modellen ökar sin belastning när tiden går på. Om du har beviljat andra rollen som global administratör för saker som att konfigurera företags program kan du nu avlasta dem till följande mindre privilegierade roller. Detta hjälper till att förbättra din säkerhets position och minskar risken för olycklig misstag. De mest privilegierade program administratörs rollerna är:

* Rollen **program administratör** , som ger möjlighet att hantera alla program i katalogen, inklusive registreringar, inställningar för enkel inloggning, användar-och grupp tilldelningar och licensiering, inställningar för programproxy och medgivande. Det ger inte möjlighet att hantera villkorlig åtkomst.
* Rollen som **moln program administratör** , som ger alla funktioner i program administratören, förutom att den inte beviljar åtkomst till programproxy-inställningar (eftersom den inte har någon lokal behörighet).

## <a name="delegate-app-registration"></a>Delegera registrering av appar

Som standard kan alla användare skapa program registreringar. För att selektivt ge möjlighet att skapa program registreringar:

* Ange **användare kan registrera program** till inga i **användar inställningar**
* Tilldela användaren rollen som program utvecklare

För att selektivt ge möjlighet att ge ett program åtkomst till data:

* Ange **att användare kan godkänna program som har åtkomst till företags information i stället** för att få i **användar inställningar**
* Tilldela användaren rollen som program utvecklare

När en programutvecklare skapar en ny program registrering läggs de automatiskt till som första ägare.

## <a name="delegate-app-ownership"></a>Delegera appens ägarskap

För ännu mer detaljerade appars åtkomst delegering kan du tilldela ägarskap till enskilda företags program. Detta kompletterar det befintliga stödet för tilldelning av ägare av program registrering. Ägarskap tilldelas per företags program på bladet företags program. Fördelarna är att ägare bara kan hantera de företags program som de äger. Du kan till exempel tilldela en ägare för Salesforce-programmet och den ägaren kan hantera åtkomsten till och konfigurationen för Salesforce, och inga andra program. Ett företags program kan ha många ägare och en användare kan vara ägare till många företags program. Det finns två roller för app-ägare:

* Rollen **företags program ägare** ger möjlighet att hantera de företags program som användaren äger, inklusive inställningar för enkel inloggning, användar-och grupp tilldelningar samt lägga till ytterligare ägare. Det ger inte möjlighet att hantera inställningar för programproxy eller villkorlig åtkomst.
* **Ägar rollen program registrering** ger möjlighet att hantera program registreringar för appar som användaren äger, inklusive applikations manifestet och lägga till ytterligare ägare.

## <a name="develop-a-security-plan"></a>Utveckla en säkerhets plan

Azure AD innehåller en omfattande guide för att planera och köra en säkerhets plan för dina administratörs roller i Azure AD, vilket [skyddar privilegie rad åtkomst för Hybrid-och moln distributioner](security-planning.md).

## <a name="establish-emergency-accounts"></a>Upprätta nöd konton

För att upprätthålla åtkomsten till ditt identitets hanterings lager när det uppstår problem ska du förbereda återställnings konton i nödfall genom att [skapa administrativa konton för nöd anslutning](security-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Skydda dina administratörs roller

Angripare som får kontroll över privilegierade konton kan orsaka fantastiska skador, så skydda dessa konton först med hjälp av den [grundläggande åtkomst princip](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) som är tillgänglig som standard för alla Azure AD-organisationer (i offentlig för hands version). Principen tillämpar Multi-Factor Authentication på privilegierade Azure AD-konton. Följande Azure AD-roller omfattas av baseline-principen för Azure AD:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör för villkorlig åtkomst
* Säkerhetsadministratör

## <a name="elevate-privilege-temporarily"></a>Privilegie rad höjning tillfälligt

För de flesta dagliga aktiviteter behöver inte alla användare globala administratörs rättigheter, och alla användare behöver inte vara permanent tilldelade rollen som global administratör. När användarna behöver behörighet till en global administratör bör de aktivera roll tilldelningen i Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md) antingen på ett eget konto eller ett alternativt administrativt konto.

## <a name="next-steps"></a>Nästa steg

En referens till roll beskrivningarna för Azure AD finns i [tilldela administratörs roller i Azure AD](permissions-reference.md)