---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98674245"
---
## <a name="add-a-user-journey"></a>Lägg till en användar resa 

I det här läget har identitets leverantören kon figurer ATS, men den är inte tillgänglig ännu på någon av inloggnings sidorna. Om du inte har en egen egen användar resa skapar du en dubblett av en befintlig användar resa för användare, annars fortsätter du till nästa steg. 

1. Öppna *TrustFrameworkBase.xml* -filen från start paketet.
1. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"` .
1. Öppna *TrustFrameworkExtensions.xml* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
1. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
1. Byt namn på användar resans ID. Till exempel `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Lägg till identitets leverantören till en användar resa 

Nu när du har en användar resa lägger du till den nya identitets leverantören till användar resan. Först lägger du till en inloggnings knapp och länkar sedan knappen till en åtgärd. Åtgärden är den tekniska profil som du skapade tidigare.

1. Hitta steg elementet för Orchestration som innehåller `Type="CombinedSignInAndSignUp"` eller `Type="ClaimsProviderSelection"` i användar resan. Det är vanligt vis det första Orchestration-steget. **ClaimsProviderSelections** -elementet innehåller en lista över identitets leverantörer som en användare kan logga in med. Element ordningen styr ordningen för de inloggnings knappar som visas för användaren. Lägg till ett **ClaimsProviderSelection** XML-element. Ange värdet för **TargetClaimsExchangeId** till ett eget namn.

1. I nästa Orchestration-steg lägger du till ett **ClaimsExchange** -element. Ange **ID: t** till värdet för mål anspråkets utbytes-ID. uppdatera värdet för **TechnicalProfileReferenceId** till ID: t för den tekniska profil som du skapade tidigare.

Följande XML visar de första två Dirigerings stegen för en användar resa med identitets leverantören: