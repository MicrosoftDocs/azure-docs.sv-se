---
title: Anslut Azure Active Directory data till Azure Sentinel | Microsoft Docs
description: Lär dig att samla in data från Azure Active Directory och strömma inloggnings-, gransknings-och etablerings loggar i Azure AD i Azure Sentinel.
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
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99251989"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Anslut Azure Active Directory (Azure AD)-data till Azure Sentinel

Du kan använda Azure Sentinels inbyggda koppling för att samla in data från [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) och strömma dem till Azure Sentinel. Med anslutnings programmet kan du strömma följande logg typer:

- [**Inloggnings loggar**](../active-directory/reports-monitoring/concept-all-sign-ins.md)som innehåller information om [interaktiva användar inloggningar](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) där en användare tillhandahåller en autentiseringsnivå.

    Azure AD Connector innehåller nu följande tre ytterligare kategorier med inloggnings loggar, som för närvarande finns i för **hands version**:
    
    - [**Inloggnings loggar som inte är interaktiva**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), som innehåller information om inloggningar som utförs av en klient för en användares räkning utan någon interaktion eller autentisering från användaren.
    
    - [**Inloggnings loggar för tjänstens huvud namn**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)som innehåller information om inloggningar av appar och tjänst huvud namn som inte omfattar någon användare. I dessa inloggningar ger appen eller tjänsten en autentiseringsuppgift för det egna ombudet att autentisera eller komma åt resurser.
    
    - [**Inloggnings loggar för hanterade identiteter**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), som innehåller information om inloggningar av Azure-resurser som har hemligheter som hanteras av Azure. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Gransknings loggar**](../active-directory/reports-monitoring/concept-audit-logs.md), som innehåller information om system aktivitet som rör användar-och grupp hantering, hanterade program och katalog aktiviteter.

- [**Etablerings loggar**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (även i för **hands version**), som innehåller system aktivitets information om användare, grupper och roller som tillhandahålls av Azure AD Provisioning-tjänsten. 

> [!IMPORTANT]
> Som anges ovan är några av de tillgängliga logg typerna för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.
## <a name="prerequisites"></a>Förutsättningar

- Alla Azure AD-licenser (ledig/O365/P1/P2) räcker för att mata in inloggnings loggar i Azure Sentinel. Ytterligare per GB-kostnader kan tillkomma för Azure Monitor (Log Analytics) och Azure Sentinel.

- Användaren måste tilldelas rollen Azure Sentinel Contributor på arbets ytan.

- Användaren måste tilldelas rollen som global administratör eller säkerhets administratör på den klient som du vill strömma loggarna från.

- Användaren måste ha läs-och Skriv behörighet till Azure AD-diagnostikinställningar för att kunna se anslutnings status. 

## <a name="connect-to-azure-active-directory"></a>Anslut till Azure Active Directory

1. I Azure Sentinel väljer du **data kopplingar** på navigerings menyn.

1. I data kopplings galleriet väljer du **Azure Active Directory** och väljer sedan **Öppna kopplings sida**.

1. Markera kryss rutorna bredvid de logg typer som du vill strömma till Azure Sentinel (se ovan) och klicka på **Anslut**.

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
