---
title: Identitetsdatalagring för kunder i Australien och Nya Zeeland – Azure AD
description: Lär dig mer Azure Active Directory lagrar identitetsrelaterade data för kunder i Australien och Nya Zeeland.
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
ms.openlocfilehash: 4bb095e93a3728835e26cbe283f79569c91b7487
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479074"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitetsdatalagring för kunder i Australien och Nya Zeeland i Azure Active Directory

Identitetsdata lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst som Microsoft 365 och Azure. Om du vill ha information om var dina identitetskunddata lagras kan du använda avsnittet Var finns dina [data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft Trust Center.

> [!NOTE]
> Tjänster och program som integreras med Azure AD har åtkomst till Identity Customer Data. Utvärdera varje tjänst och program som du använder för att avgöra hur Identity Customer Data bearbetas av den specifika tjänsten och programmet och om de uppfyller företagets krav på datalagring. Läs mer om Microsoft-tjänsternas datahemvist i avsnittet Var finns dina data? på Microsoft Trust Center.

För kunder som har angett en adress i Australien eller Nya Zeeland behåller Azure AD identitetsdata för dessa tjänster i australiensiska datacenter: 
- Azure AD-kataloghantering 
- Autentisering

Alla andra Azure AD-tjänster lagrar kunddata i globala datacenter. Information om hur du hittar datacentret för en [Azure Active Directory finns i Azure Active Directory – Var finns dina data?](https://aka.ms/AADDataMap)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD multifaktorautentisering (MFA)

MFA lagrar identitetskunddata i globala datacenter. Mer information om användarinformation som samlas in och lagras av molnbaserad Azure AD MFA och Azure MFA Server finns i [Azure Multi-Factor Authentication-användardatasamling.](../authentication/concept-mfa-data-residency.md)

## <a name="next-steps"></a>Nästa steg
Mer information om de funktioner som beskrivs ovan finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md)
