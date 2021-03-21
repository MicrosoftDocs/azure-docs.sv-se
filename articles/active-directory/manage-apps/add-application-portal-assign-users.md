---
title: 'Snabb start: tilldela användare till en app som använder Azure Active Directory som identitets leverantör'
description: Den här snabb starten vägleder dig genom processen att tillåta användare att använda en app som du har konfigurerat för att använda Azure AD som en identitets leverantör.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 53dd2d15565149c3a9888ba063a6194ae033d8e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258379"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Snabb start: tilldela användare till en app som använder Azure AD som identitets leverantör

I den tidigare snabb starten konfigurerade du egenskaperna för en app. När du ställer in de egenskaper som du konfigurerade för både tilldelade och otilldelade användare. Den här snabb starten vägleder dig genom processen att tilldela användare till appen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill tilldela användare till en app som du har lagt till i din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- Valfritt: Slutför [visningen av Visa dina appar](view-applications-portal.md).
- Valfritt: slut för ande av [Lägg till en app](add-application-portal.md).
- Valfritt: Slutför [konfiguration av en app](add-application-portal-configure.md).

>[!IMPORTANT]
>Använd en icke-produktions miljö för att testa stegen i den här snabb starten.

## <a name="assign-users-to-an-app"></a>Tilldela användare en app
1. I Azure AD-portalen väljer du **företags program**. Hitta och välj sedan det program som du vill konfigurera.
2. I den vänstra navigerings menyn väljer **du användare och grupper**.
   > [!NOTE]
   > Vissa av de Microsoft 365 apparna kräver användning av PowerShell. 
3. Välj knappen **Lägg till användare** .
4. I fönstret **Lägg till tilldelning** väljer **du användare och grupper**.
5. Välj den användare eller grupp som du vill tilldela till programmet. Du kan också börja skriva in namnet på användaren eller gruppen i sökrutan. Du kan välja flera användare och grupper och dina val visas under **valda objekt**.
    > [!IMPORTANT]
    > När du tilldelar en grupp till ett program får endast användare i gruppen åtkomst. Tilldelningen överlappar inte kapslade grupper.

    > [!NOTE]
    > Gruppbaserad tilldelning kräver Azure Active Directory Premium P1 eller P2 Edition. Gruppbaserad tilldelning stöds endast för säkerhets grupper. Kapslade grupp medlemskap och Microsoft 365 grupper stöds inte för närvarande. Mer licensierings krav för de funktioner som beskrivs i den här artikeln finns på [sidan Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory). 
6. När du är färdig väljer du **Välj**.
   ![Tilldela en användare eller grupp till appen](./media/assign-user-or-group-access-portal/assign-users.png)
7. I fönstret **användare och grupper** väljer du en eller flera användare eller grupper i listan och väljer sedan knappen **Välj** längst ned i fönstret.
8. Om programmet stöder det kan du tilldela en roll till användaren eller gruppen. I fönstret **Lägg till tilldelning** väljer du **Välj roll**. Välj sedan en roll som ska gälla för de valda användarna eller grupperna i fönstret **Välj roll** och välj sedan **OK** längst ned i fönstret. 
    > [!NOTE]
    > Om programmet inte har stöd för roll val tilldelas standard åtkomst rollen. I det här fallet hanterar programmet åtkomst nivån för användare.
9. I fönstret **Lägg till tilldelning** väljer du knappen **tilldela** längst ned i fönstret.

Du kan ta bort tilldelningen av användare eller grupper med samma procedur. Välj den användare eller grupp som du vill ta bort tilldelningen för och välj sedan **ta bort**. Några av de Microsoft 365-och Office 365-apparna kräver att du använder PowerShell. 

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med snabb starten bör du ta bort appen. På så sätt kan du hålla din test-klient ren. Borttagning av appen beskrivs i den senaste snabb starten i den här serien. se [ta bort en app](delete-application-portal.md).

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel och lär dig hur du konfigurerar enkel inloggning för en app.
> [!div class="nextstepaction"]
> [Konfigurera SAML-baserad enkel inloggning](add-application-portal-setup-sso.md)

ELLER

> [!div class="nextstepaction"]
> [Konfigurera OIDC-baserad enkel inloggning](add-application-portal-setup-oidc-sso.md)
