---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760673"
---
Den här artikeln ger en översikt över de olika typerna av fel och rekommendationer för att hantera vanliga inloggnings fel.

## <a name="msal-error-handling-basics"></a>Grundläggande fel hantering för MSAL

Undantag i Microsoft Authentication Library (MSAL) är avsedda för att appar ska kunna felsöka, inte för att visa för slutanvändare. Undantags meddelanden är inte lokaliserade.

När du bearbetar undantag och fel kan du använda själva undantags typen och felkoden för att skilja mellan undantag.  En lista över felkoder finns i [fel koder för Azure AD-autentisering och-auktorisering](../articles/active-directory/develop/reference-aadsts-error-codes.md).

Under inloggningen kan du stöta på fel om medgivanden, villkorlig åtkomst (MFA, enhets hantering, platsbaserade begränsningar), utfärdande och inlösen av token och användar egenskaper.

I följande avsnitt finns mer information om fel hantering för din app.