---
title: Om Splunk-integrering
description: För att kunna åtgärda bristande insyn i säkerhet och återhämtning i nätverk med olika funktioner, har Defender för IoT utvecklat programmet Defender for IoT, IIoT och ICS Threat Monitoring för Splunk, en inbyggd integrering mellan Defender för IoT och Splunk som möjliggör en enhetlig metod för IT-och säkerhets inställningar.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 28bdc6deaac09d795c45460bb211126a105b80c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785928"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Defender för IoT och ICS Threat Monitoring Application för Splunk

Defender för IoT minimerar IIoT-, ICS-och SCADA-risker med patenterade, ICS-medvetna självstudier som ger omedelbara insikter om ICS-enheter, sårbarheter och hot på mindre än en avbildnings timme och utan att behöva lita på agenter, regler eller signaturer, specialiserade kunskaper eller tidigare kunskap om miljön.

För att kunna åtgärda bristande insyn i säkerhet och återhämtning i nätverk med olika funktioner, har Defender för IoT utvecklat programmet Defender for IoT, IIoT och ICS Threat Monitoring för Splunk, en inbyggd integrering mellan Defender för IoT och Splunk som möjliggör en enhetlig metod för IT-och säkerhets inställningar.

> [!Note]
> Referenser till CyberX hänvisar till Azure Defender för IoT.

## <a name="about-the-splunk-application"></a>Om Splunk-programmet

Programmet ger SOC-analytiker med flerdimensionell insyn i de specialiserade protokoll och IIoT-enheter som distribueras i industriella miljöer, tillsammans med ICS-medvetna beteende analys för att snabbt upptäcka misstänkt eller avvikande beteende. Programmet aktiverar även det både IT-och det andra incident svaret från en företags SOC. Detta är en viktig utveckling för den pågående konvergensen av IT och att stödja nya IIoT-initiativ, till exempel smarta datorer och real tids information.

Splunk-programmet kan installeras lokalt eller köras i ett moln. Integreringen med Defender för IoT stöder båda distributioner.

## <a name="about-the-integration"></a>Om integreringen

Integreringen av Defender för IoT och Splunk via det inbyggda programmet låter användare:

- Minska tiden som krävs för organisationer för industriella och kritiska infrastrukturer för att identifiera, undersöka och agera på cyberhot hot.

- Få information i real tid om andra risker.

- Korrelera Defender for IoT-aviseringar med Splunk Enterprise Security Threat Intelligence-databaser.

- Övervaka och svara från en enda ruta i glaset.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Huvud sidan i Splunk-verktyget.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Sidan aviseringar i Splunk.":::

Programmet gör det möjligt för Splunk-administratörer att analysera varnings meddelanden som Defender för IoT skickar, och övervaka hela den säkerhets distribution som är till exempel:

- Vilken av de fem analys motorerna har identifierat aviseringen.

- Vilket protokoll som genererade aviseringen.

- Vilken Defender för IoT-sensor som genererade aviseringen.

- Aviseringens allvarlighets grad.

- Källa och mål för kommunikationen.

## <a name="requirements"></a>Krav

### <a name="version-requirements"></a>Versions krav

Följande versioner är krav.

- Defender för IoT version 2,4 och senare.

- Splunkbase version 11 och senare.

- Splunk Enterprise version 7,2 och senare.
  
## <a name="download-the-application"></a>Hämta programmet

Ladda ned *CYBERX ICS Threat Monitoring för Splunk-program* från [Splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>Krav för Splunk-behörighet

Följande Splunk-behörighet krävs:

- Alla användare med *Administratörs* behörighet för användar roller.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Skicka Defender för IoT-aviseringar till Splunk

Defender för IoT-aviseringar innehåller information om en mängd säkerhets händelser, inklusive:

- Avvikelser från inlärtd bas linje nätverks aktivitet.

- Identifieringar av skadlig kod.

- Identifieringar baserat på misstänkt drifts ändringar.

- Nätverks avvikelser.

- Protokoll avvikelser från protokoll specifikation.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="Skärmen identifieringar.":::

Du kan konfigurera Defender för IoT att skicka aviseringar till Splunk-servern, där information om aviseringar visas i instrument panelen för Splunk Enterprise.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Visa alla aviseringar och deras information.":::

Följande aviserings information skickas till Splunk-servern.

- Datum och tid för aviseringen.

- Defender för IoT-motor som identifierade händelsen: protokoll överträdelse, princip överträdelse, skadlig kod, avvikelse eller drift motor.

- Aviserings rubriken.

- Aviserings meddelandet.

- Aviseringens allvarlighets grad: varning, mindre, Major eller kritisk.

- Källans enhets namn.

- Käll enhetens IP-adress.

- Målets enhets namn.

- Mål enhetens IP-adress.

- Defender för IoT-plattformens IP-adress (värd).

- Namnet på Defender for IoT Platform-enheten (Källtyp).

Exempel på utdata visas nedan:

| Tid | Händelse |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **Defender för IoT Platform-avisering**: en enhet har stoppats av ett PLC-kommando<br /><br />**Typ**: drifts överträdelse <br /><br />**Allvarlighets grad**: större <br /><br />**Käll namn**: my_device1 <br /><br />**Käll-IP**: 192.168.110.131 <br /><br />**Målnamn**: my_device2<br /><br /> **Mål-IP**: 10.140.33.238 <br /><br />**Meddelande**: en nätverks enhet stoppades med ett Stop PLC-kommando. Enheten kommer inte att användas förrän ett start kommando har skickats. 192.168.110.131 stoppades av 10.140.33.238 (en Siemens S7-enhet) med hjälp av ett PLC Stop-kommando.<br /><br />**Värd**: 192.168.90.43 <br /><br />**SourceType**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Definiera regler för vidarebefordran av notifieringsregler

Använd Defender för *regler för vidarebefordring* av IoT för att skicka aviserings information till Splunk-servrar.

Det finns alternativ för att anpassa aviserings reglerna baserat på:

- Vissa protokoll har identifierats.

- Händelsens allvarlighets grad.

- Defender för IoT-motor som identifierar händelser.

Så här skapar du en vidarebefordrings regel:

1. I det vänstra fönstret i sensorn eller den lokala hanterings konsolen väljer du **vidarebefordra.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Välj den blå knappen Skapa vidarebefordrings avisering.":::

1. Välj **skapa regler för vidarebefordran**. Definiera regel parametrarna i fönstret **skapa vidarebefordrings regel** .

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Skapa regler för din vidarebefordrings regel.":::

    | Parameter | Beskrivning |
    |--|--|
    | **Namn** | Namn på vidarebefordrings regel. |
    | **Välj allvarlighets grad** | Den lägsta säkerhets nivå som incidenten ska vidarebefordra. Om t. ex. minor väljs, vidarebefordras mindre aviseringar och aviseringar över den här allvarlighets graden. |
    | **Protokoll** | Som standard väljs alla protokoll. Om du vill välja ett särskilt protokoll väljer du **Specific** och väljer det protokoll som regeln tillämpas på. |
    | **Motor** | Som standard ingår alla säkerhets motorer. Om du vill välja en speciell säkerhetsmotor som regeln tillämpas på väljer du **speciell** och väljer motorn. |
    | **System meddelanden** | Vidarebefordra sensor online/offline-status. Det här alternativet är bara tillgängligt om du har loggat in på Central Manager. |

1. Om du vill att Defender för IoT ska skicka till gångs information till Splunk väljer du **åtgärd** och väljer sedan **Skicka till Splunk-Server**.

1. Ange följande Splunk-parametrar.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Splunk-parametrarna som du bör ange på den här skärmen.":::

    | Parameter | Beskrivning |
    |--|--|
    | **Värd** | Splunk-serveradress |
    | **Port** | 8089 |
    | **Användarnamn** | Användar namn för Splunk-Server |
    | **Lösenord** | Splunk server-lösenord |

1. Välj **Skicka**

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [vidarebefordrar aviserings information](how-to-forward-alert-information-to-partners.md).
