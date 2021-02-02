---
title: Vad är enkel inloggning i Azure?
description: Lär dig hur enkel inloggning (SSO) fungerar med Azure Active Directory. Använd SSO så att användarna inte behöver komma ihåg lösen ord för varje program. Använd också SSO för att förenkla administrationen av konto hantering.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 36025a5a68e4c3d0de1d6b11a13df656c72745d4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260205"
---
# <a name="what-is-single-sign-on-sso"></a>Vad är enkel inloggning (SSO)?

Enkel inloggning innebär att en användare inte behöver logga in på varje program de använder. Användaren loggar in en gång och den autentiseringsuppgiften används även för andra appar.

Om du är en slutanvändare behöver du förmodligen inte mer information om SSO. Du vill bara använda appar som gör dig produktiv utan att behöva ange ditt lösen ord så mycket. Du kan hitta dina appar på: https://myapps.microsoft.com .
 
Om du är administratör eller IT-proffs kan du läsa vidare för att lära dig mer om SSO och hur det implementeras i Azure.

## <a name="single-sign-on-basics"></a>Grundläggande information om enkel inloggning
Enkel inloggning är ett stort steg framåt i hur användare loggar in och använder program. Autentisering med enkel inloggning kallas ofta "modern autentisering". Modern autentisering och enkel inloggning ingår i en kategori med data behandling som kallas för identitets-och åtkomst hantering (IAM). Ta en titt på den här videon för att förstå vad som gör enkel inloggning möjlig.

Grundläggande autentisering: grunderna | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Enkel inloggning med webb program
Webb program är otroligt populära. Webbappar är värdar för olika företag och görs tillgängliga som en tjänst. Några populära exempel på webbappar är Microsoft 365, GitHub och Salesforce, och det finns tusentals andra. Personer har åtkomst till webbappar med hjälp av en webbläsare på sin dator. Enkel inloggning gör det möjligt för personer att navigera mellan olika webbappar utan att behöva logga in flera gånger.

Om du vill veta mer om hur enkel inloggning fungerar med webbappar kan du kolla in dessa två videor.

Grundläggande autentisering: webb program | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Grundläggande autentisering: enkel inloggning på webben | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Molnet jämfört med lokala värdbaserade appar
Hur du implementerar enkel inloggning beror på var appen finns. Värd tjänster på grund av hur nätverks trafiken dirigeras för att få åtkomst till appen. Om en app finns och nås via ditt lokala nätverk, som kallas en lokal app, så behöver användarna inte komma åt Internet för att använda appen. Om appen finns någon annan stans, kallas en molnbaserad app i molnet, måste användarna ha åtkomst till Internet för att kunna använda appen.

> [!TIP]
> Molnbaserade appar kallas även SaaS-appar (program vara som en tjänst). 

Enkel inloggning för appar som är värd för molnet är enkelt. Du låter identitets leverantören veta att den används för appen. Och sedan konfigurerar du appen så att den litar på identitets leverantören. Information om hur du använder Azure AD som identitets leverantör för en app finns i [snabb starts serien för program hantering](add-application-portal.md).

> [!TIP]
> Villkoren Cloud och Internet används ofta utbytbara. Anledningen till detta är att göra med nätverks diagram. Det är vanligt att känneteckna stora dator nätverk med en moln form i ett diagram eftersom det inte är möjligt att rita varje komponent. Internet är det mest välkända nätverket och därför är det enkelt att använda de termer som är utbytbara. Alla dator nätverk kan dock vara myntade ett moln.

Du kan också använda enkel inloggning för lokala baserade appar. Tekniken för att göra lokala SSO-filer kallas programproxy. Mer information finns i [alternativ för enkel inloggning](sso-options.md).

## <a name="multiple-identity-providers"></a>Flera identitets leverantörer
När du ställer in enkel inloggning för att arbeta mellan flera identitets leverantörer kallas den för federation. Ta en titt på den här videon om du vill veta hur federationen fungerar.

Grundläggande autentisering: Federation | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Nästa steg
* [Snabb starts serie för program hantering](view-applications-portal.md)
* [Alternativ för enkel inloggning](sso-options.md)
