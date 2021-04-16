---
title: Visa appar med din Azure Active Directory för identitetshantering
description: Förstå hur du visar alla program med hjälp Azure Active Directory klientorganisationen för identitetshantering.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: b11f2dab97b3c45502c6050e06f0f39312f8c3c0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378082"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Visa appar med din Azure AD-klientorganisation för identitetshantering
[Snabbstartsserien om programhantering](view-applications-portal.md) vägled dig grunderna. I den får du lära dig hur du visar alla appar med hjälp av din Azure AD-klientorganisation för identitetshantering. Den här artikeln går djupare in på vilka typer av appar du hittar.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Varför visas ett specifikt program i listan med alla mina program?
När du filtrerar **på Alla program** visar listan Alla **program** **alla** objekt för tjänstens huvudnamn i din klientorganisation. Objekt för tjänstens huvudnamn kan visas i den här listan på olika sätt:
- När du lägger till ett program från programgalleriet, inklusive:
   - **Azure AD – Företagsprogram** – Appar som läggs till i din klientorganisation med hjälp **av alternativet** Företagsprogram i Azure AD-portalen. Appar som vanligtvis är integrerade med HJÄLP av SAML-standarden.
   - **Azure AD – Appregistreringar –** Appar som läggs till i din **klientorganisation med Appregistreringar** på Azure AD-portalen. Vanligtvis anpassade utvecklade appar med hjälp av Open ID Connect- och OAuth-standarder.
   - **Programproxy Program** – ett program som körs i din lokala miljö och som du vill tillhandahålla säker enkel inloggning till externt
- När du registrerar dig för eller loggar in på ett tredjepartsprogram integrerat med Azure Active Directory. Ett exempel är [Smartsheet](https://app.smartsheet.com/b/home) eller [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- Microsoft-appar som Microsoft 365.
- När du lägger till en ny programregistrering genom att skapa ett egenutvecklat program med hjälp av [programregistret](../develop/quickstart-register-app.md)
- När du lägger till en ny programregistrering genom att skapa ett egenutvecklat program med [hjälp av V2.0-programregistreringsportalen](../develop/quickstart-register-app.md)
- När du lägger till ett program utvecklar du med hjälp Visual Studio av ASP.NET [autentiseringsmetoder eller](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) [anslutna tjänster](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- När du skapar ett objekt för tjänstens huvudnamn med [hjälp av Azure AD PowerShell-modulen](/powershell/azure/active-directory/install-adv2)
- När du [samtycker till att ett](../develop/howto-convert-app-to-be-multi-tenant.md) program som administratör använder data i din klientorganisation
- När en [användare samtycker till att ett program](../develop/howto-convert-app-to-be-multi-tenant.md) använder data i din klientorganisation
- När du aktiverar vissa tjänster som lagrar data i din klientorganisation. Ett exempel är Lösenordsåterställning, som modelleras som ett huvudnamn för tjänsten för att lagra din princip för lösenordsåterställning på ett säkert sätt.

Läs mer om hur och varför appar läggs till i din katalog i [Hur program läggs till i Azure AD.](../develop/active-directory-how-applications-are-added.md)

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
