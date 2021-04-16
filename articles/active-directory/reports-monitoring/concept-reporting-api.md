---
title: Kom igång med Azure AD Reporting API-| Microsoft Docs
description: Så här kommer du igång med API:Azure Active Directory rapportering
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e5a095c87e46839c7c120bdd6d8db1595164e57
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532517"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Kom igång med API:Azure Active Directory reporting

Azure Active Directory innehåller en mängd olika rapporter [som](overview-reports.md)innehåller användbar information för program som SIEM-system, gransknings- och business intelligence verktyg. 

Genom att använda Microsoft Graph API för Azure AD-rapporter kan du få programmatisk åtkomst till data via en uppsättning REST-baserade API:er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Den här artikeln innehåller en översikt över rapporterings-API:et, inklusive olika sätt att komma åt det.

Om du får problem kan du se [hur du kan få support för Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).

## <a name="prerequisites"></a>Förutsättningar

För att få åtkomst till rapporterings-API:et, med eller utan åtgärd från användaren, måste du:

1. Tilldela roller (säkerhetsläsare, säkerhetsadministratör, global administratör)
2. Registrera ett program
3. Bevilja behörigheter
4. Samla in konfigurationsinställningar

Detaljerade anvisningar finns i förutsättningarna [för att komma åt Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>API-slutpunkter 

Slutpunkten Microsoft Graph API:et för `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` granskningsloggar är Microsoft Graph API-slutpunkten för inloggningar är `https://graph.microsoft.com/v1.0/auditLogs/signIns` . Mer information finns i referensen för [gransknings-API:et](/graph/api/resources/directoryaudit) [och inloggnings-API:et.](/graph/api/resources/signIn)

Du kan använda [API:et för riskidentifiering i Identity Protection](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) för att få programmatisk åtkomst till säkerhetsidentifiering med hjälp av Microsoft Graph. Mer information finns i [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md). 
  
Du kan också använda [API:et för etableringsloggar](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) för att få programmatisk åtkomst till etableringshändelser i din klientorganisation. 

## <a name="apis-with-microsoft-graph-explorer"></a>API:er med Microsoft Graph Explorer

Du kan använda Microsoft Graph [explorer för](https://developer.microsoft.com/graph/graph-explorer) att verifiera dina inloggnings- och gransknings-API-data. Se till att logga in på ditt konto med båda inloggningsknapparna i användargränssnittet för Graph Explorer och ange **auditLog.Read.All-** och **Directory.Read.All-behörigheter** för din klient enligt bilden.   

![Graph-testaren](./media/concept-reporting-api/graph-explorer.png)

![Ändra användargränssnittet för behörigheter](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Använda certifikat för att få åtkomst till Azure AD-rapporterings-API:et 

Använd Azure AD Reporting-API:et med certifikat om du planerar att hämta rapportdata utan inblandning av användaren.

Detaljerade anvisningar finns i Hämta [data med hjälp av Azure AD Reporting API med certifikat.](tutorial-access-api-with-certificates.md)

## <a name="next-steps"></a>Nästa steg

 * [Krav för att få åtkomst till rapporterings-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Hämta data med hjälp av Azure AD Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
 * [Felsöka fel i Azure AD Reporting API](troubleshoot-graph-api.md)