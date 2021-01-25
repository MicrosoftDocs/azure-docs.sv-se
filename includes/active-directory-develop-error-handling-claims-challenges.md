---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760692"
---
## <a name="conditional-access-and-claims-challenges"></a>Utmaningarna för villkorlig åtkomst och anspråk

När token hämtas i bakgrunden kan ditt program få fel när en [utmanings utmaning för anspråk](../articles/active-directory/develop/v2-conditional-access-dev-guide.md) , till exempel MFA-princip, krävs av ett API som du försöker få åtkomst till.

Mönstret för att hantera det här felet är att interaktivt hämta en token med hjälp av MSAL. Detta frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen för villkorlig åtkomst.

I vissa fall när du anropar ett API som kräver villkorlig åtkomst kan du få en anspråks utmaning i fel meddelandet från API: et. Om den villkorliga åtkomst principen till exempel är att ha en hanterad enhet (Intune), kommer felet att vara något som liknar [AADSTS53000: din enhet måste hanteras för att få åtkomst till den här resursen](../articles/active-directory/develop/reference-aadsts-error-codes.md) eller något liknande. I det här fallet kan du skicka anspråken i det Hämta token-anropet så att användaren uppmanas att uppfylla den aktuella principen.
