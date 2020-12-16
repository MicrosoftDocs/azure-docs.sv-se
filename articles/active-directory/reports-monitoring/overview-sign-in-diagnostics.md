---
title: Vad är inloggnings diagnostik i Azure AD? | Microsoft Docs
description: Innehåller en allmän översikt över inloggnings diagnostiken i Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6aedf41fbf1ed0d70467a2efe97431fdecaa4fa
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585928"
---
# <a name="what-is-sign-in-diagnostic-in-azure-ad"></a>Vad är inloggnings diagnostik i Azure AD?

Azure AD ger dig en flexibel säkerhets modell som styr vad användarna kan göra med de hanterade resurserna. Åtkomst till dessa resurser kontrol leras inte bara av **vem** du är, utan även av **hur** du kommer åt dem. Flexibiliteten ger vanligt vis en viss komplexitets grad på grund av antalet konfigurations alternativ som du har. Komplexiteten ger möjlighet att öka risken för fel.

Som IT-administratör behöver du en lösning som ger dig rätt nivå av insikter om aktiviteterna i systemet så att du enkelt kan diagnostisera och lösa problem när de uppstår. Inloggnings diagnostiken för Azure AD är ett exempel på en sådan lösning. Använd diagnostiken för att analysera vad som hände under en inloggning och vilka åtgärder du kan vidta för att lösa problem utan att behöva delta i Microsofts support.

Den här artikeln ger en översikt över vad lösningen gör och hur du kan använda den.


## <a name="requirements"></a>Krav

Den diagnostiska inloggningen är tillgänglig i alla versioner av Azure AD.<br> Du måste vara global administratör i Azure AD för att kunna använda den.

## <a name="how-it-works"></a>Så här fungerar det

I Azure AD är svaret på ett inloggnings försök kopplat till **vem** du är och **hur** du får åtkomst till din klient. Som administratör kan du till exempel konfigurera alla aspekter av din klient organisation när du loggar in från företags nätverket. Du kan dock vara även blockerad när du loggar in med samma konto från ett ej betrott nätverk.
 
På grund av den större flexibiliteten i systemet för att svara på ett inloggnings försök, kan det hända att du får fel söknings scenarier där du behöver felsöka inloggningar. Den inloggade diagnostiken är en funktion som:

- Analyserar data från inloggningar. 

- Visar vad som hände och rekommendationer om hur du löser problem. 

Den diagnostiska inloggningen för Azure AD är utformad för att möjliggöra självdiagnostik av inloggnings fel. För att slutföra diagnos processen måste du:

![Process för inloggnings diagnostik](./media/overview-sign-in-diagnostics/process.png)
 
1. **Definiera** omfånget för de inloggnings händelser du bryr dig om

2. **Välj** den inloggning som du vill granska

3. **Granska** det diagnostiska resultatet

4. **Vidta** åtgärder

 
### <a name="define-scope"></a>Definiera omfång

Målet med det här steget är att definiera omfånget för de inloggningar som du vill undersöka. Ditt omfång är antingen baserat på en användare eller identifierare (correlationId, requestId) och ett tidsintervall. Om du vill begränsa omfånget ytterligare kan du också ange ett namn på appen. I Azure AD används omfattnings informationen för att hitta rätt händelser.  

### <a name="select-sign-in"></a>Välj inloggning  

Baserat på dina Sök villkor hämtar Azure AD alla matchande inloggningar och visar dem i en lista med sammanfattning av autentisering. 

![Sammanfattning av autentisering](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
Du kan anpassa de kolumner som visas i den här vyn.

### <a name="review-diagnostic"></a>Granska diagnostik 

För den valda inloggnings händelsen ger Azure AD dig ett diagnostiskt resultat. 

![Diagnostiska resultat](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
Resultatet börjar med en utvärdering. Utvärderingen förklarar några få meningar vad som hände. Förklaringen hjälper dig att förstå hur systemet fungerar. 

Som nästa steg får du en översikt över de relaterade principerna för villkorlig åtkomst som tillämpades på den valda inloggningen. Den här delen slutförs med hjälp av rekommenderade åtgärder för att lösa problemet. Eftersom det inte alltid är möjligt att lösa problem utan ytterligare hjälp kan ett rekommenderat steg vara att öppna ett support ärende. 

### <a name="take-action"></a>Vidta åtgärd 
Nu bör du ha den information du behöver för att åtgärda problemet.


## <a name="scenarios"></a>Scenarier

Det här avsnittet ger en översikt över de scenarier som omfattas av diagnostik. Följande scenarier implementeras: 
 
- Blockeras av villkorlig åtkomst

- Villkorlig åtkomst misslyckades

- MFA från villkorlig åtkomst

- MFA från andra krav

- MFA-korrektur krävs

- MFA-korrekturet krävs, men användar inloggnings försöket är inte från en säker plats

- Lyckad inloggning


### <a name="blocked-by-conditional-access"></a>Blockeras av villkorlig åtkomst

Det här scenariot baseras på en inloggning som har blockerats av en princip för villkorlig åtkomst.

![Blockera åtkomst](./media/overview-sign-in-diagnostics/block-access.png)

Diagnostic-avsnittet i det här scenariot visar information om användar inloggning och tillämpade principer.


### <a name="failed-conditional-access"></a>Villkorlig åtkomst misslyckades

Det här scenariot är vanligt vis ett resultat av en inloggning som misslyckades på grund av att kraven för en princip för villkorlig åtkomst inte uppfylldes. Vanliga exempel:

![Kräv kontroller](./media/overview-sign-in-diagnostics/require-controls.png)

- Kräv hybrid Azure AD-ansluten enhet

- Kräv godkänd klientapp

- Kräva appskyddsprincip   


Diagnostic-avsnittet i det här scenariot visar information om användar inloggning och tillämpade principer.


### <a name="mfa-from-conditional-access"></a>MFA från villkorlig åtkomst

Det här scenariot baseras på en princip för villkorlig åtkomst som har kravet på inloggning med Multi-Factor Authentication set.

![Kräv Multi-Factor Authentication](./media/overview-sign-in-diagnostics/require-mfa.png)

Diagnostic-avsnittet i det här scenariot visar information om användar inloggning och tillämpade principer.



### <a name="mfa-from-other-requirements"></a>MFA från andra krav

Det här scenariot baseras på ett krav för Multi-Factor Authentication som inte tillämpats av en princip för villkorlig åtkomst. Till exempel Multi-Factor Authentication per användare.


![Kräv Multi-Factor Authentication per användare](./media/overview-sign-in-diagnostics/mfa-per-user.png)


Syftet med det här diagnostiska scenariot är att ge mer information om:

- Källan för Multi-Factor Authentication-avbrottet. 
- Resultatet av klient interaktionen.

Dessutom ger det här avsnittet också all information om användar inloggnings försöket. 


### <a name="mfa-proof-up-required"></a>MFA-korrektur krävs

Det här scenariot baseras på inloggningar som avbrutits av förfrågningar för att konfigurera Multi-Factor Authentication. Den här konfigurationen kallas även "proofing".

Autentisering av Multi-Factor Authentication sker när en användare måste använda Multi-Factor Authentication men inte har konfigurerat den ännu, eller så har en administratör konfigurerat användaren för att konfigurera den.

Avsikten med det här diagnostiska scenariot är att ge insikter om att Multi-Factor Authentication-avbrott var att ställa in det och för att ge rekommendationen att slutföra korrekturet.

### <a name="mfa-proof-up-required-from-a-risky-sign-in"></a>MFA-korrektur som krävs från en riskfylld inloggning

Det här scenariot resulterar i inloggningar som avbrutits av en begäran om att konfigurera Multi-Factor Authentication från en riskfylld inloggning. 

Avsikten med det här diagnostiska scenariot är att ge insikter om att Multi-Factor Authentication-avbrott var att ställa in det, för att ge rekommendationen att låta användaren slutföra korrekturet, men att göra det från en nätverks plats som inte ser riskfylld ut. Om ett företags nätverk till exempel har definierats som en namngiven plats försöker att göra korrekturet uppåt från företags nätverket i stället.


### <a name="successful-sign-in"></a>Lyckad inloggning

Det här scenariot baseras på inloggningar som inte har avbrutits av villkorlig åtkomst eller Multi-Factor Authentication.

Avsikten med det här diagnostiska scenariot är att ge insikter om vad användaren angav under inloggningen, om det finns en princip för villkorlig åtkomst eller principer som förväntades gälla, eller en konfigurerad Multi-Factor Authentication som förväntades avbryta användar inloggningen.



## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Active Directory-rapporter?](overview-reports.md)
* [Vad är Azure Active Directory-övervakning?](overview-monitoring.md)
