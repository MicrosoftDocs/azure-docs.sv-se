---
title: Konfigurera säkerhet för att bevilja data åtkomst – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du konfigurerar säkerhet, behörigheter och hanterar principer för data åtkomst i din Azure Time Series Insightss miljö.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780866"
---
# <a name="grant-data-access-to-an-environment"></a>Bevilja data åtkomst till en miljö

I den här artikeln beskrivs de två typerna av Azure Time Series Insights åtkomst principer.

## <a name="sign-in-to-azure-time-series-insights"></a>Logga in på Azure Time Series Insights

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Leta upp din Azure Time Series Insights-miljö genom att ange `Time Series Insights environments` i sökrutan.  Välj `Time Series Insights environments` i Sök resultaten.
1. Välj din Azure Time Series Insights-miljö i listan.

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg om du vill bevilja data åtkomst för ett huvud konto för användare.

1. Välj **data åtkomst principer** och välj sedan **+ Lägg till**.

    [![Välj och Lägg till en princip för data åtkomst](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Välj **Välj användare**. Sök efter användar namnet eller e-postadressen för att hitta den användare som du vill lägga till. Välj **Välj** för att bekräfta valet.

    [![Välj en användare som ska läggas till](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Välj **Välj roll**. Välj lämplig åtkomst roll för användaren:

    * Välj **deltagare** om du vill tillåta att användaren ändrar referens data och delar sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer du **läsare** så att användaren kan fråga efter data i miljön och spara personliga, inte delade, frågor i miljön.

   Bekräfta valet av roll genom att klicka på **OK** .

    [![Bekräfta den valda rollen](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Välj **OK** på sidan **Välj användar roll** .

    [![Välj OK på sidan Välj användar roll](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Bekräfta att sidan för **data åtkomst principer** visar användarna och rollerna för varje användare.

    [![Verifiera rätt användare och roller](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Ge gäst åtkomst från en annan Azure AD-klient

`Guest`Rollen är inte en hanterings roll. Det är en term som används för ett konto som har bjudits in från en klient till en annan. När gäst kontot har bjudits in till klientens katalog kan samma åtkomst kontroll tillämpas på samma sätt som andra konton. Du kan bevilja hanterings åtkomst till en Azure Time Series Insights miljö genom att använda bladet Access Control (IAM). Du kan också bevilja åtkomst till data i miljön via bladet data åtkomst principer. Mer information om Azure Active Directory (Azure AD) gäst åtkomsten finns [i lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](../active-directory/external-identities/add-users-administrator.md).

Följ dessa steg om du vill ge gäst åtkomst till en Azure Time Series Insights miljö till en Azure AD-användare från en annan klient.

1. Gå till Azure Portal, klicka på  **Azure Active Directory**, rulla ned på fliken **Översikt** och välj **gäst användare**.

    [![Välj data åtkomst principer och sedan + Bjud in](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Ange e-postadressen för den användare som du vill bjuda in. Den här e-postadressen måste vara kopplad till Azure AD. Du kan också inkludera ett personligt meddelande med inbjudan.

    [![Ange e-postadressen för att hitta den valda användaren](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Leta efter den bekräftelse bubbla som visas på skärmen. Du kan också klicka på **meddelanden** för att bekräfta att gäst användaren har lagts till.

    [![Leta efter bekräftelse bubblan som ska visas](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Gå tillbaka till Time Series Insightss miljön för att lägga till den nya gäst användaren. Klicka på **data åtkomst principer** enligt beskrivningen under **bevilja åtkomst till data**. **Välj användare**. Sök efter e-postadressen för gäst användaren som du har bjudit in för att hitta den användare som du vill lägga till. **Välj** sedan för att bekräfta valet.

    [![Välj användaren och bekräfta valet](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Välj **Välj roll**. Välj lämplig åtkomst roll för gäst användaren:

    * Välj **deltagare** om du vill tillåta att användaren ändrar referens data och delar sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer du **läsare** så att användaren kan fråga efter data i miljön och spara personliga, inte delade, frågor i miljön.

   Bekräfta valet av roll genom att klicka på **OK** .

    [![Bekräfta valet av roll](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Välj **OK** på sidan **Välj användar roll** .

1. Bekräfta att sidan för **data åtkomst principer** visar gäst användaren och rollerna för varje gäst användare.

    [![Kontrol lera att användare och roller har tilldelats korrekt](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Nu får gäst användaren ett e-postmeddelande med inbjudan till den angivna e-postadressen ovan. Gäst användaren väljer **Kom igång** för att bekräfta sitt godkännande och ansluta till Azure-molnet.

    [![Gäst väljer kom igång för att acceptera](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. När du **har valt kom igång** visas gäst användaren med en behörighets ruta som är kopplad till administratörens organisation. När du beviljar behörighet genom att välja **acceptera** loggas de in.

    [![Gästen granskar behörigheter och accepterar](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Administratören [delar miljöns URL](time-series-insights-parameterized-urls.md) med sin gäst.

1. När gäst användaren är inloggad på den e-postadress som du använde för att bjuda in dem, och de accepterar inbjudan, kommer de att dirigeras till Azure Portal.

1. Gästen kan nu komma åt den delade miljön med hjälp av den miljö-URL som tillhandahålls av administratören. De kan ange URL-adressen i webbläsaren för omedelbar åtkomst.

1. Administratörens klient visas för gäst användaren när du har valt profil ikonen i det övre högra hörnet i Time Series Explorer.

    [![Val av avatar på insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    När gäst användaren väljer administratörens klient, kan de välja den delade Azure Time Series Insightss miljön.

    De har nu alla funktioner som är kopplade till den roll som du angav i **steg 5**.

    [![Gäst användare väljer din Azure-klient från listruta](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Läs [autentisering och auktorisering](time-series-insights-authentication-and-authorization.md) för Azure Active Directory registrerings steg för appar.

* Visa [din miljö i Azure Time Series Insights Explorer](./concepts-ux-panels.md).
