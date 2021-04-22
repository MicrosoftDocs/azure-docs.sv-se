---
title: Ansluta Azure Active Directory data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du samlar in data från Azure Active Directory och strömmar Inloggning, granskning och etablering av Azure AD-loggar till Azure Sentinel.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: d2cfc2d592c24cf5d15a489ea4bde36ea3c2f863
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872379"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Ansluta Azure Active Directory (Azure AD)-data till Azure Sentinel

Du kan använda Azure Sentinel inbyggda anslutningsapp för att samla in data [från Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) och strömma dem till Azure Sentinel. Med anslutningsappen kan du strömma följande loggtyper:

- [**Inloggningsloggar**](../active-directory/reports-monitoring/concept-all-sign-ins.md), som innehåller information [om interaktiva användarloggar där](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) en användare tillhandahåller en autentiseringsfaktor.

    Azure AD-anslutningsappen innehåller nu följande tre ytterligare kategorier av inloggningsloggar, som för närvarande är i **FÖRHANDSVERSION:**
    
    - [**Inloggningsloggar för icke-interaktiva**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)användare , som innehåller information om inloggningar som utförs av en klient för en användares räkning utan interaktion eller autentiseringsfaktor från användaren.
    
    - [**Inloggningsloggar för tjänstens**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)huvudnamn, som innehåller information om inloggningar av appar och tjänstens huvudnamn som inte omfattar någon användare. I dessa inloggningar tillhandahåller appen eller tjänsten autentiseringsuppgifter för egen räkning för att autentisera eller komma åt resurser.
    
    - [**Inloggningsloggar för hanterad**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)identitet , som innehåller information om inloggningar av Azure-resurser som har hemligheter som hanteras av Azure. Mer information finns i Vad [är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Granskningsloggar**](../active-directory/reports-monitoring/concept-audit-logs.md), som innehåller information om systemaktivitet som rör användar- och grupphantering, hanterade program och katalogaktiviteter.

- [**Etableringsloggar**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (även **i FÖRHANDSVERSION**), som innehåller information om systemaktivitet om användare, grupper och roller som etablerats av Azure AD-etableringstjänsten. 

> [!IMPORTANT]
> Som vi nämnt ovan är vissa av de tillgängliga loggtyperna för närvarande i **FÖRHANDSVERSION.** Se kompletterande [användningsvillkor för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Microsoft Azure-förhandsversioner för ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som ännu inte har släppts i allmän tillgänglighet.
## <a name="prerequisites"></a>Förutsättningar

- En Azure Active Directory P1- eller P2-licens krävs för att mata in inloggningsloggar i Azure Sentinel. Alla Azure AD-licenser (Free/O365/P1/P2) räcker för att mata in de andra loggtyperna. Ytterligare avgifter per gigabyte kan tillkomma för Azure Monitor (Log Analytics) och Azure Sentinel.

- Användaren måste tilldelas rollen Azure Sentinel deltagare på arbetsytan.

- Användaren måste tilldelas rollerna Global administratör eller Säkerhetsadministratör på den klientorganisation som du vill strömma loggarna från.

- Användaren måste ha läs- och skrivbehörighet till Azure AD-diagnostikinställningarna för att kunna se anslutningsstatusen. 

## <a name="connect-to-azure-active-directory"></a>Ansluta till Azure Active Directory

1. I Azure Sentinel du **Datakopplingar** från navigeringsmenyn.

1. I galleriet med dataanslutningsappar väljer **Azure Active Directory** sedan Öppna **anslutningssidan.**

1. Markera kryssrutorna bredvid de loggtyper som du vill strömma till Azure Sentinel (se ovan) och klicka på **Anslut**.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **Loggar** under **avsnittet LogManagement** i följande tabeller:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Om du vill fråga Azure AD-loggarna anger du relevant tabellnamn överst i frågefönstret.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Active Directory till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur [du får insyn i dina data och potentiella hot.](quickstart-get-visibility.md)
- Kom igång [med att identifiera hot Azure Sentinel](tutorial-detect-threats-built-in.md).
