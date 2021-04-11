---
title: Så här skapar du en kostnads fri Azure Active Directory Developer-klient
description: Den här artikeln visar hur du skapar ett Developer-konto
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1894ffb7ca75a283e3a74d17c3e73de51fc46d8d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170248"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Så här skapar du en kostnads fri Azure Active Directory Developer-klient

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> I för hands versionen krävs en P2-licens. 

Det finns två enkla sätt att skapa en kostnads fri Azure Active Directory med en P2 Trial-licens så att du kan installera tjänsten för verifierbara autentiseringsuppgifter och du kan testa att skapa och verifiera verifierbara autentiseringsuppgifter:

- [Delta](https://aka.ms/o365devprogram) i det kostnads fria Microsoft 365 Developer-programmet och få en kostnads fri sandbox, verktyg och andra resurser som en Azure Active Directory med P2-licenser. Konfigurerade användare, grupper, post lådor osv.
- Skapa en ny [klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) och aktivera en [kostnads fri utvärderings version](https://azure.microsoft.com/trial/get-started-active-directory/) av Azure AD Premium P1 eller P2 i din nya klient.

Om du väljer att registrera dig för det kostnads fria Microsoft 365 Developer-programmet måste du följa några enkla steg:

1. Klicka på knappen Anslut nu på skärmen

2. Logga in med ett nytt Microsoft-konto eller Använd ett befintligt (arbets) konto som du redan har.

3. På sidan registrering väljer du din region, anger ett företags namn och accepterar villkoren i programmet innan du klickar på nästa

4. Klicka på Konfigurera prenumeration. Ange den region där du vill skapa din nya klient, skapa ett användar namn, en domän och ange ett lösen ord. Då skapas en ny klient organisation och den första administratören av klienten

5. Ange den säkerhets information som krävs för att skydda administratörs kontot för den nya innehavaren. Detta ställer in MFA-autentisering för kontot


Nu har du skapat en klient med 25 E5-användar licenser. E5-licenserna omfattar Azure AD P2-licenser. Du kan också lägga till exempel data paket med användare, grupper, e-post och SharePoint för att hjälpa dig att testa i utvecklings miljön. De är inte obligatoriska för tjänsten för att utfärda verifierbara autentiseringsuppgifter.

För din bekvämlighet kan du lägga till ditt eget arbets konto som [gäst](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) i den nya klienten och använda det kontot för att administrera klient organisationen. Om du vill att gäst kontot ska kunna hantera tjänsten för verifierbar behörighet måste du tilldela rollen global administratör till användaren.

## <a name="next-steps"></a>Nästa steg

Nu när du har ett konto för utvecklare kan du prova vår [första självstudie](get-started-verifiable-credentials.md) för att lära dig mer om verifierbara autentiseringsuppgifter.