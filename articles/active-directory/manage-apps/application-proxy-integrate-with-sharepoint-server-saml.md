---
title: Publicera på = lokalt SharePoint med Application Proxy – Azure AD
description: Beskriver grunderna för hur du integrerar en lokal SharePoint-Server med Azure AD-programproxy för SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bca7331722640547218ecb6aff7c3c5651efdfd0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099336"
---
# <a name="integrate-with-sharepoint-saml"></a>Integrera med SharePoint (SAML)

I den här steg-för-steg-guiden beskrivs hur du skyddar åtkomsten till [Azure Active Directory integrerade lokala SharePoint (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial) med Azure AD-programproxy, där användare i din organisation (Azure AD, B2B) ansluter till SharePoint via Internet.

> [!NOTE] 
> Om du är nybörjare på Azure AD-programproxy och vill veta mer, se [fjärråtkomst till lokala program via Azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Det finns tre huvudsakliga fördelar med den här konfigurationen:

- Azure AD-programproxy säkerställer att autentiserad trafik kan komma åt det interna nätverket och SharePoint-servern.
- Dina användare kan komma åt SharePoint-webbplatserna som vanligt utan att använda VPN.
- Du kan styra åtkomsten genom användar tilldelning på Azure AD-programproxys nivå och du kan öka säkerheten med Azure AD-funktioner som villkorlig åtkomst och Multi-Factor Authentication (MFA).

Den här processen kräver två företags program. En är en lokal SharePoint-instans som du publicerar från galleriet till listan över hanterade SaaS-appar. Det andra är ett lokalt program (ej Galleri program) som du använder för att publicera det första Enterprise Gallery-programmet.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här konfigurationen behöver du följande resurser:
 - En SharePoint 2013-Server grupp eller senare. SharePoint-servergruppen måste vara [integrerad med Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial).
 - En Azure AD-klient med en plan som inkluderar programproxy. Lär dig mer om [Azure AD-planer och priser](https://azure.microsoft.com/pricing/details/active-directory/).
 - En [anpassad, verifierad domän](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) i Azure AD-klienten. Den verifierade domänen måste matcha SharePoint-URL-suffixet.
 - Ett SSL-certifikat krävs. Se informationen i [anpassad domän publicering](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
 - Lokala Active Directory användare måste synkroniseras med Azure AD Connect och måste konfigureras för att logga in [på Azure](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin). 
 - För gäst användare med enbart moln och B2B måste du [bevilja åtkomst till ett gäst konto till SharePoint lokalt i Azure Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal).
 - En Application Proxy-koppling installeras och körs på en dator i företags domänen.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Steg 1: integrera SharePoint lokalt med Azure AD 

1. Konfigurera den lokala SharePoint-appen. Mer information finns i [Självstudier: Azure Active Directory integration med enkel inloggning med SharePoint lokalt](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial).
2. Verifiera konfigurationen innan du går vidare till nästa steg. Du kan kontrol lera det genom att försöka komma åt SharePoint lokalt från det interna nätverket och bekräfta att det är tillgängligt internt. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Steg 2: publicera det lokala SharePoint-programmet med Application Proxy

I det här steget skapar du ett program i Azure AD-klienten som använder programproxy. Du anger den externa URL: en och anger den interna URL: en som används senare i SharePoint.

> [!NOTE] 
> De interna och externa URL: erna måste matcha **inloggnings-URL: en** i den SAML-baserade program konfigurationen i steg 1.

   ![Skärm bild som visar värdet för inloggnings-URL.](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Skapa ett nytt Azure AD-programproxy-program med en anpassad domän. Stegvisa instruktioner finns i [anpassade domäner i Azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).

    - Intern URL: https://portal.contoso.com/
    - Extern URL: https://portal.contoso.com/
    - Förautentisering: Azure Active Directory
    - Översätt URL: er i huvuden: Nej
    - Översätt URL: er i program texten: Nej

        ![Skärm bild som visar de alternativ som du använder för att skapa appen.](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Tilldela [samma grupper](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial#create-an-azure-ad-security-group-in-the-azure-portal) som du har tilldelat till det lokala SharePoint Gallery-programmet.

3. Till sist går du till avsnittet **Egenskaper** och anger **Visible för användare?** till **Nej**. Med det här alternativet ser du till att endast ikonen för det första programmet visas på min Apps-portal ( https://myapplications.microsoft.com) .

   ![Skärm bild som visar var du kan ställa in synligheten för användarna? alternativet.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Steg 3: testa programmet

Använd en webbläsare från en dator i ett externt nätverk och navigera till URL: en ( https://portal.contoso.com/) som du konfigurerade under publicerings steget). Kontrol lera att du kan logga in med det test konto som du har konfigurerat.

