---
title: Så här konfigurerar du tilldelning av självbetjäningsprogram | Microsoft Docs
description: Aktivera programåtkomst via självbetjäning så att användarna kan hitta sina egna program
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c3043cc38c8ab3d3387b171ea6f3793d485730
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373969"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Så här konfigurerar du tilldelning av självbetjäningsprogram

Innan användarna kan själva identifiera program från sina Mina appar  måste du aktivera programåtkomst via självbetjäning till alla program som du vill tillåta användare att själva identifiera och begära åtkomst till. Den här funktionen är tillgänglig för program som har lagts till från [Azure AD-galleriet,](./add-application-portal.md) [Azure AD Programproxy](./application-proxy.md) eller som har lagts till via [användar- eller administratörsmedgivande.](../develop/application-consent-experience.md) 

Den här funktionen är ett bra sätt för dig att spara tid och pengar som IT-grupp och rekommenderas starkt som en del av en modern programdistribution med Azure Active Directory.

Med den här funktionen kan du:

-   Låt användarna själva identifiera program från [Mina appar](https://myapps.microsoft.com/) utan att störa IT-gruppen.

-   Lägg till dessa användare i en förkonfigurerad grupp så att du kan se vem som har begärt åtkomst, ta bort åtkomst och hantera de roller som tilldelats dem.

-   Du kan också tillåta att en företags godkännare godkänner begäranden om programåtkomst så att IT-gruppen inte behöver göra det.

-   Du kan också konfigurera upp till 10 personer som kan godkänna åtkomst till det här programmet.

-   Du kan också tillåta att en företags godkännare anger de lösenord som användarna kan använda för att logga in på programmet, direkt från företags godkännaren [Mina appar](https://myapps.microsoft.com/).

-   Du kan också automatiskt tilldela självbetjäning tilldelade användare till en programroll direkt.

> [!NOTE]
> En Azure Active Directory Premium -licens (P1 eller P2) krävs för att användarna ska kunna begära att ansluta till en självbetjäningsapp och för att ägare ska kunna godkänna eller neka begäranden. Utan en Azure Active Directory Premium licens kan användarna inte lägga till självbetjäningsprogram.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera programåtkomst via självbetjäning så att användarna kan hitta sina egna program

Programåtkomst via självbetjäning är ett bra sätt att tillåta användare att själv identifiera program och eventuellt tillåta att affärsgruppen godkänner åtkomst till dessa program. För program med enkel inloggning med lösenord kan du även tillåta att affärsgruppen hanterar de autentiseringsuppgifter som tilldelats till dessa användare från sina egna Mina appar åtkomstpaneler.

Följ stegen nedan om du vill aktivera programåtkomst via självbetjäning för ett program:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.

2. Välj **Azure Active Directory**. I den vänstra navigeringsmenyn väljer du **Företagsprogram**.

3. Välj programmet i listan. Om du inte ser programmet börjar du skriva dess namn i sökrutan. Du kan också använda filterkontrollerna för att välja programtyp, status eller synlighet och sedan **välja Tillämpa.**

4. I den vänstra navigeringsmenyn väljer du **Självbetjäning.**

5. Om du vill aktivera programåtkomst via självbetjäning för det här programmet ändrar du Alternativet Tillåt användare att begära åtkomst till det här **programmet?** till **Ja.**

6. Bredvid Vilken **grupp ska tilldelade användare läggas till? klickar du** på Välj **grupp.** Välj en grupp och klicka sedan på **Välj**. När en användares begäran har godkänts läggs de till i den här gruppen. När du visar den här gruppens medlemskap kan du se vem som har beviljats åtkomst till programmet via självbetjäningsåtkomst.
  
    > [!NOTE]
    > Den här inställningen stöder inte grupper som synkroniseras lokalt.

7. **Valfritt:** Om du vill kräva affärsgodkännande innan användare tillåts åtkomst ställer du in **Kräv godkännande innan du** beviljar åtkomst till det här programmet? på **Ja.**

8. **Valfritt:** För program som endast använder enkel inloggning med lösenord, så att företagsgodkännare kan ange de lösenord som skickas till det här programmet för godkända användare, ställer du in Tillåt godkännare för att ställa in **användarens** lösenord för det här programmet på **Ja.**

9. **Valfritt:** Om du vill ange vilka företags godkännare som har behörighet att godkänna åtkomst till det här programmet, bredvid Vem har behörighet att godkänna åtkomst till det här **programmet?** klickar du på Välj godkännare och väljer sedan upp till 10 enskilda företags godkännare. Klicka sedan på **Välj**.

    >[!NOTE]
    >Grupper stöds inte. Du kan välja upp till 10 enskilda företags godkännare. Om du anger flera godkännare kan alla godkännare godkänna en åtkomstbegäran.

10. **Valfritt:** För program som exponerar roller , för att tilldela självbetjäning godkända användare till en roll, bredvid till vilken roll ska användare tilldelas i det här **programmet?** klickar du på **Välj** roll och väljer sedan den roll som dessa användare ska tilldelas. Klicka sedan på **Välj**.

11. Klicka på **knappen** Spara överst i fönstret för att slutföra.

När du har slutfört konfigurationen av självbetjäningsprogram kan användarna  navigera till sina [Mina appar](https://myapps.microsoft.com/) och klicka på knappen Lägg till självbetjäningsprogram för att hitta de appar som aktiveras med självbetjäning. Företags godkännare ser också ett meddelande i sina [Mina appar](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande som meddelar dem när en användare har begärt åtkomst till ett program som kräver deras godkännande.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](../enterprise-users/groups-self-service-management.md)