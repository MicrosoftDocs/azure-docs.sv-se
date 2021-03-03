---
title: 'Hybrididentitet: Jämförelse av katalogintegreringsverktyg | Microsoft Docs'
description: Den här sidan innehåller en detaljerad tabell som jämför olika katalogintegreringsverktyg som kan användas för katalogintegrering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e5c7e5fa428d5a71ca1a7468bbaab2fc94078e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643853"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hybrididentitet: Jämförelse av katalogintegreringsverktyg
Katalogintegreringsverktygen har vuxit och utvecklats med åren.  


- [FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) och [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016) stöds fortfarande och aktiverar i första hand synkronisering mellan lokala system.   [FIM Windows Azure AD Connector](/previous-versions/mim/dn511001(v=ws.10)) stöds i både FIM och mim, men rekommenderas inte för nya distributioner – kunder med lokala källor som anteckningar eller SAP HCM bör använda MIM för att fylla i Active Directory Domain Services (AD DS) och sedan använda antingen Azure AD Connect synkronisering eller Azure AD Connect moln etablering för att synkronisera från AD DS till Azure AD.
- [Azure AD Connect Sync](how-to-connect-sync-whatis.md) införlivar de komponenter och funktioner som tidigare släpptes i DirSync och Azure AD Sync, för synkronisering mellan AD DS-skogar och Azure AD.  
- [Azure AD Connect moln etablering](../cloud-sync/what-is-cloud-sync.md) är en ny Microsoft-Agent för synkronisering från AD DS till Azure AD, vilket är användbart för scenarier som fusion och förvärv där det förvärvade företagets AD-skogar är isolerade från moder BOLAGets AD-skogar.

Om du vill veta mer om skillnaderna mellan Azure AD Connect synkronisering och Azure AD Connect moln etablering kan du läsa artikeln [Vad är Azure AD Connect moln etablering?](../cloud-sync/what-is-cloud-sync.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).