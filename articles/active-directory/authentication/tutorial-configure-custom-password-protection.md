---
title: Konfigurera anpassade Azure Active Directory lösen ords skydds listor
description: I den här självstudien får du lära dig hur du konfigurerar anpassade listor över blockerade lösen ords skydd för Azure Active Directory att begränsa vanliga ord i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abdcf4c76c92c2ed967594527d323b2cd8be32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741192"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Självstudie: Konfigurera anpassade förbjudna lösen ord för Azure Active Directory lösen ords skydd

Användare skapar ofta lösen ord som använder vanliga lokala ord som skolan, idrotts lag eller berömda person. Dessa lösen ord är lätta att gissa sig till och är svaga mot ordbaserade attacker. Om du vill framtvinga starka lösen ord i din organisation kan du med den anpassade listan över blockerade lösen ord i Azure Active Directory (Azure AD) lägga till särskilda strängar för att utvärdera och blockera. En begäran om lösen ords ändring Miss lyckas om det finns en matchning i listan anpassat blockerade lösen ord.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Aktivera anpassade förbjudna lösen ord
> * Lägg till poster i den anpassade listan över förbjudna lösen ord
> * Testa lösen ords ändringar med ett blockerat lösen ord

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En fungerande Azure AD-klient med minst en Azure AD Premium P1-eller utvärderings licens aktive rad.
    * Om det behövs kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med *Global administratörs* behörighet.
* En icke-administratörs användare med ett lösen ord som du känner till, till exempel *testuser*. Du testar en händelse för lösen ords ändring med det här kontot i den här självstudien.
    * Om du behöver skapa en användare, se [snabb start: Lägg till nya användare i Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
    * För att testa lösen ords ändrings åtgärden med ett blockerat lösen ord måste Azure AD-klienten [konfigureras för lösen ords återställning](tutorial-enable-sspr.md)via självbetjäning.

## <a name="what-are-banned-password-lists"></a>Vad är förbjudna lösen ords listor?

Azure AD innehåller en lista för globalt blockerade lösen ord. Innehållet i den globala listan över förbjudna lösen ord baseras inte på någon extern data källa. I stället baseras den globala listan över förbjudna lösen ord på det pågående resultatet av Azure AD-säkerhetstelemetri och analys. När en användare eller administratör försöker ändra eller återställa sina autentiseringsuppgifter, kontrol leras det önskade lösen ordet mot listan över blockerade lösen ord. Begäran om lösen ords ändring Miss lyckas om det finns en matchning i listan globalt blockerade lösen ord. Du kan inte redigera den här standard listan över blockerade globala lösen ord.

För att ge dig flexibilitet i vilka lösen ord som tillåts kan du också definiera en anpassad lista över blockerade lösen ord. Listan med anpassade förbjudna lösen ord fungerar tillsammans med den globala listan över blockerade lösen ord för att framtvinga starka lösen ord i din organisation. Organisatoriska villkor kan läggas till i listan med anpassade förbjudna lösen ord, t. ex. följande exempel:

* Märkes namn
* Produkt namn
* Platser, till exempel företagets huvud kontor
* Företagsspecifika interna villkor
* Förkortningar som har ett bestämt företags betydelse

När en användare försöker återställa ett lösen ord till något som finns i listan globalt eller anpassat blockerat lösen ord visas ett av följande fel meddelanden:

* *Ditt lösen ord innehåller tyvärr ett ord, en fras eller ett mönster som gör ditt lösen ord lätt att gissa. Försök igen med ett annat lösen ord.*
* *Tyvärr kan du inte använda lösen ordet eftersom det innehåller ord eller tecken som har blockerats av administratören. Försök igen med ett annat lösen ord.*

Listan med anpassade förbjudna lösen ord är begränsad till högst 1000 villkor. Den är inte utformad för att blockera stora listor med lösen ord. Du kan maximera fördelarna med den anpassade listan över blockerade lösen ord genom att granska [Översikt över](concept-password-ban-bad.md#how-are-passwords-evaluated)begrepp och lösen ord för [anpassad lista över blockerade lösen ord](concept-password-ban-bad.md#custom-banned-password-list) .

## <a name="configure-custom-banned-passwords"></a>Konfigurera anpassade förbjudna lösen ord

Vi aktiverar den anpassade listan över förbjudna lösen ord och lägger till vissa poster. Du kan när som helst lägga till ytterligare poster i den anpassade listan över blockerade lösen ord.

Om du vill aktivera listan med anpassade förbjudna lösen ord och lägga till poster i den utför du följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto med *globala administratörs* behörigheter.
1. Sök efter och välj **Azure Active Directory** och välj sedan **säkerhet** på menyn på vänster sida.
1. Under **Hantera** meny rubriken väljer du **autentiseringsmetoder** och sedan **lösen ords skydd**.
1. Ange alternativet för **tvinga anpassad lista** till *Ja*.
1. Lägg till strängar i **listan anpassat blockerade lösen ord**, en sträng per rad. Följande överväganden och begränsningar gäller för listan med anpassade förbjudna lösen ord:

    * Listan med anpassade förbjudna lösen ord kan innehålla upp till 1000 villkor.
    * Den anpassade listan över blockerade lösen ord är Skift läges okänslig.
    * Listan med anpassade förbjudna lösen ord ser till att vanlig tecken ersättning används, till exempel "o" och "0", eller "a" och " \@ ".
    * Den minsta sträng längden är fyra tecken och det maximala värdet är 16 tecken.

    Ange egna anpassade lösen ord för att förbjuda detta, som visas i följande exempel

    [![Ändra listan med anpassade förbjudna lösen ord under autentiseringsmetoder i Azure Portal ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Lämna alternativet för **att aktivera lösen ords skydd på Windows Server Active Directory** till *Nej*.
1. Om du vill aktivera anpassade förbjudna lösen ord och dina poster väljer du **Spara**.

Det kan ta flera timmar att uppdatera listan med anpassade förbjudna lösen ord som ska användas.

För en hybrid miljö kan du också [Distribuera Azure AD Password Protection till en lokal miljö](howto-password-ban-bad-on-premises-deploy.md). Samma globala och anpassade listor över blockerade lösen ord används för både moln-och lokal för att ändra lösen ord.

## <a name="test-custom-banned-password-list"></a>Testa anpassad lista över blockerade lösen ord

Försök att ändra lösen ordet till en variant av en som du lade till i föregående avsnitt, om du vill se den anpassade listan över blockerade lösen ord i praktiken. När Azure AD försöker bearbeta lösen ords ändringen matchas lösen ordet mot en post i listan anpassat blockerade lösen ord. Sedan visas ett fel för användaren.

> [!NOTE]
> Innan en användare kan återställa sina lösen ord på den webbaserade portalen måste Azure AD-klienten [konfigureras för lösen ords återställning](tutorial-enable-sspr.md)via självbetjäning. Vid behov kan användaren [registrera sig för SSPR på https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup).

1. Gå till sidan **Mina appar** på [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. Välj ditt namn i det övre högra hörnet och välj sedan **profil** på den nedrullningsbara menyn.

    ![Välj profil](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. På sidan **profil** väljer du **ändra lösen ord**.
1. På sidan **ändra lösen ord** anger du det befintliga (gamla) lösen ordet. Ange och bekräfta ett nytt lösen ord som finns på den anpassade listan över förbjudna lösen ord som du definierade i föregående avsnitt och välj sedan **Skicka**.
1. Ett fel meddelande visas som anger att lösen ordet har blockerats av administratören, vilket visas i följande exempel:

    ![Fel meddelande visas när du försöker använda ett lösen ord som är en del av den anpassade listan över förbjudna lösen ord](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda den anpassade listan över blockerade lösen ord som du har konfigurerat som en del av den här självstudien utför du följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory** och välj sedan **säkerhet** på menyn på vänster sida.
1. Under **Hantera** meny rubriken väljer du **autentiseringsmetoder** och sedan **lösen ords skydd**.
1. Ange alternativet för **tvinga anpassad lista** till *Nej*.
1. Om du vill uppdatera den anpassade konfigurationen för förbjudna lösen ord väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat och konfigurerat anpassade lösen ords skydds listor för Azure AD. Du har lärt dig att:

> [!div class="checklist"]
> * Aktivera anpassade förbjudna lösen ord
> * Lägg till poster i den anpassade listan över förbjudna lösen ord
> * Testa lösen ords ändringar med ett blockerat lösen ord

> [!div class="nextstepaction"]
> [Aktivera riskbaserad Azure AD-multifaktorautentisering](./tutorial-enable-azure-mfa.md)