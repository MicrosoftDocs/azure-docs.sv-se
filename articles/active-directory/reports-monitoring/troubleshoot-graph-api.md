---
title: Felsöka fel i API för Azure Active Directory rapportering | Microsoft Docs
description: 'Ger en lösning på fel när du anropar Azure Active Directory rapporterings-API: er.'
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: abc8badf261e631dd6ceb7af9a6a0cb3676ae25d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017601"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Felsöka fel i Azure Active Directory rapporterings-API

Den här artikeln innehåller vanliga fel meddelanden som du kan köra i när du får åtkomst till aktivitets rapporter med hjälp av Microsoft Graph API och steg för deras lösning.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP internt Server fel vid åtkomst till Microsoft Graph v2-slutpunkt

Vi stöder för närvarande inte Microsoft Graph v2-slutpunkten – se till att få åtkomst till aktivitets loggarna med hjälp av Microsoft Graph v1-slutpunkten.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: ingen klient organisation är B2C eller klienten har ingen Premium-licens

Åtkomst till inloggnings rapporter kräver en licens för Azure Active Directory Premium 1 (P1). Om du ser det här fel meddelandet vid åtkomst till inloggningar kontrollerar du att klienten är licensierad med en Azure AD P1-licens.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fel: användaren har inte de tillåtna rollerna 

Om du ser det här fel meddelandet när du försöker få åtkomst till gransknings loggar eller inloggningar med hjälp av API: et kontrollerar du att ditt konto är en del av rollen **säkerhets läsare** eller **rapport läsare** i Azure Active Directory klient organisationen. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: appen saknar AAD-behörighet för Läs katalog data 

Följ stegen i [kraven för att få åtkomst till Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md) för att se till att programmet körs med rätt behörighets uppsättning. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fel: programmet saknar Microsoft Graph API: et Läs alla Gransknings logg data behörighet

Följ stegen i [kraven för att få åtkomst till Azure Active Directory rapporterings-API: et](howto-configure-prerequisites-for-reporting-api.md) för att se till att programmet körs med rätt behörighets uppsättning. 

## <a name="next-steps"></a>Nästa steg

[Använda gransknings-API-referensen](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
 [Använd rapport-API-referens för inloggnings aktivitet](/graph/api/resources/signin?view=graph-rest-beta)