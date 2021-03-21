---
title: Azure Active Directory Connect Health åtgärder
description: I den här artikeln beskrivs ytterligare åtgärder som kan utföras när du har distribuerat Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e41a00c1489d5b7b4822fec8bb4da35e917826
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686469"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health åtgärder
I det här avsnittet beskrivs de olika åtgärder som du kan utföra med hjälp av Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Aktivera e-postmeddelanden
Du kan konfigurera Azure AD Connect Health tjänsten att skicka e-postaviseringar när aviseringar visar att din identitets infrastruktur inte är felfri. Detta inträffar när en avisering genereras och när den har lösts.

![Skärm bild av Azure AD Connect Health inställningar för e-postaviseringar](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-postaviseringar är aktiverade som standard.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Så här aktiverar du Azure AD Connect Health e-postaviseringar
1. Öppna bladet **aviseringar** för den tjänst som du vill ta emot e-postaviseringar för.
2. I åtgärds fältet klickar du på **meddelande inställningar**.
3. I växeln för e-postavisering väljer du **på**.
4. Markera kryss rutan om du vill att alla globala administratörer ska kunna ta emot e-postaviseringar.
5. Om du vill ta emot e-postaviseringar på andra e-postadresser anger du dem i rutan **ytterligare e-postmottagare** . Om du vill ta bort en e-postadress från listan högerklickar du på posten och väljer **ta bort**.
6. Klicka på **Spara** om du vill slutföra ändringarna. Ändringarna börjar gälla först när du har sparat.

>[!NOTE] 
> När det finns problem med att bearbeta synkroniseringsproblem i vår server dels tjänst skickar den här tjänsten ett e-postmeddelande med information om felet till den administrativa kontaktens e-postadress (er) för din klient. Vi har hört feedback från kunder som i vissa fall är volymen av dessa meddelanden prohibitively stor så vi ändrar hur vi skickar dessa meddelanden. 
>
> I stället för att skicka ett meddelande för varje synkroniseringsfel varje gång kommer vi att skicka en daglig sammanfattning av alla fel som backend-tjänsten har returnerat. Detta gör det möjligt för kunderna att bearbeta felen på ett mer effektivt sätt och minska antalet dubbletter av fel meddelanden.

## <a name="delete-a-server-or-service-instance"></a>Ta bort en server eller tjänst instans

>[!NOTE] 
> Azure AD Premium-licens krävs för borttagnings stegen.

I vissa fall kanske du vill ta bort en server från att övervakas. Här är vad du behöver veta för att ta bort en server från Azure AD Connect Healths tjänsten.

Tänk på följande när du tar bort en server:

* Den här åtgärden avslutar insamling av ytterligare data från den servern. Den här servern tas bort från övervaknings tjänsten. Efter den här åtgärden kan du inte Visa nya aviseringar, övervakning eller användnings analys data för den här servern.
* Den här åtgärden avinstallerar inte hälso agenten från servern. Om du inte har avinstallerat hälso agenten innan du utför det här steget kan du se fel som rör hälso agenten på servern.
* Den här åtgärden tar inte bort de data som redan har samlats in från den här servern. Dessa data tas bort i enlighet med policyn för data bevarande i Azure.
* När du har genomfört den här åtgärden måste du avinstallera och installera om hälso agenten på den här servern om du vill börja övervaka samma server igen.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Ta bort en server från Azure AD Connect Healths tjänsten

>[!NOTE] 
> Azure AD Premium-licens krävs för borttagnings stegen.

Azure AD Connect Health för Active Directory Federation Services (AD FS) (AD FS) och Azure AD Connect (synkronisering):

1. Öppna bladet **Server** från bladet **server lista** genom att välja det Server namn som ska tas bort.
2. I åtgärds fältet på bladet **Server** klickar du på **ta bort**.
![Skärm bild av Azure AD Connect Health ta bort Server](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Bekräfta genom att skriva Server namnet i bekräftelse rutan.
4. Klicka på **Ta bort**.

Azure AD Connect Health för Azure Active Directory Domain Services:

1. Öppna instrument panelen **domänkontrollanter** .
2. Välj den domänkontrollant som ska tas bort.
3. I åtgärds fältet klickar du på **ta bort markerade**.
4. Bekräfta åtgärden för att ta bort servern.
5. Klicka på **Ta bort**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Ta bort en tjänst instans från Azure AD Connect Health-tjänsten
I vissa fall kanske du vill ta bort en tjänst instans. Här är vad du behöver veta för att ta bort en tjänst instans från Azure AD Connect Healths tjänsten.

Tänk på följande när du tar bort en tjänst instans:

* Den här åtgärden tar bort den aktuella tjänst instansen från övervaknings tjänsten.
* Den här åtgärden avinstallerar eller tar inte bort hälso agenten från någon av de servrar som övervakades som en del av den här tjänst instansen. Om du inte har avinstallerat hälso agenten innan du utför det här steget kan du se fel som rör hälso agenten på servrarna.
* Alla data från den här tjänst instansen tas bort i enlighet med policyn för data bevarande i Azure.
* När du har genomfört den här åtgärden måste du avinstallera och installera om hälso agenten på alla servrar om du vill börja övervaka tjänsten. När du har genomfört den här åtgärden måste du avinstallera, installera om och registrera hälso agenten på servern om du vill börja övervaka samma server igen.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Ta bort en tjänst instans från tjänsten Azure AD Connect Health
1. Öppna bladet **tjänst** från bladet **tjänst lista** genom att välja tjänst-ID (Server grupp namn) som du vill ta bort. 
2. Klicka på **ta bort** i åtgärds fältet på bladet **tjänst** . 
![Skärm bild av Azure AD Connect Health ta bort tjänst](./media/how-to-connect-health-operations/DeleteServer.png)
3. Bekräfta genom att skriva tjänst namnet i bekräftelse rutan (till exempel: sts.contoso.com).
4. Klicka på **Ta bort**.
   <br><br>

[//]: # (Avsnittet börjar med RBAC)
## <a name="manage-access-with-azure-rbac"></a>Hantera åtkomst med Azure RBAC
[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) för Azure AD Connect Health ger åtkomst till användare och grupper som inte är globala administratörer. Azure RBAC tilldelar roller till avsedda användare och grupper och tillhandahåller en mekanism för att begränsa globala administratörer i din katalog.

### <a name="roles"></a>Roller
Azure AD Connect Health stöder följande inbyggda roller:

| Roll | Behörigheter |
| --- | --- |
| Ägare |Ägare kan *Hantera åtkomst* (till exempel tilldela en roll till en användare eller grupp), *Visa all information* (till exempel Visa aviseringar) från portalen och *ändra inställningar* (till exempel e-postaviseringar) i Azure AD Connect Health. <br>Som standard tilldelas globala Azure AD-administratörer den här rollen och kan inte ändras. |
| Deltagare |Deltagare kan *Visa all information* (till exempel Visa aviseringar) från portalen och *ändra inställningar* (till exempel e-postmeddelanden) i Azure AD Connect Health. |
| Läsare |Läsarna kan *Visa all information* (till exempel Visa aviseringar) från portalen i Azure AD Connect Health. |

Alla andra roller (till exempel användarens åtkomst administratörer eller DevTest Labs användare) har ingen påverkan på åtkomst inom Azure AD Connect Health, även om rollerna är tillgängliga i Portal upplevelsen.

### <a name="access-scope"></a>Åtkomstscope
Azure AD Connect Health stöder hantering av åtkomst på två nivåer:

* **Alla tjänst instanser**: det här är den rekommenderade sökvägen i de flesta fall. Den styr åtkomsten för alla tjänst instanser (till exempel en AD FS server grupp) över alla roll typer som övervakas av Azure AD Connect Health.
* **Tjänst instans**: i vissa fall kan du behöva åtskilja åtkomst baserat på roll typer eller av en tjänst instans. I det här fallet kan du hantera åtkomst på tjänst instans nivå.  

Behörighet beviljas om en användare har åtkomst till antingen på katalog-eller tjänst instans nivå.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Tillåt användare eller grupper åtkomst till Azure AD Connect Health
Följande steg visar hur du tillåter åtkomst.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Steg 1: Välj lämpligt åtkomstscope
Om du vill ge en användare åtkomst på nivån *Alla tjänste instanser* i Azure AD Connect Health öppnar du huvud bladet i Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Steg 2: Lägg till användare och grupper och tilldela roller
1. I avsnittet **Konfigurera** klickar du på **användare**.<br>
   ![Skärm bild av Azure AD Connect Health resurs marginal List](./media/how-to-connect-health-operations/startRBAC.png)
2. Välj **Lägg till**.
3. I fönstret **Välj en roll** väljer du en roll (till exempel **ägare**).<br>
   ![Skärm bild av Azure AD Connect Health och Azure RBAC-konfigurations meny](./media/how-to-connect-health-operations/RBAC_add.png)
4. Ange namnet eller identifieraren för mål användaren eller gruppen. Du kan välja en eller flera användare eller grupper samtidigt. Klicka på **Välj**.
   ![Skärm bild av listan Azure AD Connect Health och Azure-roll](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Välj **OK**.<br>
6. När roll tilldelningen är klar visas användare och grupper i listan.<br>
   ![Skärm bild av Azure AD Connect Health och Azure RBAC och nya användare markerade](./media/how-to-connect-health-operations/RBAC_user_list.png)

Nu har listan över användare och grupper åtkomst, enligt de tilldelade rollerna.

> [!NOTE]
> * Globala administratörer har alltid fullständig åtkomst till alla åtgärder, men globala administratörs konton finns inte i föregående lista.
> * Funktionen Bjud in användare stöds inte i Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Steg 3: dela bladet med användare eller grupper
1. När du har tilldelat behörigheter kan en användare komma åt Azure AD Connect Health genom att gå [hit](https://aka.ms/aadconnecthealth).
2. På bladet kan användaren fästa bladet eller olika delar av det på instrument panelen. Klicka bara på ikonen **Fäst på instrument panelen** .<br>
   ![Skärm bild av bladet för Azure AD Connect Health och Azure RBAC-fästa med PIN-ikonen markerad](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> En användare med den tilldelade läsar rollen kan inte hämta Azure AD Connect Health-tillägget från Azure Marketplace. Användaren kan inte utföra den nödvändiga "skapa"-åtgärden för att göra det. Användaren kan fortfarande komma åt bladet genom att gå till föregående länk. För efterföljande användning kan användaren fästa bladet på instrument panelen.
>
>

### <a name="remove-users-or-groups"></a>Ta bort användare eller grupper
Du kan ta bort en användare eller en grupp som lagts till Azure AD Connect Health och Azure RBAC. Högerklicka bara på användaren eller gruppen och välj **ta bort**.<br>
![Skärm bild av Azure AD Connect Health och Azure RBAC med ta bort markerade](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Slutet av RBAC-avsnittet)

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Agent installation](how-to-connect-health-agent-install.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](reference-connect-health-version-history.md)
