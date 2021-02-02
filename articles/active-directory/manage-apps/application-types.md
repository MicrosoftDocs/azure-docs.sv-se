---
title: Visa appar med hjälp av din Azure Active Directory-klient för identitets hantering
description: Lär dig hur du visar alla program med hjälp av din Azure Active Directory-klient för identitets hantering.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 443e8c83ec29f9f0478e3881b9e6d6a8eb0bf403
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259794"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Visa appar med hjälp av din Azure AD-klient för identitets hantering
[Snabb starts serien i program hantering](view-applications-portal.md) guidar dig grunderna. I det här avsnittet får du lära dig hur du visar alla appar som använder Azure AD-klienten för identitets hantering. Den här artikeln dykningar en bit djupare i de typer av appar som du hittar.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Varför visas ett särskilt program i listan Mina program?
När de filtreras till **alla program**, visar **listan** **alla program** alla tjänst huvud namn objekt i din klient organisation. Tjänstens huvud objekt kan visas i den här listan på olika sätt:
- När du lägger till ett program från program galleriet, inklusive:
   - **Azure AD-Enterprise-program** – appar som har lagts till i din klient med alternativet **företags program** på Azure AD-portalen. Vanligt vis integrerade appar med hjälp av SAML-standarden.
   - **Azure AD-Appregistreringar** – appar som läggs till i din klient med hjälp av alternativet **APPREGISTRERINGAR** på Azure AD-portalen. Vanligt vis anpassade utvecklade appar med Open-ID Connect och OAuth-standarder.
   - Program för **programproxy** – ett program som körs i din lokala miljö som du vill tillhandahålla säker enkel inloggning till externt
- När du registrerar dig för eller loggar in på, är program från tredje part integrerad med Azure Active Directory. Ett exempel är [Smartsheet](https://app.smartsheet.com/b/home) eller [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- Microsoft-appar som Microsoft 365.
- När du lägger till en ny program registrering genom att skapa ett anpassat program som har utvecklats med [program registret](../develop/quickstart-register-app.md)
- När du lägger till en ny program registrering genom att skapa ett anpassat program som har utvecklats med [v 2.0-program registrerings portalen](../develop/quickstart-register-app.md)
- När du lägger till ett program utvecklar du med hjälp av Visual Studios [ASP.net-autentiseringsmetoder](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) eller [anslutna tjänster](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- När du skapar ett huvud namns objekt för tjänsten med hjälp av [Azure AD PowerShell-modulen](/powershell/azure/active-directory/install-adv2)
- När du [samtycker till ett program](../develop/howto-convert-app-to-be-multi-tenant.md) som administratör för att använda data i din klient organisation
- När en [användare samtycker till ett program](../develop/howto-convert-app-to-be-multi-tenant.md) för att använda data i din klient organisation
- När du aktiverar vissa tjänster som lagrar data i din klient organisation. Ett exempel är lösen ords återställning, som modelleras som tjänstens huvud namn för att lagra din princip för lösen ords återställning på ett säkert sätt.

Läs mer om hur och varför program läggs till i din katalog, se [hur program läggs till i Azure AD](../develop/active-directory-how-applications-are-added.md).

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
