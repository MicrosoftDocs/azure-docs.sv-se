---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760673"
---
Den här artikeln ger en översikt över de olika typerna av fel och rekommendationer för att hantera vanliga inloggnings fel.

## <a name="msal-error-handling-basics"></a>Grundläggande fel hantering för MSAL

Undantag i Microsoft Authentication Library (MSAL) är avsedda för att appar ska kunna felsöka, inte för att visa för slutanvändare. Undantags meddelanden är inte lokaliserade.

När du bearbetar undantag och fel kan du använda själva undantags typen och felkoden för att skilja mellan undantag.  En lista över felkoder finns i [fel koder för Azure AD-autentisering och-auktorisering](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Under inloggningen kan du stöta på fel om medgivanden, villkorlig åtkomst (MFA, enhets hantering, platsbaserade begränsningar), utfärdande och inlösen av token och användar egenskaper.

I följande avsnitt finns mer information om fel hantering för din app.