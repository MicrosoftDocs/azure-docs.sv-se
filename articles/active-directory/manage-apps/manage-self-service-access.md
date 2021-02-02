---
title: Så här konfigurerar du självbetjäning för program tilldelning | Microsoft Docs
description: Aktivera självbetjäning för program åtkomst så att användarna kan hitta sina egna program
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9742a21cf00733607237c0eaf548f96b434abb33
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260222"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Så här konfigurerar du självbetjäning för program tilldelning

Innan användarna kan själv identifiera program från sina appar måste du aktivera självbetjänings **program åtkomst** till alla program som du vill ge användare möjlighet att själv identifiera och begära åtkomst till. Den här funktionen är tillgänglig för program som har lagts till från [Azure AD-galleriet](./add-application-portal.md), [Azure AD-programproxy](./application-proxy.md) eller lades till via [användare eller administrativa medgivande](../develop/application-consent-experience.md). 

Den här funktionen är ett bra sätt att spara tid och pengar som en IT-grupp och är starkt rekommenderat som en del av en modern program distribution med Azure Active Directory.

Med den här funktionen kan du:

-   Låt användare själv identifiera program från [Mina appar](https://myapps.microsoft.com/) utan att bry sig om IT-gruppen.

-   Lägg till dessa användare i en förkonfigurerad grupp så att du kan se vem som har begärt åtkomst, ta bort åtkomst och hantera de roller som har tilldelats dem.

-   Alternativt kan en företags god kännare godkänna begär Anden om program åtkomst så att IT-gruppen inte behöver.

-   Du kan också konfigurera upp till 10 personer som kan godkänna åtkomst till det här programmet.

-   Alternativt kan en företags god kännare ange de lösen ord som användarna kan använda för att logga in i programmet, direkt från affärs god kännaren för att få [Mina appar](https://myapps.microsoft.com/).

-   Du kan också automatiskt tilldela självbetjänings användare till en program roll direkt.

> [!NOTE]
> En Azure Active Directory Premium-eller P2-licens krävs för att användarna ska kunna ansluta till en självbetjänings app och för att ägare ska kunna godkänna eller neka begär Anden. Utan en Azure Active Directory Premium licens kan användarna inte lägga till självbetjänings appar.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera självbetjäning för program åtkomst så att användarna kan hitta sina egna program

Åtkomst till självbetjänings program är ett bra sätt att ge användare möjlighet att identifiera program och eventuellt tillåta affärs gruppen att godkänna åtkomst till dessa program. För lösen ord med enkel inloggning kan du också tillåta att affärs gruppen hanterar de autentiseringsuppgifter som tilldelats dessa användare från sina egna åtkomst paneler för Mina appar.

Följ stegen nedan om du vill aktivera självbetjänings program åtkomst till ett program:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.

2. Välj **Azure Active Directory**. I den vänstra navigerings menyn väljer du **företags program**.

3. Välj programmet i listan. Om du inte ser programmet börjar du skriva dess namn i sökrutan. Eller Använd filter kontrollerna för att välja program typ, status eller synlighet och välj sedan **Använd**.

4. I den vänstra navigerings menyn väljer du **självbetjäning**.

5. Aktivera självbetjäning för program åtkomst för det här programmet genom att aktivera alternativet **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

6. Klicka på **Välj grupp** bredvid **till vilken grupp användare ska läggas till?** Välj en grupp och klicka sedan på **Välj**. När en användares begäran godkänns läggs de till i den här gruppen. När du visar medlemskapet för den här gruppen kan du se vem som har beviljats åtkomst till programmet via självbetjänings åtkomst.
  
    > [!NOTE]
    > Den här inställningen stöder inte grupper som synkroniseras lokalt.

7. **Valfritt:** Om du vill kräva godkännande av företag innan användarna tillåts måste du ställa in **Kräv godkännande innan jag beviljar åtkomst till det här programmet?** växla till **Ja**.

8. **Valfritt: endast för program som använder enkel inloggning** för att tillåta affärs god kännare att ange lösen ord som skickas till det här programmet för godkända användare, ställer du in alternativet **Tillåt god kännare att ange användarens lösen ord för det här programmet?** växla till **Ja**.

9. **Valfritt:** Om du vill ange de affärs god kännare som tillåts att godkänna åtkomst till det här programmet, klickar du på **Välj god kännare** bredvid **vem som tillåts att godkänna åtkomst till programmet?** och väljer sedan upp till 10 enskilda affärs god kännare. Klicka sedan på **Välj**.

    >[!NOTE]
    >Grupper stöds inte. Du kan välja upp till 10 enskilda affärs god kännare. Om du anger flera god kännare kan en enskild god kännare godkänna en åtkomstbegäran.

10. **Valfritt:** **för program som visar roller**, för att tilldela självbetjänings godkända användare till en roll, klickar du på Välj roll bredvid **rollen till vilken du vill att användare ska tilldelas i det här programmet?**, klicka på **Välj roll** och välj sedan rollen som dessa användare ska tilldelas till. Klicka sedan på **Välj**.

11. Klicka på knappen **Spara** längst upp i fönstret för att avsluta.

När du har slutfört självbetjänings program konfigurationen kan användarna navigera till sina [appar](https://myapps.microsoft.com/) och klicka på knappen **Lägg till** självbetjänings program för att hitta de appar som är aktivera med självbetjänings åtkomst. Affärs god kännare ser också ett meddelande i sina [appar](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande som meddelar dem när en användare har begärt åtkomst till ett program som kräver godkännande.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../enterprise-users/groups-self-service-management.md)