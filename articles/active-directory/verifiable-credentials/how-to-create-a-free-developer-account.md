---
title: Så här skapar du en kostnadsfri Azure Active Directory-klientorganisation för utvecklare
description: Den här artikeln visar hur du skapar ett utvecklarkonto
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a50c8b083c1cd453dbe3c51c63ec9cf53859c3bd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587274"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Så här skapar du en kostnadsfri Azure Active Directory-klientorganisation för utvecklare

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> I förhandsversionen krävs en P2-licens. 

Det finns två enkla sätt att skapa en kostnadsfri Azure Active Directory med en P2-utvärderingslicens så att du kan installera tjänsten Verifiable Credential Issuer och du kan testa att skapa och verifiera verifierbara autentiseringsuppgifter:

- [Delta](https://aka.ms/o365devprogram) i det kostnadsfria Microsoft 365 Developer Program och få en kostnadsfri sandbox-miljö, verktyg och andra resurser som en Azure Active Directory med P2-licenser. Konfigurerade användare, grupper, postlådor osv.
- Skapa en ny [klientorganisation](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) och aktivera en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/trial/get-started-active-directory/) Azure AD Premium P1 eller P2 i din nya klientorganisation.

Om du vill registrera dig för det kostnadsfria Microsoft 365-utvecklarprogrammet måste du följa några enkla steg:

1. Klicka på **knappen Anslut** nu på skärmen.

2. Logga in med ett nytt Microsoft-konto eller använd ett befintligt (arbetskonto) som du redan har.

3. På registreringssidan väljer du din region, anger ett företagsnamn och godkänner villkoren för programmet innan du klickar på **Nästa.**

4. Klicka på **Konfigurera prenumeration.** Ange den region där du vill skapa din nya klientorganisation, skapa ett användarnamn, en domän och ange ett lösenord. Då skapas en ny klient och klientorganisationens första administratör.

5. Ange den säkerhetsinformation som behövs för att skydda administratörskontot för den nya klientorganisationen. Detta kommer att konfigurera MFA-autentisering för kontot.


Nu har du skapat en klientorganisation med 25 E5-användarlicenser. E5-licenserna omfattar Azure AD P2-licenser. Du kan också lägga till exempeldatapaket med användare, grupper, e-post och SharePoint för att testa i utvecklingsmiljön. För den utfärdande tjänsten för verifierbara autentiseringsuppgifter krävs de inte.

För att underlätta för dig kan du lägga till ditt eget arbetskonto som [gäst](/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal) i den nyligen skapade klienten och använda det kontot för att administrera klientorganisationen. Om du vill att gästkontot ska kunna hantera tjänsten för verifierbara autentiseringsuppgifter måste du tilldela rollen Global administratör till den användaren.

## <a name="next-steps"></a>Nästa steg

Nu när du har ett utvecklarkonto kan du prova vår första [självstudie för](get-started-verifiable-credentials.md) att lära dig mer om verifierbara autentiseringsuppgifter.
