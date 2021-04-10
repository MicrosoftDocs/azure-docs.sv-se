---
title: Självstudie – Skapa användar flöden – Azure Active Directory B2C
description: Följ den här självstudien för att lära dig hur du skapar användar flöden i Azure Portal för att aktivera registrering, inloggning och användar profil redigering för dina program i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c42c6465af8e895d833332be847c134b97ee8ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781304"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Självstudie: skapa användar flöden i Azure Active Directory B2C

I dina program kan du ha [användar flöden](user-flow-overview.md) som gör det möjligt för användare att registrera, logga in eller hantera sina profiler. Du kan skapa flera användar flöden av olika typer i din Azure Active Directory B2C-klient (Azure AD B2C) och använda dem i dina program efter behov. Användar flöden kan återanvändas i olika program.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett användar flöde för registrering och inloggning
> * Aktivera lösenordsåterställning via självbetjäning
> * Skapa ett användarflöde för profilredigering


Den här självstudien visar hur du skapar vissa rekommenderade användar flöden med hjälp av Azure Portal. Om du vill ha information om hur du konfigurerar ett ROPC-flöde (Resource Owner Password Credential) i programmet, se [Konfigurera flödet för autentiseringsuppgifter för resurs ägare i Azure AD B2C](add-ropc-policy.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Vi har ändrat hur vi refererar till användarflödesversioner. Tidigare erbjöd vi V1-versioner (produktionsklara), V1.1 och V2 (förhandsversion). Nu har vi konsoliderat användar flöden till **Rekommenderad** (nästa generations för hands version) och **standard** versioner (allmänt tillgängliga). Alla tidigare förhands gransknings användar flöden för V 1.1 och v2 finns på en sökväg till utfasningen den **1 augusti 2021**. Mer information finns [i användar flödes versioner i Azure AD B2C](user-flow-versions.md).

## <a name="prerequisites"></a>Förutsättningar

[Registrera dina program](tutorial-register-applications.md) som är en del av de användar flöden som du vill skapa.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Skapa ett användar flöde för registrering och inloggning

Användar flödet för registrering och inloggning hanterar både registrering och inloggnings upplevelser med en enda konfiguration. Användare av ditt program är i rätt sökväg beroende på kontexten.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

    ![B2C-klient, katalog och prenumerations fönstret Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **användar flöden** och väljer sedan **nytt användar flöde**.

    ![Sidan användar flöden i portalen med knappen nytt användar flöde markerat](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. På sidan **skapa ett användar flöde** väljer du användar flödet **Registrera och logga** in.

    ![Välj sidan för användar flöde med registrering och inloggnings flöde markerat](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**. ([Läs mer](user-flow-versions.md) om användar flödes versioner.)

    ![Sidan skapa användar flöde i Azure Portal med egenskaper markerat](./media/tutorial-create-user-flows/select-version.png)

1. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
1. För **identitets leverantörer** väljer du **e-postregistrering**.
1. För användarattribut **och anspråk** väljer du de anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj till exempel **Visa mer** och välj sedan attribut och anspråk för **land/region**, **visnings namn** och **post nummer**. Klicka på **OK**.

    ![Sidan för attribut och val av anspråk med tre anspråk valda](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* anpassningsprefix automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde** och välj sedan **Registrera dig nu**.

    ![Sidan kör användar flöde i portalen med knappen Kör användar flöde markerat](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Ange en giltig e-postadress, klicka på **Skicka verifierings kod**, ange den verifierings kod som du får och välj sedan **verifiera kod**.
1. Ange ett nytt lösen ord och bekräfta lösen ordet.
1. Välj land och region, ange det namn som du vill visa, ange ett post nummer och klicka sedan på **skapa**. Token returneras till `https://jwt.ms` och ska visas för dig.
1. Nu kan du köra användar flödet igen och du bör kunna logga in med det konto som du skapade. Den returnerade token innehåller de anspråk som du har valt land/region, namn och post nummer.

> [!NOTE]
> Upplevelsen "kör användar flöde" är för närvarande inte kompatibel med URL-typen SPA-svar med hjälp av Authorization Code Flow. Om du vill använda upplevelsen "kör användar flöde" med dessa typer av appar registrerar du en svars-URL av typen "Web" och aktiverar det implicita flödet enligt beskrivningen [här](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Aktivera [självbetjäning för återställning av lösen ord](add-password-reset-policy.md) för registrerings-eller inloggnings användar flödet:

1. Välj det användar flöde för registrering eller inloggning som du skapade.
1. Under **Inställningar** i den vänstra menyn väljer du **Egenskaper**.
1. Under **lösen ords komplexitet** väljer du **självbetjäning för återställning av lösen ord**.
1. Välj **Spara**.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj **Kör användar flöde**.
1. På sidan registrering eller inloggning väljer du **glömt lösen ordet?**.
1. Kontrol lera e-postadressen för det konto som du skapade tidigare och välj sedan **Fortsätt**.
1. Nu har du möjlighet att ändra lösen ordet för användaren. Ändra lösen ordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna ska kunna redigera sin profil i ditt program använder du en profil som redigerar användar flöde.

1. På menyn på sidan för Azure AD B2C klient översikt väljer du **användar flöden** och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du **profil redigering** användar flöde. 
1. Under **Välj en version** väljer du **rekommenderas** och väljer sedan **skapa**.
1. Ange ett **Namn** för användarflödet. Till exempel *profileediting1*.
1. För **identitets leverantörer** väljer du **lokal konto inloggning**.
2. För **användarattribut** väljer du de attribut som du vill att kunden ska kunna redigera i profilen. Välj till exempel **Visa mer** och välj sedan både attribut och anspråk för **visnings namn** och **befattning**. Klicka på **OK**.
3. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* läggs automatiskt till i namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program** väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde** och logga sedan in med det konto som du skapade tidigare.
1. Nu har du möjlighet att ändra visnings namn och befattning för användaren. Klicka på **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa ett användar flöde för registrering och inloggning
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

Nu ska du lära dig hur du använder Azure AD B2C för att logga in och registrera användare i ett program. Följ ASP.NET-webbprogrammet som är länkat nedan eller navigera till ett annat program i innehålls förteckningen under **autentisera användare**.

> [!div class="nextstepaction"]
> [Självstudie: aktivera autentisering i ett webb program med hjälp av Azure AD B2C >](tutorial-web-app-dotnet.md)
