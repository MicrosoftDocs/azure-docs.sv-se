---
title: Vad är HR driven-etablering med Azure Active Directory? | Microsoft Docs
description: Beskriver översikt över HR driven etablering.
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
ms.openlocfilehash: 1ffe8c1397525348e472e965a407909fee36152d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168456"
---
# <a name="what-is-hr-driven-provisioning"></a>Vad är HR driven etablering?

![HR-etablering](./media/what-is-hr-driven-provisioning/cloud2a.png)

HR driven etablering är en process för att skapa digitala identiteter baserade på ett personal system.  HR-systemen blir auktoritets start för dessa nyligen skapade digitala identiteter och är ofta start punkten för flera etablerings processer.  Om en ny medarbetare till exempel ansluter till företaget, skapas de i det mänskliga resurs systemet.  Skapandet, utlöser etableringen av ett användar konto till Active Directory och Azure AD Connect etablerar det här kontot till Azure AD osv.

HR driven etablering kan antingen vara lokalt baserad eller molnbaserad.

## <a name="on-premises-based-hr-provisioning"></a>Lokal baserad HR-etablering
Lokal baserad HR-etablering sker med hjälp av ett lokalt HR-system och ett sätt att tillhandahålla nya digitala identiteter.

HR-system levereras i flera olika paket, program varu paket och kan använda SQL-servrar, LDAP-kataloger osv.

För närvarande använder Microsofts lokala HR-etablerings lösningar Microsoft Identity Manager för att utlösa etablering när en ny identitet skapas i de här HR-systemen.

Med MIM kan du etablera användare från dina lokala HR-system för att Active Directory eller Azure AD.

Information om Microsoft Identity Manager och de system som stöds av finns i [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) -dokumentationen.

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Nästa steg 
- [Vad är hantering av identitets livs cykel](what-is-identity-lifecycle-management.md)
- [Vad är etablering?](what-is-provisioning.md)
- [Vad är appetablering?](what-is-app-provisioning.md)
- [Vad är etablering mellan kataloger?](what-is-inter-directory-provisioning.md)