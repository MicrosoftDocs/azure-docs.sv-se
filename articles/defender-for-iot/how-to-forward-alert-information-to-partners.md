---
title: Vidarebefordra aviseringsinformation
description: Du kan skicka aviserings information till partner system genom att arbeta med regler för vidarebefordran.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: bc405f7d4837bf81d9cfcd859d562b7152cfc54b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778805"
---
# <a name="forward-alert-information"></a>Vidarebefordra aviseringsinformation

Du kan skicka aviserings information till partner som integrerar med Azure Defender för IoT, till syslog-servrar, till e-postadresser med mera. Genom att arbeta med regler för vidarebefordran kan du snabbt leverera aviserings information till säkerhets intressenter.  

Syslog och andra standard åtgärder för vidarebefordran levereras med systemet. Fler vidarebefordrings åtgärder kan bli tillgängliga när du integrerar med partner leverantörer, till exempel Microsoft Azure Sentinel, ServiceNow eller Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Aviserings information.":::

Defender för IoT-administratörer har behörighet att använda regler för vidarebefordran.

## <a name="about-forwarded-alert-information"></a>Om vidarebefordrad aviserings information

Aviseringar ger information om en stor mängd säkerhets-och drift händelser. Exempel:

  - Datum och tid för aviseringen

  - Motor som identifierade händelsen

  - Aviserings rubrik och beskrivande meddelande

  - Allvarlighetsgrad för avisering

  - Käll-och mål namn och IP-adress

  - Misstänkt trafik har identifierats

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Adress genomsökning identifierat.":::

Relevant information skickas till partner system när regler för vidarebefordran skapas.

## <a name="create-forwarding-rules"></a>Skapa regler för vidarebefordran

Så här skapar du en ny vidarebefordrings regel:

1. Välj **vidarebefordran** på menyn på sidan.

   :: bildtyp = "content" källa = "media/How-to-work-with-Alerts-sensor/create-forwarding-rule-screen.png" Alt-text = "skapa en vidarebefordrings regel ikon."::

2. Välj **skapa vidarebefordrings regel**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Skapa en ny vidarebefordrings regel.":::

3. Ange namnet på vidarebefordrings regeln.

### <a name="forwarding-rule-criteria"></a>Villkor för vidarebefordrande regel 

Definiera villkor för att utlösa en vidarebefordrings regel. Genom att arbeta med kriterier för vidarebefordring kan du hitta och hantera mängden information som skickas från sensorn till externa system. Följande alternativ är tillgängliga:

**Protokoll**: Utlös bara vidarebefordrings regeln om den identifierade trafiken har körts över vissa protokoll. Välj de protokoll som krävs i list rutan eller Välj alla.

**Motorer**: Välj de motorer som krävs eller Välj alla. Aviseringar från valda motorer kommer att skickas.

**Allvarlighets grad**: Detta är den lägsta incidenten som ska vidarebefordras, i termer av allvarlighets grad. Om du till exempel väljer **mindre**, mindre aviseringar och aviseringar ovanför allvarlighets graden vidarebefordras. Nivåer är fördefinierade.

### <a name="forwarding-rule-actions"></a>Åtgärder för vidarebefordrande regler

Åtgärder för vidarebefordrings regel instruerar sensorn att vidarebefordra aviserings information till partner leverantörer eller-servrar. Du kan skapa flera åtgärder för varje vidarebefordrings regel.

Förutom de vidarebefordrings åtgärder som levereras med systemet kan andra åtgärder bli tillgängliga när du integrerar med partner leverantörer. 

#### <a name="email-address-action"></a>Åtgärd för e-postadress

Skicka e-post som innehåller aviserings informationen. Du kan ange en e-postadress per regel.

Så här definierar du e-post för vidarebefordrings regeln:

1. Ange en enda e-postadress. Om fler än ett e-postmeddelande måste skickas skapar du en annan åtgärd.

2. Ange tids zonen för tidsstämpeln för aviserings identifieringen på SIEM.

3. Välj **Skicka**.

#### <a name="syslog-server-actions"></a>Syslog-server åtgärder

Följande format stöds:

- SMS

- CEF-meddelanden

- LEEF-meddelanden

- Objekt meddelanden

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Skapa åtgärder för vidarebefordrings regler.":::

Ange följande parametrar:

- Syslog-värdnamn och-port.

- Protokollets TCP och UDP.

- Tidszon för tidstämpeln för aviserings identifieringen på SIEM.

- TLS-kryptering certifikat fil och nyckel fil för CEF-servrar (valfritt).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Konfigurera krypteringen för din vidarebefordrings regel.":::

| Data fält för syslog-textmeddelande | Beskrivning |
|--|--|
| Datum och tid | Datum och tid då syslog-serverdatorn fick informationen. |
| Prioritet | User. Alert |
| Värdnamn | Sensor-IP-adress |
| Protokoll | TCP eller UDP |
| Meddelande | Sensor: sensor namnet.<br /> Varning: rubriken på aviseringen.<br /> Typ: typen av avisering. Kan vara **protokoll överträdelse**, **princip överträdelse**, **skadlig kod**, **avvikelse** eller **drift**.<br /> Allvarlighets grad: aviseringens allvarlighets grad. Kan vara **Varning**, **mindre**, **större** eller **kritiska**.<br /> Källa: källans enhets namn.<br /> Käll-IP: käll enhetens IP-adress.<br /> Mål: målets enhets namn.<br /> Mål-IP: IP-adressen för mål enheten.<br /> Meddelande: meddelandets avisering.<br /> Aviserings grupp: den aviserings grupp som är associerad med aviseringen. |


| Syslog-objektets utdata | Beskrivning |
|--|--|
| Datum och tid |   Datum och tid då syslog-serverdatorn fick informationen. |  
| Prioritet |    User. Alert | 
| Värdnamn |    Sensor-IP | 
| Meddelande | Sensor namn: namnet på enheten. <br /> Aviserings tid: tiden som aviseringen identifierades: kan variera från tiden för syslog-serverdatorn och beror på tids zons konfigurationen för vidarebefordrings regeln. <br /> Aviserings rubrik: rubriken på aviseringen. <br /> Varnings meddelande: meddelandets aviserings meddelande. <br /> Allvarlighets grad för avisering: aviseringens allvarlighets grad: **Varning**, **mindre**, **Major** eller **kritisk**. <br /> Aviserings typ: **protokoll överträdelse**, **princip överträdelse**, **skadlig kod**, **avvikelse** eller **drift**. <br /> Protokoll: protokollet för aviseringen.  <br /> **Source_MAC**: IP-adress, namn, leverantör eller operativ system för käll enheten. <br /> Destination_MAC: IP-adress, namn, leverantör eller operativ system för målet. Om data saknas **, är värdet saknas.** <br /> alert_group: den aviserings grupp som är associerad med aviseringen. |


| Formatet syslog CEF output | Beskrivning |
|--|--|
| Datum och tid | Datum och tid då syslog-serverdatorn fick informationen. |
| Prioritet | User. Alert | 
| Värdnamn | Sensor-IP-adress |
| Meddelande | CEF: 0 <br />Azure Defender för IoT <br />Sensor namn: namnet på sensorn. <br />Sensor version <br />Aviserings rubrik: rubriken på aviseringen. <br />Msg: meddelandets avisering. <br />protokoll: protokollet för aviseringen. <br />allvarlighets grad: **Varning**, **mindre**, **större** eller **kritisk**. <br />typ: **protokoll överträdelse**, **princip överträdelse**, **skadlig kod**, **avvikelse** eller **drift**. <br /> Start: den tid då aviseringen identifierades. <br />Kan variera från tiden för syslog-serverdatorn och är beroende av tids zons konfigurationen för vidarebefordrings regeln. <br />src_ip: käll enhetens IP-adress.  <br />dst_ip: IP-adressen för mål enheten.<br />Kat: den aviserings grupp som är associerad med aviseringen.  |

| Formatet syslog LEEF output | Beskrivning |
|--|--|
| Datum och tid |   Datum och tid då syslog-serverdatorn fick informationen. |  
| Prioritet |    User. Alert | 
| Värdnamn |    Sensor-IP |
| Meddelande | Sensor namn: namnet på Azure Defender för IoT-enheten. <br />LEEF: 1.0 <br />Azure Defender för IoT <br />Mäta  <br />Sensor version <br />Azure Defender för IoT-avisering <br />title: aviseringens rubrik. <br />Msg: meddelandets avisering. <br />protokoll: protokollet för aviseringen.<br />allvarlighets grad: **Varning**, **mindre**, **större** eller **kritisk**. <br />typ: typ av avisering: **protokoll överträdelse**, **princip överträdelse**, **skadlig kod**, **avvikelse** eller **drift**. <br />Start: tiden för aviseringen.Observera att det kan skilja sig från tiden för syslog-serverdatorn. (Detta beror på tids zons konfigurationen.) <br />src_ip: käll enhetens IP-adress.<br />dst_ip: IP-adressen för mål enheten. <br />Kat: den aviserings grupp som är associerad med aviseringen. |

När du har angett all information väljer du **Skicka**.

#### <a name="netwitness-action"></a>Åtgärden netvittne

Skicka aviserings information till en netvittnes Server.

Definiera parametrar för vidarebefordring av net-vittne:

1. Ange netvittne- **värdnamn** och **portinformation** .

2. Ange tids zonen för tidsstämpeln för aviserings identifieringen på SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Lägg till en tidszon i din vidarebefordrings regel.":::

3. Välj **Skicka**.

#### <a name="integrated-vendor-actions"></a>Integrerade leverantörs åtgärder

Du kan ha integrerat systemet med en säkerhet, enhets hantering eller en annan bransch leverantör. Dessa integreringar gör det möjligt att:

  - Skicka aviserings information.

  - Skicka information om enhets inventering.

  - Kommunicera med brand väggar på leverantörs sidan.

Integreringar hjälper till att överbrygga tidigare silode säkerhetslösningar, förbättra enhets synlighet och påskynda hela systemet för att minimera riskerna snabbare.

Använd avsnittet åtgärder för att ange autentiseringsuppgifter och annan information som krävs för att kommunicera med integrerade leverantörer.

Mer information om hur du konfigurerar regler för vidarebefordran för integreringarna finns i relevanta partners integrerings artiklar.

### <a name="test-forwarding-rules"></a>Testa regler för vidarebefordran

Testa anslutningen mellan sensorn och partner servern som definieras i reglerna för vidarebefordran:

1. Välj regeln i dialog rutan **vidarebefordrings regel** .

2. Markera rutan **mer** .

3. Välj **Skicka test meddelande**.

4. Gå till ditt partner system för att kontrol lera att den information som skickas av sensorn mottogs.

### <a name="edit-and-delete-forwarding-rules"></a>Redigera och ta bort regler för vidarebefordran 

Så här redigerar du en vidarebefordrings regel:

- På skärmen **vidarebefordra regel** väljer du **Redigera** under menyn **mer** . Gör önskade ändringar och välj **Skicka**.

Så här tar du bort en vidarebefordrings regel:

- På skärmen **vidarebefordra regel** väljer du **ta bort** under List menyn **mer** . I dialog rutan **Varning** väljer du **OK**.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Regler för vidarebefordran och undantags regler för varningar

Administratören kan ha definierat undantags regler för varningar. Dessa regler hjälper administratörer att få mer detaljerad kontroll över varnings utlösare genom att instruera sensorn att ignorera aviserings händelser baserat på olika parametrar. Dessa parametrar kan omfatta enhets adresser, aviserings namn eller vissa sensorer.

Det innebär att de vidarebefordrings regler som du definierar kan ignoreras baserat på undantags regler som administratören har skapat. Undantags regler definieras i den lokala hanterings konsolen.

## <a name="see-also"></a>Se även

[Påskynda aviserings arbets flöden](how-to-accelerate-alert-incident-response.md)
