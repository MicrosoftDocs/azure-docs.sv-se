---
title: Integrera lokala appar med Cloud App Security – Azure AD
description: Konfigurera ett lokalt program i Azure Active Directory att fungera med Microsoft Cloud App Security (MCAS). Använd MCAS-Appkontroll för villkorsstyrd åtkomst för att övervaka och kontrol lera sessioner i real tid baserat på principer för villkorlig åtkomst. Du kan använda dessa principer för lokala program som använder Application Proxy i Azure Active Directory (Azure AD).
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/19/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139f347a3d1bce7a8f841cd1c39cd250b9a2182d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259363"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurera övervakning av program åtkomst i real tid med Microsoft Cloud App Security och Azure Active Directory
Konfigurera ett lokalt program i Azure Active Directory (Azure AD) för att använda Microsoft Cloud App Security (MCAS) för övervakning i real tid. MCAS använder Appkontroll för villkorsstyrd åtkomst för att övervaka och kontrol lera sessioner i real tid baserat på principer för villkorlig åtkomst. Du kan använda dessa principer för lokala program som använder Application Proxy i Azure Active Directory (Azure AD).

Här följer några exempel på de typer av principer som du kan skapa med MCAS:

- Blockera eller skydda nedladdningen av känsliga dokument på ohanterade enheter.
- Övervaka när användare med hög risk loggar in på program och sedan loggar in sina åtgärder inifrån sessionen. Med den här informationen kan du analysera användar beteendet för att avgöra hur du ska tillämpa arbetsköprinciper.
- Använd klient certifikat eller enhets kompatibilitet för att blockera åtkomst till vissa program från ohanterade enheter.
- Begränsa användarsessioner från icke-företags nätverk. Du kan ge begränsad åtkomst till användare som har åtkomst till ett program utanför företagets nätverk. Denna begränsade åtkomst kan till exempel hindra användaren från att hämta känsliga dokument.

Mer information finns i [Skydda appar med Microsoft Cloud App Security appkontroll för villkorsstyrd åtkomst](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Krav

Licensavtalet

- EMS E5-licens, eller 
- Azure Active Directory Premium P1 och MCAS standalone.

Lokalt program:

- Det lokala programmet måste använda Kerberos-begränsad delegering (KCD)

Konfigurera Application Proxy:

- Konfigurera Azure AD för att använda programproxy, inklusive förbereda din miljö och installera Application Proxy Connector. En själv studie kurs finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Lägg till lokalt program i Azure AD

Lägg till ett lokalt program i Azure AD. En snabb start finns i [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). När du lägger till programmet måste du ange följande två inställningar i bladet **Lägg till ditt lokala program** :

- **Förautentisering**: ange **Azure Active Directory**.
- **Översätt URL: er i program texten**: Välj **Ja**.

De här två inställningarna krävs för att programmet ska fungera med MCAS.

## <a name="test-the-on-premises-application"></a>Testa det lokala programmet

När du har lagt till ditt program i Azure AD kan du använda stegen i [testa programmet](application-proxy-add-on-premises-application.md#test-the-application) för att lägga till en användare för testning och testa inloggningen. 

## <a name="deploy-conditional-access-app-control"></a>Distribuera Appkontroll för villkorsstyrd åtkomst

Om du vill konfigurera ditt program med program kontrollen för villkorlig åtkomst följer du anvisningarna i [distribuera program kontroll för villkorlig åtkomst för Azure AD-appar](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Test Appkontroll för villkorsstyrd åtkomst

Om du vill testa distributionen av Azure AD-program med program kontroll för villkorlig åtkomst följer du anvisningarna i [testa distributionen av Azure AD-appar](/cloud-app-security/proxy-deployment-aad).





