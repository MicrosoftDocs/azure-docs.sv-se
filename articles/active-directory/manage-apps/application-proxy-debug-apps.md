---
title: Felsöka program för programproxy – Azure Active Directory | Microsoft Docs
description: Felsöka problem med Azure Active Directory (Azure AD) Application Proxy-program.
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
ms.openlocfilehash: 2066030b04feae4e50fee3442a663209801d5875
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99253399"
---
# <a name="debug-application-proxy-application-issues"></a>Felsöka problem med programproxy 

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory (Azure AD) Application Proxy-program. Om du använder Application Proxy-tjänsten för fjärråtkomst till ett lokalt webb program, men du har problem med att ansluta till programmet, kan du använda det här flödesschemat för att felsöka program problem. 

## <a name="before-you-begin"></a>Innan du börjar

När du felsöker problem med programproxyn rekommenderar vi att du börjar med anslutningarna. Börja med att följa fel söknings flödet i [Felsöka anslutnings problem för programproxy](application-proxy-debug-connectors.md) för att kontrol lera att Application Proxy-kopplingar är korrekt konfigurerade. Om du fortfarande har problem kan du gå tillbaka till den här artikeln för att felsöka programmet.  

Mer information om Application Proxy finns i:

- [Fjärråtkomst till lokala program via programproxy](application-proxy.md)
- [Application Proxy-kopplingar](application-proxy-connectors.md)
- [Installera och registrera ett anslutningsprogram](application-proxy-add-on-premises-application.md)
- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Flödes schema för program problem

Det här flödesschemat vägleder dig genom stegen för att felsöka några av de vanligaste problemen med att ansluta till programmet. Mer information om varje steg finns i tabellen efter flödesschemat.

![Flödes schema som visar steg för att felsöka ett program](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| Steg | Åtgärd | Beskrivning |
|---------|---------|---------|
|1 | Öppna en webbläsare, öppna appen och ange dina autentiseringsuppgifter | Försök att använda dina autentiseringsuppgifter för att logga in i appen och kontrol lera om det finns några användarspecifika fel som [den här företags appen inte går att komma åt](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Verifiera användar tilldelning till appen | Se till att ditt användar konto har behörighet att komma åt appen inifrån företags nätverket och testa sedan att logga in på appen genom att följa stegen i [testa programmet](application-proxy-add-on-premises-application.md#test-the-application). Om inloggnings problem kvarstår, se [Felsöka inloggnings fel](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Öppna en webbläsare och försök att komma åt appen | Om ett fel visas omedelbart kontrollerar du att programproxyn har kon figurer ATS korrekt. Mer information om specifika fel meddelanden finns i [Felsöka problem med programproxy och fel meddelanden](application-proxy-troubleshoot.md).  |
|4 | Kontrol lera den anpassade domän konfigurationen eller Felsök felet | Om sidan inte visas alls kontrollerar du att den anpassade domänen är korrekt konfigurerad genom att granska [arbetet med anpassade domäner](application-proxy-configure-custom-domain.md).<br></br>Om sidan inte läses in och ett fel meddelande visas, felsöka felet genom att referera till fel  [sökning av problem med programproxy och fel meddelanden](application-proxy-troubleshoot.md). <br></br>Om det tar längre tid än 20 sekunder innan ett fel meddelande visas kan det vara problem med anslutningen. Gå till fel söknings artikeln för fel söknings [program proxy-kopplingar](application-proxy-debug-connectors.md) .  |
|5 | Om problemen kvarstår går du till anslutnings fel sökning | Det kan finnas anslutnings problem mellan proxyn och anslutningen eller mellan anslutningen och Server delen. Gå till fel söknings artikeln för fel söknings [program proxy-kopplingar](application-proxy-debug-connectors.md) . |
|6 | Publicera alla resurser, kontrol lera webbläsarens utvecklingsverktyg och åtgärda länkar | Se till att publicerings Sök vägen innehåller alla bilder, skript och formatmallar som krävs för ditt program. Mer information finns i [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Använd webbläsarens utvecklarverktyg (F12-verktyg i Internet Explorer eller Microsoft Edge) och Sök efter publicerings problem på det sätt som beskrivs på [sidan program visas inte på rätt sätt](application-proxy-page-appearance-broken-problem.md). <br></br>Gransknings alternativ för att lösa brutna länkar i [länkar på sidan fungerar inte](application-proxy-page-links-broken-problem.md). |
|7 | Sök efter nätverks fördröjning | Om sidan läses in långsamt kan du läsa om hur du minimerar nätverks fördröjningen i [överväganden för att minska svars tiden](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Se ytterligare fel söknings hjälp | Om problemen kvarstår hittar du ytterligare fel söknings artiklar i [fel söknings dokumentationen för programproxyn](application-proxy-troubleshoot.md). |

## <a name="next-steps"></a>Nästa steg


* [Publicera program i separata nätverk och platser med anslutnings grupper](application-proxy-connector-groups.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
* [Felsöka Application Proxy och anslutnings fel](application-proxy-troubleshoot.md)
* [Tyst installation av Azure AD-programproxy Connector](application-proxy-register-connector-powershell.md)
