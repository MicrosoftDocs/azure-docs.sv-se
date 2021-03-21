---
title: Azure Active Directory-programproxy och Tableau | Microsoft Docs
description: Lär dig hur du använder Azure Active Directory (Azure AD) Application Proxy för att tillhandahålla fjärråtkomst för din Tableau-distribution.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6720a5ad963bc73e11ef7b46150e946521928c01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258633"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory-programproxy och Tableau 

Azure Active Directory-programproxy och Tableau har samarbetat för att säkerställa att du enkelt kan använda programproxy för att tillhandahålla fjärråtkomst för din Tableau-distribution. Den här artikeln förklarar hur du konfigurerar det här scenariot.  

## <a name="prerequisites"></a>Förutsättningar 

Scenariot i den här artikeln förutsätter att du har:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) har kon figurer ATS. 

- En [Application Proxy-anslutning](application-proxy-add-on-premises-application.md) är installerad. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Aktivera Application Proxy för Tableau 

Application Proxy stöder OAuth 2,0-tilldelnings flödet, vilket krävs för att Tableau ska fungera korrekt. Det innebär att det inte längre finns några särskilda steg som krävs för att aktivera det här programmet, förutom att konfigurera det genom att följa publicerings stegen nedan.


## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 

Om du vill publicera Tableau måste du publicera ett program i Azure-portalen.

Så här blir det för följande:

- Detaljerade anvisningar om steg 1-8 finns i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md). 
- Information om hur du hittar Tableau-värden för App proxy-fälten finns i Tableau-dokumentationen.  

**Så här publicerar du din app**: 


1. Logga in på [Azure Portal](https://portal.azure.com) som program administratör. 

2. Välj **Azure Active Directory > företags program**. 

3. Välj **Lägg till** överst på bladet. 

4. Välj **lokalt program**. 

5. Fyll i de obligatoriska fälten med information om din nya app. Använd följande vägledning för inställningarna: 

    - **Intern URL**: det här programmet bör ha en intern URL som är själva TABLEAU-URL: en. Till exempel `https://adventure-works.tableau.com`. 

    - **Metod för förautentisering**: Azure Active Directory (rekommenderas men krävs inte). 

6. Välj **Lägg till** överst på bladet. Programmet läggs till och snabb starts menyn öppnas. 

7. I snabb starts menyn väljer du **tilldela en användare för testning** och lägger till minst en användare i programmet. Kontrol lera att det här test kontot har åtkomst till det lokala programmet. 

8. Välj **tilldela** för att spara test användar tilldelningen. 

9. Valfritt På sidan hantering av appar väljer du **enkel inloggning**. Välj **integrerad Windows-autentisering** på den nedrullningsbara menyn och fyll i de obligatoriska fälten baserat på din Tableau-konfiguration. Välj **Spara**. 

 

## <a name="testing"></a>Testning 

Ditt program är nu redo att testas. Få åtkomst till den externa URL som du använde för att publicera Tableau och logga in som en användare som är tilldelad till båda programmen.



## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD-programproxy finns i [så här ger du säker fjärråtkomst till lokala program](application-proxy.md).

