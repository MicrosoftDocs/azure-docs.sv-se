---
title: Vad är enkel inloggning (SSO) i Azure?
description: Lär dig hur enkel inloggning (SSO) fungerar med Azure Active Directory. Använd enkel inloggning så att användarna inte behöver komma ihåg lösenord för varje program. Använd också enkel inloggning för att förenkla administrationen av kontohantering.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 65e4b32c7c41eea6a885a09dd7ad772980595a9e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379510"
---
# <a name="what-is-single-sign-on-sso"></a>Vad är enkel inloggning (SSO)?

Enkel inloggning innebär att en användare inte behöver logga in på alla program som de använder. Användaren loggar in en gång och autentiseringssuppgifter används även för andra appar.

Om du är slutanvändare bryr du dig förmodligen inte så mycket om SSO-information. Du vill bara använda de appar som gör att du är produktiv utan att behöva skriva in lösenordet så mycket. Du hittar dina appar på: https://myapps.microsoft.com .
 
Om du är administratör eller IT-proffs kan du läsa vidare för att lära dig mer om enkel inloggning och hur den implementeras i Azure.

## <a name="single-sign-on-basics"></a>Grundläggande om enkel inloggning
Enkel inloggning ger ett stort steg framåt i hur användare loggar in och använder program. System för enkel inloggning kallas ofta för "modern autentisering". Modern autentisering och enkel inloggning tillhör en kategori för databehandling som kallas identitets- och åtkomsthantering (IAM). Om du vill förstå vad som gör enkel inloggning möjligt kan du titta på den här videon.

Grundläggande om autentisering: Grunderna | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Enkel inloggning med webbprogram
Webbprogram är otroligt populära. Webbappar finns hos olika företag och görs tillgängliga som en tjänst. Några populära exempel på webbappar är Microsoft 365, GitHub och Salesforce och det finns tusentals andra. Användare kommer åt webbappar med hjälp av en webbläsare på sin dator. Enkel inloggning gör det möjligt för användare att navigera mellan de olika webbapparna utan att behöva logga in flera gånger.

Mer information om hur enkel inloggning fungerar med webbappar finns i dessa två videor.

Grundläggande om autentisering: Webbprogram | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Grundläggande om autentisering: Enkel inloggning för | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Molnbaserade och lokala värdbaserade appar
Hur du implementerar enkel inloggning beror på var appen finns. Värdtjänster är viktiga på grund av hur nätverkstrafiken dirigeras för att få åtkomst till appen. Om en app finns och nås via ditt lokala nätverk, vilket kallas en lokal app, behöver användarna inte komma åt Internet för att använda appen. Om appen finns någon annanstans, en så kallad molnbaserad app, måste användarna komma åt Internet för att kunna använda appen.

> [!TIP]
> Molnbaserade appar kallas även SaaS-appar (Programvara som en tjänst). 

Enkel inloggning för molnbaserade appar är enkelt. Du låter identitetsprovidern veta att den används för appen. Sedan konfigurerar du appen så att den litar på identitetsprovidern. Information om hur du använder Azure AD som identitetsprovider för en app finns i [Snabbstartsserie om programhantering.](add-application-portal.md)

> [!TIP]
> Termerna moln och Internet används ofta utbytbara. Orsaken till detta har att göra med nätverksdiagram. Det är vanligt att ange stora datornätverk med en molnform i ett diagram eftersom det inte är möjligt att rita varje komponent. Internet är det mest välkända nätverket och därför är det enkelt att använda termerna synonymt. Alla datornätverk kan dock myntas i ett moln.

Du kan också använda enkel inloggning för lokala appar. Tekniken för att göra lokal enkel inloggning sker kallas för Programproxy. Mer information om det finns [i Alternativ för enkel inloggning.](sso-options.md)

## <a name="multiple-identity-providers"></a>Flera identitetsproviders
När du ställer in enkel inloggning för att fungera mellan flera identitetsproviders kallas det federation. Om du vill lära dig hur federation fungerar kan du titta på den här videon.

Grundläggande om autentisering: | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Nästa steg
* [Snabbstartsserie om programhantering](view-applications-portal.md)
* [Alternativ för enkel inloggning](sso-options.md)
