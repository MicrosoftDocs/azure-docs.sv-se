---
title: Om ServiceNow-integrering
description: Hanterings applikationen Defender för IoT-anslutningsdelning för ServiceNow ger SOC-analytiker med flerdimensionell insyn i specialiserade protokoll och IoT-enheter som distribueras i industriella miljöer, tillsammans med ICS-medvetna beteende analys för att snabbt upptäcka misstänkta eller avvikande beteenden.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786013"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>Hanterings programmet Defender för IoT-anslutningsdelning för ServiceNow

Hanterings applikationen Defender för IoT-anslutningsdelning för ServiceNow ger SOC-analytiker med flerdimensionell insyn i specialiserade protokoll och IoT-enheter som distribueras i industriella miljöer, tillsammans med ICS-medvetna beteende analys för att snabbt upptäcka misstänkta eller avvikande beteenden. Detta är en viktig utveckling för den pågående konvergensen av IT och att stödja nya IoT-initiativ, till exempel smarta datorer och real tids information.

Programmet aktiverar även det både IT-och det andra incident svaret från en företags SOC.

## <a name="about-defender-for-iot"></a>Om Defender för IoT

Defender for IoT levererar den enda ICS-och IoT cybersäkerhet-plattform som skapats av Blue-team-experter med en spårnings post som skyddar nationell infrastruktur och den enda plattformen med patenterad ICS-medveten hot analys och maskin inlärning. Defender för IoT tillhandahåller:

- Omedelbar insikter om ICS är enhets landskapet och innehåller en mängd information om attribut.

- ICS-medvetna djup inbäddad kunskap om protokoll, enheter, program och deras beteenden.

- Omedelbara insikter om sårbarheter och kända hot på noll dagar.

- En automatiserad teknik för att utforma hot modellering för att förutse de mest sannolika vägarna till riktade ICS-attacker via patentskyddad analys.

> [!Note]
> Referenser till CyberX hänvisar till Azure Defender för IoT.

## <a name="about-the-integration"></a>Om integreringen

Defender for IoT-integrering med ServiceNow ger en ny nivå av centraliserad synlighet, övervakning och kontroll för IoT och landskap. Dessa bryggade plattformar möjliggör automatisk synlighet och hot hantering för enheter till tidigare otillgängliga ICS-& IoT-enheter.

### <a name="threat-management"></a>Hothantering

Hanterings programmet Defender för IoT-anslutningsdelning hjälper:

- Minska tiden som krävs för organisationer för industriella och kritiska infrastrukturer för att identifiera, undersöka och agera på cyberhot hot.

- Få information i real tid om andra risker.

- Korrelera Defender för IoT-aviseringar med arbets flöden för ServiceNow Threat Monitoring och incident hantering.

- Utlös ServiceNow biljetter och arbets flöden med andra tjänster och program på ServiceNow-plattformen.

ICS-och SCADA-säkerhetshoten identifieras av Defender för IoT-säkerhetsmotorer, som ger omedelbara aviserings svar på angrepp mot skadlig kod, nätverks-och säkerhets principer, samt drift-och protokoll avvikelser. Mer information om aviserings information som skickas till ServiceNow finns i [varnings rapportering](#alert-reporting).

### <a name="device-visibility-and-management"></a>Synlighet och hantering av enheter

ServiceNow Configuration Management-databasen (CMDB) är berikad och komplett med en omfattande uppsättning enhets attribut som skickas av Defender för IoT-plattformen. Detta garanterar omfattande och kontinuerlig insyn i enhetens landskap och gör att du kan övervaka och svara från ett enda fönster. Information om enhets attribut som skickas till ServiceNowSee finns i [Visa Defender för IoT-identifieringar i ServiceNow](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>System krav och arkitektur

I den här artikeln beskrivs:

- **Program varu krav**  
- **Arkitektur**

## <a name="software-requirements"></a>Programvarukrav

- ServiceNow Service Management-version 3.0.2

- Defender för IoT patch-2.8.11.1 eller senare

> [!Note]
> Om du redan arbetar med en Defender för IoT-och ServiceNow-integrering och uppgraderar med hjälp av den lokala hanterings konsolen, bör tidigare-data som tas emot från Defender för IoT-sensorer rensas från ServiceNow.

## <a name="architecture"></a>Arkitektur

### <a name="on-premises-management-console-architecture"></a>Arkitektur för lokal hanterings konsol

Den lokala hanterings konsolen tillhandahåller en enhetlig källa för all enhets-och aviserings information som skickas till ServiceNow.

Du kan konfigurera en lokal hanterings konsol för att kommunicera med en instans av ServiceNow. Den lokala hanterings konsolen push-överför sensor data till programmet Defender för IoT med hjälp av REST API.

Om du konfigurerar systemet så att det fungerar med en lokal hanterings konsol inaktiverar du ServiceNow Sync, vidarebefordrings regler och proxy-konfigurationer i sensorer, om de har kon figurer ATS.

De här konfigurationerna bör konfigureras för den lokala hanterings konsolen. Konfigurations instruktioner beskrivs i den här artikeln.

### <a name="sensor-architecture"></a>Sensor arkitektur

Om du vill konfigurera din miljö så att den inkluderar direkt kommunikation mellan sensorer och ServiceNow för varje sensor definierar du synkroniseringen av ServiceNow, vidarebefordrings regler och proxy-konfiguration (om en proxy behövs).

Vi rekommenderar att du konfigurerar din integrering med hjälp av den lokala hanterings konsolen för att kommunicera med ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Skapa åtkomsttoken i ServiceNow

Den här artikeln beskriver hur du skapar en åtkomsttoken i ServiceNow. Token krävs för att kommunicera med Defender för IoT.

Du behöver **klient-ID** och **klient hemlighet** när du skapar Defender för regler för vidarebefordring av IoT, som vidarebefordrar aviserings information till ServiceNow och när du konfigurerar Defender för IoT att push-enhetens attribut till ServiceNow-tabeller.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Konfigurera Defender för IoT för att kommunicera med ServiceNow

Den här artikeln beskriver hur du konfigurerar Defender för IoT för att kommunicera med ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Skicka Defender för IoT-aviserings information

Den här artikeln beskriver hur du konfigurerar Defender för IoT för att skicka aviserings information till ServiceNow-tabeller. Information om hur du skickar aviserings data finns i [varnings rapportering](#alert-reporting).

Skydds funktioner för IoT-aviseringar visas i ServiceNow som säkerhets incidenter.

Definiera en Defender för *vidarebefordrings* regel för IoT för att skicka aviserings information till ServiceNow.

Definiera regeln:

1. I det vänstra fönstret Defender för IoT väljer du **vidarebefordra**.  

1. Välj :::image type="content" source="media/integration-servicenow/plus.png" alt-text="knappen plus ikon."::: i det vänstra fönstret. Dialog rutan skapa vidarebefordrings regel öppnas.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Skapa vidarebefordrings regel":::

1. Lägg till ett regel namn.

1. Definiera villkor under vilka Defender för IoT ska utlösa vidarebefordrings regeln. Genom att arbeta med kriterier för vidarebefordring kan du hitta och hantera mängden information som skickas från Defender för IoT till ServiceNow. Följande alternativ är tillgängliga:

    - **Allvarlighets grad:** Detta är den lägsta säkerhets nivå som incidenten ska vidarebefordra. Om t. ex. **mindre** är markerat, så vidarebefordras mindre aviseringar och aviseringar ovanför allvarlighets graden. Nivåer definieras i förväg av Defender för IoT.

    - **Protokoll:** Utlös bara vidarebefordrings regeln om den identifierade trafiken har körts över vissa protokoll. Välj de protokoll som krävs i list rutan eller Välj alla.

    - **Motorer:** Välj de motorer som krävs eller Välj alla. Aviseringar från valda motorer kommer att skickas.

1. Kontrol lera att **aviserings aviseringar** har valts.

1. I avsnittet åtgärder väljer du **Lägg till** och väljer sedan **ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Välj ServiceNow i list rutan alternativ.":::

1. Ange åtgärds parametrar för ServiceNow:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Fyll i parametrarna för ServiceNow-åtgärden":::

1. I fönstret **åtgärder** ställer du in följande parametrar:

  | Parameter | Beskrivning |
  |--|--|
  | Domain | Ange IP-adressen för ServiceNow-servern. |
  | Användarnamn | Ange ServiceNow-serverns användar namn. |
  | Lösenord | Ange lösen ordet för ServiceNow-servern. |
  | Klient-ID | Ange det klient-ID som du fick för Defender för IoT på sidan **program register** i ServiceNow. |
  | Client Secret (Klienthemlighet) | Ange den klient hemlighet som du skapade för Defender för IoT på sidan **program register** i ServiceNow. |
  | Rapport typ | **Incidenter**: vidarebefordra en lista över aviseringar som presenteras i ServiceNow med ett incident-ID och en kort beskrivning av varje avisering.<br /><br />**Defender för IoT-program**: vidarebefordra fullständig aviserings information, inklusive sensor information, motor, källa och mål adresser. Informationen vidarebefordras till Defender for IoT i ServiceNow-programmet. |

1. Välj **Spara**. Försvarare för IoT-aviseringar visas som incidenter i ServiceNow.

### <a name="send-defender-for-iot-device-attributes"></a>Skicka Defender för IoT-enhetens attribut

I den här artikeln beskrivs hur du konfigurerar Defender för IoT för att skicka en stor mängd enhets attribut till ServiceNow-tabeller. Se ***inventerings information*** för information om vilken typ av information som skickas till ServiceNow.

Om du vill skicka attribut till ServiceNow måste du mappa den lokala hanterings konsolen till en ServiceNow-instans. Detta säkerställer att Defender för IoT-plattformen kan kommunicera och autentisera med-instansen.

Så här lägger du till en ServiceNow-instans:

1. Logga in på din lokala Defender för IoT-hanteringskonsolen.

1. Välj **Systeminställningar** och sedan **ServiceNow** i integrations avsnittet för den lokala hanterings konsolen.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Välj knappen ServiceNow.":::

1. Ange följande Sync-parametrar i dialog rutan ServiceNow Sync.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="Dialog rutan ServiceNow Sync.":::

     Parameter | Beskrivning |
    |--|--|
    | Aktivera synkronisering | Aktivera och inaktivera synkronisera när parametrar har definierats. |
    | Synkroniseringsfrekvens (minuter) | Som standard flyttas information till ServiceNow var 60: e minut. Minimivärdet är 5 minuter. |
    | ServiceNow-instans | Ange ServiceNow-instansens URL. |
    | Klient-ID | Ange det klient-ID som du fick för Defender för IoT på sidan **program register** i ServiceNow. |
    | Client Secret (Klienthemlighet) | Ange den klient hemlighet som du skapade för Defender för IoT på sidan **program register** i ServiceNow. |
    | Användarnamn | Ange användar namnet för instansen. |
    | Lösenord | Ange lösen ordet för den här instansen. |

1. Välj **Spara**.

## <a name="verify-communication"></a>Verifiera kommunikation

Kontrol lera att den lokala hanterings konsolen är ansluten till ServiceNow-instansen genom att granska det senaste datumet för *synkronisering* .

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Kontrol lera att kommunikationen har genomförts genom att titta på den senaste synkroniseringen.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Konfigurera integreringarna med HTTPS-proxy

När du konfigurerar Defender för IoT-och ServiceNow-integrering, kommunicerar den lokala hanterings konsolen och ServiceNow-servern med port 443. Om ServiceNow-servern ligger bakom proxyservern går det inte att använda standard porten.

Defender för IoT har stöd för en HTTPS-proxy i ServiceNow-integreringen genom att aktivera ändringen av standard porten som används för integrering.

Konfigurera proxyn:

1. Redigera globala egenskaper i den lokala hanterings konsolen:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Lägg till följande parametrar:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Spara och avsluta.

4. Kör följande kommando: `sudo monit restart all`

Efter konfigurationen vidarebefordras alla ServiceNow-data med den konfigurerade proxyn.

## <a name="download-the-defender-for-iot-application"></a>Hämta programmet Defender för IoT

Den här artikeln beskriver hur du hämtar programmet.

För att komma åt Defender for IoT-programmet:

1. Navigera till <https://store.servicenow.com/>

2. Sök efter `Defender for IoT` eller `CyberX IoT/ICS Management` .

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Sök efter CyberX i Sök fältet.":::

3. Välj programmet.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Välj programmet i listan.":::

4. Välj **appen för begäran.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Logga in på programmet med dina autentiseringsuppgifter.":::

5. Logga in och hämta programmet.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Visa Defender för IoT-identifieringar i ServiceNow

Den här artikeln beskriver enhetens attribut och aviserings information som presenteras i ServiceNow.

Så här visar du enhetens attribut:

1. Logga in på ServiceNow.

2. Gå till **CyberX-plattformen**.

3. Navigera till **lager** eller **avisering**.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Inventering eller avisering":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Inventerings information

Konfigurations hanterings databasen (CMDB) är omfattande och kompletteras av data som skickats av Defender för IoT till ServiceNow. Genom att lägga till eller uppdatera enhets attribut på ServiceNows CMDB-konfigurations objekts tabeller kan Defender för IoT utlösa ServiceNow arbets flöden och affärs regler.

Följande information är tillgänglig:

- Enhetens attribut, till exempel enhetens MAC, OS, leverantör eller protokoll som identifieras.

- Information om inbyggd program vara, till exempel version och serie nummer för inbyggd program vara.

- Ansluten enhets information, till exempel riktningen på trafiken mellan källan och målet.

### <a name="devices-attributes"></a>Enheter-attribut

I den här artikeln beskrivs enhetens attribut som skickas till ServiceNow.

| Objekt | Beskrivning |
|--|--|
| Enhet | Namnet på sensorn som identifierade trafiken. |
| ID | Enhets-ID som tilldelats av Defender för IoT. |
| Name | Enhetens namn. |
| IP-adress | Enhetens IP-adress eller adresser. |
| Typ | Enhets typ, till exempel en växel, PLC, historian eller domänkontrollant. |
| MAC-adress | Enhetens MAC-adress eller adresser. |
| Operativsystem | Enhetens operativ system. |
| Leverantör | Enhets leverantören. |
| Protokoll | De protokoll som identifierades i trafiken som genereras av enheten. |
| Ägare | Ange namnet på enhetens ägare. |
| Location | Ange enhetens fysiska plats. |

Visa enheter som är anslutna till en enhet i den här vyn.

Så här visar du anslutna enheter:

1. Välj en enhet och välj **sedan den enhet** som anges i för enheten.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Välj den önskade enheten i listan.":::

1. I dialog rutan **enhets information** väljer du **anslutna enheter**.

### <a name="firmware-details"></a>Information om inbyggd program vara

I den här artikeln beskrivs information om enhetens inbyggda program vara i ServiceNow.

| Objekt | Beskrivning |
|--|--|
| Enhet | Namnet på sensorn som identifierade trafiken. |
| Enhet | Enhetens namn. |
| Adress | Enhetens IP-adress. |
| Modulens adress | Enhets modell och fack nummer eller ID. |
| Nummer | Enhetens serie nummer. |
| Modell | Enhetens modell nummer. |
| Version | Versions numret för den inbyggda program varan. |
| Ytterligare data | Mer information om den inbyggda program varan som definieras av leverantören, till exempel enhets typ. |

### <a name="connection-details"></a>Anslutningsinformation

Den här artikeln beskriver enhets anslutnings informationen som skickas till ServiceNow.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="Enhetens anslutnings information":::

| Objekt | Beskrivning |
|--|--|
| Enhet | Namnet på sensorn som identifierade trafiken. |
| Riktning | Trafikens riktning. <br /> <br /> - **Ett sätt** indikerar att målet är servern och att källan är klienten. <br /> <br /> - **Två sätt** anger att både källan och målet är servrar, eller att klienten är okänd. |
| Käll enhets-ID | IP-adressen för enheten som kommunicerar med den anslutna enheten. |
| Käll enhets namn | Namnet på enheten som kommunicerar med den anslutna enheten. |
| Mål enhets-ID | Den anslutna enhetens IP-adress. |
| Mål enhets namn | Namnet på den anslutna enheten. |

## <a name="alert-reporting"></a>Varnings rapportering

Aviseringar utlöses när försvarare för IoT-motorer identifierar ändringar i nätverks trafik och beteende som kräver din uppmärksamhet. Mer information om vilka typer av aviseringar varje motor genererar finns i [om aviserings motorer](#about-alert-engines).

Den här artikeln beskriver enhetens aviserings information som skickas till ServiceNow.

| Objekt | Beskrivning |
|--|--|
| Skapad | Datum och tid då aviseringen genererades. |
| Motor | Den motor som identifierade händelsen. |
| Rubrik | Aviserings rubriken. |
| Beskrivning | Aviserings beskrivningen. |
| Protokoll | Protokollet identifierades i trafiken. |
| Allvarlighetsgrad | Allvarlighets graden för aviseringen som definieras av Defender för IoT. |
| Enhet | Namnet på sensorn som identifierade trafiken. |
| Källnamn | Källans namn. |
| Källans IP-adress| Käll-IP-adressen. |
| Målnamn | Mål namnet. |
| Mål-IP-adress | Mål-IP-adressen. |
| Tilldelad person | Ange namnet på den person som tilldelats biljetten. |

### <a name="updating-alert-information"></a>Uppdaterar aviserings information

Välj posten i kolumnen skapad om du vill visa aviserings information i ett formulär. Du kan uppdatera aviserings informationen och tilldela aviseringen till en individ att granska och hantera.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Visa aviseringens information.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Om aviserings motorer

I den här artikeln beskrivs vilka typer av aviseringar varje motor utlösare.

| Aviseringstyp | Beskrivning |
|--|--|
| Aviseringar om princip överträdelser | Utlöses när motorn för policy överträdelse identifierar en avvikelse från trafik som tidigare har lärts. Exempel: <br /><br />-En ny enhet har identifierats. <br /><br />– En ny konfiguration identifieras på en enhet. <br /><br />– En enhet som inte har definierats som en programmerings enhet utför en program ändring. <br /><br />-En version av inbyggd program vara har ändrats. |
| Varningar om protokoll fel | Utlöses när motorn för protokoll överträdelse identifierar paket strukturer eller fält värden som inte uppfyller protokoll specifikationen. |
| Drift aviseringar | Utlöses när drift motorn identifierar problem med nätverks drift eller enhets fel. Till exempel stoppades en nätverks enhet med ett Stop PLC-kommando, eller ett gränssnitt på en sensor som slutade övervaka trafiken. |
| Varningar om skadlig kod | Utlöses när motorn för skadlig kod identifierar skadlig nätverks aktivitet, till exempel kända attacker som Conficker. |
| Avvikelse aviseringar | Utlöses när avvikelse motorn identifierar en avvikelse. En enhet utför till exempel nätverks genomsökning men definieras inte som en skannings enhet. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [vidarebefordrar aviserings information](how-to-forward-alert-information-to-partners.md).
