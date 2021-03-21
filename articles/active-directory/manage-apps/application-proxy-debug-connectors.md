---
title: Felsöka Application Proxy-kopplingar – Azure Active Directory | Microsoft Docs
description: Felsöka problem med Azure Active Directory (Azure AD) Application Proxy-kopplingar.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c656bfe7f9cc8923859a99f31210cd554aa66a41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256974"
---
# <a name="debug-application-proxy-connector-issues"></a>Felsöka problem med anslutningsprogram för programproxy 

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory (Azure AD) Application Proxy-kopplingar. Om du använder Application Proxy-tjänsten för fjärråtkomst till ett lokalt webb program, men du har problem med att ansluta till programmet, använder du det här flödesschemat för att felsöka anslutnings problem. 

## <a name="before-you-begin"></a>Innan du börjar

I den här artikeln förutsätter vi att du har installerat Application Proxy Connector och har ett problem. När du felsöker problem med programproxyn rekommenderar vi att du börjar med det här fel söknings flödet för att avgöra om Application Proxy-kopplingar är korrekt konfigurerade. Om du fortfarande har problem med att ansluta till programmet följer du fel söknings flödet i fel söknings program för [programproxy](application-proxy-debug-apps.md).  


Mer information om Application Proxy och hur du använder dess anslutningar finns i:

- [Fjärråtkomst till lokala program via programproxy](application-proxy.md)
- [Application Proxy-kopplingar](application-proxy-connectors.md)
- [Installera och registrera ett anslutningsprogram](application-proxy-add-on-premises-application.md)
- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Flödes schema för anslutnings problem

Det här flödesschemat vägleder dig genom stegen för att felsöka några av de vanligaste anslutnings problemen. Mer information om varje steg finns i tabellen efter flödesschemat.

![Flödes schema med steg för att felsöka en koppling](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| Steg | Åtgärd | Beskrivning |
|---------|---------|---------|
|1 | Hitta den kopplings grupp som har tilldelats appen | Du har förmodligen en anslutning som är installerad på flera servrar, i vilket fall kopplingarna ska [tilldelas till anslutnings grupper](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Läs mer om anslutnings grupper i [Publicera program i separata nätverk och platser med anslutnings grupper](application-proxy-connector-groups.md). |
|2 | Installera anslutningen och tilldela en grupp | Om du inte har installerat en anslutning kan du läsa [Installera och registrera en anslutning](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Om du har problem med att installera anslutningen, se [problem med att installera anslutningen](application-proxy-connector-installation-problem.md).<br></br> Om kopplingen inte är kopplad till en grupp, se [tilldela kopplingen till en grupp](application-proxy-connector-groups.md#create-connector-groups).<br></br>Om programmet inte har tilldelats någon kopplings grupp, se [tilldela programmet till en kopplings grupp](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Köra ett port test på anslutnings servern | På kopplings servern kör du ett port test genom att använda [telnet](/windows-server/administration/windows-commands/telnet) eller något annat port test verktyg för att kontrol lera om portarna [443 och 80 är öppna](application-proxy-add-on-premises-application.md#open-ports).|
|4 | Konfigurera domäner och portar | [Kontrol lera att dina domäner och portar är korrekt konfigurerade](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) För att anslutningen ska fungera korrekt finns det vissa portar som måste vara öppna och URL: er som servern måste kunna komma åt. |
|5 | Kontrol lera om en server dels proxyserver används | Kontrol lera om anslutningarna använder Server dels proxyservrar eller kringgå dem. Mer information finns i [Felsöka problem med anslutnings proxy och tjänst anslutnings problem](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Uppdatera anslutningen och uppdateraren så att den använder Server dels proxyn | Om en backend-proxy används bör du kontrol lera att anslutningen använder samma proxy. Information om hur du felsöker och konfigurerar anslutningar för att arbeta med proxyservrar finns i [arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Läs in appens interna URL på anslutnings servern | Läs in appens interna URL på kopplings servern. |
|8 | Kontrol lera intern nätverks anslutning | Det finns ett anslutnings problem i det interna nätverket som det här fel söknings flödet inte kan diagnostisera. Programmet måste vara tillgängligt internt för att anslutningarna ska fungera. Du kan aktivera och Visa händelse loggar för Connector enligt beskrivningen i [Application Proxy-kopplingar](application-proxy-connectors.md#under-the-hood). |
|9 | Förlänga timeout-värdet på Server delen | I de **ytterligare inställningarna** för ditt program ändrar du timeout-inställningen för **Server del programmet** till **lång**. Se [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Om problemen kvarstår kan du läsa mer om specifika flödes problem, granska fel söknings flöden för appar och SSO | Använd fel söknings programmet för [programproxy](application-proxy-debug-apps.md) för att felsöka flödet. |

## <a name="next-steps"></a>Nästa steg


* [Publicera program i separata nätverk och platser med anslutnings grupper](application-proxy-connector-groups.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
* [Felsöka Application Proxy och anslutnings fel](application-proxy-troubleshoot.md)
* [Tyst installation av Azure AD-programproxy Connector](application-proxy-register-connector-powershell.md)