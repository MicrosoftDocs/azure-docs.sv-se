---
title: Lägg till en användare som en labb skapare i Azure Lab Services
description: Den här artikeln visar hur du lägger till en användare i labb skaparen-rollen för ett labb konto i Azure Lab Services. Labb skapare kan skapa labb i det här labb kontot.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a6c89483744f943926f126701d28988358627a27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96435968"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Lägga till labb skapare i ett labb konto i Azure Lab Services
Den här artikeln visar hur du lägger till användare som labbs kapare i ett labb konto i Azure Lab Services. Dessa används sedan för att skapa labb i labb kontot. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Lägg till Microsoft-användarkonto till labb skapare rollen
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste användaren vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa labbkontot läggs automatiskt till i den här rollen. Om du planerar att använda samma användarkonto för att skapa ett klassrumslabb kan du hoppa över det här steget. Om du vill använda ett annat konto för att skapa ett klassrumslabb gör du följande: 

Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen **Labbskapare**:

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** och klickar på **+ Lägg till rolltilldelning** i verktygsfältet. 

    ![Åtkomstkontroll -> Lägg till rolltilldelning, knapp](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. På sidan **Lägg till rolltilldelning** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägg till labbuppgiftsförfattare](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Om du lägger till en icke-Microsoft-konto användare som en labb skapare, se avsnittet [Lägg till en icke-Microsoft-konto användare som en labb skapare](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Lägg till en icke-Microsoft-konto användare som en labb skapare
Om du vill lägga till en användare som en labb skapare, använder du deras e-postkonton. Följande typer av e-postkonton kan användas:

- Ett e-postkonto som tillhandahålls av ditt universitets Azure Active Directory (AAD).
- Ett Microsoft-e-postkonto, till exempel,, `@outlook.com` `@hotmail.com` `@msn.com` eller `@live.com` .
- Ett e-postkonto som inte kommer från Microsoft, till exempel ett som tillhandahålls av Yahoo eller Google. Dessa typer av konton måste dock länkas till ett Microsoft-konto.
- Ett GitHub-konto. Kontot måste vara länkat till ett Microsoft-konto.

### <a name="using-a-non-microsoft-email-account"></a>Använda ett e-postkonto som inte kommer från Microsoft
Labb skapare/lärare kan använda e-postkonton som inte kommer från Microsoft för att registrera sig och logga in i ett klass rums labb.  Inloggningen till labb tjänst portalen kräver dock att instruktörerna först skapar en Microsoft-konto som är länkad till den e-postadress som inte kommer från Microsoft.

Många instruktörer kanske redan har ett Microsoft-konto kopplat till sina e-postadresser som inte kommer från Microsoft. Lärare har till exempel redan en Microsoft-konto om de har använt sin e-postadress med Microsofts andra produkter eller tjänster, t. ex. Office, Skype, OneDrive eller Windows.  

När lärare loggar in på labb tjänst portalen uppmanas de att ange sina e-postadresser och lösen ord. Om instruktören försöker logga in med en icke-Microsoft-konto som inte har något Microsoft-konto länkat, får instruktören följande fel meddelande: 

![Felmeddelande](./media/how-to-configure-student-usage/cant-find-account.png)

Om du vill registrera dig för en Microsoft-konto bör instruktörer gå till [http://signup.live.com](http://signup.live.com) .  


### <a name="using-a-github-account"></a>Använda ett GitHub-konto
Lärare kan också använda ett befintligt GitHub-konto för att registrera sig och logga in i ett klass rums labb. Om instruktören redan har en Microsoft-konto länkat till sitt GitHub-konto kan de logga in och ange sitt lösen ord som visas i föregående avsnitt. Om de inte har länkat sitt GitHub-konto till en Microsoft-konto bör de välja **inloggnings alternativ**:

![Länk för inloggnings alternativ](./media/how-to-configure-student-usage/signin-options.png)

På sidan **inloggnings alternativ** väljer du logga in **med GitHub**.

![Logga in med GitHub-länk](./media/how-to-configure-student-usage/signin-github.png)

Slutligen uppmanas de att skapa en Microsoft-konto som är länkad till sitt GitHub-konto. Det sker automatiskt när instruktören väljer **Nästa**.  Instruktören är sedan omedelbart inloggad och ansluten till klass rummets labb.


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst labb](how-to-use-classroom-lab.md)
