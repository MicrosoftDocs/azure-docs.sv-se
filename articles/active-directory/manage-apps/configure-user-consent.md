---
title: Konfigurera hur slutanvändare godkänner program med hjälp av Azure AD
description: Lär dig hur du hanterar och när användare kan godkänna program som kommer att ha åtkomst till din organisations data.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 992f082c5157521ea7f0d8a681557d6c7cc3dc04
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089993"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurera hur slutanvändare godkänner program

Du kan integrera dina program med Microsoft Identity Platform så att användarna kan logga in med sitt arbets-eller skol konto och komma åt din organisations data för att leverera omfattande data drivna upplevelser.

Innan ett program kan komma åt din organisations data måste en användare ge programmet behörighet att göra det. Olika behörigheter tillåter olika åtkomst nivåer. Som standard har alla användare tillåtelse att godkänna program för behörigheter som inte kräver administratörs medgivande. Som standard kan en användare godkänna att en app får åtkomst till sin post låda men inte kan godkänna att en app oinskränkt till att läsa och skriva till alla filer i din organisation.

Genom att tillåta användare att ge appar åtkomst till data kan användarna enkelt få användbara program och vara produktiva. Men i vissa fall kan den här konfigurationen representera en risk om den inte övervakas och kontrol leras noggrant.

> [!IMPORTANT]
> För att minska risken för skadliga program som försöker lura användare att ge dem åtkomst till din organisations data, rekommenderar vi att du endast tillåter användar medgivande för program som har publicerats av en [verifierad utgivare](../develop/publisher-verification-overview.md).

## <a name="user-consent-settings"></a>Inställningar för godkännande av användare

Principer för program medgivande beskriver villkor som måste uppfyllas innan en app kan skickas till. Dessa principer kan omfatta villkor för appen som begär åtkomst, samt de behörigheter som appen begär.

Genom att välja vilka principer för utfärdande av appar som gäller för alla användare kan du ange gränser för när slutanvändare får bevilja medgivande till appar, och när de behöver för att begära administratörs granskning och godkännande:

* **Inaktivera användar medgivande** – användare kan inte bevilja åtkomst till program. Användare kan fortsätta att logga in på appar som de tidigare har samtyckt till eller som har godkänts av administratörer för deras räkning, men de kommer inte att kunna samtycka till nya behörigheter eller till nya appar. Endast användare som har beviljats en katalog roll som innehåller behörigheten för att bevilja tillstånd kan godkänna nya appar.

* **Användare kan godkänna appar från verifierade utgivare eller din organisation, men endast för de behörigheter du väljer** – alla användare kan bara godkänna appar som publicerats av en [verifierad utgivare](../develop/publisher-verification-overview.md) och appar som är registrerade i din klient organisation. Användare kan bara godkänna de behörigheter som du har klassificerat som "låg påverkan". Du måste [klassificera behörigheter](configure-permission-classifications.md) för att välja vilka behörigheter som användare tillåts att godkänna.

* **Användare kan godkänna alla appar** – med det här alternativet kan alla användare godkänna alla behörigheter som inte kräver administratörs medgivande, för alla program.

* **Anpassad princip** för utfärdande av appar – för ännu fler alternativ för de villkor som gäller när användaren godkänner det kan du [skapa en anpassad princip för program medgivande](manage-app-consent-policies.md#create-a-custom-app-consent-policy)och konfigurera de som ska gälla för användarens medgivande.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konfigurera användar medgivande inställningar via Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../roles/permissions-reference.md#global-administrator).
1. Välj **Azure Active Directory**  >  **företags program**  >  **medgivande-och**  >  **användar** tillstånds inställningar.
1. Under **användar medgivande för program** väljer du vilken godkännande inställning som du vill konfigurera för alla användare.
1. Spara inställningarna genom att välja **Spara** .

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Inställningar för godkännande av användare":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda den senaste Azure AD PowerShell Preview-modulen, [AzureADPreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview), för att välja vilken app medgivande policy som styr användar medgivande för program.

#### <a name="disable-user-consent"></a>Inaktivera användar medgivande

Om du vill inaktivera användar medgivande anger du de medgivande principer som styr användar medgivande som ska vara tomt:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Tillåt användar medgivande beroende på en app medgivande-princip

Om du vill tillåta användar medgivande väljer du vilken app medgivande policy som ska styra användarnas tillstånd att bevilja medgivande till appar:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Ersätt `{consent-policy-id}` med ID för den princip som du vill använda. Du kan välja en [anpassad princip för program medgivande](manage-app-consent-policies.md#create-a-custom-app-consent-policy) som du har skapat, eller så kan du välja bland följande inbyggda principer:

| ID | Description |
|:---|:------------|
| Microsoft-User-default-Low | **Tillåt användar medgivande för appar från verifierade utgivare, för valda behörigheter**<br /> Tillåt endast begränsade användare för appar från verifierade utgivare och appar som registrerats i din klient organisation, och endast för behörigheter som du klassificerar som "låg påverkan". (Glöm inte att [klassificera behörigheter](configure-permission-classifications.md) för att välja vilka behörigheter som användare tillåts att godkänna.) |
| Microsoft-User-default – bakåtkompatibelt | **Tillåt användar tillstånd för appar**<br /> Med det här alternativet kan alla användare godkänna alla behörigheter som inte kräver administratörs medgivande, för alla program |
  
Om du till exempel vill aktivera användar medgivande för den inbyggda principen `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [Aktivera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md) så att användarna kan begära en administratörs granskning och godkännande av ett program som användaren inte har tillåtelse att godkänna till, till exempel när användar medgivande har inaktiverats eller när ett program begär behörigheter som användaren inte får bevilja.

## <a name="risk-based-step-up-consent"></a>Riskfylldt steg-för-steg-godkännande

Riskfylldt steg-för-steg-godkännande bidrar till att minska användar exponeringen för skadliga appar som gör [illegala medgivande begär Anden](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Om Microsoft identifierar en riskfylld begäran om godkännande av slutanvändare kräver begäran ett "steg-för-steg" för administratörs medgivande i stället. Den här funktionen är aktive rad som standard, men den kommer bara att resultera i en funktion som ändras när slutanvändaren har Aktiver ATS.

När en riskfylld begäran identifieras visar medgivande frågan ett meddelande som anger att administratörs godkännande krävs. Om [arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md) är aktiverat kan användaren skicka begäran till en administratör för ytterligare granskning direkt från medgivande frågan. Om den inte är aktive rad visas följande meddelande:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; måste ha behörighet för att få åtkomst till resurser i din organisation som bara en administratör kan bevilja. Be en administratör att bevilja behörighet till den här appen innan du använder den.

I det här fallet loggas även en gransknings händelse med en kategori av typen "ApplicationManagement", aktivitets typ "medgivande till program" och status orsak för "riskfylld program upptäcktes".

> [!IMPORTANT]
> Administratörer bör [utvärdera alla medgivande begär Anden](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) noggrant innan en begäran godkänns, särskilt när Microsoft har identifierat risk.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Inaktivera eller återaktivera riskfylldt Step-based-godkännande med PowerShell

Du kan använda Azure AD PowerShell Preview-modulen, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), för att inaktivera steget-upp till administratörs medgivande som krävs i de fall där Microsoft identifierar risker eller aktiverar det igen om det tidigare har inaktiverats.

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Förstå inställning svärdet:

    | Inställningen       | Typ         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolesk |  Flagga som anger om användar medgivande ska blockeras när en riskfylld begäran identifieras. |

1. Uppdatera inställnings värde för önskad konfiguration:

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

* [Konfigurera inställningar för användar godkännande](configure-user-consent.md)
* [Hantera principer för appmedgivande](manage-app-consent-policies.md)
* [Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och att utvärdera medgivande begär Anden](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

För att få hjälp eller hitta svar på dina frågor:
* [Azure AD på Microsoft Q&A.](https://docs.microsoft.com/answers/topics/azure-active-directory.html)