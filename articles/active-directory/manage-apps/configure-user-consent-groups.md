---
title: Konfigurera grupp ägare medgivande till appar som har åtkomst till grupp data med hjälp av Azure AD
description: Läs om hur grupp-och team ägare kan godkänna program som kommer att ha åtkomst till gruppens eller teamets data.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 24b2b8ee6398ef1306aee59b5d7ca7525284f96b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644555"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurera grupp ägare medgivande till appar som har åtkomst till grupp data

Grupp-och team ägare kan auktorisera program, till exempel program som publiceras av tredjepartsleverantörer, för att komma åt din organisations data som är associerade med en grupp. Till exempel kan en grupp ägare i Microsoft-team tillåta att en app läser alla team meddelanden i teamet eller listar grund profilen för en grupps medlemmar. Mer information finns i [Resource-Specific medgivande i Microsoft Teams](/microsoftteams/resource-specific-consent) .

## <a name="manage-group-owner-consent-to-apps"></a>Hantera grupp ägarens medgivande till appar

Du kan konfigurera vilka användare som tillåts att godkänna appar som har åtkomst till sina gruppers eller teams data, eller så kan du inaktivera detta för alla användare.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ de här stegen för att hantera grupp ägarens medgivande till appar som har åtkomst till grupp data:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../roles/permissions-reference.md#global-administrator).
2. Välj **Azure Active Directory**  >  **företags program**  >  **medgivande-och**  >  **användar** tillstånds inställningar.
3. Under **grupp ägarens medgivande för appar som har åtkomst till data** väljer du det alternativ som du vill aktivera.
4. Spara inställningarna genom att välja **Spara** .

I det här exemplet har alla grupp ägare tillåtelse att tillåta appar som har åtkomst till sina gruppers data:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Inställningar för godkännande av grupp ägare":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda Azure AD PowerShell Preview-modulen, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), för att aktivera eller inaktivera grupp ägarens möjlighet att godkänna program som har åtkomst till organisationens data för de grupper som de äger.

1. Kontrol lera att du använder [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) -modulen. Det här steget är viktigt om du har installerat både [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) -modulen och [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) -modulen.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Anslut till Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Hämta det aktuella värdet för katalog inställningarna för **medgivande princip inställningarna** i din klient organisation. Detta kräver att du kontrollerar om katalog inställningarna för den här funktionen har skapats, och om inte använder värdena från motsvarande katalog inställnings mall.

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

1. Förstå inställnings värden. Det finns två inställnings värden som definierar vilka användare som ska kunna tillåta en app åtkomst till deras grupp data:

    | Inställningen       | Typ         | Beskrivning  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolesk | Flagga som anger om grupp ägare tillåts att bevilja gruppdefinierade behörigheter. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | Om _EnableGroupSpecificConsent_ är inställt på "true" och det här värdet har angetts till en grupps objekt-ID kommer medlemmar i den identifierade gruppen ha behörighet att bevilja gruppspecifika behörigheter till de grupper som de äger. |

1. Uppdatera inställnings värden för önskad konfiguration:

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

* [Konfigurera inställningar för användar godkännande](configure-user-consent.md)
* [Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och att utvärdera medgivande begär Anden](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

För att få hjälp eller hitta svar på dina frågor:
* [Azure AD på Microsoft Q&A ](/answers/topics/azure-active-directory.html)