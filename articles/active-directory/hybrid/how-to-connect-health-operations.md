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
ms.openlocfilehash: 836c7bf9aefd4b2cb7d52c66bbd37e7ba38a467c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377318"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health åtgärder
I det här avsnittet beskrivs de olika åtgärder som du kan utföra med hjälp Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Aktivera e-postaviseringar
Du kan konfigurera tjänsten Azure AD Connect Health skicka e-postaviseringar när aviseringar visar att din identitetsinfrastruktur inte är felfri. Detta inträffar när en avisering genereras och när den har lösts.

![Skärmbild av Azure AD Connect Health inställningar för e-postaviseringar](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-postmeddelanden är aktiverade som standard.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Så här aktiverar du Azure AD Connect Health-postaviseringar
1. I Azure-portalen söker du efter Azure AD Connect Health
2. Välj **Synkroniseringsfel**
3. Välj **Meddelandeinställningar.**
5. Välj **PÅ** i e-postaviseringsväxeln.
6. Markera kryssrutan om du vill att alla globala administratörer ska ta emot e-postaviseringar.
7. Om du vill ta emot e-postaviseringar på andra e-postadresser anger du dem i **rutan Ytterligare e-postmottagare.** Om du vill ta bort en e-postadress från den här listan högerklickar du på posten och väljer Ta **bort.**
8. Slutför ändringarna genom att klicka på **Spara**. Ändringarna gäller först när du har sparat.

>[!NOTE] 
> När det uppstår problem med att bearbeta synkroniseringsbegäranden i vår backend-tjänst skickar den här tjänsten ett e-postmeddelande med information om felet till den administrativa kontaktens e-postadresser för din klientorganisation. Vi har hört feedback från kunder om att volymen av dessa meddelanden i vissa fall är oöverkomligt stor, så vi ändrar hur vi skickar dessa meddelanden. 
>
> I stället för att skicka ett meddelande för varje synkroniseringsfel varje gång det inträffar skickar vi en daglig sammanfattning av alla fel som backend-tjänsten har returnerat. Detta gör det möjligt för kunder att bearbeta dessa fel på ett mer effektivt sätt och minska antalet duplicerade felmeddelanden.

## <a name="delete-a-server-or-service-instance"></a>Ta bort en server eller tjänstinstans

>[!NOTE] 
> Azure AD Premium-licens krävs för borttagningsstegen.

I vissa fall kanske du vill ta bort en server från att övervakas. Det här behöver du veta för att ta bort en server från Azure AD Connect Health tjänsten.

Tänk på följande när du tar bort en server:

* Den här åtgärden slutar samla in ytterligare data från den servern. Den här servern tas bort från övervakningstjänsten. Efter den här åtgärden kan du inte visa nya aviseringar, övervakning eller användningsanalysdata för den här servern.
* Den här åtgärden avinstallerar inte hälsoagenten från servern. Om du inte har avinstallerat hälsoagenten innan du utför det här steget kan du se fel som rör hälsoagenten på servern.
* Den här åtgärden tar inte bort data som redan har samlats in från den här servern. Dessa data tas bort i enlighet med Azures datalagringsprincip.
* Om du vill börja övervaka samma server igen när du har utfört den här åtgärden måste du avinstallera och installera om hälsoagenten på den här servern.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Ta bort en server från Azure AD Connect Health tjänsten

>[!NOTE] 
> Azure AD Premium-licens krävs för borttagningsstegen.

Azure AD Connect Health för Active Directory Federation Services (AD FS) (AD FS) och Azure AD Connect (Sync):

1. Öppna **bladet Server** från **bladet Serverlista** genom att välja det servernamn som ska tas bort.
2. Klicka på **Ta** bort i åtgärdsfältet på bladet **Server.**
![Skärmbild av Azure AD Connect Health ta bort server](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Bekräfta genom att skriva servernamnet i bekräftelserutan.
4. Klicka på **Ta bort**.

Azure AD Connect Health för Azure Active Directory Domain Services:

1. Öppna **instrumentpanelen Domänkontrollanter.**
2. Välj den domänkontrollant som ska tas bort.
3. I åtgärdsfältet klickar du på Ta **bort valda**.
4. Bekräfta åtgärden för att ta bort servern.
5. Klicka på **Ta bort**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Ta bort en tjänstinstans från Azure AD Connect Health tjänst
I vissa fall kanske du vill ta bort en tjänstinstans. Det här behöver du veta för att ta bort en tjänstinstans från Azure AD Connect Health tjänsten.

Tänk på följande när du tar bort en tjänstinstans:

* Den här åtgärden tar bort den aktuella tjänstinstansen från övervakningstjänsten.
* Den här åtgärden avinstallerar eller tar inte bort hälsoagenten från någon av de servrar som övervakades som en del av den här tjänstinstansen. Om du inte har avinstallerat hälsoagenten innan du utför det här steget kan du se fel som rör hälsoagenten på servrarna.
* Alla data från den här tjänstinstansen tas bort i enlighet med Azures datalagringsprincip.
* När du har utfört den här åtgärden kan du avinstallera och installera om hälsoagenten på alla servrar om du vill börja övervaka tjänsten. Om du vill börja övervaka samma server igen när du har utfört den här åtgärden avinstallerar du, installerar om och registrerar hälsoagenten på den servern.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Ta bort en tjänstinstans från Azure AD Connect Health tjänsten
1. Öppna **bladet Tjänst** från **bladet Tjänstlista** genom att välja tjänstidentifieraren (servergruppens namn) som du vill ta bort. 
2. Klicka på **Ta** bort i åtgärdsfältet på **bladet Tjänst.** 
![Skärmbild av Azure AD Connect Health ta bort tjänst](./media/how-to-connect-health-operations/DeleteServer.png)
3. Bekräfta genom att skriva namnet på tjänsten i bekräftelserutan (till exempel: sts.contoso.com).
4. Klicka på **Ta bort**.
   <br><br>

[//]: # (Start av RBAC-avsnitt)
## <a name="manage-access-with-azure-rbac"></a>Hantera åtkomst med Azure RBAC
[Rollbaserad åtkomstkontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) för Azure AD Connect Health ger åtkomst till andra användare och grupper än globala administratörer. Azure RBAC tilldelar roller till de avsedda användarna och grupperna och tillhandahåller en mekanism för att begränsa de globala administratörerna i din katalog.

### <a name="roles"></a>Roller
Azure AD Connect Health har stöd för följande inbyggda roller:

| Roll | Behörigheter |
| --- | --- |
| Ägare |Ägare kan *hantera* åtkomst (till exempel tilldela en roll till en användare eller grupp), visa *all information* (till exempel visa aviseringar) från portalen och ändra inställningar *(till* exempel e-postaviseringar) i Azure AD Connect Health. <br>Som standard tilldelas globala Azure AD-administratörer den här rollen och detta kan inte ändras. |
| Deltagare |Deltagare kan visa *all information* (till exempel visa aviseringar) från portalen och ändra inställningar *(till* exempel e-postaviseringar) inom Azure AD Connect Health. |
| Läsare |Läsare kan *visa all information* (till exempel visa aviseringar) från portalen i Azure AD Connect Health. |

Alla andra roller (till exempel administratörer för användaråtkomst eller DevTest Labs-användare) påverkar inte åtkomsten inom Azure AD Connect Health, även om rollerna är tillgängliga i portalupplevelsen.

### <a name="access-scope"></a>Åtkomstomfång
Azure AD Connect Health har stöd för åtkomsthantering på två nivåer:

* **Alla tjänstinstanser:** Det här är den rekommenderade sökvägen i de flesta fall. Den styr åtkomsten för alla tjänstinstanser (till exempel en AD FS servergrupp) över alla rolltyper som övervakas av Azure AD Connect Health.
* **Tjänstinstans:** I vissa fall kan du behöva åtsega åtkomst baserat på rolltyper eller av en tjänstinstans. I det här fallet kan du hantera åtkomst på tjänstinstansnivå.  

Behörighet beviljas om en slutanvändare har åtkomst på katalog- eller tjänstinstansnivå.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Ge användare eller grupper åtkomst till Azure AD Connect Health
Följande steg visar hur du tillåter åtkomst.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Steg 1: Välj lämpligt åtkomstomfång
Om du vill tillåta användaråtkomst på *alla tjänstinstanser* inom Azure AD Connect Health öppnar du huvudbladet i Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Steg 2: Lägg till användare och grupper och tilldela roller
1. I avsnittet **Konfigurera** klickar du på **Användare.**<br>
   ![Skärmbild av Azure AD Connect Health resurssida](./media/how-to-connect-health-operations/startRBAC.png)
2. Välj **Lägg till**.
3. I fönstret **Välj en roll** väljer du en roll (till exempel **Ägare).**<br>
   ![Skärmbild av Azure AD Connect Health konfigurera Azure RBAC](./media/how-to-connect-health-operations/RBAC_add.png)
4. Ange namnet eller identifieraren för den aktuella användaren eller gruppen. Du kan välja en eller flera användare eller grupper samtidigt. Klicka på **Välj**.
   ![Skärmbild av Azure AD Connect Health och Azure-rolllista](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Välj **OK**.<br>
6. När rolltilldelningen är klar visas användare och grupper i listan.<br>
   ![Skärmbild av Azure AD Connect Health, Azure RBAC och nya användare markerade](./media/how-to-connect-health-operations/RBAC_user_list.png)

Nu har de listade användarna och grupperna åtkomst enligt deras tilldelade roller.

> [!NOTE]
> * Globala administratörer har alltid fullständig åtkomst till alla åtgärder, men globala administratörskonton finns inte i föregående lista.
> * Funktionen Bjud in användare stöds inte i Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Steg 3: Dela bladplatsen med användare eller grupper
1. När du har tilldelar behörigheter kan en användare komma Azure AD Connect Health genom att gå [hit](https://aka.ms/aadconnecthealth).
2. På bladet kan användaren fästa bladet, eller olika delar av det, på instrumentpanelen. Klicka bara på **ikonen Fäst på instrumentpanelen.**<br>
   ![Skärmbild av Azure AD Connect Health pin-bladet för Azure RBAC med fästikonen markerad](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> En användare med rollen Läsare kan inte hämta Azure AD Connect Health från Azure Marketplace. Användaren kan inte utföra den nödvändiga "create"-åtgärden för att göra det. Användaren kan fortfarande komma till bladet genom att gå till länken ovan. För efterföljande användning kan användaren fästa bladet på instrumentpanelen.
>
>

### <a name="remove-users-or-groups"></a>Ta bort användare eller grupper
Du kan ta bort en användare eller en grupp som lagts till i Azure AD Connect Health och Azure RBAC. Högerklicka bara på användaren eller gruppen och välj Ta **bort.**<br>
![Skärmbild av Azure AD Connect Health Azure RBAC med Ta bort markerat](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Slutet på RBAC-avsnittet)

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health agentinstallation](how-to-connect-health-agent-install.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](reference-connect-health-version-history.md)
