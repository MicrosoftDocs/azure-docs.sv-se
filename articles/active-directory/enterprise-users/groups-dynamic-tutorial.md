---
title: Lägga till användare i en dynamisk grupp – självstudie – Azure AD | Microsoft Docs
description: I den här självstudiekursen använder du grupper med användarmedlemskapsregler för att lägga till eller ta bort användare automatiskt
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc891a7b07305fc0ef55376d7b6fbb262d740bb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547499"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Självstudier: Lägga till eller ta bort gruppmedlemmar automatiskt

I Azure Active Directory (Azure AD) kan du automatiskt lägga till eller ta bort användare i säkerhets grupper eller Microsoft 365 grupper, så du behöver inte alltid göra det manuellt. När alla egenskaper för en användare eller enhet ändras, utvärderar Azure AD alla dynamiska grupp regler i din Azure AD-organisation för att se om ändringen ska lägga till eller ta bort medlemmar.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en automatiskt ifylld grupp av gäst användare från ett partner företag
> * Tilldela licenser till gruppen för partnerspecifika funktioner för gästanvändare för åtkomst
> * Bonus: skydda gruppen **Alla användare** genom att ta bort gästanvändare så att du, till exempel, kan ge medlemmarna användaråtkomst till webbplatser som är endast interna

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Den här funktionen kräver en Azure AD Premium licens för dig som organisationens globala administratör. Om du inte har någon, i Azure AD, väljer du **licenser**  >  **produkter**  >  **försök/köp**.

Du behöver inte tilldela licenser till användarna för att de ska bli medlemmar i dynamiska grupper. Du behöver bara det minsta antalet tillgängliga Azure AD Premium P1-licenser i organisationen för att omfatta alla sådana användare. 

## <a name="create-a-group-of-guest-users"></a>Skapa en grupp med gästanvändare

Först skapar du en grupp för dina gästanvändare som alla finns i samma partnerföretag. De behöver en speciallicens, så det är ofta effektivare att skapa en grupp för det här syftet.

1. Logga in på Azure Portal ( https://portal.azure.com) med ett konto som är den globala administratören för din organisation.
2. Välj **Azure Active Directory**  >  **grupper**  >  **ny grupp**.
   ![Välj kommando för att starta en ny grupp](./media/groups-dynamic-tutorial/new-group.png)
3. På bladet **Grupp**:
  
   * Välj **säkerhet** som grupptyp.
   * Ange `Guest users Contoso` som namn och beskrivning för gruppen.
   * Ändra **Medlemstyp** till **Dynamisk användare**.
   
4. Välj **ägare** och Sök efter önskade ägare i bladet **Lägg till ägare** . Klicka på önskade ägare för att lägga till i valet.
5. Stäng bladet **Lägg till ägare** genom att klicka på **Välj** .  
6. Välj **Redigera dynamisk fråga** i rutan **dynamiska användar medlemmar** .
7. Gör följande på bladet **Regler för dynamiskt medlemskap**:

   * I **egenskaps** fältet, klickar du på det befintliga värdet och väljer **userType**. 
   * Kontrol lera att **operator** fältet har **lika med** markerat.  
   * Välj fältet **värde** och ange **gäst**. 
   * Klicka på hyperlänken **Lägg till uttryck** för att lägga till en annan rad.
   * I fältet **och/eller** väljer du **och**.
   * I fältet **egenskap** väljer du **företags namn**.
   * Kontrol lera att **operator** fältet har **lika med** markerat.
   * I fältet **värde** anger du **contoso**.
   * Klicka på **Spara** för att stänga bladet **dynamiska medlemskaps regler** .
   
8. På bladet **Grupp** väljer du **Skapa** för att skapa gruppen.

## <a name="assign-licenses"></a>Tilldela licenser

Nu när du har skapat din nya grupp kan du tillämpa licenserna som dessa partneranvändare behöver.

1. I Azure AD väljer du **Licenser**. Välj en eller flera licenser och välj sedan **Tilldela**.
2. Välj **Användare och grupper** och välj gruppen **Gästanvändare Contoso**, och spara dina ändringar.
3. Med **Tilldelningsalternativ** kan du aktivera eller inaktivera tjänstabonnemangen som innehåller de licenser du har valt. När du gör en ändring klickar du på **OK** för att spara dina ändringar.
4. Slutför tilldelningen genom att i fönstret **Tilldela licens** klicka på **Tilldela** längst ned i fönstret.

## <a name="remove-guests-from-all-users-group"></a>Ta bort gäster från gruppen Alla användare

Kanske är din slutliga plan att tilldela alla dina gästanvändare till egna grupper efter företag. Nu kan du ändra gruppen **alla användare** så att den endast är reserverad för medlemmar i din organisation. Du kan sedan använda den till att tilldela appar och licenser som är specifika för din hemorganisation.

   ![Andra gruppen Alla användare till endast medlemmar](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Rensa resurser

**Ta bort gästanvändargruppen**

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är den globala administratören för din organisation.
2. Välj **Azure Active Directory**  >  **grupper**. Välj gruppen **Gästanvändare Contoso**, välj ellipsen (...) och välj sedan **Ta bort**. När du tar bort gruppen tas alla tilldelade licenser bort.

**Återställa gruppen Alla användare**
1. Välj **Azure Active Directory**  >  **grupper**. Välj namnet på gruppen **Alla användare** för att öppna gruppen.
1. Välj **Regler för dynamiskt medlemskap**, ta bort all text i regeln och välj **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> * Skapa en grupp med gästanvändare
> * Tilldela licenser till den nya gruppen
> * Andra gruppen Alla användare till endast medlemmar

Gå vidare till nästa artikel om du vill veta mer om grunderna i gruppbaserade licenser
> [!div class="nextstepaction"]
> [Grundläggande om grupplicenser](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



