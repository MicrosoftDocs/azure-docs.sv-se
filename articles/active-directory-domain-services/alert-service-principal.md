---
title: Lösa aviseringar om tjänstens huvud namn i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du felsöker tjänstens huvud konfigurations aviseringar för Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15dba02d3db45d663b8ba0aa7eb93bbc3f388532
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645882"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Kända problem: aviseringar för tjänstens huvud namn i Azure Active Directory Domain Services

[Tjänstens huvud namn](../active-directory/develop/app-objects-and-service-principals.md) är program som Azure-plattformen använder för att hantera, uppdatera och underhålla en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän. Om ett huvud namn för tjänsten tas bort påverkas funktionerna i den hanterade domänen.

Den här artikeln hjälper dig att felsöka och lösa tjänstens huvud namn för konfigurations aviseringar.

## <a name="alert-aadds102-service-principal-not-found"></a>Aviserings AADDS102: det gick inte att hitta tjänstens huvud namn

### <a name="alert-message"></a>Aviserings meddelande

*Ett huvud namn för tjänsten som krävs för att Azure AD Domain Services ska fungera korrekt har tagits bort från Azure AD-katalogen. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, uppdatera och synkronisera din hanterade domän.*

Om ett obligatoriskt huvud namn för tjänsten tas bort kan Azure-plattformen inte utföra automatiserade hanterings uppgifter. Den hanterade domänen kan inte tillämpa uppdateringar eller göra säkerhets kopior på rätt sätt.

### <a name="check-for-missing-service-principals"></a>Sök efter saknade tjänst huvud namn

Följ stegen nedan om du vill kontrol lera vilket tjänst huvud namn som saknas och måste återskapas:

1. I Azure Portal väljer du **Azure Active Directory** i navigerings menyn till vänster.
1. Välj **företags program**. Välj *alla program* på list menyn **program typ** och välj sedan **Använd**.
1. Sök efter vart och ett av följande program-ID: n. Om det inte finns något befintligt program följer du *lösnings* stegen för att skapa tjänstens huvud namn eller registrera namn området igen.

    | Program-ID | Lösning |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Återskapa ett tjänst huvud namn som saknas](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Registrera Microsoft. AAD-namnrymden igen](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Registrera Microsoft. AAD-namnrymden igen](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Registrera Microsoft. AAD-namnrymden igen](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Återskapa ett tjänst huvud namn som saknas

Om program-ID *2565bd9d-DA50-47d4-8b85-4c97f669dc36* saknas i Azure AD-katalogen använder du Azure AD PowerShell för att utföra följande steg. Mer information finns i [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Om det behövs installerar du Azure AD PowerShell-modulen och importerar den på följande sätt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Återskapa nu tjänstens huvud namn med cmdleten [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal] :

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Den hanterade domänens hälsa uppdateras automatiskt inom två timmar och aviseringen tas bort.

### <a name="re-register-the-microsoft-aad-namespace"></a>Registrera Microsoft AAD-namnrymden igen

Om program-ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022* eller *d87dcbc6-a371-462e-88e3-28ad15ec4e64* saknas i Azure AD-katalogen utför du följande steg för att registrera *Microsoft. AAD* -resurs leverantören på nytt:

1. Sök efter och välj **prenumerationer** i Azure Portal.
1. Välj den prenumeration som är kopplad till din hanterade domän.
1. Välj **resurs leverantörer** i det vänstra navigerings fältet.
1. Sök efter *Microsoft. AAD* och välj sedan **registrera igen**.

Den hanterade domänens hälsa uppdateras automatiskt inom två timmar och aviseringen tas bort.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Varning AADDS105: programmet för Lösenordssynkronisering är inaktuellt

### <a name="alert-message"></a>Aviserings meddelande

*Tjänstens huvud namn med program-ID: t "d87dcbc6-a371-462e-88e3-28ad15ec4e64" togs bort och återskapades. Rekreationet lämnar inkonsekventa behörigheter på Azure AD Domain Services resurser som behövs för att underhålla din hanterade domän. Synkronisering av lösen ord på din hanterade domän kan påverkas.*

Azure AD DS synkroniserar automatiskt användar konton och autentiseringsuppgifter från Azure AD. Om det finns ett problem med Azure AD-programmet som används för den här processen Miss lyckas synkroniseringen av autentiseringsuppgifter mellan Azure AD DS och Azure AD.

### <a name="resolution"></a>Lösning

Om du vill återskapa Azure AD-programmet som används för synkronisering av autentiseringsuppgifter använder du Azure AD PowerShell för att utföra följande steg. Mer information finns i [Installera Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Om det behövs installerar du Azure AD PowerShell-modulen och importerar den på följande sätt:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Ta nu bort det gamla programmet och objektet med hjälp av följande PowerShell-cmdletar:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

När du har tagit bort båda programmen återskapas automatiskt Azure-plattformen och försök görs att återuppta Lösenordssynkronisering. Den hanterade domänens hälsa uppdateras automatiskt inom två timmar och aviseringen tas bort.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
