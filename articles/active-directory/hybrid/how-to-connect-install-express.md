---
title: 'Azure AD Connect: Komma igång med standardinställningarna | Microsoft Docs'
description: Lär dig hur du laddar ned, installerar och kör installationsguiden för Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a655f355bb77d937f4daff2f8987769416ebd8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279677"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Komma igång med Azure AD Connect med standardinställningar
**Expressinställningar** för Azure AD Connect används om du har en topologi med en enda skog och om du använder [synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md) för autentisering. **Standardinställningar** är standardalternativet och används i de vanligaste distributionsscenarierna. Du är bara några få klick bort från att utöka din lokala katalog till molnet.

Innan du börjar installera Azure AD Connect [laddar du ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) och kontrollerar att du uppfyller kraven i [Azure AD Connect: Maskinvara och krav](how-to-connect-install-prerequisites.md).

Om standardinställningarna inte passar din topologi läser du den [relaterade dokumentationen](#related-documentation) för andra scenarier.

## <a name="express-installation-of-azure-ad-connect"></a>Snabbinstallation av Azure AD Connect
Du kan se dessa steg i praktiken i avsnittet med [videoklipp](#videos).

1. Logga in som lokal administratör på den server som du vill installera Azure AD Connect på. Gör detta på den server som ska vara synkroniseringsservern.
2. Navigera till och dubbelklicka på **AzureADConnect.msi**.
3. På välkomstskärmen markerar du kryssrutan för att acceptera licensvillkoren och klickar sedan på **Fortsätt**.  
4. På skärmen Standardinställningar klickar du på **Använd standardinställningar**.  
   ![Välkommen till Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. På sidan Anslut till Azure AD anger du användarnamnet och lösenordet för en global administratör för din Azure AD. Klicka på **Nästa**.  
   ![Anslut till Azure AD](./media/how-to-connect-install-express/connectaad.png)  
   Om du får ett fel och har problem med anslutningen läser du [Felsöka anslutningsproblem](tshoot-connect-connectivity.md).
6. På skärmen Anslut till AD DS anger du användarnamnet och lösenordet för ett företagsadministratörskonto. Du kan ange domändelen i NetBios- eller FQDN-format, d.v.s. FABRIKAM\administrator eller fabrikam.com\administrator. Klicka på **Nästa**.  
   ![Anslut till AD DS](./media/how-to-connect-install-express/connectad.png)
7. Sidan [**Konfigurera Azure AD-inloggning**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) visas bara om du inte slutförde [domänverifieringen](../fundamentals/add-custom-domain.md) under [kravfasen](how-to-connect-install-prerequisites.md).
   ![Overifierade domäner](./media/how-to-connect-install-express/unverifieddomain.png)  
   Om den här sidan visas granskar du alla domäner som har markerats med **Inte tillagd** och **Inte verifierad**. Kontrollera att de domäner som du använder har verifierats i Azure AD. Klicka på symbolen Uppdatera när du har verifierat dina domäner.
8. Klicka på **Installera** på skärmen Klart att konfigurera.
   * Du kan också välja att avmarkera kryssrutan **Starta synkroniseringen så fort konfigurationen är klar** på sidan Klart att konfigurera. Du måste avmarkera kryssrutan om du vill göra ytterligare konfigurationsinställningar, t.ex. [filtrering](how-to-connect-sync-configure-filtering.md). Om du avmarkerar det här alternativet konfigurerar guiden synkronisering men lämnar schemaläggaren inaktiverad. Den körs inte förrän du aktiverar den manuellt genom att [köra installationsguiden igen](how-to-connect-installation-wizard.md).
   * Om du låter kryssrutan **Starta synkroniseringsprocessen så snart som konfigurationen är klar** vara markerad utlöses omedelbart en fullständig synkronisering mot Microsoft Azure Active Directory för alla användare, grupper och kontakter.
   * Om du har Exchange i din lokala Active Directory kan du också välja att aktivera [**Exchange-hybridinstallation**](/exchange/exchange-hybrid). Aktivera det här alternativet om du planerar att ha Exchange-postlådor både i molnet och lokalt samtidigt.
     ![Redo att konfigurera Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. När installationen är klar klickar du på **Avsluta**.
10. När installationen har slutförts kan du logga ut och logga in igen innan du använder Synchronization Service Manager eller Synchronization Rule Editor.

## <a name="videos"></a>Video
Här finns ett videoklipp om snabbinstallationer:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>Nästa steg
Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](how-to-connect-post-installation.md).

Lär dig mer om dessa funktioner, som aktiverades med installationen: [Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md), [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](how-to-connect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](how-to-connect-sync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

## <a name="related-documentation"></a>Relaterad dokumentation

| Avsnitt | Länk |
| --- | --- |
| Översikt över Azure AD Connect | [Integrerar dina lokala kataloger med Azure Active Directory](whatis-hybrid-identity.md)
| Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md) |
| Uppgradera från DirSync | [Uppgradera från Azure AD-synkroniseringsverktyget (DirSync)](how-to-dirsync-upgrade-get-started.md)|
| Konton som används för installation | [Mer information om Azure AD Connect-autentiseringsuppgifter och -behörigheter](reference-connect-accounts-permissions.md) |