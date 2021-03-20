---
title: Enkel inloggning till appar med Azure AD-programproxy | Microsoft Docs
description: Aktivera enkel inloggning för dina publicerade lokala program med Azure AD-programproxy i Azure Portal.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0cb2830c019635e9020a4b586bdc370450fddb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254010"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Lösen ords valv för enkel inloggning med programproxy

Azure Active Directory-programproxy hjälper dig att förbättra produktiviteten genom att publicera lokala program så att fjärranslutna anställda kan komma åt dem på ett säkert sätt. I Azure Portal kan du också konfigurera enkel inloggning (SSO) till de här apparna. Användarna behöver bara autentisera med Azure AD och de kan komma åt ditt företags program utan att behöva logga in igen.

Application Proxy stöder flera [lägen för enkel inloggning](sso-options.md#choosing-a-single-sign-on-method). Lösenordsbaserad inloggning är avsedd för program som använder en kombination av användar namn/lösen ord för autentisering. När du konfigurerar lösenordsbaserad inloggning för ditt program måste användarna logga in till det lokala programmet en gång. Därefter lagrar Azure Active Directory inloggnings informationen och skickar den automatiskt till programmet när användarna kommer åt det via fjärr anslutning.

Du bör redan ha publicerat och testat din app med Application Proxy. Annars följer du stegen i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md) och kommer tillbaka hit.

## <a name="set-up-password-vaulting-for-your-application"></a>Konfigurera lösen ords valv för ditt program

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Välj **Azure Active Directory**  >  **företags program**  >  **alla program**.
1. I listan väljer du den app som du vill konfigurera med SSO.  
1. Välj **programproxy**. 
1. Ändra **typen för autentisering** till **passthrough** och välj **Spara**. Senare kan du växla tillbaka till **Azure Active Directory** typ igen! 
1. Välj **enkel inloggning**.

   ![Välj enkel inloggning på appens översikts sida](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. I SSO-läge väljer du **lösenordsbaserad inloggning**.
1. För inloggnings-URL: en anger du webb adressen till sidan där användarna anger sitt användar namn och lösen ord för att logga in på din app utanför företags nätverket. Detta kan vara den externa URL som du skapade när du publicerade appen via programproxy.

   ![Välj lösenordsbaserad inloggning och ange din URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Välj **Spara**.
1. Välj **programproxy**. 
1. Ändra **typen för förautentisering** till **Azure Active Directory** och välj **Spara**. 
1. Välj **användare och grupper**.
1. Tilldela användare till programmet med att välja **Lägg till användare**. 
1. Om du vill fördefiniera autentiseringsuppgifter för en användare markerar du kryss rutan framför användar namnet och väljer **uppdatera autentiseringsuppgifter**.
1. Välj **Azure Active Directory**  >  **Appregistreringar**  >  **alla program**.
1. I listan väljer du den app som du konfigurerade med lösen ordet SSO.
1. Välj **anpassning**. 
1. Uppdatera **Start sidans URL** med **inloggnings-URL:** en från inloggnings sidan för lösen ord och välj **Spara**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testa appen

Gå till portalen Mina appar. Logga in med dina autentiseringsuppgifter (eller autentiseringsuppgifterna för ett test konto som du har konfigurerat med åtkomst). När du har loggat in klickar du på ikonen för appen. Detta kan utlösa installationen av tillägget Mina appar säker inloggnings webbläsare. Om din användare hade fördefinierade autentiseringsuppgifter ska autentiseringen till appen ske automatiskt, annars måste du ange användar namnet eller lösen ordet för första gången. 

## <a name="next-steps"></a>Nästa steg

- Läs om andra sätt att implementera [enkel inloggning](what-is-single-sign-on.md)
- Lär dig mer om [säkerhets överväganden för att komma åt appar via fjärr anslutning med Azure AD-programproxy](application-proxy-security.md)
