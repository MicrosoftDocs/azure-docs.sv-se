---
title: Skapa & ta bort Azure AD B2C konsument användar konton i Azure Portal
description: Lär dig hur du använder Azure Portal för att skapa och ta bort konsument användare i din Azure AD B2Cs katalog.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ace0ccb8372ff21a2d3e8721baf09bab539846c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033677"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Använd Azure Portal för att skapa och ta bort konsument användare i Azure AD B2C

Det kan finnas scenarier där du manuellt vill skapa konsument konton i din Azure Active Directory B2C-katalog (Azure AD B2C). Även om konsument konton i en Azure AD B2C katalog vanligt vis skapas när användarna registrerar sig för att använda ett av dina program, kan du skapa dem program mässigt och genom att använda Azure Portal. Den här artikeln fokuserar på Azure Portal metoden för att skapa och ta bort användare.

För att lägga till eller ta bort användare måste ditt konto tilldelas rollen *användar administratör* eller *Global administratör* .

## <a name="types-of-user-accounts"></a>Typer av användar konton

Enligt beskrivningen i [Översikt över användar konton i Azure AD B2C](user-overview.md)finns det tre typer av användar konton som kan skapas i en Azure AD B2C katalog:

* Arbete
* Gäst
* Konsument

Den här artikeln fokuserar på att arbeta med **konsument konton** i Azure Portal. Information om hur du skapar och tar bort arbets-och gäst konton finns i [lägga till eller ta bort användare med hjälp av Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Skapa en konsument användare

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Under **Hantera** väljer du **Användare**.
1. Välj **Ny användare**.
1. Välj **skapa Azure AD B2C användare**.
1. Välj en **inloggnings metod** och ange antingen en **e-** postadress eller ett **användar namn** för den nya användaren. Den inloggnings metod som du väljer här måste matcha den inställning som du har angett för din Azure AD B2C klients *lokala konto* identitets leverantör (se **Hantera**  >  **identitets leverantörer** i Azure AD B2C klient organisationen).
1. Ange ett **namn** för användaren. Detta är vanligt vis användarens fullständiga namn (anges och efter namn).
1. Valfritt Du kan **blockera inloggning** om du vill försena möjligheten för användaren att logga in. Du kan aktivera logga in senare genom att redigera användarens **profil** i Azure Portal.
1. Välj **AutoGenerate Password** eller **Låt mig skapa lösen ord**.
1. Ange användarens **förnamn** och **efter namn**.
1. Välj **Skapa**.

Om du inte har valt **blockera inloggning** kan användaren nu logga in med inloggnings metoden (e-post eller användar namn) som du har angett.

## <a name="reset-a-users-password"></a>Återställa ett användarlösenord

Som administratör kan du återställa en användares lösen ord, om användaren glömmer sitt lösen ord. När du återställer användarens lösen ord genereras ett tillfälligt lösen ord automatiskt för användaren. Det tillfälliga lösen ordet upphör aldrig att gälla. Nästa gången användaren loggar in, kommer lösen ordet fortfarande att fungera, oavsett hur lång tid det tar att använda sedan det tillfälliga lösen ordet genererades. Användaren måste sedan återställa lösen ordet till ett permanent. 

> [!IMPORTANT]
> Innan du återställer en användares lösen ord måste du [Konfigurera ett lösen ord för återställning av lösen ord i Azure Active Directory B2C](force-password-reset.md), annars kan användaren inte logga in.

Återställa en användares lösen ord:

1. I Azure AD B2C katalog väljer du **användare** och väljer sedan den användare som du vill återställa lösen ordet för.
1. Sök efter och välj den användare som behöver återställas och välj sedan **Återställ lösen ord**.

    Sidan **Alain Charon-profil** visas med alternativet **Återställ lösen ord** .

    ![Användarens profil sida med alternativet Återställ lösen ord markerat](media/manage-users-portal/user-profile-reset-password-link.png)

1. På sidan **Återställ lösen ord** väljer du **Återställ lösen ord**.
1. Kopiera lösen ordet och ge det till användaren. Användaren kommer att behöva ändra lösen ordet under nästa inloggnings process.


## <a name="delete-a-consumer-user"></a>Ta bort en konsument användare

1. I Azure AD B2C katalog väljer du **användare** och väljer sedan den användare som du vill ta bort.
1. Välj **ta bort** och sedan **Ja** för att bekräfta borttagningen.

Mer information om hur du återställer en användare inom de första 30 dagarna efter borttagning, eller om du vill ta bort en användare permanent, finns i [återställa eller ta bort en nyligen borttagen användare med hjälp av Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Nästa steg

För automatiserade scenarier för användar hantering, till exempel när du migrerar användare från en annan identitetsprovider till din Azure AD B2C katalog, se [Azure AD B2C:](user-migration.md)användarmigrering.
