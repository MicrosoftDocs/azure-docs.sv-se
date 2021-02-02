---
title: Ta bort personliga data – Azure Active Directory-programproxy
description: Ta bort personliga data från anslutningar som är installerade på enheter för Azure Active Directory-programproxy.
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90913ba8f7fbe8158a5cfea01e49a175180677b6
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258956"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Ta bort personliga data för Azure Active Directory-programproxy

Azure Active Directory-programproxy kräver att du installerar anslutningar på dina enheter, vilket innebär att det kan finnas personliga data på dina enheter. Den här artikeln innehåller anvisningar för hur du tar bort personliga data för att förbättra sekretessen.

## <a name="where-is-the-personal-data"></a>Var finns personliga data?

Programproxy kan skriva personliga data till följande logg typer:

- Anslutnings händelse loggar
- Windows-händelseloggar

## <a name="remove-personal-data-from-windows-event-logs"></a>Ta bort personliga data från händelse loggar i Windows

Information om hur du konfigurerar datakvarhållning för händelse loggar i Windows finns i [Inställningar för händelse loggar](https://technet.microsoft.com/library/cc952132.aspx). Mer information om händelse loggar i Windows finns i [använda Windows-händelseloggen](/windows/win32/wes/using-windows-event-log).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Ta bort personliga data från anslutnings händelse loggar

För att säkerställa att programproxy-loggarna inte har personliga data kan du antingen:

- Ta bort eller visa data vid behov, eller
- Inaktivera loggning

Använd följande avsnitt för att ta bort personliga data från anslutnings händelse loggar. Du måste slutföra borttagnings processen för alla enheter där anslutningen är installerad.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Visa eller exportera vissa data

Om du vill visa eller exportera vissa data kan du söka efter relaterade poster i varje anslutnings händelse loggar. Loggarna finns på `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` .

Eftersom loggarna är textfiler kan du använda [findstr](/windows-server/administration/windows-commands/findstr) för att söka efter text poster som är relaterade till en användare.  

Om du vill söka efter personliga data söker du efter UserID i loggfilerna.

Om du vill hitta personliga data som loggats av ett program som använder Kerberos-begränsad delegering söker du efter följande komponenter av typen username:

- Lokala User Principal Name
- Användar namn del av User Principal Name
- Användar namn del av lokal User Principal Name
- Konto namn för lokal säkerhets konto hanterare (SAM)

### <a name="delete-specific-data"></a>Ta bort vissa data

Ta bort vissa data:

1. Starta om Microsoft Azure AD Application Proxy Connector-tjänsten för att generera en ny loggfil. Med den nya logg filen kan du ta bort eller ändra de gamla loggfilerna. 
1. Följ den [vy eller exportera en speciell data](#view-or-export-specific-data) process som beskrivits tidigare för att hitta information som måste tas bort. Sök i alla kopplings loggar.
1. Ta antingen bort relevanta loggfiler eller ta bort de fält som innehåller personliga data selektivt. Du kan också ta bort alla gamla loggfiler om du inte behöver dem längre.

### <a name="turn-off-connector-logs"></a>Inaktivera kopplings loggar

Ett alternativ för att se till att anslutnings loggar inte innehåller personliga data är att inaktivera logg genereringen. Om du vill stoppa genereringen av kopplings loggar tar du bort följande markerade rad från `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` .

![Visar ett kodfragment med den markerade koden som ska tas bort](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Nästa steg

En översikt över Application Proxy finns i [så här ger du säker fjärråtkomst till lokala program](application-proxy.md).