---
title: Åtgärda risker och avblockera användare i Azure AD Identity Protection
description: Lär dig mer om alternativen du stänger aktiva risk identifieringar.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e322f78468c89a549955a01f73952e8cde7a13c8
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872832"
---
# <a name="remediate-risks-and-unblock-users"></a>Åtgärda risker och avblockera användare

När du har slutfört [undersökningen](howto-identity-protection-investigate-risk.md)ska du vidta åtgärder för att åtgärda risken eller avblockera användare. Organisationer har också möjlighet att aktivera automatisk reparation med hjälp av deras [risk principer](howto-identity-protection-configure-risk-policies.md). Organisationer bör försöka stänga alla risk identifieringar som de presenteras med under en tids period som din organisation är van vid. Microsoft rekommenderar att du stänger händelser så snart som möjligt, eftersom det är dags att samar beta med risk.

## <a name="remediation"></a>Åtgärder

Alla aktiva risk identifieringar bidrar till beräkningen av ett värde som kallas användar risk nivå. Användar risk nivån är en indikator (låg, medel, hög) för sannolikheten att ett konto har komprometterats. Som administratör vill du få alla risk identifieringar stängda, så att de berörda användarna inte längre är utsatta för risk.

Vissa risk identifieringar kan markeras med identitets skydd som "stängt (system)" eftersom händelserna inte längre bedöms vara riskfyllda.

Administratörer har följande alternativ för att åtgärda:

- Själv reparation med risk policy
- Manuell lösen ords återställning
- Ignorera användar risk
- Stänga enskilda risk identifieringar manuellt

### <a name="self-remediation-with-risk-policy"></a>Själv reparation med risk policy

Om du tillåter att användare själv åtgärdar, med Azure AD Multi-Factor Authentication (MFA) och lösen ords återställning via självbetjäning (SSPR) i risk principerna, kan de avblockera sig själva när risken upptäcks. Dessa identifieringar betraktas sedan som stängda. Användare måste tidigare ha registrerats för Azure AD MFA och SSPR för att kunna använda när risken identifieras.

Vissa identifieringar kan inte öka risken för att en användare själv åtgärds åtgärd krävs, men administratörer bör fortfarande utvärdera dessa identifieringar. Administratörer kan fastställa att ytterligare åtgärder krävs som att [blockera åtkomst från platser](../conditional-access/howto-conditional-access-policy-location.md) eller minska den acceptabla risken i sina principer.

### <a name="manual-password-reset"></a>Manuell lösen ords återställning

Om det inte finns något alternativ för att återställa lösen ord med en användar risk princip kan administratörer stänga alla risk identifieringar för en användare med manuell lösen ords återställning.

Administratörer får två alternativ när de återställer ett lösen ord för sina användare:

- **Generera ett tillfälligt lösen ord** – genom att skapa ett tillfälligt lösen ord kan du omedelbart flytta tillbaka en identitet till ett säkert tillstånd. Den här metoden kräver att du kontaktar berörda användare eftersom de måste veta vad det tillfälliga lösen ordet är. Eftersom lösen ordet är tillfälligt uppmanas användaren att ändra lösen ordet till något nytt vid nästa inloggning.

- **Kräv att användaren vill återställa lösen ord** – som kräver att användarna återställer lösen ord aktiverar själv återställning utan att kontakta supportavdelningen eller en administratör. Den här metoden gäller endast för användare som är registrerade för Azure AD MFA och SSPR. Det här alternativet är inte tillgängligt för användare som inte har registrerats.

### <a name="dismiss-user-risk"></a>Ignorera användar risk

Om det inte finns något alternativ för lösen ords återställning, till exempel om användaren har tagits bort, kan du välja att ignorera identifieringar av användar risker.

När du klickar på **Ignorera användar risk** stängs alla händelser och den berörda användaren är inte längre utsatt för risk. Men eftersom den här metoden inte påverkar det befintliga lösen ordet får den inte den relaterade identiteten tillbaka till ett säkert tillstånd. 

### <a name="close-individual-risk-detections-manually"></a>Stänga enskilda risk identifieringar manuellt

Du kan stänga enskilda risk identifieringar manuellt. Genom att stänga risk identifieringar manuellt kan du sänka användar risk nivån. Normalt stängs risk identifieringen manuellt som svar på en relaterad undersökning. Om du till exempel pratar med en användare visar att det inte krävs någon aktiv risk identifiering. 
 
När du stänger risk identifieringar manuellt kan du välja att vidta någon av följande åtgärder för att ändra status för en risk identifiering:

- Bekräfta komprometterad användare
- Ignorera användar risk
- Bekräfta inloggning på ett säkert sätt
- Bekräfta att inloggningen är komprometterad

## <a name="unblocking-users"></a>Avblockera användare

En administratör kan välja att blockera en inloggning baserat på deras risk policy eller utredningar. Ett block kan inträffa baserat på inloggnings-eller användar risk.

### <a name="unblocking-based-on-user-risk"></a>Avblockera baserat på användar risk

För att avblockera ett konto som blockeras på grund av användar risk, har administratörerna följande alternativ:

1. **Återställ lösen ord** – du kan återställa användarens lösen ord.
1. **Ignorera användar risk** – användar risk principen blockerar en användare om den konfigurerade användar risk nivån för blockerad åtkomst har nåtts. Du kan minska risken för en användare genom att stänga av användar risker eller manuellt stänga rapporterade identifieringar av risker.
1. **Exkludera användaren från princip** – om du tror att den aktuella konfigurationen av din inloggnings princip orsakar problem för specifika användare kan du undanta användarna från den. Mer information finns i avsnittet undantag i artikeln [How to: Configure and Enable risk policies](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Inaktivera princip** – om du tror att princip konfigurationen orsakar problem för alla dina användare kan du inaktivera principen. Mer information finns i artikeln [How to: Configure and Enable risk policies](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Avblockera baserat på inloggnings risk

Administratörer har följande alternativ för att avblockera ett konto baserat på inloggnings risk:

1. **Logga in från en välbekant plats eller enhet** – en vanlig orsak till blockerade misstänkta inloggningar är inloggnings försök från okända platser eller enheter. Användarna kan snabbt avgöra om den här orsaken är den spärrnings orsaken genom att försöka logga in från en välbekant plats eller enhet.
1. **Exkludera användaren från princip** – om du tror att den aktuella konfigurationen av din inloggnings princip orsakar problem för specifika användare kan du undanta användarna från den. Mer information finns i avsnittet undantag i artikeln [How to: Configure and Enable risk policies](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Inaktivera princip** – om du tror att princip konfigurationen orsakar problem för alla dina användare kan du inaktivera principen. Mer information finns i artikeln [How to: Configure and Enable risk policies](howto-identity-protection-configure-risk-policies.md).

## <a name="powershell-preview"></a>PowerShell-förhandsgranskning

Med hjälp av Microsoft Graph PowerShell SDK Preview-modulen kan organisationer hantera risker med PowerShell. Förhands gransknings moduler och exempel kod finns i [Azure AD GitHub-lagrings platsen](https://github.com/AzureAD/IdentityProtectionTools). 

`Invoke-AzureADIPDismissRiskyUser.ps1`Skriptet som ingår i lagrings platsen gör det möjligt för organisationer att stänga av alla riskfyllda användare i sin katalog.

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection, se [Översikt över Azure AD Identity Protection](overview-identity-protection.md).
