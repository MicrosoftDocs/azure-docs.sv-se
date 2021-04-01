---
title: Skapa applösenord från sidan säkerhets information (för hands version) – Azure AD
description: Skapa automatiskt genererade lösen ord (applösenord) som ska användas med appar som inte är webbläsarbaserade eller appar som inte stöder tvåstegsverifiering, i din organisation. Det här applösenord är skilt från ett vanligt lösen ord och kan konfigureras från sidan säkerhets information.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 84588130788e9be8d3be52a8ea0f3988dce7b952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537012"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Skapa applösenord på sidan Säkerhetsinformation (förhandsversion)

Vissa appar, till exempel Outlook 2010, stöder inte tvåstegsverifiering. Detta saknar stöd innebär att om du använder tvåstegsverifiering i din organisation fungerar inte appen. För att komma runt det här problemet kan du skapa ett automatiskt genererat lösen ord som ska användas med varje annan app än en webbläsare, separat från ditt normala lösen ord.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Din administratör kanske inte tillåter att du använder applösenord. Om du inte ser **applösenord** som ett alternativ är de inte tillgängliga i din organisation.

När du använder applösenord är det viktigt att komma ihåg:

- Applösenord genereras automatiskt och bör skapas och anges en gång per app.

- Det finns en gräns på 40 lösen ord per användare. Om du försöker skapa en efter den gränsen uppmanas du att ta bort ett befintligt lösen ord innan du får skapa det nya.

    >[!Note]
    >Office 2013-klienter (inklusive Outlook) har stöd för nya autentiseringsprotokoll och kan användas med tvåstegsverifiering. Det här stödet innebär att när tvåstegsverifiering har Aktiver ATS behöver du inte längre applösenord för Office 2013-klienter. Mer information finns i artikeln [så här fungerar modern autentisering för office 2013 och office 2016-klient program](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Skapa nya applösenord

Om du använder tvåstegsverifiering med ditt arbets-eller skol konto och din administratör har aktiverat säkerhets informations upplevelsen, kan du skapa och ta bort dina applösenord på sidan **säkerhets information** .

>[!Note]
>Om din administratör inte har aktiverat säkerhets informations upplevelsen måste du följa anvisningarna och informationen i avsnittet [Hantera applösenord för att kontrol lera appar i två steg](multi-factor-authentication-end-user-app-passwords.md) .

### <a name="to-create-a-new-app-password"></a>Skapa ett nytt applösenord

1. Logga in på ditt arbets-eller skol konto och gå sedan till din https://myaccount.microsoft.com/ sida.

    ![Sidan min profil, som visar markerade länkar för säkerhets information](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhets information** i det vänstra navigerings fönstret eller från länken i **säkerhets informations** blocket och välj sedan **Lägg till metod** på sidan **säkerhets information** .

    ![Sidan säkerhets information med markerat Lägg till metod-alternativ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **applösenord** i list rutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod med applösenord valt](media/security-info/securityinfo-myprofile-addpassword.png)

4. Skriv namnet på den app som kräver applösenord och välj sedan **Nästa**.

    ![Skärm bild som visar sidan "applösenord" med namnet på den angivna appen.](media/security-info/securityinfo-myprofile-password-appname.png)

5. Kopiera texten från rutan **lösen ord** , klistra in lösen ordet i appens lösen ord (i det här exemplet Outlook 2010) och välj sedan **färdig**.

    ![Sidan applösenord med namnet på appen](media/security-info/securityinfo-myprofile-password-copytext.png)

    Lösen ordet läggs till och du kan logga in på din app framåt.

## <a name="delete-your-app-passwords"></a>Ta bort dina applösenord

Om du inte längre behöver använda en app som kräver ett applösenord kan du ta bort det associerade applösenord. Om du tar bort app-lösenordet frigörs ett av de tillgängliga applösenord för appar för användning i framtiden.

>[!Important]
>Om du tar bort ett applösenord av misstag finns det inget sätt att ångra det. Du måste skapa ett nytt applösenord och ange det på nytt i appen genom att följa stegen i avsnittet [skapa nya applösenord](#create-new-app-passwords) i den här artikeln.

### <a name="to-delete-an-app-password"></a>Ta bort ett applösenord

1. På sidan **säkerhets information** väljer du länken **ta bort** bredvid **appens lösen ords** alternativ för den aktuella appen.

    ![Länk för att ta bort appens lösen ords metod från säkerhets information](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Välj **Ja** i bekräftelse rutan om du vill ta bort **appens lösen ord**. När applösenord har tagits bort tas den bort från din säkerhets information och försvinner från sidan **säkerhets information** .

## <a name="for-more-information"></a>Mer information

- Mer information om sidan **säkerhets information** och hur du konfigurerar den finns i [Översikt över säkerhets information](./security-info-setup-signin.md)