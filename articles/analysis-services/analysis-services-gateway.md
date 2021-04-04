---
title: Lokal datagateway för Azure Analysis Services | Microsoft Docs
description: En lokal gateway krävs om din Analysis Services-server i Azure ska ansluta till lokala data källor.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6ca96f76287482a445d8a9a1cdc441333b36efbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97739611"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Ansluta till lokala data källor med lokal datagateway

Den lokala datagatewayen ger säker data överföring mellan lokala data källor och dina Azure Analysis Services-servrar i molnet. Förutom att arbeta med flera Azure Analysis Services-servrar i samma region, fungerar den senaste versionen av gatewayen också med Azure Logic Apps, Power BI, Power Apps och automatiserad energi. Även om den gateway som du installerar är samma för alla dessa tjänster, Azure Analysis Services och Logic Apps har ytterligare steg.

Informationen som anges här är speciell för hur Azure Analysis Services fungerar med den lokala datagatewayen. Mer information om gatewayen i allmänhet och hur det fungerar med andra tjänster finns i [Vad är en lokal datagateway?](/data-integration/gateway/service-gateway-onprem).

För Azure Analysis Services hämtar installationen med gatewayen första gången en process i fyra delar:

- **Hämta och kör installationen** – det här steget installerar en gateway-tjänst på en dator i din organisation. Du loggar också in på Azure med ett konto i [klient organisationens](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD. Konton för Azure B2B (gäst) stöds inte.

- **Registrera din gateway** – i det här steget anger du ett namn och en återställnings nyckel för din gateway och väljer en region, registrerar din gateway med moln tjänsten Gateway. Din gateway-resurs kan registreras i vilken region som helst, men det rekommenderas att den finns i samma region som dina Analysis Services-servrar. 

- **Skapa en gateway-resurs i Azure** – i det här steget skapar du en gateway-resurs i Azure.

- **Anslut Gateway-resursen till-servrar** – när du har en gateway-resurs kan du börja ansluta servrar till den. Du kan ansluta flera servrar och andra resurser förutsatt att de finns i samma region.

## <a name="installing"></a>Installation

När du installerar för en Azure Analysis Services-miljö är det viktigt att du följer stegen som beskrivs i [Installera och konfigurera en lokal datagateway för Azure Analysis Services](analysis-services-gateway-install.md). Den här artikeln är unik för Azure Analysis Services. Den innehåller ytterligare steg som krävs för att konfigurera en lokal datagateway-resurs i Azure och ansluta din Azure Analysis Services-server till resursen.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Ansluta till en gateway-resurs i en annan prenumeration

Vi rekommenderar att du skapar din Azure gateway-resurs i samma prenumeration som servern. Du kan dock konfigurera servrarna så att de ansluter till en gateway-resurs i en annan prenumeration. Det går inte att ansluta till en gateway-resurs i en annan prenumeration när du konfigurerar befintliga Server inställningar eller skapar en ny server i portalen, men kan konfigureras med hjälp av PowerShell. Läs mer i [ansluta gateway-resurs till Server](analysis-services-gateway-install.md#connect-gateway-resource-to-server).

## <a name="ports-and-communication-settings"></a>Portar och kommunikations inställningar

Gatewayen skapar en utgående anslutning till Azure Service Bus. Den kommunicerar via utgående portar: TCP 443 (standard), 5671, 5672, 9350 till 9354.  Gatewayen behöver inte några ingående portar.

Du kan behöva inkludera IP-adresser för ditt data område i brand väggen. Du kan ladda ned [IP-listan för Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=56519). Listan uppdateras en gång i veckan. De IP-adresser som finns i IP-listan för Azure Datacenter använder CIDR-notering. Mer information finns i [klass lös Inter-Domain routning](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Följande är fullständigt kvalificerade domän namn som används av gatewayen.

| Domännamn | Utgående portar | Beskrivning |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP används för att hämta installationsprogrammet. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671–5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350–9354 |Lyssnare på Service Bus Relay via TCP (kräver 443 för att hämta token för åtkomstkontroll) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |80 |Används för att testa Internetanslutningen om denna gateway inte kan nås av Power BI-tjänsten. |
| *.microsoftonline-p.com |443 |Används för autentisering beroende på konfiguration. |
| dc.services.visualstudio.com    |443 |Används av AppInsights för att samla in telemetri. |

## <a name="next-steps"></a>Nästa steg 

Följande artiklar ingår i den lokala datagatewayens allmänna innehåll som gäller för alla tjänster som gatewayen stöder:

* [Vanliga frågor och svar om lokal datagateway](/data-integration/gateway/service-gateway-onprem-faq)   
* [Använda appen för lokal datagateway](/data-integration/gateway/service-gateway-app)   
* [Administration på klientorganisationsnivå](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurera proxyinställningar](/data-integration/gateway/service-gateway-proxy)   
* [Justera kommunikationsinställningar](/data-integration/gateway/service-gateway-communication)   
* [Konfigurera loggfiler](/data-integration/gateway/service-gateway-log-files)   
* [Felsöka](/data-integration/gateway/service-gateway-tshoot)
* [Övervaka och optimera gatewayprestanda](/data-integration/gateway/service-gateway-performance)
