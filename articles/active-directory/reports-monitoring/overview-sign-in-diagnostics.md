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
ms.openlocfilehash: e113753bee5c0a94fbec47a2ea14b98c1779a394
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578157"
---
# <a name="what-are-sign-in-diagnostics-in-azure-ad"></a>Vad är inloggnings diagnostik i Azure AD?

Azure AD ger dig en flexibel säkerhets modell som styr vad användarna kan göra med de hanterade resurserna. Åtkomst till dessa resurser kontrol leras inte bara av vem du är, utan även av hur du kommer åt dem. Flexibiliteten ger vanligt vis en viss komplexitets grad på grund av antalet konfigurations alternativ som du har. Komplexiteten ger möjlighet att öka risken för fel.

Som IT-administratör behöver du en lösning som ger dig rätt nivå av insikter om aktiviteterna i systemet så att du enkelt kan diagnostisera och lösa problem när de uppstår. Inloggnings-diagnostik för Azure AD är ett exempel på ett sådant verktyg. Använd diagnostiken för att analysera vad som hände under en inloggning och vilka åtgärder du kan vidta för att lösa problem utan att behöva delta i Microsofts support.

Den här artikeln ger en översikt över vad verktyget gör och hur du kan använda det.


## <a name="requirements"></a>Krav

Inloggnings-diagnostiken är tillgänglig i alla versioner av Azure AD.<br> Du måste vara en global administratörs medlem i Azure AD för att kunna använda det här verktyget.

## <a name="how-it-works"></a>Så här fungerar det

I Azure AD är svaret på ett inloggnings försök inte bara knutet till de som du är, utan även hur du får åtkomst till din klient. Som administratör kan du till exempel konfigurera alla aspekter av klienten när du är inloggad från företags nätverket. Du kan dock vara även blockerad när du loggar in med samma konto från ett ej betrott nätverk. På grund av den större flexibiliteten att systemet måste svara på ett inloggnings försök kan du få hjälp i scenarier där du behöver felsöka inloggnings försök. Inloggnings diagnostiken är en funktion som analyserar data från inloggningen och visar vissa meddelanden om vad som hände och rekommendationer om vad som ska göras för att lösa problem. Inloggnings-diagnostik för Azure AD är utformad för att möjliggöra självdiagnostik av inloggnings fel. Diagnostics-processen består av fyra huvud Bygg stenar:

![Process för inloggnings diagnostik](./media/overview-sign-in-diagnostics/process.png)
 
1. **Definiera** omfattningen för de inloggnings händelser du bryr dig om

2. **Välj** händelsen som du vill granska

3. **Granska** diagnostik

4. **Vidta** åtgärder

 
### <a name="define-sign-ins"></a>Definiera inloggningar

Målet med det här steget är att definiera omfånget för inloggnings händelser som du vill undersöka. Ditt omfång är antingen baserat på en användare eller identifierare (correlationId, requestId) och ett tidsintervall. Dessutom kan du också ange ett namn på appen. I Azure AD används omfattnings informationen för att hitta rätt händelser.  

### <a name="select-sign-in-event"></a>Välj inloggnings händelse 

Baserat på dina Sök villkor hämtar Azure AD alla matchande inloggnings händelser och visar dem i en lista över Sammanfattning för autentisering. 

![Sammanfattning av autentisering](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
Du kan anpassa de kolumner som visas i den här vyn.

### <a name="review-diagnosis"></a>Granska diagnostik

För den valda inloggnings händelsen ger Azure AD dig ett diagnostiskt resultat. 

![Diagnostiska resultat](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
Resultatet börjar med en utvärdering. Utvärderingen förklarar några få meningar vad som hände. Förklaringen hjälper dig att förstå hur systemet fungerar. 

Som nästa steg får du en översikt över de relaterade principerna för villkorlig åtkomst som tillämpades på den valda inloggningen. Den här delen slutförs med hjälp av rekommenderade åtgärder för att lösa problemet. Eftersom det inte alltid är möjligt att lösa problem utan ytterligare hjälp kan ett rekommenderat steg vara att öppna ett support ärende. 

### <a name="take-action"></a>Vidta åtgärd 
Nu bör du ha den information du behöver för att åtgärda problemet.


## <a name="scenarios"></a>Scenarier

Det här avsnittet innehåller en översikt över de scenarier som omfattas av diagnostik. Följande scenarier implementeras: 
 
- Blockeras av villkorlig åtkomst

- Villkorlig åtkomst misslyckades

- MFA från villkorlig åtkomst

- MFA från andra krav

- MFA-korrektur krävs

- MFA-korrekturet krävs, men användar inloggnings försöket är inte från en säker plats

- Lyckad inloggning


### <a name="blocked-by-conditional-access"></a>Blockeras av villkorlig åtkomst

Det här scenariot orsakas av en inloggning som har blockerats av en princip för villkorlig åtkomst.

![Blockera åtkomst](./media/overview-sign-in-diagnostics/block-access.png)

Avsnittet Diagnostic visar information om användar inloggning och tillämpade principer.


### <a name="failed-conditional-access"></a>Villkorlig åtkomst misslyckades

Det här scenariot är vanligt vis ett resultat av en inloggning som misslyckades på grund av att kraven för en princip för villkorlig åtkomst inte uppfylldes. Vanliga exempel:

![Kräv kontroller](./media/overview-sign-in-diagnostics/require-controls.png)

- Kräv hybrid Azure AD-ansluten enhet

- Kräv godkänd klientapp

- Kräva appskyddsprincip   


Diagnostiskt visar information om användar inloggningen och principen eller principerna som tillämpades.


### <a name="mfa-from-conditional-access"></a>MFA från villkorlig åtkomst

Det här scenariot orsakas av en princip för villkorlig åtkomst som har kravet på inloggning med Multi-Factor Authentication set.

![Kräv Multi-Factor Authentication](./media/overview-sign-in-diagnostics/require-mfa.png)

Avsnittet Diagnostic visar information om användar inloggning och tillämpade principer.



### <a name="mfa-from-other-requirements"></a>MFA från andra krav

Det här scenariot inträffar när Multi-Factor Authentication inte tillämpades av en princip för villkorlig åtkomst. Du har till exempel konfigurerat Multi-Factor Authentication per användare i din klient.


![Kräv Multi-Factor Authentication per användare](./media/overview-sign-in-diagnostics/mfa-per-user.png)


Syftet med det här diagnostiska scenariot är att ge mer information om:

- Källan för Multi-Factor Authentication-avbrottet. 
- Resultatet av klient interaktionen.

Dessutom ger det här avsnittet också all information om användar inloggnings försöket. 


### <a name="mfa-proof-up-required"></a>MFA-korrektur krävs

Det här scenariot resulterar i inloggningar som avbrutits av förfrågningar för att konfigurera Multi-Factor Authentication. Den här installations processen kallas även "proofing".

Autentisering av Multi-Factor Authentication sker när en användare måste använda Multi-Factor Authentication men aldrig har ställt in den tidigare, eller så har en administratör konfigurerat användaren så att den måste konfigureras.

Avsikten med det här diagnostiska scenariot är att ge insikter om att Multi-Factor Authentication-avbrott var att ställa in det och för att ge rekommendationen att slutföra korrekturet.

### <a name="mfa-proof-up-required-but-user-sign-in-attempt-is-not-from-secure-location"></a>MFA-korrekturet krävs, men användar inloggnings försöket är inte från en säker plats

Det här scenariot resulterar i inloggningar som avbrutits av en begäran om att konfigurera Multi-Factor Authentication, men inloggningen visade sig vara riskfylld. 

Autentisering av Multi-Factor Authentication sker när en användare måste använda Multi-Factor Authentication men aldrig har ställt in den tidigare, eller så har en administratör konfigurerat användaren så att den måste konfigureras.

Avsikten med det här diagnostiska scenariot är att ge insikter om att Multi-Factor Authentication-avbrott var att ställa in det, för att ge rekommendationen att låta användaren slutföra korrekturet, men att göra det från en nätverks plats som inte ser riskfylld ut. Om ett företags nätverk till exempel har definierats som en namngiven plats försöker att göra korrekturet uppåt från företags nätverket i stället.


### <a name="successful-sign-in"></a>Lyckad inloggning

Det här scenariot täcker Azure AD-inloggningen utan avbrott från villkorlig åtkomst eller Multi-Factor Authentication.

Avsikten med det här diagnostiska scenariot är att ge insikter om vad användaren angav under inloggningen, om det finns en princip för villkorlig åtkomst eller principer som förväntades gälla, eller en konfigurerad Multi-Factor Authentication som förväntades avbryta användar inloggningen.



## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Active Directory-rapporter?](overview-reports.md)
* [Vad är Azure Active Directory-övervakning?](overview-monitoring.md)
