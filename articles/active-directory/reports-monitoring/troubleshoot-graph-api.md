---
title: Felsöka fel i API:Azure Active Directory reporting | Microsoft Docs
description: Ger dig en lösning på fel när du anropar Azure Active Directory Reporting-API:er.
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
ms.openlocfilehash: 445514297550210d80cd50895201d1129fac4f20
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533717"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Felsöka fel i API:Azure Active Directory Reporting

Den här artikeln innehåller vanliga felmeddelanden som du kan få när du använder aktivitetsrapporter med hjälp Microsoft Graph API och steg för deras lösning.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 INTERNT HTTP-serverfel vid åtkomst till Microsoft Graph V2-slutpunkt

Vi stöder för närvarande inte Microsoft Graph v2-slutpunkten – se till att komma åt aktivitetsloggarna med hjälp Microsoft Graph v1-slutpunkten.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: Ingen av klienterna är B2C eller så har klientorganisationen ingen Premium-licens

Åtkomst till inloggningsrapporter kräver en Azure Active Directory premium 1-licens (P1). Om du ser det här felmeddelandet när du använder inloggningar kontrollerar du att din klientorganisation är licensierad med en Azure AD P1-licens.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Fel: Användaren är inte i de tillåtna rollerna 

Om du ser det här felmeddelandet när du försöker komma åt granskningsloggar eller  inloggningar  med hjälp av API:et kontrollerar du att ditt konto är en del av rollen Säkerhetsläsare eller Rapportläsare i din Azure Active Directory klientorganisation. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: Programmet saknar behörigheten Läsa katalogdata för AAD 

Följ stegen i Krav för att få åtkomst till [Azure Active Directory reporting-API:et](howto-configure-prerequisites-for-reporting-api.md) för att säkerställa att programmet körs med rätt uppsättning behörigheter. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fel: Program som saknar Microsoft Graph API-behörigheten "Läsa alla granskningsloggdata"

Följ stegen i Krav för att få åtkomst till [Azure Active Directory reporting-API:et](howto-configure-prerequisites-for-reporting-api.md) för att säkerställa att programmet körs med rätt uppsättning behörigheter. 

## <a name="next-steps"></a>Nästa steg

[Använda referensen för gransknings-API:et](/graph/api/resources/directoryaudit) 
 [Använda API-referensen för inloggningsaktivitetsrapporten](/graph/api/resources/signin)