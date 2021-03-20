---
title: 'Azure AD Connect synkronisering: ändra AD DS-kontots lösen ord | Microsoft Docs'
description: I det här avsnittet beskrivs hur du uppdaterar Azure AD Connect efter att lösen ordet för AD DS-kontot har ändrats.
services: active-directory
keywords: AD DS-konto, Active Directory konto, lösen ord
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4077146292db1266d5dbc51cc577f952b2bff191
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85357519"
---
# <a name="changing-the-ad-ds-account-password"></a>Ändra AD DS-kontolösenordet
AD DS-kontot refererar till det användar konto som används av Azure AD Connect för att kommunicera med lokala Active Directory. Om du ändrar lösen ordet för AD DS-kontot måste du uppdatera Azure AD Connect-synkroniseringstjänsten med det nya lösen ordet. Annars kan synkroniseringen inte längre synkroniseras korrekt med den lokala Active Directory och du kommer att stöta på följande fel:

* I Synchronization Service Manager Miss lyckas eventuella import-eller export åtgärder med lokal AD med fel meddelandet **inga-start-autentiseringsuppgifter** .

* Under Windows Loggboken innehåller program händelse loggen ett fel med **händelse-ID 6000** och meddelandet " **hanterings agentens" contoso.com "kunde inte köras eftersom autentiseringsuppgifterna var ogiltiga**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Så här uppdaterar du synkroniseringstjänsten med nytt lösen ord för AD DS-konto
Så här uppdaterar du synkroniseringstjänsten med det nya lösen ordet:

1. Starta Synchronization Service Manager (START → synkroniseringstjänst).
</br>![Synkronisera Service Manager](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Gå till fliken **anslutningar** .

3. Välj den **AD-anslutning** som motsvarar det AD DS-konto som lösen ordet har ändrats för.

4. Under **åtgärder** väljer du **Egenskaper**.

5. I popup-dialogrutan väljer **du Anslut till Active Directory skog**:

6. Ange det nya lösen ordet för AD DS-kontot i text rutan **lösen ord** .

7. Klicka på **OK** för att spara det nya lösen ordet och stänga popup-dialogrutan.

8. Starta om Azure AD Connect-synkroniseringstjänsten under Windows Service Control Manager. Detta görs för att säkerställa att alla referenser till det gamla lösen ordet tas bort från cacheminnet.

## <a name="next-steps"></a>Nästa steg
**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
