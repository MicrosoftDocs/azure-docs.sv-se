---
title: Återaktivera inaktiverade namn områden för Azure Access Control Service (ACS)
description: Hitta och aktivera dina Azure Access Control Service-namnområden (ACS) och begär ett tillägg för att hålla dem aktiverade fram till 4 februari 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7bb572e9e22519491290e54417f5ca350d6c0b5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383725"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Gör så här: återaktivera inaktiverade Access Control Service namn områden

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

I november 2017 meddelade vi att Microsoft Azure Access Control Service (ACS), en tjänst av Azure Active Directory (Azure AD), dras tillbaka den 7 november 2018.

Sedan dess har vi skickat e-postmeddelanden till administrations e-postmeddelandet för ACS-prenumerationen om ACS-pensionen 12 månader, 6 månader, 3 månader, 1 månad, 2 veckor, 1 vecka och 1 dag innan indragnings datumet den 7 november 2018.

Den 3 oktober 2018 presenterade vi (via e-post och [ett blogg](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)inlägg) ett förlängnings erbjudande till kunder som inte kan slutföra migreringen före 7 november 2018. Meddelandet hade också instruktioner för att begära tillägget.

## <a name="why-your-namespace-is-disabled"></a>Varför ditt namn område är inaktiverat

Om du inte har valt tillägget för tillägget börjar vi med att inaktivera ACS-namnområden från 7 november 2018. Du måste ha begärt tillägget till 4 februari 2019 redan. annars kommer du inte att kunna aktivera namn områden via PowerShell.

> [!NOTE]
> Du måste vara tjänst administratör eller medadministratör för prenumerationen för att köra PowerShell-kommandona och begära ett tillägg.

## <a name="find-and-enable-your-acs-namespaces"></a>Hitta och aktivera ACS-namnområden

Du kan använda ACS PowerShell för att visa en lista över alla ACS-namnområden och återaktivera dem som har inaktiverats.

1. Hämta och installera ACS PowerShell:
    1. Gå till PowerShell-galleriet och hämta [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Installera modulen:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Hämta en lista över alla möjliga kommandon:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Om du vill ha hjälp med ett speciellt kommando kör du:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        där `[Command-Name]` är namnet på ACS-kommandot.
1. Anslut till ACS med cmdleten **Connect-AcsAccount** . 

    Du kan behöva ändra körnings principen genom att köra **set-ExecutionPolicy** innan du kan köra kommandot.
1. Lista dina tillgängliga Azure-prenumerationer med hjälp av cmdleten **Get-AcsSubscription** .
1. Visa en lista med ACS-namnområden med cmdleten **Get-AcsNamespace** .
1. Bekräfta att namn områdena är inaktiverade genom att bekräfta `State` det `Disabled` .

    [![Bekräfta att namn områdena är inaktiverade](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Du kan också använda `nslookup {your-namespace}.accesscontrol.windows.net` för att bekräfta om domänen fortfarande är aktiv.

1. Aktivera ACS-namnområden med hjälp av cmdleten **Enable-AcsNamespace** .

    När du har aktiverat dina namn rymder kan du begära ett tillägg så att namn områdena inte inaktive ras igen före den 4 februari 2019. Efter det datumet kommer alla förfrågningar till ACS att Miss förfalla.

## <a name="request-an-extension"></a>Begär ett tillägg

Vi tar nya förlängnings begär Anden från den 21 januari 2019.

Vi kommer att börja inaktivera namn områden för kunder som har begärt tillägg till 4 februari 2019. Du kan fortfarande återaktivera namn områden via PowerShell, men namn rymderna kommer att inaktive ras igen efter 48 timmar.

Efter den 4 mars 2019 kommer kunder inte längre att kunna återaktivera namn områden via PowerShell.

Ytterligare tillägg kommer inte längre att godkännas automatiskt. Om du behöver ytterligare tid för migreringen kan du kontakta [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support) om du vill ha en detaljerad tids linje för migrering.

### <a name="to-request-an-extension"></a>Begära ett tillägg

1. Logga in på Azure Portal och skapa en [ny support förfrågan](https://portal.azure.com/#create/Microsoft.Support).
1. Fyll i det nya formuläret supportbegäran som det visas i följande exempel.

    | Supportbegäran fält | Värde |
    |-----------------------|--------------------|
    | **Typ av problem** | `Technical` |
    | **Prenumeration** | Ange till din prenumeration |
    | **Tjänst** | `All services` |
    | **Resurs** | `General question/Resource not available` |
    | **Problem typ** | `ACS to SAS Migration` |
    | **Ämne** | Beskriv problemet |

   ![Visar ett exempel på en ny begäran om teknisk support](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Hjälp och support

- Kontakta [Azure-supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)om du stöter på problem efter följande:
- Om du har frågor eller feedback om ACS-pensionen kan du kontakta oss på acsfeedback@microsoft.com .

## <a name="next-steps"></a>Nästa steg

- Granska informationen om ACS-pensionering i [så här: Migrera från Azure-Access Control Service](active-directory-acs-migration.md).
