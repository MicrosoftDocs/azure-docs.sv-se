---
title: Konfigurera hur slutanvändare godkänner program via Azure AD
description: Lär dig hur du hanterar hur och när användare kan samtycka till program som har åtkomst till din organisations data.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 95a651f6201c9f60500c9191821edb7eb76b8535
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374445"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurera hur slutanvändare godkänner program

Du kan integrera dina program med Microsoft Identity-plattformen så att användarna kan logga in med sitt arbets- eller skolkonto och få åtkomst till organisationens data för att leverera omfattande datadrivna upplevelser.

Innan ett program kan komma åt organisationens data måste en användare bevilja programmet behörighet att göra det. Olika behörigheter tillåter olika åtkomstnivåer. Som standard tillåts alla användare att godkänna program för behörigheter som inte kräver administratörsmedgivande. Till exempel kan en användare som standard samtycka till att tillåta att en app får åtkomst till sin postlåda, men kan inte samtycka till att tillåta ohämad åtkomst för en app att läsa och skriva till alla filer i din organisation.

Genom att tillåta användare att ge appar åtkomst till data kan användarna enkelt få användbara program och vara produktiva. Men i vissa situationer kan den här konfigurationen utgöra en risk om den inte övervakas och kontrolleras noggrant.

> [!IMPORTANT]
> För att minska risken för skadliga program som försöker lura användare att ge dem åtkomst till din organisations data, rekommenderar vi att du endast tillåter användarmedgivande för program som har publicerats av en verifierad [utgivare](../develop/publisher-verification-overview.md).

## <a name="user-consent-settings"></a>Inställningar för användarmedgivande

Principer för appmedgivande beskriver villkor som måste uppfyllas innan en app kan godkännas. Dessa principer kan innehålla villkor för den app som begär åtkomst, samt de behörigheter som appen begär.

Genom att välja vilka principer för appmedgivande som gäller för alla användare kan du ange gränser för när slutanvändare får bevilja medgivande till appar och när de måste begära administratörsgranskning och godkännande:

* **Inaktivera användarmedgivande** – Användare kan inte bevilja behörigheter till program. Användare kan fortsätta att logga in på appar som de tidigare har samtyckt till eller som har samtyckts till av administratörer för deras räkning, men de kan inte godkänna nya behörigheter eller nya appar på egen hand. Endast användare som har beviljats en katalogroll som innehåller behörighet att bevilja medgivande kan godkänna nya appar.

* **Användare kan godkänna** appar från verifierade utgivare eller din organisation, men endast för behörigheter som [](../develop/publisher-verification-overview.md) du väljer – Alla användare kan bara godkänna appar som har publicerats av en verifierad utgivare och appar som är registrerade i din klientorganisation. Användarna kan bara godkänna de behörigheter som du har klassificerat som "låg påverkan". Du måste [klassificera behörigheter](configure-permission-classifications.md) för att välja vilka behörigheter som användare får godkänna.

* **Användare kan samtycka till alla** appar – Med det här alternativet kan alla användare godkänna alla behörigheter som inte kräver administratörsmedgivande för alla program.

* **Anpassad appmedgivandeprincip** – Om du vill ha ännu fler alternativ för de villkor som styr när användaren ger sitt medgivande kan du skapa en anpassad [appmedgivandeprincip](manage-app-consent-policies.md#create-a-custom-app-consent-policy)och konfigurera dem så att de gäller för användarmedgivande.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här konfigurerar du inställningar för användarmedgivande via Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) som [global administratör.](../roles/permissions-reference.md#global-administrator)
1. Välj **Azure Active Directory För**  >  **företagsprogram Medgivande** och behörigheter  >  **Inställningar** för  >  **användarmedgivande.**
1. Under **Användarmedgivande för** program väljer du vilken medgivandeinställning som du vill konfigurera för alla användare.
1. Spara **inställningarna** genom att välja Spara.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Inställningar för användarmedgivande":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda den senaste Azure AD PowerShell Preview-modulen [AzureADPreview för](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)att välja vilken appmedgivandeprincip som styr användarens medgivande för program.

#### <a name="disable-user-consent"></a>Inaktivera användarmedgivande

Om du vill inaktivera användarmedgivande anger du att medgivandeprinciperna som styr användarens medgivande ska vara tomma:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Tillåt användarmedgivande enligt en appmedgivandeprincip

Om du vill tillåta användarmedgivande väljer du vilken princip för appmedgivande som ska styra användarnas behörighet att bevilja medgivande till appar:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Ersätt `{consent-policy-id}` med ID:t för den princip som du vill tillämpa. Du kan välja en [anpassad princip för appmedgivande](manage-app-consent-policies.md#create-a-custom-app-consent-policy) som du har skapat eller välja bland följande inbyggda principer:

| ID | Beskrivning |
|:---|:------------|
| microsoft-user-default-low | **Tillåt användarmedgivande för appar från verifierade utgivare för valda behörigheter**<br /> Tillåt endast begränsat användarmedgivande för appar från verifierade utgivare och appar som är registrerade i din klientorganisation och endast för behörigheter som du klassificerar som "Låg påverkan". (Glöm inte att klassificera [behörigheter för](configure-permission-classifications.md) att välja vilka behörigheter användare får godkänna.) |
| microsoft-user-default-legacy | **Tillåt användarmedgivande för appar**<br /> Med det här alternativet kan alla användare godkänna alla behörigheter som inte kräver administratörsmedgivande för alla program |
  
Om du till exempel vill aktivera användarmedgivande enligt den inbyggda principen `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [](configure-admin-consent-workflow.md) Aktivera arbetsflödet för administratörsmedgivande så att användarna kan begära en administratörs granskning och godkännande av ett program som användaren inte tillåts att godkänna, till exempel när användarmedgivande har inaktiverats eller när ett program begär behörigheter som användaren inte tillåts bevilja.

## <a name="risk-based-step-up-consent"></a>Riskbaserat stegvist medgivande

Riskbaserat stegvist medgivande bidrar till att minska användarexponeringen för skadliga appar som gör [begäranden om otillåtet medgivande](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Om Microsoft identifierar en riskabel begäran om medgivande från slutanvändaren kräver begäran ett steg uppåt för att administratören ska ge sitt medgivande i stället. Den här funktionen är aktiverad som standard, men den resulterar bara i en beteendeändring när slutanvändares medgivande är aktiverat.

När en begäran om riskabelt medgivande identifieras visas ett meddelande om att ett administratörsgodkännande krävs. Om [arbetsflödet för begäran om administratörsmedgivande](configure-admin-consent-workflow.md) är aktiverat kan användaren skicka begäran till en administratör för ytterligare granskning direkt från medgivandeuppfråga. Om den inte är aktiverad visas följande meddelande:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; behöver behörighet att komma åt resurser i din organisation som endast en administratör kan bevilja. Be en administratör att bevilja behörighet till den här appen innan du använder den.

I det här fallet loggas även en granskningshändelse med kategorin "ApplicationManagement", aktivitetstypen "Medgivande till program" och statusorsaken för "riskfyllda program har identifierats".

> [!IMPORTANT]
> Administratörer bör utvärdera [alla begäranden om medgivande noggrant](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) innan de godkänner en begäran, särskilt när Microsoft har identifierat en risk.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Inaktivera eller återaktivera riskbaserat steg-up-medgivande med hjälp av PowerShell

Du kan använda Azure AD PowerShell Preview-modulen [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)för att inaktivera det steg-upp till administratörsmedgivande som krävs i fall där Microsoft identifierar risker eller återaktiverar det om det tidigare har inaktiverats.

1. Kontrollera att du använder modulen [AzureADPreview.](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) Det här steget är viktigt om du har installerat både [AzureAD-modulen](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) och [AzureADPreview-modulen).](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Förstå inställningsvärdet:

    | Inställningen       | Typ         | Beskrivning  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolesk |  Flagga som anger om användarens medgivande kommer att blockeras när en riskabel begäran identifieras. |

1. Uppdatera inställningsvärdet för önskad konfiguration:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>Nästa steg

Mer information:

* [Konfigurera inställningar för användarmedgivande](configure-user-consent.md)
* [Hantera principer för appmedgivande](manage-app-consent-policies.md)
* [Konfigurera arbetsflödet för administratörsmedgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och utvärderar begäranden om medgivande](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsofts identitetsplattform](../develop/v2-permissions-and-consent.md)

För att få hjälp eller få svar på dina frågor:
* [Azure AD på Microsoft Q&A.](/answers/topics/azure-active-directory.html)