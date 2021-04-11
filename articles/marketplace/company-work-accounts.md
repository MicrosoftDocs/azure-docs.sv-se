---
title: Företagets arbets konton och partner Center
description: Så här kontrollerar du om ditt företag har ett arbets konto som har skapats med Microsoft, skapar ett nytt arbets konto eller konfigurerar flera arbets konton för användning med partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 05/30/2019
ms.openlocfilehash: 69e9f9d6d7d132476796a1cdfdaaa2490457640c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108402"
---
# <a name="company-work-accounts-and-partner-center"></a>Företagets arbets konton och partner Center

Partner Center använder företags arbets konton, även kallade Azure Active Directory (AD)-klienter, för att hantera konto åtkomst för flera användare, kontrol lera behörigheter, värd grupper och program och underhålla profil data. Genom att länka ditt företags e-postkonto till ditt partner Center-konto kan anställda på ditt företag Logga in på Partner Center för att hantera Marketplace-erbjudanden med sina egna användar namn och lösen ord för arbets kontot.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Kontrol lera om ditt företag redan har ett arbets konto

Om ditt företag prenumererar på en moln tjänst från Microsoft, till exempel Azure, Microsoft Intune eller Microsoft 365, har du redan en domän för e-postkonto för arbetet (kallas även en Azure Active Directory klient) som kan användas med partner Center.

Följ de här stegen för att kontrol lera:
1. Logga in på Azure Admin Portal på https://portal.azure.com .
2. Välj **Azure Active Directory** på menyn till vänster-navigerings och välj sedan **anpassade domän namn**.
3. Om du redan har ett arbets konto visas ditt domän namn.

Om ditt företag inte redan har ett arbets konto skapas ett för dig under registreringen av Partner Center.

## <a name="set-up-multiple-work-accounts"></a>Konfigurera flera arbets konton

Innan du bestämmer dig för att använda ett befintligt arbets konto bör du fundera över hur många användare i arbets kontot som behöver komma åt Partner Center. Om du har användare i arbets kontot som inte behöver komma åt Partner Center, kan du överväga att skapa flera arbets konton så att endast de användare som behöver åtkomst till Partner Center visas på ett visst konto.

## <a name="create-a-new-work-account"></a>Skapa ett nytt arbets konto

Följ stegen nedan om du vill skapa ett nytt arbets konto för ditt företag. Du kan behöva be om hjälp från vem som har administratörs behörighet på ditt företags Microsoft Azure konto.

1. Logga in på [Microsoft Azure-portalen](https://portal.azure.com).
2. I den vänstra navigerings menyn väljer du **Azure Active Directory**  ->  **användare**.
3. Välj **ny användare** och skapa ett nytt Azure-arbetskonto genom att ange ett namn och en e-postadress. Se till att **katalog rollen** har angetts till **användare** och markera kryss rutan **Visa lösen ord** längst ned för att visa och anteckna det automatiskt genererade lösen ordet.
4. Välj **skapa** för att spara den nya användaren.

E-postadressen för användar kontot måste vara ett verifierat domän namn i din katalog. Du kan visa en lista över alla verifierade domäner i din katalog genom att välja **Azure Active Directory**  ->  **anpassade domän namn** i den vänstra navigerings menyn.

Mer information om hur du lägger till anpassade domäner i Azure Active Directory finns i [lägga till eller associera en domän i Azure AD](/azure/active-directory/fundamentals/add-custom-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Felsöka e-postinloggning för arbete

Om du har problem med att logga in på ditt arbets konto (även kallat din Azure AD-klient) hittar du scenariot i diagrammet nedan som bäst matchar din situation och följer de rekommenderade stegen.

![Diagram för fel sökning av inloggning för arbets konto](./media/manage-accounts/onboarding-aad-flow.png)

## <a name="next-steps"></a>Nästa steg

- [Hantera ditt kommersiella Marketplace-konto i Partner Center](./manage-account.md)