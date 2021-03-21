---
title: Skapa ett kommersiellt marknads plats konto i Partner Center för Azure Marketplace
description: Lär dig hur du skapar ett Microsoft-konto för kommersiell marknads plats i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/02/2020
author: parthpandyaMSFT
ms.author: parthp
ms.custom: contperf-fy21q2
ms.openlocfilehash: d88e2b18d4e4f9df0606832356e383aaaf51cf73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97028452"
---
# <a name="create-a-commercial-marketplace-account-in-partner-center"></a>Skapa ett kommersiellt marknads plats konto i Partner Center

Om du vill publicera dina erbjudanden till [Microsoft AppSource](https://appsource.microsoft.com/) eller [Azure Marketplace](https://azuremarketplace.microsoft.com/)måste du skapa ett konto i det kommersiella Marketplace-programmet i Partner Center. Den här artikeln beskriver hur du skapar ett partner Center-konto och hur du lägger till nya utgivare i programmet för kommersiella marknads platser.

>[!NOTE]
>Om du hade ett konto i Cloud Partner Portal (CPP) har vi flyttat det till Partner Center. Du behöver inte skapa ett nytt konto. Mer information finns i [utgivare som har flyttat från Cloud Partner Portal](#publishers-who-moved-from-the-cloud-partner-portal).

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du uppfyller följande krav för att skapa ett konto i Partner Center. Vi ska kontrol lera den här informationen när kontot skapas.

- Du måste använda ett arbets konto som är kopplat till ditt företag eller din organisation. Personliga konton stöds inte. Mer information finns i [företagets arbets konton och partner Center](company-work-accounts.md).
- Känner till företagets juridiska företags namn, adress och primär kontakt. Den här personen kan vara du.
- Du måste ha behörighet att signera juridiska avtal på ditt företags vägnar.

Det finns två sätt att skapa ett konto:

- Om du inte har använt Partner Center tidigare och inte har ett Microsoft Partner Network-konto (MPN) fortsätter du att [skapa ett konto med hjälp av sidan för registrering av Partner Center](#create-an-account-using-the-partner-center-enrollment-page).
- Om du redan har registrerat dig i Microsoft Partner Network eller ett program för utvecklare skapar du ett konto direkt från Partner Center. Gå till [skapa ett konto med hjälp av befintliga partner Center-registreringar](#create-an-account-using-existing-partner-center-enrollments).

## <a name="create-an-account-using-the-partner-center-enrollment-page"></a>Skapa ett konto med hjälp av sidan för registrering av Partner Center

Använd den här metoden om du är nybörjare på Partner Center och inte är registrerad i Microsoft Partner Network. Slutför stegen i det här avsnittet för att skapa ett nytt Partner Center-konto och utgivar profil.

### <a name="register-on-the-partner-center-enrollment-page"></a>Registrera dig på sidan för registrering av Partner Center

Läs informationen på sidan [**Välkommen till Microsoft Partner Center**](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) -registrering och registrera dig för ett konto.

### <a name="sign-in-with-a-work-account"></a>Logga in med ett arbets konto

Logga in med ett arbets konto så att du kan koppla ditt företags jobb för e-postkonto till ditt nya partner Center-konto. När du har associerat dessa två konton kan företagets anställda logga in på Partner Center med sitt arbets konto användar namn och lösen ord.

>[!NOTE]
>Om du vill se om ditt företag redan har ett arbets konto, lär du dig hur du skapar ett nytt arbets konto eller Lär dig hur du konfigurerar flera arbets konton som ska användas med partner Center, se [företagets arbets konton och partner Center](company-work-accounts.md).

### <a name="agree-to-the-terms-and-conditions"></a>Godkänn de allmänna villkoren

Som en del av registrerings processen måste du samtycka med villkoren i [Microsoft Publisher-avtalet](https://go.microsoft.com/fwlink/?LinkID=699560).

### <a name="provide-your-publisher-profile"></a>Ange din utgivar profil

Din utgivar profil innehåller ditt företags namn och ditt MPN-ID. Om du inte redan har gjort det går du till [Microsoft Partner Network](https://partner.microsoft.com/commercial). När du har anslutit till Microsoft Partner Network får du ett MPN-ID.

1. Skapa ett utgivar-ID. Ditt utgivar-ID identifierar ditt företag och dina erbjudanden på Azure Marketplace och AppSource.

1. När du har bekräftat din utgivar profil information godkänner du villkoren och skapar sedan ditt partner Center-konto genom att välja **Godkänn och fortsätt**.

    > [!IMPORTANT]
    > Om du vill acceptera dessa villkor måste du ha behörighet att agera på ditt företags vägnar.

    När du har registrerat dig går du till översikts sidan för den kommersiella Marketplace. Kontot för kommersiell marknads plats visas i den vänstra rutan.

1. För att kontrol lera att det kommersiella Marketplace-kontot visas som registrerat väljer du **Inställningar** (kugg hjuls ikon) i det övre högra hörnet > **konto inställnings**  >  **program**.

Nu har du skapat ett kommersiellt marknads plats konto i Partner Center. Fortsätt att [lägga till nya utgivare på den kommersiella marknads platsen](#add-new-publishers-to-the-commercial-marketplace-program).

## <a name="create-an-account-using-existing-partner-center-enrollments"></a>Skapa ett konto med hjälp av befintliga partner Center-registreringar

Använd det här avsnittet om du vill skapa ett kommersiellt marknads plats konto om du redan har en registrering i Microsoft Partner Center. Det finns två typer av befintliga registreringar som du kan använda för att konfigurera ditt kommersiella Marketplace-konto. Välj det scenario som gäller för dig:

*Vad händer om jag redan har registrerat sig i Microsoft Partner Network?*
- [Använd en befintlig Microsoft Partner Network-registrering](#use-a-microsoft-partner-network-enrollment) för att skapa ditt konto.

*Vad händer om jag redan har registrerat i ett program för utvecklare?*
- [Använd ett befintligt program registrering för utvecklare](#use-a-developer-program-enrollment) för att skapa ditt konto.

För båda registrerings typerna loggar du in på Partner Center med dina befintliga autentiseringsuppgifter. Se till att ditt konto och din utgivares profil information är tillgänglig.

### <a name="use-a-microsoft-partner-network-enrollment"></a>Använd en Microsoft Partner Network-registrering

När du använder ditt Microsoft Partner Network-konto länkar det företagets jobb för e-postkontot till ditt nya partner Center-konto. När du har associerat dessa två konton kan företagets anställda logga in på Partner Center med sitt arbets konto användar namn och lösen ord. 

>[!NOTE]
> Du måste ha en **konto administratör** eller en **Global administratörs** roll för att kunna logga in på Microsoft Partner Network.

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/) med ditt Microsoft Partner Network-konto.
1. Välj **Inställningar**  >  **konto inställnings**  >  **program**  >  **Kom igång**.

   Microsoft Partner Network identifierar din prenumeration och visar fönstret **utgivar profil** .

1. Ange företagets namn och MPN-ID.
1. Läs igenom villkoren i [Microsoft Publisher-avtalet](https://go.microsoft.com/fwlink/?LinkID=699560)och välj sedan **Godkänn och fortsätt** för att slutföra registreringen.

    > [!IMPORTANT]
    > Om du vill acceptera dessa villkor måste du ha behörighet att agera på ditt företags vägnar.

    När du har registrerat dig går du till översikts sidan för den kommersiella Marketplace. Kontot för kommersiell marknads plats visas i den vänstra rutan.

1. För att kontrol lera att det kommersiella Marketplace-kontot visas som registrerat väljer du **Inställningar**  >  **konto inställningar**  >  **program**.

Nu har du skapat ett kommersiellt marknads plats konto i Partner Center. Fortsätt att [lägga till nya utgivare på den kommersiella marknads platsen](#add-new-publishers-to-the-commercial-marketplace-program).

### <a name="use-a-developer-program-enrollment"></a>Använd en program registrering för utvecklare

>[!NOTE]
>Om du vill registrera dig för den kommersiella marknads platsen under samma konto som du är inloggad med måste du ha registrerat dig i Partner Center med ett företags konto. Om du har registrerat dig för ett enskilt konto kommer din marknads plats registrering att visas under ett nytt konto.
>
>Enskilda konton är för utvecklare som arbetar på egen hand. Företags konton är för organisationer och företag. Företags konton ger dig åtkomst till att skicka appar med ytterligare funktioner.
>
> För att säkerställa att du har behörighet att konfigurera kontot för ditt företag kräver vi ytterligare verifiering av företags konton när du har registrerat dig. Den här verifieringen kan ta från några dagar till några veckor, och det innehåller ofta ett telefonsamtal till ditt företag. Med båda typerna av konton kan du skicka appar, tillägg och tjänster. Mer information finns i [konto typer, platser och avgifter](/windows/uwp/publish/account-types-locations-and-fees).

När du registrerar dig för programmet för kommersiella marknads platser med samma konto som Developer-programmet kan du se alla befintliga program registreringar i det vänstra fönstret i Partner Center.

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/) med ditt befintliga konto.
1. Välj **Inställningar**  >  **konto inställnings**  >  **program**  >  **Kom igång** och ange din e-postadress till arbetet.
1. Slutför informationen på **utgivarens profil** sida.
2. Läs igenom villkoren i [Microsoft Publisher-avtalet](https://go.microsoft.com/fwlink/?LinkID=699560)och välj sedan **Godkänn och fortsätt** för att slutföra registreringen.

    >[!Important]
    > Om du vill acceptera dessa villkor måste du ha behörighet att agera på ditt företags vägnar.

    När du har registrerat dig går du till översikts sidan för den kommersiella Marketplace. Kontot för kommersiell marknads plats visas i den vänstra rutan.

1. För att kontrol lera att det kommersiella Marketplace-kontot visas som registrerat väljer du **Inställningar**  >  **konto inställningar**  >  **program**.

Nu har du skapat ett kommersiellt marknads plats konto i Partner Center. Om du vill ha hjälp med att lägga till utgivare fortsätter du att [lägga till nya utgivare på den kommersiella marknads platsen](#add-new-publishers-to-the-commercial-marketplace-program).

## <a name="publishers-who-moved-from-the-cloud-partner-portal"></a>Utgivare som har flyttat från Cloud Partner Portal

Om du har ett Cloud Partner Portal-konto har vi flyttat det till Partner Center. Du behöver inte skapa ett nytt Partner Center-konto. Du bör ha tagit emot en anpassad länk till ditt nya partner Center-konto i både e-post och i ett informations meddelande när du har loggat in på ditt befintliga Cloud Partner Portal-konto.

När du har valt den anpassade länken och aktiverat ditt nya partner Center-konto kan du gå tillbaka till ditt konto genom att gå till [instrument panelen för extern Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i Partner Center.

Publicerings avtalet och företags profil informationen övergår till ditt nya partner Center-konto, tillsammans med tidigare konfigurerade profil information för konto utbetalning, användar konton och behörigheter samt aktiva erbjudanden som har associerats med ditt Cloud Partner Portal-konto.

## <a name="add-new-publishers-to-the-commercial-marketplace-program"></a>Lägga till nya utgivare i programmet för kommersiella marknads platser

En organisation kan ha flera utgivare som är kopplade till ett kommersiellt marknads plats konto. En befintlig användare kan lägga till fler utgivare efter att ha loggat in på Partner Center genom att välja **Inställningar**  >  **konto inställningar**  >  **organisation profil**  >  **identifierare**  >  **Lägg till utgivare**.

>[!NOTE]
>Innan du lägger till en ny utgivare granskar du listan över befintliga utgivare genom att logga in på Partner Center och välja **konto inställnings**  >  **utgivare**.

Ytterligare användare från samma Azure Active Directory-klient kan använda följande steg för att lägga till en ny utgivare.

1. Starta registrerings flödet på [Microsoft Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership).
2. Välj **Logga in med ett arbets konto** och ange din e-postadress till arbetet.
3. Välj knappen **Lägg till utgivare** .
4. Välj det MPN-ID som du vill koppla till utgivaren.
5. Uppdatera **utgivar informationen** i formuläret.

   * **Utgivar namn**: det namn som visas i den kommersiella marknads platsen med erbjudandet.  
   * **PublisherID**: en identifierare som används av Partner Center för att identifiera utgivaren unikt. Standardvärdet för det här fältet mappar till ett befintligt och unikt utgivar-ID i systemet. Eftersom utgivar-ID: t inte kan återanvändas måste det här fältet uppdateras.  
   * **Kontakt information**: uppdatera kontakt uppgifterna när det behövs.

När den här processen har slutförts går du till det kommersiella Marketplace-kontot som visas i den vänstra rutan för att hantera den nya utgivaren. Om du inte ser det kommersiella Marketplace-kontot uppdaterar du sidan. Den nya utgivaren visas i listan **utgivare** .

## <a name="next-steps"></a>Nästa steg

- [Vad är Microsofts kommersiella marknadsplats?](../overview.md)
- [Hantera ditt kommersiella Marketplace-konto i Partner Center](manage-account.md)