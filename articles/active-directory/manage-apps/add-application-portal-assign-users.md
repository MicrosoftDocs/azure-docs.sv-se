---
title: 'Snabbstart: Tilldela användare till en app som använder Azure Active Directory som identitetsprovider'
description: Den här snabbstarten går igenom processen för att tillåta användare att använda en app som du har ställt in för att använda Azure AD som identitetsprovider.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: iangithinji
ms.openlocfilehash: eb6797fdfb57a503b6dece9f2dfdc3cb0c67feef
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375346"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Snabbstart: Tilldela användare till en app som använder Azure AD som identitetsprovider

I den föregående snabbstarten konfigurerade du egenskaperna för en app. När du anger egenskaperna konfigurerade du upplevelsen för både tilldelade och otilldelade användare. Den här snabbstarten går igenom processen för att tilldela användare till appen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill tilldela användare till en app som du har lagt till i din Azure AD-klientorganisation behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En av följande roller: Global administratör, Molnprogramadministratör, Programadministratör eller ägare av tjänstens huvudnamn.
- Valfritt: Slutförande [av Visa dina appar](view-applications-portal.md).
- Valfritt: Slutförande [av Lägg till en app](add-application-portal.md).
- Valfritt: Slutförande [av Konfigurera en app](add-application-portal-configure.md).

>[!IMPORTANT]
>Använd en icke-produktionsmiljö för att testa stegen i den här snabbstarten.

## <a name="assign-users-to-an-app"></a>Tilldela användare en app
1. I Azure AD-portalen väljer du **Företagsprogram.** Leta sedan upp och välj det program som du vill konfigurera.
2. I den vänstra navigeringsmenyn väljer du **Användare och grupper.**
   > [!NOTE]
   > Vissa av Microsoft 365 kräver användning av PowerShell. 
3. Välj knappen **Lägg till** användare.
4. I fönstret **Lägg till** tilldelning väljer du Användare **och grupper.**
5. Välj den användare eller grupp som du vill tilldela till programmet. Du kan också börja skriva namnet på användaren eller gruppen i sökrutan. Du kan välja flera användare och grupper, så visas dina val under **Valda objekt.**
    > [!IMPORTANT]
    > När du tilldelar en grupp till ett program har endast användare i gruppen åtkomst. Tilldelningen överlappar inte till kapslade grupper.

    > [!NOTE]
    > Gruppbaserad tilldelning kräver en Azure Active Directory Premium P1- eller P2-version. Gruppbaserad tilldelning stöds endast för säkerhetsgrupper. Kapslade gruppmedlemskap Microsoft 365 grupper stöds inte för närvarande. Fler licensieringskrav för de funktioner som beskrivs i den här artikeln finns [Azure Active Directory sidan med priser.](https://azure.microsoft.com/pricing/details/active-directory) 
6. När du är klar väljer du **Välj**.
   ![Tilldela en användare eller grupp till appen](./media/assign-user-or-group-access-portal/assign-users.png)
7. I fönstret **Användare och** grupper väljer du en eller flera användare  eller grupper i listan och väljer sedan knappen Välj längst ned i fönstret.
8. Om programmet stöder det kan du tilldela en roll till användaren eller gruppen. I fönstret **Lägg till** tilldelning väljer du **Välj roll.** I fönstret Välj **roll väljer** du sedan en roll som ska tillämpas på de valda användarna eller grupperna och väljer **sedan OK** längst ned i fönstret. 
    > [!NOTE]
    > Om programmet inte stöder rollval tilldelas standardåtkomstrollen. I det här fallet hanterar programmet den åtkomstnivå som användarna har.
9. I fönstret **Lägg till** tilldelning väljer **du knappen** Tilldela längst ned i fönstret.

Du kan ta bort tilldelning av användare eller grupper med samma procedur. Välj den användare eller grupp som du vill ta bort tilldelning av och välj sedan **Ta bort.** Vissa av Microsoft 365 och Office 365-appar kräver användning av PowerShell. 

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med snabbstarten kan du ta bort appen. På så sätt kan du hålla testklienten ren. Du kan ta bort appen i den senaste snabbstarten i den här serien. Se [Ta bort en app.](delete-application-portal.md)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du ställer in enkel inloggning för en app.
> [!div class="nextstepaction"]
> [Konfigurera SAML-baserad enkel inloggning](add-application-portal-setup-sso.md)

ELLER

> [!div class="nextstepaction"]
> [Konfigurera OIDC-baserad enkel inloggning](add-application-portal-setup-oidc-sso.md)
