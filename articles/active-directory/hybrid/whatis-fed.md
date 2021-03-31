---
title: Vad är federation med Azure AD? | Microsoft Docs
description: Beskriver federation med Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb1c3bc9f89db3f4b694803a63293a5537d4b98b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278760"
---
# <a name="what-is-federation-with-azure-ad"></a>Vad är federation med Azure AD?

Federation är en samling av domäner som har upprättat förtroende. Förtroendenivån kan variera, men omfattar vanligtvis autentisering och nästan alltid auktorisering. En typisk federation kan innehålla ett antal organisationer som har upprättat förtroende för delad åtkomst till en uppsättning resurser.

Du kan federera din lokala miljö med Azure AD och använda den här federationstjänsten för autentisering och auktorisering.  Med en här inloggningsmetoden sker all användarautentisering lokalt.  Med den här metoden kan administratörer implementera mer rigorösa nivåer av åtkomstkontroll. Federation med AD FS och PingFederate är tillgänglig.

![Federerad identitet](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Om du bestämmer dig för att använda federation med Active Directory Federation Services (AD FS) kan du även konfigurera synkronisering av hashlösenord som en reservåtgärd för de fall då det inte går att nå AD FS-infrastrukturen.


## <a name="next-steps"></a>Nästa steg

- [Vad är hybrididentitet?](./whatis-hybrid-identity.md)
- [Vad är Azure AD Connect och Connect Health?](whatis-azure-ad-connect.md)
- [Vad är synkronisering av lösenordshash?](whatis-phs.md)
- [Vad är federation?](whatis-fed.md)
- [Vad är enkel inloggning?](how-to-connect-sso.md)
- [Så här fungerar federation](how-to-connect-fed-whatis.md)
- [Federation med PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)