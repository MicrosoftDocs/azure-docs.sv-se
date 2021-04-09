---
title: Avinstallera Azure AD Connect
description: I det här dokumentet beskrivs hur du avinstallerar Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96934216"
---
# <a name="uninstall-azure-ad-connect"></a>Avinstallera Azure AD Connect

I det här dokumentet beskrivs hur du avinstallerar Azure AD Connect korrekt.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Avinstallera Azure AD Connect från servern
Det första du behöver göra är att ta bort Azure AD Connect från servern som den körs på.  Gör så här:

 1. På den server som kör Azure AD Connect går du till **kontroll panelen**.
 2. Klicka på **Avinstallera ett program** 
  ![ Avinstallera ett program](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Välj **Azure AD Connect**.
 ![Välj Azure AD Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. När du uppmanas till det klickar du på **Ja** för att bekräfta.
 5. Den här bekräftelsen visas Azure AD Connect skärmen.  Klicka på **Ta bort**.
 ![Ta bort](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. När den här åtgärden har slutförts klickar du på **Avsluta**.
 7. ![Avsluta](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. Tillbaka i **kontroll panelen** klickar du på **Uppdatera** och alla komponenter bör ha tagits bort.


## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
- [Installera Azure AD Connect med en befintlig ADSync-databas](how-to-connect-install-existing-database.md)
- [Installerar Azure AD Connect med SQL-delegerade administratörsbehörigheter](how-to-connect-install-sql-delegation.md)

