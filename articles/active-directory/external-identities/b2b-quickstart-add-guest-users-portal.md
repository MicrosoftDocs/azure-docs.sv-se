---
title: 'Snabb start: Lägg till gäst användare i Azure Portal – Azure AD'
description: Använd den här snabbstarten för att lära dig hur Azure AD-administratörer kan lägga till B2B-gästanvändare i Azure-portalen och gå igenom arbetsflödet för B2B-inbjudan.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f134a11b6d0a561a8b53156c927906afe2874a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87906142"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Snabbstart: Lägga till gästanvändare till din katalog i Azure-portalen

Du kan bjuda in vem som helst till att samarbeta med din organisation genom att lägga till dem i din katalog som gästanvändare. Du kan antingen skicka en e-postinbjudan med en länk för inlösen eller så kan du skicka en direktlänk till en app som du vill dela. Gästanvändarna kan logga med sina egna arbets- eller skolidentiteter eller sociala identiteter. Förutom den här snabb starten kan du lära dig mer om att lägga till gäst användare [i Azure Portal](add-users-administrator.md), via [PowerShell](b2b-quickstart-invite-powershell.md)eller [i bulk](tutorial-bulk-invite.md).

I den här snabb starten ska du lägga till en ny gäst användare i Azure AD-katalogen via Azure Portal, skicka en inbjudan och se vad gäst användarens återställnings process ser ut.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra scenariot i den här självstudien behöver du:

 - En roll som gör att du kan skapa användare i din klientkatalog, som rollen Global administratör eller någon av de begränsade katalogadministratörsrollerna.
 - Ett giltigt e-postkonto som du kan lägga till i din klientkatalog och som du kan använda för att ta emot e-postmeddelandet med testinbjudan.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Lägga till en ny gästanvändare i Azure AD

1. Logga in till [Azure-portalen](https://portal.azure.com/) som Azure AD-administratör.
2. Välj **Azure Active Directory** i den vänstra rutan.
3.  Under **Hantera** väljer du **Användare**.

    ![Skärm bild som visar var du väljer alternativet användare](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Välj **Ny gästanvändare**.

    ![Skärm bild som visar var du väljer alternativet ny gäst användare](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. På sidan **ny användare** väljer du **Bjud in användare** och lägger sedan till gäst användarens information. 

   - **Namn.** Det första och sista namnet på gäst användaren.
   - **E-postadress (krävs)**. E-postadressen till gäst användaren.
   - **Personligt meddelande (valfritt)** Ta med ett personligt välkomst meddelande till gäst användaren.
   - **Grupper**: du kan lägga till gäst användaren i en eller flera befintliga grupper, eller så kan du göra det senare.
   - **Katalog roll**: om du kräver administratörs behörighet för Azure AD för användaren kan du lägga till dem i en Azure AD-roll. 

6. Välj **Bjud in** för att skicka inbjudan till gästanvändaren automatiskt. Ett meddelande visas längst upp till höger med meddelandet **Användaren har bjudits in**. 
7.  När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.

## <a name="assign-an-app-to-the-guest-user"></a>Tilldela en app till gästanvändaren
Lägg till Salesforce-appen till din testklient och tilldela testgästanvändaren till appen.
1.  Logga in till Azure-portalen som Azure AD-administratör.
2.  I den vänstra rutan väljer du **Företagsprogram**.
3.  Välj **Nytt program**.
4. Under **Lägg till från galleriet** söker du efter **Salesforce** och väljer sedan det.

    ![Skärm bild som visar kryss rutan Lägg till från galleriet](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Välj **Lägg till**.
6. Under **Hantera** väljer du **Enkel inloggning** och under **Läge för enkel inloggning** väljer du **Lösenordsbaserad inloggning** och klickar på **Spara**.
7. Under **Hantera** väljer du **Användare och grupper** > **Lägg till användare** > **Användare och grupper**.
8. Använd sökrutan för att söka efter testanvändaren (vid behov) och välj testanvändaren i listan. Klicka sedan på **Välj**.
9. Välj **Tilldela**. 

## <a name="accept-the-invitation"></a>Acceptera inbjudan
Logga nu in som gästanvändaren för att visa inbjudan.
1.  Logga in på testgästanvändarens e-postkonto.
2.  Du hittar e-postmeddelandet ”Du har fått en inbjudan” i inkorgen.

    ![Skärm bild som visar e-postinbjudan om B2B](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  I e-postmeddelandets brödtext väljer du **Komma igång**. Sidan **Granska behörigheter** öppnas i webbläsaren. 

    ![Skärm bild som visar sidan gransknings behörigheter](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Välj **Acceptera**. Åtkomstpanelen öppnas och här visas de program som gästanvändaren kan komma åt.

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort testgästanvändaren och testappen när de inte längre behövs.
1.  Logga in till Azure-portalen som Azure AD-administratör.
2.  Välj **Azure Active Directory** i den vänstra rutan.
3.  Under **Hantera** väljer du **Företagsprogram**.
4.  Öppna programmet **Salesforce** och välj sedan **Ta bort**.
5.  Välj **Azure Active Directory** i den vänstra rutan.
6.  Under **Hantera** väljer du **Användare**.
7.  Välj testanvändaren och välj sedan **Ta bort användare**.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en gästanvändare i Azure-portalen och skickat en inbjudan till att dela appar. Sedan fick du se inlösenprocessen från gästanvändarens perspektiv och verifierade att appen visades på gästanvändarens åtkomstpanel. Läs mer om att lägga till gästanvändare för samarbete i [Lägga till B2B-samarbetsanvändare för Azure Active Directory i Azure Portal](add-users-administrator.md).
