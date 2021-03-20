---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78187019"
---
## <a name="ropc-flow-notes"></a>ROPC Flow-anteckningar
I Azure Active Directory B2C (Azure AD B2C) stöds följande alternativ:

- **Inbyggd klient**: användar interaktion under autentisering sker när kod körs på en enhet på användar sidan. Enheten kan vara ett mobilt program som körs i ett inbyggt operativ system, till exempel Android och iOS.
- **Offentligt klient flöde**: endast användarautentiseringsuppgifter som samlats in av ett program skickas i API-anropet. Autentiseringsuppgifterna för programmet skickas inte.
- **Lägg till nya anspråk**: innehållet i ID-token kan ändras för att lägga till nya anspråk.

Följande flöden stöds inte:

- **Server-till-Server**: identitets skydds systemet behöver en tillförlitlig IP-adress som samlats in från anroparen (den interna klienten) som en del av interaktionen. I ett API-anrop på Server sidan används bara serverns IP-adress. Om ett dynamiskt tröskelvärde för misslyckade autentiseringar överskrids, kan identitets skydds systemet identifiera en upprepad IP-adress som en angripare.
- **Konfidentiellt klient flöde**: programmets klient-ID är verifierat, men program hemligheten är inte verifierad.

Tänk på följande när du använder ROPC-flödet:

- ROPC fungerar inte om det sker ett avbrott i det autentiseringsschema som kräver användar interaktion. Till exempel, när ett lösen ord har upphört att gälla eller behöver ändras, krävs Multi-Factor Authentication eller när mer information måste samlas in under inloggningen (till exempel användar medgivande).
- ROPC stöder endast lokala konton. Användare kan inte logga in med federerade identitets leverantörer som Microsoft, Google +, Twitter, AD-FS eller Facebook.
- Hantering av sessioner, inklusive Håll mig inloggad (KMSI avgör), är inte tillämpligt.
