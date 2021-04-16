---
title: Konfigurera gruppägarens medgivande till att appar får åtkomst till gruppdata med hjälp av Azure AD
description: Lär dig hur du hanterar om grupp- och teamägare kan samtycka till program som har åtkomst till gruppens eller teamets data.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be28148aacf270f2f3cfabad4cbd5f03afa05d3b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374428"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurera gruppägarens medgivande till att appar får åtkomst till gruppdata

Grupp- och teamägare kan auktorisera program, till exempel program som publicerats av tredjepartsleverantörer, för att få åtkomst till organisationens data som är associerade med en grupp. En teamägare i Microsoft Teams kan till exempel tillåta att en app läser alla Teams-meddelanden i teamet eller listar den grundläggande profilen för en grupps medlemmar. Mer [information finns i Resursspecifikt medgivande i Microsoft Teams.](/microsoftteams/resource-specific-consent)

## <a name="manage-group-owner-consent-to-apps"></a>Hantera gruppägarens medgivande till appar

Du kan konfigurera vilka användare som ska tillåtas godkänna att appar får åtkomst till deras gruppers eller teams data, eller så kan du inaktivera detta för alla användare.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill hantera gruppens ägarmedgivande för appar som kommer åt gruppdata:

1. Logga in på [Azure Portal](https://portal.azure.com) som [global administratör.](../roles/permissions-reference.md#global-administrator)
2. Välj **Azure Active Directory För**  >  **företagsprogram Medgivande** och behörigheter  >  **Inställningar** för  >  **användarmedgivande.**
3. Under **Medgivande från gruppägare för appar som har åtkomst** till data väljer du det alternativ som du vill aktivera.
4. Välj **Spara** för att spara inställningarna.

I det här exemplet tillåts alla gruppägare att godkänna att appar får åtkomst till sina gruppers data:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Inställningar för gruppägares medgivande":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda Azure AD PowerShell Preview-modulen [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)för att aktivera eller inaktivera gruppägares möjlighet att samtycka till program som har åtkomst till organisationens data för de grupper som de äger.

1. Kontrollera att du använder modulen [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Det här steget är viktigt om du har installerat både [AzureAD-modulen](/powershell/module/azuread/) och [AzureADPreview-modulen).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Anslut till Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Hämta det aktuella värdet för **kataloginställningarna för inställningar för** medgivandeprincip i din klientorganisation. Detta kräver att du kontrollerar om kataloginställningarna för den här funktionen har skapats, och om inte, med hjälp av värdena från motsvarande kataloginställningsmall.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Förstå inställningsvärdena. Det finns två inställningsvärden som definierar vilka användare som ska kunna tillåta att en app får åtkomst till gruppens data:

    | Inställningen       | Typ         | Beskrivning  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolesk | Flagga som anger om gruppägare tillåts bevilja gruppspecifika behörigheter. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | Om _EnableGroupSpecificConsent_ är inställt på "True" och det här värdet är inställt på en grupps objekt-ID, kommer medlemmar i den identifierade gruppen att ha behörighet att bevilja gruppspecifika behörigheter till de grupper som de äger. |

1. Uppdatera inställningsvärden för önskad konfiguration:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Spara inställningarna.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

---

## <a name="next-steps"></a>Nästa steg

Mer information:

* [Konfigurera inställningar för användarmedgivande](configure-user-consent.md)
* [Konfigurera arbetsflödet för administratörsmedgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och utvärderar begäranden om medgivande](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsofts identitetsplattform](../develop/v2-permissions-and-consent.md)

För att få hjälp eller få svar på dina frågor:
* [Azure AD på Microsoft Q&A ](/answers/topics/azure-active-directory.html)