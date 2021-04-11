---
title: Azure AD App proxy och Qlik Sense | Microsoft Docs
description: Aktivera Application Proxy i Azure Portal och installera anslutningarna för den omvända proxyn.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a03f3c57d3fbc7efe20055343eab3db33aa8194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259092"
---
# <a name="application-proxy-and-qlik-sense"></a>Application Proxy och Qlik Sense 
Azure Active Directory-programproxy och Qlik Sense har samarbetat för att se till att du enkelt kan använda Application Proxy för att ge fjärråtkomst till din Qlik Sense-distribution.  

## <a name="prerequisites"></a>Förutsättningar 
Resten av det här scenariot förutsätter att du har utfört följande:
 
- Konfigurerat [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Installerat en Application Proxy-koppling](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 
Om du vill publicera QlikSense måste du publicera två program i Azure.  

### <a name="application-1"></a>Program #1: 
Följ de här stegen för att publicera din app. En mer detaljerad genom gång av steg 1-8 finns i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md). 


1. Logga in på Azure Portal som global administratör. 
2. Välj **Azure Active Directory**  >  **företags program**. 
3. Välj **Lägg till** överst på bladet. 
4. Välj **lokalt program**. 
5. Fyll i de obligatoriska fälten med information om din nya app. Använd följande vägledning för inställningarna: 
   - **Intern URL**: det här programmet bör ha en intern URL som är själva QLIKSENSE-URL: en. Till exempel **https&#58;//demo.qlikemm.com:4244** 
   - **Metod för förautentisering**: Azure Active Directory (rekommenderas men krävs inte) 
1. Välj **Lägg till** längst ned på bladet. Programmet läggs till och snabb starts menyn öppnas. 
2. I snabb starts menyn väljer du **tilldela en användare för testning** och lägger till minst en användare i programmet. Kontrol lera att det här test kontot har åtkomst till det lokala programmet. 
3. Välj **tilldela** för att spara test användar tilldelningen. 
4. Valfritt På bladet program hantering väljer du enkel inloggning. Välj **Kerberos-begränsad delegering** på den nedrullningsbara menyn och fyll i de obligatoriska fälten baserat på din Qlik-konfiguration. Välj **Spara**. 

### <a name="application-2"></a>Program #2: 
Följ samma steg som för program #1, med följande undantag: 

**Steg #5**: den interna URL: en ska nu vara QLIKSENSE-URL: en med den port för autentisering som används av programmet. Standardvärdet är **4244** för HTTPS och **4248** för http för QlikSense-versioner före april 2018. Standardvärdet för QlikSense-versioner efter 2018 april är **443** för HTTPS och **80** för http.  T. ex. **https&#58;//demo.Qlik.com:4244**</br></br>
**Steg #10:** Konfigurera inte SSO och lämna **enkel inloggning inaktive rad**
 
 
## <a name="testing"></a>Testning 
Ditt program är nu redo att testas. Få åtkomst till den externa URL som du använde för att publicera QlikSense i program #1 och logga in som en användare som är tilldelad till båda programmen.  

## <a name="additional-references"></a>Ytterligare referenser
Mer information om hur du publicerar Qlik Sense med Application Proxy finns i följande Qlik community-artiklar: 
- [Azure AD med integrerad Windows-autentisering med en Kerberos-begränsad delegering med Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense-integrering med Azure AD-programproxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Nästa steg

- [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md)
- [Arbeta med Application Proxy-kopplingar](application-proxy-connector-groups.md)

