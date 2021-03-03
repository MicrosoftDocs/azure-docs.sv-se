---
title: Vad är etablering mellan kataloger med Azure Active Directory? | Microsoft Docs
description: Beskriver översikt över etablering av identiteter mellan kataloger.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4246bc4c62fd8e5e73ff18e383b8bf115d25ede8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643854"
---
# <a name="what-is-inter-directory-provisioning"></a>Vad är etablering mellan kataloger?

En katalog är en infrastruktur för delad information som används för att hitta, hantera, administrera och organisera objekt och nätverks resurser.  Exempel på program som använder katalog tjänster är Microsoft Active Directory och Azure AD.  Identiteter hjälper katalog systemen att bestämma, till exempel vem som har åtkomst till vad, och vem som har behörighet att använda vissa resurser.

Etablering mellan kataloger har etablering av en identitet mellan två olika katalog tjänst system.   Det vanligaste scenariot för etablering mellan kataloger är när en användare som redan finns i Active Directory har tillhandahållits till Azure AD. Den här etableringen kan utföras av agenter som Azure AD Connect synkronisering eller Azure AD Connect moln etablering.

Med etablering mellan kataloger kan vi skapa [hybrid identitets](../hybrid/whatis-hybrid-identity.md) miljöer.


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Vilka typer av etablering mellan kataloger gör Azure AD-supporten

Azure AD stöder för närvarande tre metoder för att utföra etablering mellan kataloger. Dessa metoder är:

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) – Microsoft-verktyget har utformats för att möta och slutföra din hybrid identitet, inklusive etablering mellan kataloger från Active Directory till Azure AD.

- [Azure AD Connect moln etablering](../cloud-sync/what-is-cloud-sync.md) – en ny Microsoft-Agent som utformats för att möta och uppnå dina hybrid identitets mål.  Det ger en låg vikt för etablering av mellan kataloger mellan Active Directory och Azure AD.

- [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) – Microsofts lokala identitets-och åtkomst hanterings lösning som hjälper dig att hantera användare, autentiseringsuppgifter, principer och åtkomst i din organisation. MIM tillhandahåller dessutom avancerad etablering mellan kataloger för att uppnå hybrid identitets miljöer för Active Directory, Azure AD och andra kataloger.

### <a name="key-benefits"></a>Viktiga fördelar

Den här funktionen för etablering mellan kataloger ger följande betydande affärs förmåner:

- [Synkronisering av lösenordshash](../hybrid/whatis-phs.md) – En inloggningsmetod som synkroniserar en hash av en användares lokala AD-lösenord med Azure AD.
- [Direktautentisering](../hybrid/how-to-connect-pta.md) – En inloggningsmetod som gör att användare kan använda samma lösenord lokalt och i molnet men inte kräver den ytterligare infrastrukturen hos en federerad miljö.
- [Federations integrering](../hybrid/how-to-connect-fed-whatis.md) – kan användas för att konfigurera en hybrid miljö med en lokal AD FS infrastruktur. Det har även AD FS-hanteringsfunktioner som certifikatsförnyelse och ytterligare AD FS-serverdistributioner.
- [Synkronisering](../hybrid/how-to-connect-sync-whatis.md) – Ansvarar för att skapa användare, grupper och andra objekt.  Den ser även till att identitetsinformationen för dina lokala användare och grupper matchar molnet.  Den här synkroniseringen omfattar även lösenordshasher.
- [Hälso övervakning](../hybrid/whatis-azure-ad-connect.md) – kan tillhandahålla robust övervakning och tillhandahålla en central plats i Azure Portal för att visa den här aktiviteten. 


## <a name="next-steps"></a>Nästa steg 
- [Vad är hantering av identitets livs cykel](what-is-identity-lifecycle-management.md)
- [Vad är etablering?](what-is-provisioning.md)
- [Vad är HR driven etablering?](what-is-hr-driven-provisioning.md)
- [Vad är appetablering?](what-is-app-provisioning.md)