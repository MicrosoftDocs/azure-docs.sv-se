---
title: Anslut Azure Active Directory data till Azure Sentinel | Microsoft Docs
description: Lär dig att samla in data från Azure Active Directory och strömma inloggnings loggar för Azure AD och gransknings loggar i Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 409a316bd9c4222dd9b8ff30e42e37d23805c38b
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757770"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Anslut data från Azure Active Directory (Azure AD)

Du kan använda Azure Sentinels inbyggda koppling för att samla in data från [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) och strömma dem till Azure Sentinel. Med anslutnings programmet kan du strömma [inloggnings loggar](../active-directory/reports-monitoring/concept-sign-ins.md) och [gransknings loggar](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Krav

- Alla Azure AD-licenser (ledig/O365/P1/P2) räcker för att mata in inloggnings loggar i Azure Sentinel. Ytterligare per GB-kostnader kan tillkomma för Azure Monitor (Log Analytics) och Azure Sentinel.

- Användaren måste tilldelas rollen Azure Sentinel Contributor på arbets ytan.

- Användaren måste tilldelas rollen som global administratör eller säkerhets administratör på den klient som du vill strömma loggarna från.

- Användaren måste ha läs-och Skriv behörighet till Azure AD-diagnostikinställningar för att kunna se anslutnings status. 

## <a name="connect-to-azure-active-directory"></a>Anslut till Azure Active Directory

1. I Azure Sentinel väljer du **data kopplingar** på navigerings menyn.

1. I data kopplings galleriet väljer du **Azure Active Directory** och väljer sedan **Öppna kopplings sida**.

1. Markera kryss rutorna bredvid de logg typer som du vill strömma till i Azure Sentinel och klicka på **Anslut**. Detta är de logg typer som du kan välja mellan:

    - **Inloggnings loggar**: information om användningen av hanterade program och användar inloggnings aktiviteter.
    - **Gransknings loggar**: system aktivitets information om användar-och grupp hantering, hanterade program och katalog aktiviteter.
    - **Inloggnings loggar som inte är interaktiva**: information om inloggningar som utförs av en klient åt en användare, vilket inte kräver någon interaktion eller autentisering från användaren.
    - **Inloggnings loggar för tjänstens huvud namn**: information om inloggningar av appar och tjänst huvud namn som inte omfattar någon användare. I dessa inloggningar ger appen eller tjänsten en autentiseringsuppgift för det egna ombudet att autentisera eller komma åt resurser.
    - **Inloggnings loggar för hanterade identiteter**: inloggningar av Azure-resurser som har hemligheter som hanteras av Azure.
    - **Etablerings loggar**: system aktivitets information om användare, grupper och roller som tillhandahålls av Azure AD Provisioning-tjänsten.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **LogManagement** i följande tabeller:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Om du vill fråga Azure AD-loggarna anger du det relevanta tabell namnet överst i frågefönstret.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Active Directory till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
