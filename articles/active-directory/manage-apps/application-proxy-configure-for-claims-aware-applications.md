---
title: Anspråksmedvetna appar – Azure AD App proxy | Microsoft Docs
description: Publicera lokala ASP.NET-program som accepterar ADFS-anspråk för säker fjärråtkomst av dina användare.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: b90c2d47e70a4f7595ac535d5f8ba9506087eb72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258532"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Arbeta med anspråks medveten appar i Application Proxy
[Anspråksmedvetna appar](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85)) utför en omdirigering till säkerhetstokentjänsten (Security Token Service). STS begär autentiseringsuppgifter från användaren i Exchange för en token och omdirigerar sedan användaren till programmet. Det finns några sätt att aktivera Application Proxy för att arbeta med dessa omdirigeringar. Använd den här artikeln för att konfigurera distributionen för anspråksmedvetna appar. 

## <a name="prerequisites"></a>Förutsättningar
Se till att STS som anspråks medveten app omdirigerar till är tillgängligt utanför ditt lokala nätverk. Du kan göra STS tillgängligt genom att exponera det via en proxyserver eller genom att tillåta externa anslutningar. 

## <a name="publish-your-application"></a>Publicera programmet

1. Publicera programmet enligt instruktionerna som beskrivs i [Publicera program med programproxy](application-proxy-add-on-premises-application.md).
2. Gå till program sidan i portalen och välj **enkel inloggning**.
3. Om du valde **Azure Active Directory** som **Förautentiserings metod** väljer du **enkel inloggning för Azure AD inaktiverat** som din **interna autentiseringsmetod**. Om du väljer **genom strömning** som **metod för förautentisering** behöver du inte ändra något.

## <a name="configure-adfs"></a>Konfigurera ADFS

Du kan konfigurera ADFS för anspråksmedvetna appar på ett av två sätt. Det första är genom att använda anpassade domäner. Den andra är med WS-Federation. 

### <a name="option-1-custom-domains"></a>Alternativ 1: anpassade domäner

Om alla interna URL: er för dina program är fullständigt kvalificerade domän namn (FQDN) kan du konfigurera [anpassade domäner](application-proxy-configure-custom-domain.md) för dina program. Använd de anpassade domänerna för att skapa externa URL: er som är samma som de interna URL: erna. När dina externa URL: er matchar dina interna webb adresser, fungerar STS-omdirigeringen oavsett om dina användare är lokala eller fjärranslutna. 

### <a name="option-2-ws-federation"></a>Alternativ 2: WS-Federation

1. Öppna ADFS-hantering.
2. Gå till **förlitande parts förtroenden**, högerklicka på den app som du publicerar med Application Proxy och välj **Egenskaper**.  

   ![Förtroenden för förlitande part högerklickar på appens namn – skärm bild](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. På fliken **slut punkter** under typ av **slut punkt** väljer du **WS-Federation**.
4. Under **betrodd URL** anger du den URL som du angav i Application Proxy under **extern URL** och klickar på **OK**.  

   ![Lägg till ett slut punkts värde för betrodd URL-skärm bild](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Nästa steg
* [Aktivera inbyggda klient program för att interagera med proxy-program](application-proxy-configure-native-client-application.md)