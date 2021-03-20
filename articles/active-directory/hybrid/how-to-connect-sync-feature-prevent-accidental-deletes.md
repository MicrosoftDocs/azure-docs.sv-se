---
title: 'Azure AD Connect synkronisering: förhindra oavsiktliga borttagningar | Microsoft Docs'
description: I det här avsnittet beskrivs funktionen förhindra oavsiktliga borttagningar (förhindra oavsiktliga borttagningar) i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16d48cda87b8226ebc3bbab179c1034abf0a486f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084617"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect-synkronisering: Förhindra oavsiktliga borttagningar
I det här avsnittet beskrivs funktionen förhindra oavsiktliga borttagningar (förhindra oavsiktliga borttagningar) i Azure AD Connect.

När du installerar Azure AD Connect förhindrar du att oavsiktliga borttagningar är aktiverat som standard och har kon figurer ATS för att inte tillåta export med fler än 500 rader. Den här funktionen är avsedd att skydda dig från oavsiktliga konfigurationsändringar och ändringar i din lokala katalog som kan påverka många användare och andra objekt.

## <a name="what-is-prevent-accidental-deletes"></a>Vad är förhindra oavsiktliga borttagningar
Vanliga scenarier när du ser många borttagningar inkluderar:

* Ändringar av [filtreringen](how-to-connect-sync-configure-filtering.md) där en hel [organisationsenhet](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) eller [domän](how-to-connect-sync-configure-filtering.md#domain-based-filtering) är omarkerad.
* Alla objekt i en organisationsenhet tas bort.
* En organisationsenhet ges nytt namn så att alla objekt i den anses hamna utanför synkroniseringens omfång.

Standardvärdet för 500-objekt kan ändras med PowerShell med `Enable-ADSyncExportDeletionThreshold` , som är en del av den AD Sync modulen som installeras med Azure Active Directory Connect. Du bör konfigurera det här värdet så att det passar din organisations storlek. Eftersom synkroniseringsschemat körs var 30: e minut, är värdet antalet borttagningar som visas inom 30 minuter.

Om för många borttagningar är mellanlagrade för att exporteras till Azure AD, stoppas exporten och du får ett e-postmeddelande som detta:

![Förhindra oavsiktliga borttagningar av e-post](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hej (teknisk kontakt). Vid (tid) upptäckte Identitetssynkronisering tjänsten att antalet borttagningar översteg det konfigurerade tröskelvärdet för borttagning av (organisations namn). Ett totalt antal (tal)-objekt har skickats för borttagning i den här Identitetssynkronisering körningen. Detta är uppfyllt eller översteg det konfigurerade tröskelvärdet för borttagning av (antal) objekt. Du måste ange en bekräftelse på att dessa borttagningar ska bearbetas innan vi fortsätter. Mer information om felet som anges i det här e-postmeddelandet finns i förhindra oavsiktliga borttagningar.*
>
> 

Du kan också se statusen `stopped-deletion-threshold-exceeded` när du tittar på **Synchronization Service Manager** användar gränssnittet för export profilen.
![Förhindra oavsiktliga borttagningar av Sync Service Manager UI](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Om detta var oväntat undersöker du och vidtar lämpliga åtgärder. Gör så här om du vill se vilka objekt som ska tas bort:

1. Starta **synkroniseringstjänsten** från Start-menyn.
2. Gå till **kopplingar**.
3. Välj kopplingen med typen **Azure Active Directory**.
4. Välj **search Connector Space** under **åtgärder** till höger.
5. I popup-fönstret under **omfång** väljer du **frånkopplat sedan** och väljer en tid i det förflutna. Klicka på **Sök**. Den här sidan innehåller en vy över alla objekt som ska tas bort. Genom att klicka på varje objekt kan du få mer information om objektet. Du kan också klicka på **kolumn inställning** för att lägga till ytterligare attribut som ska visas i rutnätet.

![Sök efter anslutnings utrymme](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Om du inte är säker på att alla borttagningar önskas och vill gå en säkrare väg. Du kan använda PowerShell-cmdleten: `Enable-ADSyncExportDeletionThreshold` om du vill ange ett nytt tröskelvärde snarare än att inaktivera tröskelvärdet som kan tillåta oönskade borttagningar. 

## <a name="if-all-deletes-are-desired"></a>Om alla borttagningar önskas
Om alla borttagningar önskas gör du följande:

1. Kör PowerShell-cmdleten för att hämta det aktuella tröskelvärdet för borttagning `Get-ADSyncExportDeletionThreshold` . Ange ett globalt administratörs konto och lösen ord för Azure AD. Standardvärdet är 500.
2. Om du vill inaktivera det här skyddet tillfälligt kan du köra PowerShell-cmdleten genom att köra PowerShell-cmdleten: `Disable-ADSyncExportDeletionThreshold` . Ange ett globalt administratörs konto och lösen ord för Azure AD.
   ![Skärm bild som visar en dialog ruta för att ange användar namn och lösen ord för global administratör i Azure AD.](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. När Azure Active Directory anslutningen fortfarande är markerad väljer du åtgärden **Kör** och väljer **Exportera**.
4. Kör PowerShell-cmdleten för att återaktivera skyddet: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500` . Ersätt 500 med det värde som du noterade när du hämtade det aktuella tröskelvärdet för borttagning. Ange ett globalt administratörs konto och lösen ord för Azure AD.

## <a name="next-steps"></a>Nästa steg
**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
