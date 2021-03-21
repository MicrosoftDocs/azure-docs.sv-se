---
title: Data hämtning för Azure AD Connect Healths instruktioner | Microsoft Docs
description: Den här sidan beskriver hur du hämtar data från Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89463658"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Azure AD Connect Health instruktioner för data hämtning

I det här dokumentet beskrivs hur du använder Azure AD Connect för att hämta data från Azure AD Connect Health.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Hämta alla e-postadresser för användare som kon figurer ATS för hälso aviseringar.

Använd följande steg för att hämta e-postadresserna för alla användare som har kon figurer ATS i Azure AD Connect Health att ta emot aviseringar.

1.  Starta på bladet Azure Active Directory Connect hälsa och välj **Sync Services** i det vänstra navigerings fältet.
 ![Sync Services](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Klicka på panelen **aviseringar** .</br>
 ![Varning](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Klicka på **meddelande inställningar**.
 ![Avisering](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  På bladet **meddelande inställning** hittar du en lista över e-postadresser som har Aktiver ATS som mottagare för aviseringar om hälso tillstånd.
 ![E-postmeddelanden](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>Hämta konton som har flaggats med AD FS Felaktiga lösen ords försök

Använd följande steg för att hämta konton som har flaggats med AD FS Felaktiga lösen ords försök.

1.  Från bladet Azure Active Directory hälsa väljer du **synkroniseringsfel**.
 ![Synkroniseringsfel](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  Klicka på **Exportera** i bladet **synkroniseringsfel** . En lista med de inspelade synkroniseringsfel kommer att exporteras.
 ![Exporteraera](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installation av Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](how-to-connect-health-operations.md)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
* [Azure AD Connect Health versions historik](reference-connect-health-version-history.md)