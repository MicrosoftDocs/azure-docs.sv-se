---
title: Åtkomst Azure AD App-proxy-appar i team | Microsoft Docs
description: Använd Azure AD-programproxy för att få åtkomst till ditt lokala program via Microsoft Teams.
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
ms.date: 09/05/2017
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c44716f045340022c871501609cf582015ba20f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256617"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Få åtkomst till dina lokala program via Microsoft Teams

Azure Active Directory-programproxy ger dig enkel inloggning till lokala program oavsett var du befinner dig. Microsoft Teams effektiviserar dina samarbets insatser på ett och samma ställe. Att integrera de två tillsammans innebär att användarna kan vara produktiva med sina arbets kamrater i alla situationer.

Användarna kan lägga till molnappar till sina team kanaler [med hjälp av flikar](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), men vad finns det för SharePoint-webbplatser eller planerings verktyg som finns lokalt? Application Proxy är lösningen. De kan lägga till appar som publicerats via programproxyn till deras kanaler med samma externa URL: er som de alltid använder för att fjärrans luta till sina appar. Och eftersom programproxyn autentiseras via Azure Active Directory får användarna en enda inloggnings upplevelse.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Installera Application Proxy Connector och publicera din app

Om du inte redan har gjort det [konfigurerar du programproxyn för din klient organisation och installerar anslutnings programmet](application-proxy-add-on-premises-application.md). Publicera sedan [ditt lokala program](application-proxy-add-on-premises-application.md) för fjärråtkomst. När du publicerar appen ska du anteckna den externa URL: en eftersom den används för att lägga till appen i Teams.

Om du redan har dina appar publicerade, men inte kommer ihåg sina externa URL: er, kan du se dem i [Azure Portal](https://portal.azure.com). Logga in och gå sedan till **Azure Active Directory**  >  **företags program**  >  **alla program** > välj din app > **Application Proxy**.

## <a name="add-your-app-to-teams"></a>Lägg till din app i Teams

När du har publicerat appen via Application Proxy kan användarna se att de kan lägga till den som en flik direkt i deras team kanaler, och sedan är appen tillgänglig för alla i teamet som ska använda den. Följ dessa tre steg:

1. Navigera till den Team kanal där du vill lägga till den här appen och välj **+** att lägga till en flik.

   ![Välj + om du vill lägga till en flik i Teams](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Välj **webbplats** från fliken Alternativ.

   ![Välj webbplats på skärmen Lägg till en flik](./media/application-proxy-integrate-with-teams/website.png)

1. Ge fliken ett namn och ange URL: en till den externa URL: en för programproxyn.

   ![Namnge fliken och Lägg till den externa URL: en](./media/application-proxy-integrate-with-teams/tab-name-url.png)

När en medlem i ett team lägger till fliken visas den för alla i kanalen. Alla användare som har åtkomst till appen får enkel inloggning med de autentiseringsuppgifter de använder för Microsoft Teams. Användare som inte har åtkomst till appen kan se fliken i Teams, men blockeras tills du ger dem behörighet till den lokala appen och den Azure Portal publicerade versionen av appen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [publicerar lokala SharePoint-webbplatser](application-proxy-integrate-with-sharepoint-server.md) med Application Proxy.
- Konfigurera apparna att använda [anpassade domäner](application-proxy-configure-custom-domain.md) för deras externa URL.
