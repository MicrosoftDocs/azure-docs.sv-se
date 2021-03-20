---
title: 'Azure AD Connect: nästa steg och hur du hanterar Azure AD Connect | Microsoft Docs'
description: Lär dig hur du utökar standard konfiguration och drifts aktiviteter för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d6689b4b67b8462e983ae9b111e0fbc60c422b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89657123"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Nästa steg och hur du hanterar Azure AD Connect
Använd de operativa procedurerna i den här artikeln för att anpassa Azure Active Directory (Azure AD) Connect för att uppfylla organisationens behov och krav.  

## <a name="add-additional-sync-admins"></a>Lägg till ytterligare Sync-administratörer
Som standard kan endast den användare som gjorde installationen och lokala administratörer hantera den installerade Synkroniseringsmotorn. För att fler personer ska kunna komma åt och hantera Synkroniseringsmotorn, letar du upp gruppen med namnet ADSyncAdmins på den lokala servern och lägger till dem i den här gruppen.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Tilldela licenser till Azure AD Premium och Enterprise Mobility Suite-användare
Nu när användarna har synkroniserats till molnet måste du tilldela dem en licens så att de kan komma igång med molnappar som Microsoft 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Tilldela en licens för Azure AD Premium eller Enterprise Mobility Suite

1. Logga in på Azure Portal som administratör.
2. Välj **Active Directory** till vänster.
3. På sidan **Active Directory** dubbelklickar du på katalogen som innehåller de användare som du vill konfigurera.
4. Överst på katalogsidan väljer du **Licenser**.
5. På sidan **licenser** väljer du **Active Directory Premium** -eller **Enterprise Mobility Suite** och klickar sedan på **tilldela**.
6. I dialogrutan väljer du de användare som du vill tilldela licenser till och klickar sedan på bockmarkeringen för att spara ändringarna.

## <a name="verify-the-scheduled-synchronization-task"></a>Verifiera aktiviteten för schemalagd synkronisering
Använd Azure Portal för att kontrol lera status för en synkronisering.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Så här verifierar du aktiviteten för schemalagd synkronisering
1. Logga in på Azure Portal som administratör.
2. Välj **Active Directory** till vänster.
3. Välj **Azure AD Connect** till vänster
4. Lägg märke till den senaste synkroniseringen längst upp på sidan.

![Katalogens synkroniserings tid](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Starta en schemalagd synkronisering-aktivitet
Om du behöver köra en synkroniseringsuppgift kan du göra det genom att:

1. Starta guiden genom att dubbelklicka på genvägen Azure AD Connect skriv bord.
2. Klicka på **Konfigurera**.
3. På skärmen uppgifter väljer du alternativet **Anpassa synkronisering** och klickar på **Nästa**
4. Ange dina autentiseringsuppgifter för Azure AD
5. Klicka på **Nästa**. Klicka på **Nästa**.  Klicka på **Nästa**.
5.  På skärmen **redo att konfigurera** kontrollerar du att rutan **starta synkroniseringen När konfigurationen är klar** är markerad.
6.  Klicka på **Konfigurera**.

Mer information om Azure AD Connect Sync Scheduler finns [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Ytterligare uppgifter är tillgängliga i Azure AD Connect
Efter den första installationen av Azure AD Connect kan du alltid starta guiden igen från Azure AD Connect start sida eller Skriv bords gen väg.  Om du går igenom guiden igen visas några nya alternativ i form av ytterligare uppgifter.  

Följande tabell innehåller en sammanfattning av dessa uppgifter och en kort beskrivning av varje aktivitet.

![Lista över ytterligare aktiviteter](./media/how-to-connect-post-installation/addtasks2.png)

| Ytterligare uppgift | Beskrivning |
| --- | --- |
|**Sekretessinställningar**|Visa vilka telemetridata som delas med Microsoft.|
|**Visa aktuell konfiguration**|Visa din aktuella Azure AD Connect-lösning.  Detta inkluderar allmänna inställningar, synkroniserade kataloger och synkroniseringsinställningar. |
| **Anpassa synkroniseringsalternativ** |Ändra den aktuella konfigurationen, t. ex. att lägga till ytterligare Active Directory-skogar i konfigurationen eller aktivera synkroniseringsproblem som användare, grupp, enhet eller tillbakaskrivning av lösen ord. |
|**Konfigurera enhets alternativ**|Enhets alternativ som är tillgängliga för synkronisering|
|**Uppdatera katalog schema**|Gör att du kan lägga till nya lokala katalog objekt för synkronisering|
|**Konfigurera mellanlagrings läge** |Stegvis information som inte synkroniseras omedelbart och som inte exporteras till Azure AD eller lokalt Active Directory.  Med den här funktionen kan du förhandsgranska synkroniseringarna innan de inträffar. |
|**Ändra användar inloggning**|Ändra den autentiseringsmetod som användare använder för att logga in|
|**Hantera Federation**|Hantera din AD FS-infrastruktur, förnya certifikat och Lägg till AD FS-servrar|
|**Felsöka**|Hjälp med fel sökning av Azure AD Connect problem|

## <a name="next-steps"></a>Nästa steg
Läs mer om hur [du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
