---
title: Identitets data lagring för australiska och nya Zeeland-kunder – Azure AD
description: Lär dig mer om var Azure Active Directory lagrar identitets relaterade data för de australiska och nya Zeeland-kunderna.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 498b75a6541da97e57e23465ae6eb23a6c854727
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836962"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitets data lagring för australiska och nya Zeeland-kunder i Azure Active Directory

Identitets data lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst, till exempel Microsoft 365 och Azure. Information om var dina identitets kunddata lagras, kan du använda avsnittet var finns [dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft säkerhets Center.

> [!NOTE]
> Tjänster och program som integreras med Azure AD har åtkomst till identitets kund information. Utvärdera varje tjänst och program som du använder för att avgöra hur identitetens kund data bearbetas av den specifika tjänsten och programmet, och om de uppfyller företagets krav på data lagring. Läs mer om Microsoft-tjänsternas datahemvist i avsnittet Var finns dina data? på Microsoft Trust Center.

För kunder som tillhandahöll en adress i Australien eller nya Zeeland behåller Azure AD identitets data för dessa tjänster i de australiska data centren: 
- Azure AD-katalog hantering 
- Autentisering

Alla andra Azure AD-tjänster lagrar kund information i globala data Center. Information om hur du hittar data centret för en tjänst finns i [Azure Active Directory – var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD Multi-Factor Authentication (MFA)

MFA lagrar identitets kund information i globala data Center. Mer information om den användar information som samlas in och lagras av molnbaserad Azure AD MFA-och Azure MFA-Server finns i [azure Multi-Factor Authentication User Data Collection](../authentication/concept-mfa-data-residency.md).

## <a name="next-steps"></a>Nästa steg
Mer information om de funktioner och funktioner som beskrivs ovan finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md)