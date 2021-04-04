---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 4967991b0edaa854acbf6b308596859d662311fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993370"
---
## <a name="prerequisites"></a>Förutsättningar
Innan du skriver kod för CDN-hantering måste du göra en förberedelse för att aktivera koden för att interagera med Azure Resource Manager. För att göra det här preparatet måste du:

* Skapa en resurs grupp som innehåller CDN-profilen som skapats i den här självstudien
* Konfigurera Azure Active Directory för att tillhandahålla autentisering för programmet
* Använd behörigheter för resurs gruppen så att endast auktoriserade användare från din Azure AD-klient kan interagera med CDN-profilen

### <a name="creating-the-resource-group"></a>Skapa resursgruppen
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **skapa en resurs**.
3. Sök efter **resurs grupp** och klicka på **skapa** i fönstret resurs grupp.

    ![Skapa en ny resurs grupp](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Namnge resurs gruppen *CdnConsoleTutorial*.  Välj din prenumeration och välj en plats nära dig.  Om du vill kan du klicka på kryss rutan **Fäst på instrument panelen** för att fästa resurs gruppen på instrument panelen i portalen.  Genom att fästa blir det enklare att hitta senare.  När du har gjort dina val klickar du på **skapa**.

    ![Skärm bild av dialog rutan resurs grupp.](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. När resurs gruppen har skapats, om du inte har fäst den på instrument panelen, kan du hitta den genom att klicka på **Bläddra** och sedan på **resurs grupper**.  Öppna den genom att klicka på resurs gruppen.  Anteckna ditt **prenumerations-ID**. Vi behöver den senare.

    ![Skärm bild av avsnittet C D N-konsol självstudie.](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Skapa Azure AD-programmet och tillämpa behörigheter
Det finns två sätt att autentisera appar med Azure Active Directory: enskilda användare eller ett huvud namn för tjänsten. Ett tjänst huvud namn liknar ett tjänst konto i Windows.  I stället för att bevilja en viss användar behörighet att interagera med CDN-profilerna, beviljas behörigheter i stället till tjänstens huvud namn.  Tjänstens huvud namn används vanligt vis för automatiserade, icke-interaktiva processer.  Även om den här självstudien skriver en interaktiv konsol app, kommer vi att fokusera på metoden för tjänstens huvud namn.

Att skapa ett huvud namn för tjänsten består av flera steg, inklusive att skapa ett Azure Active Directory-program.  Vi planerar att [följa den här självstudien](../articles/active-directory/develop/howto-create-service-principal-portal.md)för att skapa den.

> [!IMPORTANT]
> Se till att följa alla steg i den [länkade självstudien](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Det är *viktigt* att du slutför den exakt som det beskrivs.  Se till att anteckna **klient-ID**, **klient domän namn** (vanligt vis en *. onmicrosoft.com* -domän, om du inte har angett en anpassad domän), **klient-ID** och **klientautentisering** som vi behöver den här informationen senare.  Var noga med att skydda ditt **klient-ID** och din **klient verifierings nyckel**, eftersom dessa autentiseringsuppgifter kan användas av alla som ska köra åtgärder som tjänstens huvud namn.
>
> När du kommer till steget Konfigurera program med flera innehavare väljer du **Nej**.
>
> När du kommer till steget [tilldela programmet till en roll](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)använder du den resurs grupp som skapades tidigare,  *CdnConsoleTutorial*, men i stället för rollen **läsare** tilldelar du rollen **CDN-profil deltagare** .  När du har tilldelat programmet rollen **CDN Profile Contributor** i din resurs grupp går du tillbaka till den här självstudien. 
>
>

När du har skapat tjänstens huvud namn och tilldelat rollen **CDN-profil deltagare** bör bladet **användare** för resurs gruppen se ut ungefär så här:

![Bladet användare](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktiv användarautentisering
Om du i stället för ett huvud namn för tjänsten har en interaktiv individuell användarautentisering, är processen liknande för ett huvud namn för tjänsten.  I själva verket måste du följa samma procedur, men göra några små ändringar.

> [!IMPORTANT]
> Följ bara dessa steg om du väljer att använda individuell användarautentisering i stället för ett huvud namn för tjänsten.
>
>

1. När du skapar ditt program, i stället för **webb program**, väljer du **internt program**.

    ![Internt program](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. På nästa sida uppmanas du att ange en **omdirigerings-URI**.  URI: n är inte verifierad, men kom ihåg det du angav. Du behöver den senare.
3. Du behöver inte skapa en nyckel för **klientautentisering**.
4. I stället för att tilldela ett huvud namn för tjänsten till rollen **CDN Profile Contributor** ska vi tilldela enskilda användare eller grupper.  I det här exemplet kan du se att jag har tilldelat  *CDN demo-användaren* till rollen **CDN Profile Contributor** .  

    ![Individuell användar åtkomst](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
