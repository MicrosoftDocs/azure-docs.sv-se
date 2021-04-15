---
title: 'Snabbstart: Konfigurera egenskaper för ett program i din Azure Active Directory klientorganisation (Azure AD)'
description: Den här snabbstarten använder Azure Portal för att konfigurera ett program som har registrerats med din Azure Active Directory -klientorganisation (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: iangithinji
ms.openlocfilehash: 3b7a5d88aa40422dc46c6ca2c1681447cb030ea4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379527"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snabbstart: Konfigurera egenskaper för ett program i din Azure Active Directory klientorganisation (Azure AD)

I den föregående snabbstarten lade du till ett program till din Azure Active Directory (Azure AD)-klientorganisation. När du lägger till ett program låter du din Azure AD-klientorganisation veta att det är identitetsprovidern för appen. Nu ska du konfigurera några av egenskaperna för appen.
 
## <a name="prerequisites"></a>Förutsättningar

För att konfigurera egenskaperna för ett program i din Azure AD-klientorganisation behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En av följande roller: Global administratör, Molnprogramadministratör, Programadministratör eller ägare av tjänstens huvudnamn.
- Valfritt: Slutförande [av Visa dina appar](view-applications-portal.md).
- Valfritt: Slutförande [av Lägg till en app](add-application-portal.md).

>[!IMPORTANT]
>Använd en icke-produktionsmiljö för att testa stegen i den här snabbstarten.

## <a name="configure-app-properties"></a>Konfigurera appegenskaper

När du har lagt till ett program till din Azure AD-klientorganisation visas översiktssidan. Om du konfigurerar ett program som redan har lagts till kan du titta på den första snabbstarten. Den visar hur du visar de program som har lagts till i din klientorganisation. 

Så här redigerar du programegenskaperna:

1. I Azure AD-portalen väljer du **Företagsprogram.** Leta sedan upp och välj det program som du vill konfigurera.
2. I avsnittet **Hantera** väljer du **Egenskaper för** att öppna fönstret **Egenskaper** för redigering.
3. Ta en stund och se vilka alternativ som är tillgängliga. Vilka alternativ som är tillgängliga beror på hur appen är integrerad med Azure AD. En app som använder SAML-baserad enkel inloggning har till  exempel fält som URL för användaråtkomst, medan en app som använder OIDC-baserad enkel inloggning inte kommer att göra det. Observera också att appar som läggs **till via Azure Active Directory > Appregistreringar** är OIDC-baserade appar som standard. Appar som läggs **till via Azure Active Directory > Enterprise-program** kan använda någon av flera standarder för enkel inloggning. Alla appar har fält för att konfigurera när en app visas och kan användas. Dessa fält är:
    - **Har användare aktiverats för att logga in?** avgör om användare som är tilldelade till programmet kan logga in.
    - **Användartilldelning krävs?** avgör om användare som inte har tilldelats till programmet kan logga in.
    - **Är det synligt för användarna?** avgör om användare som är tilldelade till en app kan se [den Mina appar](https://myapps.microsoft.com) Microsoft 365 appstartaren. (Se våffelmenyn i det övre vänstra hörnet på en Microsoft 365 webbplats.)
    
    > [!TIP]
    > Tilldelning av användare sker i **avsnittet Användare och grupper** i navigeringen.

    De tre alternativen kan vara oberoende av varandra och det resulterande beteendet är inte alltid uppenbart. Här är en tabell som kan vara till hjälp:
    
    | Har användare aktiverats för att logga in? | Användartilldelning krävs? | Synlig för användare? | Beteende för användare som antingen har tilldelats till appen eller inte. |
    |---|---|---|---|
    | Ja | Ja | Ja | Tilldelade användare kan se appen och logga in.<br>Otilldelade användare kan inte se appen och kan inte logga in. |
    | Ja | Ja | Inga  | Tilldelade användningsområden kan inte se appen, men de kan logga in.<br>Otilldelade användare kan inte se appen och kan inte logga in. |
    | Ja | Inga  | Ja | Tilldelade användare kan se appen och logga in.<br>Otilldelade användare kan inte se appen men kan logga in. |
    | Ja | Inga  | Inga  | Tilldelade användare kan inte se appen men kan logga in.<br>Otilldelade användare kan inte se appen men kan logga in. |
    | Inga  | Ja | Ja | Tilldelade användare kan inte se appen och kan inte logga in.<br>Otilldelade användare kan inte se appen och kan inte logga in. |
    | Inga  | Ja | Inga  | Tilldelade användare kan inte se appen och kan inte logga in.<br>Otilldelade användare kan inte se appen och kan inte logga in. |
    | Inga  | Inga  | Ja | Tilldelade användare kan inte se appen och kan inte logga in.<br>Otilldelade användare kan inte se appen och kan inte logga in. |
    | Inga  | Inga  | Inga  | Tilldelade användare kan inte se appen och kan inte logga in.<br>Otilldelade användare kan inte se appen och kan inte logga in. |

4. När du är klar väljer du **Spara**.

## <a name="use-a-custom-logo"></a>Använda en anpassad logotyp

Så här använder du en anpassad logotyp:

1. Skapa en logotyp som är 215 x 215 bildpunkter och spara den i PNG-format.
2. I Azure AD-portalen väljer du **Företagsprogram.** Leta sedan upp och välj det program som du vill konfigurera.
3. I avsnittet **Hantera** väljer du **Egenskaper för** att öppna **fönstret Egenskaper** för redigering. 
4. Välj ikonen för att ladda upp logotypen.
5. När du är klar väljer du **Spara**.

    ![Skärmbild av skärmen Egenskaper som visar hur du ändrar logotypen.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatyrbilden som visas **i det** här egenskapsfönstret uppdateras inte direkt. Du kan stänga och öppna fönstret **Egenskaper** igen om du vill se den uppdaterade ikonen.


> [!TIP]
> Du kan automatisera apphantering med hjälp av Graph API i [Automatisera apphantering med Microsoft Graph API.](/graph/application-saml-sso-configure-api)

## <a name="add-notes"></a>Lägg till anteckningar

Du kan använda anteckningsfältet för att lägga till information som är relevant för hantering av programmet i Azure AD. Anteckningar är ett fritextfält med en maximal storlek på 1 024 tecken.

1. I Azure AD-portalen väljer du **Företagsprogram**. Leta sedan reda på och välj det program som du vill konfigurera.
2. I avsnittet **Hantera** väljer du **Egenskaper för** att öppna fönstret **Egenskaper** för redigering.
3. Uppdatera fältet Anteckningar och välj **Spara.**

    ![Skärmbild av skärmen Egenskaper som visar hur du ändrar anteckningarna](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta med snabbstartsserien kan du ta bort appen för att rensa testklienten. Du kan ta bort appen i den senaste snabbstarten i den här serien. Se [Ta bort en app.](delete-application-portal.md)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du tilldelar användare till appen.
> [!div class="nextstepaction"]
> [Tilldela användare en app](add-application-portal-assign-users.md)