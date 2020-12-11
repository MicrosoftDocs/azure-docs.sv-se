---
title: Logga in på Azure Notebooks för hands version
description: Logga in snabbt i Azure Notebooks för hands version och ange ett användar-ID, vilket ger dig möjlighet att komma åt sparade projekt och dela antecknings böcker med andra.
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: 168da5d4d36cd4cfadb4ef120ddfabbd261cafaf
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109461"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>Snabb start: Logga in och ange ett användar-ID för Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Även om du alltid kan visa Azure Notebooks utan att logga in, måste du logga in för att köra antecknings böcker, komma åt sparade projekt och antecknings böcker och dela dina antecknings böcker med andra.

## <a name="sign-in"></a>Logga in

1. Välj **Logga** in längst upp till höger i [Notebooks.Azure.com](https://notebooks.azure.com/).

    ![Plats för inloggnings kommandot på Azure Notebooks](media/accounts/sign-in-command.png)

1. När du uppmanas till det anger du e-postadressen till ett Microsoft-konto eller ett arbets-eller skol konto och väljer **Nästa**. Konto typer beskrivs i [ditt användar konto för Azure Notebooks](azure-notebooks-user-account.md). Om du inte har ett Microsoft-konto eller om du vill göra ett för användning särskilt med Azure Notebooks väljer du **skapa ett**:

    ![Skapa nytt Microsoft-konto kommando i inloggnings tolken](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > Om du försöker skapa ett nytt konto med en e-postadress som redan har ett konto som är associerat med det kan du se meddelandet "du kan inte registrera dig här med en e-postadress till arbetet eller skolan. Använd ett personligt e-postmeddelande, till exempel Gmail eller Yahoo!, eller skaffa ett nytt Outlook-e-postmeddelande. " I så fall kan du försöka logga in med e-postadressen för arbetet utan att skapa ett nytt konto.

1. När du uppmanas till det anger du ditt lösenord.

1. Om du loggar in för första gången uppmanas Azure Notebooks att ha behörighet att komma åt ditt konto. Välj **Ja** om du vill fortsätta:

    ![Prompt för konto behörigheter](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>Ange ett användar-ID

1. Vid första inloggningen har du tilldelat ett temporärt användar-ID som "Anon-idrca3". När du har ett användar-ID som börjar med "Anon-", kommer Azure Notebooks att be dig att skapa ett eget ID. Ditt användar-ID används i alla URL: er som du får för att dela dina projekt och antecknings böcker, så välj något som är unikt och meningsfullt för dig.

    ![Uppmana att ange ett användar-ID för Azure Notebooks](media/accounts/create-user-id.png)

    Om du väljer **Nej tack** fortsätter Azure Notebooks att uppmana dig att ange ett användar-ID varje gången du loggar in. Ditt användar-ID kan också ställas in när som helst i din [användar profil](azure-notebooks-user-profile.md).

1. När du har loggat in går Azure Notebooks till din offentliga profil sida där du kan välja **Redigera profil information** för att fylla i resten av din information (mer information finns i [din profil och ditt användar-ID](azure-notebooks-user-profile.md)):

    ![Inledande vy av en Azure Notebooks profil sida](media/accounts/profile-page-new.png)

> [!NOTE]
> Om du ser meddelandet "användar-ID används redan", prova med ett annat ID. Användar-ID: n är unika för alla Azure Notebooks konton och Azure Notebooks också reserverar vissa användar-ID: n, t. ex. Microsoft varumärkes namn.

## <a name="sign-out"></a>Logga ut

Om du vill logga ut väljer du ditt användar namn längst upp till höger på sidan och väljer sedan **Logga ut**:

![Platsen för kommandot logga ut på Azure Notebooks](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: exportera ett Jupyter Notebook-projekt](quickstart-export-jupyter-notebook-project.md)
