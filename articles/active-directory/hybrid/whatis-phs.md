---
title: Vad är synkronisering av lösenordshash med Azure AD? | Microsoft Docs
description: Beskriver synkronisering av lösenordshash.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23f88a783ca257090fc607cc9a16ec457c7e9c8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90016596"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Vad är synkronisering av lösenordshash med Azure AD?
Synkronisering av lösenordshash är en av inloggningsmetoderna som används för att utföra hybrid-identitet. Azure AD Connect synkroniserar en hash-hash för en användares lösen ord från en lokal Active Directory instans till en molnbaserad Azure AD-instans.

Lösenordssynkronisering är ett tillägg till funktionen katalog synkronisering som implementeras av Azure AD Connect Sync. Du kan använda den här funktionen för att logga in på Azure AD-tjänster som Microsoft 365. Du loggar in till tjänsten genom att använda samma lösenord som du använder för att logga in på din lokala Active Directory-instans.

![Vad är Azure AD Connect?](./media/how-to-connect-password-hash-synchronization/arch1.png)

Synkronisering av lösenordshash hjälper till att minska antalet lösenord. Dina användare behöver bara underhålla ett. Synkronisering av lösenordshash kan:

* Förbättra användarnas produktivitet.
* Minska supportkostnaderna.  

Hash-synkronisering av lösen ord möjliggör även [läcka autentiseringsuppgifter](../identity-protection/concept-identity-protection-risks.md#user-risk) för ditt hybrid konto. Microsoft arbetar tillsammans med mörka webb forskare och juridiska myndigheter för att hitta offentligt tillgängliga användar namn/lösen ord. Om något av dessa par matchar användarnas, flyttas det associerade kontot till hög risk. 

>[!NOTE]
> Det går bara att bearbeta nya läckta autentiseringsuppgifter när du har aktiverat PHS för din klient. Det går inte att verifiera mot tidigare Funna autentiseringsuppgifter.


Om du bestämmer dig för att använda federation med [Federation with Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) kan du även konfigurera synkronisering av hashlösenord som en reservåtgärd.

Om du vill använda synkronisering av lösenordshash i din miljö måste du:

* Installera Azure AD Connect.  
* Konfigurera katalogsynkronisering mellan din lokala Active Directory-instans och din Azure Active Directory-instans.
* Aktivera hashsynkronisering för lösenord.



Mer information finns i [Vad är hybrididentitet?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Nästa steg

- [Vad är hybrididentitet?](whatis-hybrid-identity.md)
- [Vad är Azure AD Connect och Connect Health?](whatis-azure-ad-connect.md)
- [Vad är direktautentisering (PTA)?](how-to-connect-pta.md)
- [Vad är federation?](whatis-fed.md)
- [Vad är enkel inloggning?](how-to-connect-sso.md)
- [Så här fungerar hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md)
