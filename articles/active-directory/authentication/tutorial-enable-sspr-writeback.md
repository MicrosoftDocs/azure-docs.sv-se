---
title: Aktivera tillbakaskrivning av Azure Active Directory lösen ord
description: I den här självstudien får du lära dig hur du aktiverar tillbakaskrivning av lösen ord för självbetjäning i Azure AD med Azure AD Connect för att synkronisera ändringar till en lokal Active Directory Domain Services miljö.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 444ca19732921b336cae32a9b1eb5755a08e4bd3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028061"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Självstudie: Aktivera Azure Active Directory självbetjäning för återställning av lösen ord till en lokal miljö

Med Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR) kan användare uppdatera sitt lösen ord eller låsa upp sitt konto med en webbläsare. I en hybrid miljö där Azure AD är ansluten till en lokal Active Directory Domain Services-miljö (AD DS) kan det här scenariot orsaka att lösen ord skiljer sig åt mellan de två katalogerna.

Tillbakaskrivning av lösen ord kan användas för att synkronisera lösen ords ändringar i Azure AD tillbaka till din lokala AD DS-miljö. Azure AD Connect ger en säker mekanism för att skicka lösen ords ändringarna tillbaka till en befintlig lokal katalog från Azure AD.

> [!IMPORTANT]
> I den här självstudien visas en administratör om hur du aktiverar självbetjäning för återställning av lösen ord tillbaka till en lokal miljö. Om du är en slutanvändare som redan är registrerad för lösen ords återställning via självbetjäning och behöver gå tillbaka till ditt konto, går du till https://aka.ms/sspr .
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösen ord kan du kontakta supportavdelningen för ytterligare hjälp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera de behörigheter som krävs för tillbakaskrivning av lösen ord
> * Aktivera alternativet för tillbakaskrivning av lösen ord i Azure AD Connect
> * Aktivera tillbakaskrivning av lösen ord i Azure AD SSPR

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En fungerande Azure AD-klient med minst en Azure AD Premium P1-eller utvärderings licens aktive rad.
    * Om det behövs kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Mer information finns i [licens krav för Azure AD SSPR](concept-sspr-licensing.md).
* Ett konto med *Global administratörs* behörighet.
* Azure AD har kon figurer ATS för lösen ords återställning via självbetjäning.
    * Om det behövs [Slutför du den föregående själv studie kursen för att aktivera Azure AD SSPR](tutorial-enable-sspr.md).
* En befintlig lokal AD DS-miljö som kon figurer ATS med en aktuell version av Azure AD Connect.
    * Konfigurera vid behov Azure AD Connect med hjälp av inställningarna för [Express](../hybrid/how-to-connect-install-express.md) eller [anpassad](../hybrid/how-to-connect-install-custom.md) .
    * Om du vill använda tillbakaskrivning av lösen ord måste domän kontrol Lanterna vara Windows Server 2012 eller senare.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Konfigurera konto behörigheter för Azure AD Connect

Med Azure AD Connect kan du synkronisera användare, grupper och autentiseringsuppgifter mellan en lokal AD DS-miljö och Azure AD. Vanligt vis installerar du Azure AD Connect på en dator med Windows Server 2012 eller senare som är ansluten till den lokala AD DS-domänen.

För att fungera korrekt med SSPR tillbakaskrivning måste det konto som anges i Azure AD Connect ha rätt behörigheter och alternativ inställda. Om du inte är säker på vilket konto som används för närvarande öppnar du Azure AD Connect och väljer alternativet **Visa aktuell konfiguration** . Det konto som du behöver lägga till behörigheter till visas under **synkroniserade kataloger**. Följande behörigheter och alternativ måste anges för kontot:

* **Återställa lösenord**
* **Skriv behörigheter** för `lockoutTime`
* **Skriv behörigheter** för `pwdLastSet`
* **Utökade rättigheter** för "utgående lösen ord" på rotobjektet för *varje domän* i skogen, om de inte redan angetts.

Om du inte tilldelar dessa behörigheter kan tillbakaskrivning vara korrekt konfigurerat, men användarna stöter på fel när de hanterar sina lokala lösen ord från molnet. Behörigheter måste tillämpas på **det här objektet och alla underordnade objekt** för "lösen ordet upphör att gälla" visas.  

> [!TIP]
>
> Om lösen ord för vissa användar konton inte skrivs tillbaka till den lokala katalogen ser du till att arv inte har inaktiverats för kontot i lokal AD DS-miljö. Skriv behörigheter för lösen ord måste tillämpas på underordnade objekt för att funktionen ska fungera korrekt.

Utför följande steg för att ställa in rätt behörigheter för tillbakaskrivning av lösen ord:

1. I din lokala AD DS-miljö öppnar du **Active Directory användare och datorer** med ett konto som har rätt *domän administratörs* behörighet.
1. I menyn **Visa** ser du till att **avancerade funktioner** är aktiverade.
1. I den vänstra rutan högerklickar du på det objekt som representerar roten för domänen och väljer **Egenskaper**  >    >  **Avancerad** säkerhet.
1. På fliken **behörigheter** väljer du **Lägg till**.
1. För **huvud** konto väljer du det konto som behörigheter ska tillämpas på (kontot som används av Azure AD Connect).
1. I list rutan **gäller väljer du** **underordnade användar objekt**.
1. Under *behörigheter* väljer du kryss rutan för följande alternativ:
    * **Återställa lösenord**
1. Under *Egenskaper* väljer du rutorna för följande alternativ. Bläddra igenom listan för att hitta de här alternativen, som kanske redan är inställda som standard:
    * **Skriv lockoutTime**
    * **Skriv pwdLastSet**

    [![Ange lämpliga behörigheter för aktiva användare och datorer för kontot som används av Azure AD Connect ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png)](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. När du är klar väljer du **tillämpa/OK** för att tillämpa ändringarna och avsluta alla öppna dialog rutor.

När du uppdaterar behörigheter kan det ta upp till en timme eller mer för dessa behörigheter att replikera till alla objekt i din katalog.

Lösen ords principer i den lokala AD DS-miljön kan förhindra att lösen ord återställs på rätt sätt. För att tillbakaskrivning av lösen ord ska fungera effektivt måste grup principen för *lägsta ålder för lösen ord* anges till 0. Du hittar den här inställningen under **dator konfiguration > principer > Windows-inställningar > säkerhets inställningar > konto principer** i `gpedit.msc` .

Om du uppdaterar grup principen väntar du tills den uppdaterade principen replikeras eller använder `gpupdate /force` kommandot.

> [!Note]
> För att lösen ord ska kunna ändras omedelbart måste tillbakaskrivning av lösen ord anges till 0. Men om användarna följer de lokala principerna och *lägsta ålder för lösen ord* anges till ett värde som är större än noll, fungerar inte tillbakaskrivning av lösen ord när de lokala principerna utvärderas.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Aktivera tillbakaskrivning av lösen ord i Azure AD Connect

Ett av konfigurations alternativen i Azure AD Connect är för tillbakaskrivning av lösen ord. När det här alternativet är aktiverat, orsakar lösen ords ändrings händelser Azure AD Connect att synkronisera de uppdaterade autentiseringsuppgifterna tillbaka till den lokala AD DS-miljön.

Om du vill aktivera SSPR tillbakaskrivning aktiverar du först alternativet tillbakaskrivning i Azure AD Connect. Utför följande steg från din Azure AD Connect Server:

1. Logga in på Azure AD Connect-servern och starta guiden **Azure AD Connect** konfiguration.
1. På sidan **Välkommen** klickar du på **Konfigurera**.
1. På sidan **Ytterligare uppgifter** väljer du **Anpassa synkroniseringsalternativ** och väljer sedan **Nästa**.
1. På sidan **Anslut till Azure AD** anger du en global administratörs behörighet för din Azure-klient och väljer sedan **Nästa**.
1. På filtreringssidorna **Anslut kataloger** och **Domän/OU** väljer du **Nästa**.
1. På sidan **Valfria funktioner** markerar du rutan bredvid **Tillbakaskrivning av lösenord** och väljer **Nästa**.

    ![Konfigurera Azure AD Connect för tillbakaskrivning av lösen ord](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. På sidan **Klart att konfigurera** väljer du **Konfigurera** och väntar tills processen slutförts.
1. När du ser att konfigurationen är klar väljer du **Avsluta**.

## <a name="enable-password-writeback-for-sspr"></a>Aktivera tillbakaskrivning av lösen ord för SSPR

När tillbakaskrivning av lösen ord är aktiverat i Azure AD Connect, konfigurera nu Azure AD-SSPR för tillbakaskrivning. När du aktiverar SSPR för att använda tillbakaskrivning av lösen ord, har användare som ändrar eller återställer sitt lösen ord det uppdaterade lösen ordet synkroniseras tillbaka till den lokala AD DS-miljön också.

Utför följande steg för att aktivera tillbakaskrivning av lösen ord i SSPR:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett globalt administratörs konto.
1. Sök efter och välj **Azure Active Directory**, Välj **lösen ords återställning** och välj sedan **lokal integrering**.
1. Ange alternativet för **Skriv tillbaka lösen ord till din lokala katalog?** till *Ja*.
1. Ange alternativet för **Tillåt att användare låser upp konton utan att återställa sitt lösen ord?** till *Ja*.

    ![Aktivera lösen ords återställning via självbetjäning i Azure AD för tillbakaskrivning av lösen ord](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. När du är klar väljer du **Spara**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda funktionen SSPR tillbakaskrivning som du har konfigurerat som en del av den här självstudien utför du följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory**, Välj **lösen ords återställning** och välj sedan **lokal integrering**.
1. Ange alternativet för **Skriv tillbaka lösen ord till din lokala katalog?** till *Nej*.
1. Ange alternativet för **Tillåt att användare låser upp konton utan att återställa sina lösen ord?** till *Nej*.

Om du inte längre vill använda någon lösen ords funktion utför du följande steg från Azure AD Connect servern:

1. Logga in på Azure AD Connect-servern och starta guiden **Azure AD Connect** konfiguration.
1. På sidan **Välkommen** klickar du på **Konfigurera**.
1. På sidan **Ytterligare uppgifter** väljer du **Anpassa synkroniseringsalternativ** och väljer sedan **Nästa**.
1. På sidan **Anslut till Azure AD** anger du en global administratörs behörighet för din Azure-klient och väljer sedan **Nästa**.
1. På filtreringssidorna **Anslut kataloger** och **Domän/OU** väljer du **Nästa**.
1. På sidan **valfria funktioner** avmarkerar du kryss rutan bredvid **tillbakaskrivning av lösen ord** och väljer **Nästa**.
1. På sidan **Klart att konfigurera** väljer du **Konfigurera** och väntar tills processen slutförts.
1. När du ser att konfigurationen är klar väljer du **Avsluta**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien aktiverade du Azure AD SSPR tillbakaskrivning till en lokal AD DS-miljö. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera de behörigheter som krävs för tillbakaskrivning av lösen ord
> * Aktivera alternativet för tillbakaskrivning av lösen ord i Azure AD Connect
> * Aktivera tillbakaskrivning av lösen ord i Azure AD SSPR

> [!div class="nextstepaction"]
> [Utvärdera risk vid inloggning](tutorial-risk-based-sspr-mfa.md)
