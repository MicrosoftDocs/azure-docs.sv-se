---
title: Anslut hot informations data till Azure Sentinel | Microsoft Docs
description: Lär dig mer om hur du ansluter hot Intelligence-data till Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96121748"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Koppla data från leverantörer av hotinformation

> [!IMPORTANT]
> Data anslutningarna för Threat Intelligence i Azure Sentinel finns för närvarande i offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Sentinel kan du importera de hot indikatorer som din organisation använder, vilket kan förbättra din säkerhetsanalytikers möjlighet att identifiera och prioritera kända hot. Flera funktioner från Azure Sentinel blir sedan tillgängliga eller förbättrade:

- **Analytics** innehåller en uppsättning mallar för schemalagda regler som du kan aktivera för att generera aviseringar och incidenter baserat på matchningar av logg händelser från dina hot indikatorer.

- **Arbets böcker** ger sammanfattande information om de hot indikatorer som importer ATS till Azure Sentinel och alla varningar som genererats från analys regler som matchar dina hot indikatorer.

- Med **jakt** frågor kan säkerhetsövervakare använda hot indikatorer inom ramen för vanliga jakt scenarier.

- **Antecknings böcker** kan använda hot indikatorer när du undersöker avvikelser och söker efter skadliga beteenden.

Du kan strömma hot indikatorer till Azure Sentinel genom att använda en av de "TIP-produkter (Integrated Threat Intelligence Platform) som anges i nästa avsnitt, ansluta till TAXIbaserade servrar eller genom Microsoft Graph att använda direkt integrering med [tiIndicators-API: et för säkerhet](/graph/api/resources/tiindicator).

## <a name="integrated-threat-intelligence-platform-products"></a>Integrerade hot Intelligence Platform-produkter

- [MISP för hot information med öppen källkod](https://www.misp-project.org/)
    
    Ett exempel skript som förser klienter med MISP-instanser för att migrera hot indikatorer till Microsoft Graph Security API finns i avsnittet [MISP to Microsoft Graph Security script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Avvikelsei ThreatStream](https://www.anomali.com/products/threatstream)

    Information om hur du hämtar ThreatStream Integrator och tillägg, samt anvisningar för hur du ansluter ThreatStream Intelligence till Microsoft Graph Security API, finns på sidan med [ThreatStream-nedladdningar](https://ui.threatstream.com/downloads) .

- [Palo-nätverk MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Guidade instruktioner finns i [Skicka IOCs till säkerhets-API: et för Microsoft Graph med MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [ThreatConnect-plattform](https://threatconnect.com/solution/)

    Mer information finns i [ThreatConnect-integreringar](https://threatconnect.com/integrations/) och leta efter Microsoft Graph Security API på sidan.

- [EclecticIQ-plattform](https://www.eclecticiq.com/solutions)

- [ThreatQ Threat intelligence-plattform](https://www.threatq.com/)

    Information och anvisningar finns i [Microsoft Sentinel Connector for ThreatQ-integration](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview).

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Anslut Azure Sentinel till din Threat intelligence-plattform

### <a name="prerequisites"></a>Förutsättningar  

- Azure AD-rollen för antingen global administratör eller säkerhets administratör för att ge behörighet till din TIP-produkt eller anpassade program som använder direkt integrering Microsoft Graph med tiIndicators-API: et för säkerhet.

- Läs-och Skriv behörighet till arbets ytan i Azure Sentinel för att lagra dina hot indikatorer.

### <a name="instructions"></a>Instruktioner

1. [Registrera ett program](/graph/auth-v2-service#1-register-your-app) i Azure Active Directory för att hämta ett program-ID, program hemlighet och Azure Active Directory klient-ID. Du behöver dessa värden för när du konfigurerar din integrerade tips produkt eller app som använder direkt integrering med Microsoft Graph Security tiIndicators API.

2. [Konfigurera API-behörigheter](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) för det registrerade programmet: Lägg till behörigheten Microsoft Graph Application **ThreatIndicators. readwrite. OwnedBy** i det registrerade programmet.

3. Be din Azure Active Directory klient administratör att bevilja administrativt medgivande till det registrerade programmet för din organisation. Från Azure Portal: **Azure Active Directory**  >  **Appregistreringar**  >  **\<_app name_>**  >  **Visa API-behörigheter**  >  **bevilja administrativt medgivande för \<_tenant name_>**.

4. Konfigurera TIP-produkten eller appen som använder direkt integrering med Microsoft Graph Security tiIndicators API för att skicka indikatorer till Azure Sentinel genom att ange följande:
    
    a. Värdena för det registrerade programmets ID, hemlighet och klient-ID.
    
    b. För mål produkten anger du Azure Sentinel.
    
    c. Ange avisering för åtgärden.

5. I Azure Portal går du till **Azure Sentinel**  >  **data Connectors** och väljer sedan **Hot information Platforms (för hands version)** Connector.

6. Välj **Öppna anslutnings sidan** och **Anslut** sedan.

7. Om du vill visa de hot indikatorer som importeras till Azure Sentinel går du till **Azure Sentinel-logs**  >  **SecurityInsights** och expanderar sedan **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Anslut Azure Sentinel till TAXIi-servrar

### <a name="prerequisites"></a>Förutsättningar

- Läs-och Skriv behörighet till arbets ytan i Azure Sentinel för att lagra dina hot indikatorer.

- TAXIi 2,0-Server-URI och samlings-ID.

### <a name="instructions"></a>Instruktioner

1. I Azure Portal går du till **Azure Sentinel**  >  **data Connectors** och väljer sedan **Threat Intelligence-anslutningen (för hands version)** .

2. Välj **Öppna kopplings sida**.

3. Ange obligatorisk och valfri information i text rutorna.

4. Välj **Lägg till** för att aktivera anslutningen till taxi 2,0-servern.

5. Om du har ytterligare TAXI 2,0-servrar: Upprepa steg 3 och 4.

6. Om du vill visa de hot indikatorer som importeras till Azure Sentinel går du till **Azure Sentinel-logs**  >  **SecurityInsights** och expanderar sedan **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter din Threat Intelligence-Provider till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar.

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).