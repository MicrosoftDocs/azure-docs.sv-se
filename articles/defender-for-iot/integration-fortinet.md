---
title: Om Fortinet-integrering
titleSuffix: Azure Defender for IoT
description: Defender för IoT och Fortinet har upprättat ett teknik partnerskap för att upptäcka och stoppa attacker i IoT-och ICS-nätverk.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 313db90d4c9be30ef588b00caf1d6e4ce32b113b
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558552"
---
# <a name="defender-for-iot-and-fortinet-iiot-and-ics-threat-detection--prevention"></a>Defender för IoT och Fortinet IIoT och hot identifiering för ICS & skydd

Defender för IoT minimerar IIoT-och ICS-och SCADA-risker med patenterade, ICS-medvetna självstudier som ger omedelbara insikter om ICS-enheter, sårbarheter och hot på mindre än en timme och utan att behöva lita på agenter, regler eller signaturer, specialiserade kunskaper eller tidigare kunskap om miljön.

Defender för IoT och Fortinet har upprättat ett teknik partnerskap för att upptäcka och stoppa attacker i IoT-och ICS-nätverk.

Tillsammans förhindrar Fortinet och Defender för IoT:

- Obehöriga ändringar av programmerbara logiska styrenheter.

- Skadlig kod som manipulerar ICS-och IoT-enheter via deras ursprungliga protokoll.
- Rekognosering-verktyg från insamling av data.
- Protokoll överträdelser orsakade av felaktiga konfigurationer eller skadliga attacker.

## <a name="defender-for-iot-and-fortigate-joint-solution"></a>Defender för IoT och FortiGate gemensam lösning

Defender för IoT identifierar avvikande beteende i IoT-och ICS-nätverk och levererar denna information till FortiGate och FortiSIEM, enligt följande:

- **Synlighet:** Informationen från Defender för IoT ger FortiSIEM-administratörer insyn i tidigare osynliga IoT-och ICS-nätverk.

- **Blockera skadliga attacker:** FortiGate-administratörer kan använda den information som identifieras av Defender för IoT för att skapa regler för att stoppa avvikande beteende, oavsett om beteendet orsakas av kaotiska-aktörer eller felkonfigurerade enheter, innan den orsakar skada på produktion, vinst eller användare.

## <a name="the-defender-for-iot-platform"></a>Defender för IoT-plattform

Med Defender för IoT Platform identifieras och används finger avtryck som icke-hanterade IoT-och ICS-enheter samtidigt som de kan övervaka riktade attacker och skadlig kod. Funktionerna för risk-och sårbarhets hantering omfattar automatiserad hot modellering samt omfattande rapportering om både slut punkts-och nätverks sårbarheter, med rekommendationer för riskhantering som prioriteras.  

Defender for IoT-plattformen är agent lös, icke-påträngande och lätt att distribuera, vilket ger åtgärds bara insikter mindre än en timme efter att ha anslutit till nätverket.

## <a name="fortinet-fortisiem"></a>Fortinet FortiSIEM

Gällande säkerhet kräver att alla enheter visas och att infrastrukturen i real tid är synlig, men även med kontext. Vilka enheter som utgör ett hot, vad är deras förmåga att hantera det hot som företag möter, och inte det brus som flera säkerhets verktyg skapar.

Slut punkter, IoT, infrastruktur, säkerhets verktyg, program, virtuella datorer och molnet är en del av de saker som administratörer behöver för att skydda och övervaka ständigt.

FortiSIEM och Fortinets säkerhets incident för flera leverantörer och händelse hanterings lösningen ger IT-kunskaper, synlighet, korrelationer, automatiserade svar och reparationer i en enda skalbar lösning.

Med hjälp av en Business Services-vy minskas komplexiteten vid hanteringen av nätverks-och säkerhets åtgärder, vilket frigör resurser, vilket förbättrar identifieringen av intrång. FortiSIEM tillhandahåller kors korrelation samtidigt som du använder maskin inlärning och UEBA för att förbättra svars tiden för att stoppa överträdelser innan de inträffar.

## <a name="fortinet-fortigate-next-generation-firewalls"></a>Fortinet FortiGate nästa generations brand väggar

FortiGate i nästa generations brand vägg (NGFWs) använder sig av säkerhets processorer och hot information-säkerhets tjänster som bygger på AI-drivna FortiGuard labb för att leverera det mest klassificerade skyddet, högpresterande kontroller av tydlig och krypterad trafik.

Nästa generations brand väggar minskar kostnaderna och komplexiteten med fullständig insyn i program, användare och nätverk och ger bästa möjliga säkerhet. Som en del av den Fortinet säkerhets infrastruktur som nästa generations brand vägg kan kommunicera i Fortinet: s omfattande säkerhets portfölj och partner säkerhets lösningar i en miljö för att dela Hot information och förbättra säkerhets position.

## <a name="use-cases"></a>Användningsfall

### <a name="prevent-unauthorized-changes-to-programmable-logic-controllers"></a>Förhindra obehöriga ändringar av programmerbara logik kontroller

Organisationer använder programmerbara Logic controllers (PLCs) för att hantera fysiska processer, till exempel robot larm i fabriker, snurrande turbiner i likvidations anläggningar och centrifuger i kärnkrafts anläggningar.

En uppdatering av steg logiken eller den inbyggda program varan för en PLC kan representera en legitim aktivitet eller ett försök att kompromettera enheten genom att infoga skadlig kod.  Defender för IoT kan upptäcka otillåtna ändringar i PLCs och sedan leverera information om den ändringen till både FortiSIEM och FortiGate. Med den informationen kan FortSIEM-administratörer bestämma hur du bäst ska minimera lösningen. Ett alternativ för minskning skulle vara att skapa en regel i FortiGate som stoppar ytterligare kommunikation till den berörda enheten.

### <a name="stop-ransomware-before-it-damages-iot-and-ics-networks"></a>Stoppa utpressnings troprogram innan den skadar IoT-och ICS-nätverk

Defender för IoT övervakar kontinuerligt IoT-och ICS-nätverk för beteenden som orsakas av utpressnings tro Jan, till exempel LockerGoga, WannaCry och NotPetya. När det är integrerat med FortiSIEM och FortiGate kan Defender för IoT leverera information om förekomsten av de här typerna av utpressnings tro Jan så att ForiSIEM-operatörer kan se var skadlig kod är, och FortiGate-administratörer kan stoppa utpressningarna från att sprida och wreaking fler oreda.

## <a name="set-sending-defender-for-iot-alerts-to-fortisiem"></a>Ställ in skicka Defender för IoT-aviseringar till FortiSIEM

Skydds uppgifter för IoT-aviseringar ger information om en stor mängd säkerhets händelser, inklusive:

- Avvikelser från inlärtd bas linje nätverks aktivitet
- Identifieringar av skadlig kod
- Identifieringar baserat på misstänkt drifts ändringar
- Nätverks avvikelser
- Protokoll avvikelser från protokoll specifikation

:::image type="content" source="media/integration-fortinet/address-scan.png" alt-text="Skärm bild av fönstret adress genomsökning har upptäckts.":::

Du kan konfigurera Defender för IoT att skicka aviseringar till FortiSIEM-servern, där aviserings information visas i fönstret Analytics:

:::image type="content" source="media/integration-fortinet/analytics.png" alt-text="Skärm bild av fönstret Analytics.":::

Varje Defender för IoT-avisering tolkas utan någon annan konfiguration på FortiSIEM-sidan och de visas i FortiSIEM som säkerhets händelser. Följande händelse information visas som standard:

:::image type="content" source="media/integration-fortinet/event-detail.png" alt-text="Visa din händelse information i fönstret händelse information.":::

## <a name="define-alert-forwarding-rules"></a>Definiera regler för vidarebefordran av notifieringsregler

Använd Defender för IoTs regler för vidarebefordran för att skicka aviserings information till FortiSIEM.

Det finns alternativ för att anpassa aviserings reglerna baserat på:

- Vissa protokoll har identifierats.

- Händelsens allvarlighets grad.

- Defender för IoT-motor som identifierar händelser.

Så här skapar du en vidarebefordrings regel

1. I det vänstra fönstret i sensorn eller den lokala hanterings konsolen väljer du **vidarebefordra**.

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="Visa regler för vidarebefordran i vidarebefordrings fönstret.":::](media/integration-fortinet/forwarding-view.png#lightbox)

2. Välj **skapa regler för vidarebefordran**. I fönstret **skapa vidarebefordrings regel** och definiera din regels parametrar.

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="Skapa en ny regel för vidarebefordring av fönster.":::

    | Parameter | Beskrivning |
    |--|--|
    | **Namn** | Namn på vidarebefordrings regel. |
    | **Välj allvarlighets grad** | Lägsta säkerhets nivå som incidenten ska vidarebefordra. Om t. ex. **minor** väljs, vidarebefordras mindre aviseringar och aviseringar över den här allvarlighets graden. |
    | **Protokoll** | Som standard väljs alla protokoll.<br><br>Om du vill välja ett särskilt protokoll väljer du **Specific** och väljer det protokoll som regeln tillämpas på. |
    | **Motor** | Som standard ingår alla säkerhets motorer.<br><br>Om du vill välja en speciell säkerhetsmotor som regeln tillämpas på väljer du **speciell** och väljer motorn. |
    | **System meddelanden** | Vidarebefordra sensor online/offline-status. Det här alternativet är bara tillgängligt om du har loggat in på den lokala hanterings konsolen. |

3. Om du vill att Defender för IoT ska skicka, Avisera information till FortiSIEM väljer du **åtgärd** och väljer sedan **Skicka till FortiSIEM**.

    :::image type="content" source="media/integration-fortinet/forward-rule.png" alt-text="Skapa en vidarebefordrings regel och välj Skicka till Fortinet.":::

4. Ange information om FortiSIEM-servern.

    :::image type="content" source="media/integration-fortinet/details.png" alt-text="Lägg till FortiSIEm-information i vidarebefordrings regeln":::

    | Parameter | Beskrivning |
    | --------- | ----------- |
    | **Värd** | FortiSIEM-serveradress. |
    | **Port** | FortiSIEM server-port. |
    | **Informationen** | Tidstämpeln för aviserings identifieringen. |

5. Välj **Skicka**.

## <a name="set-blocking-suspected-traffic-using-fortigate-firewall"></a>Ange blockering av misstänkt trafik med Fortigate-brandvägg

Du kan ställa in spärrnings principer automatiskt i FortiGate-brandväggen baserat på Defender för IoT-aviseringar.

:::image type="content" source="media/integration-fortinet/firewall.png" alt-text="Vy över fönstret för FortiGate-brandväggen.":::

Följande avisering kan till exempel blockera skadlig Källa:

:::image type="content" source="media/integration-fortinet/suspicion.png" alt-text="Fönstret NotPetya skadlig kod":::

Ange en FortiGate brand Väggs regel som blockerar den här skadliga källan:

1. I FortiGate skapar du en API-nyckel som krävs för vidarebefordrings regeln för Defender för IoT. Mer information finns i [skapa en API-nyckel i Fortigate](#create-an-api-key-in-fortigate).

1. I Defender för IoT- **vidarebefordran** anger du en vidarebefordrings regel som blockerar varningar som rör skadlig kod. Mer information finns i [blockera misstänkt trafik med Fortigate-brandväggen](#block-suspected-traffic-using-the-fortigate-firewall).

1. I Defender för IoT blockerar **aviseringar** en skadlig källa. Mer information finns i [blockera den misstänkta källan](#block-the-suspicious-source).

    Den skadliga käll adressen visas i fönstret FortiGage **Administrator** .

   :::image type="content" source="media/integration-fortinet/administrator.png" alt-text="FortiGate administratörs fönster vy.":::

   Spärr principen skapades automatiskt och visas i fönstret FortiGate **IPv4-princip** .

   :::image type="content" source="media/integration-fortinet/policy.png" alt-text="Fönstret FortiGate IPv4-princip.":::

   :::image type="content" source="media/integration-fortinet/edit.png" alt-text="Redigera FortiGate för IPv4-princip.":::

## <a name="create-an-api-key-in-fortigate"></a>Skapa en API-nyckel i FortiGate

1. I Fortigate väljer du **system**  >  **Administratörs profiler**.

1. Skapa en profil med följande behörigheter:

    :::image type="content" source="media/integration-fortinet/admin-profile.png" alt-text="Dator Beskrivning genereras automatiskt":::

1. Välj **system**  >  **Administratörer** och skapa en ny **REST API administratör**.

    :::image type="content" source="media/integration-fortinet/cellphone.png" alt-text="Beskrivning av cell telefon skapas automatiskt":::

    | Parameter | Beskrivning |
    | --------- | ----------- |
    | **Användarnamn** | Namn på vidarebefordrings regel. |
    | **Kommentarer** | Den lägsta säkerhets nivå som incidenten ska vidarebefordra. Om t. ex. minor väljs, vidarebefordras mindre aviseringar och aviseringar över den här allvarlighets graden. |
    | **Administratörs profil** | I list rutan väljer du det profil namn som du definierade i föregående steg. |
    | **PKI-grupp** | Inaktivera. |
    | **CORS Tillåt ursprung** | Aktivera. |
    | **Begränsa inloggning till betrodda värdar** | Lägger till en IP-adress för de sensorer och CMs som ska anslutas till FortiGate. |

1. Spara API-nyckeln.

    :::image type="content" source="media/integration-fortinet/api-key.png" alt-text="Beskrivning av cell telefon genererar automatiskt ny API-nyckel":::

## <a name="block-suspected-traffic-using-the-fortigate-firewall"></a>Blockera misstänkt trafik med FortiGate-brandväggen

1. I det vänstra fönstret väljer du **vidarebefordra**.

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="Alternativet vidarebefordra fönster i en sensor.":::](media/integration-fortinet/forwarding-view.png#lightbox)

1. Välj **skapa regler för vidarebefordran** och definiera regel parametrar.

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="Skärm bild av fönstret Skapa vidarebefordrings regel":::

    | Parameter | Beskrivning |
    | --------- | ----------- |
    | **Namn** | Namn på vidarebefordrings regel. |
    | **Välj allvarlighets grad** | Den lägsta säkerhets nivå som incidenten ska vidarebefordra. Om t. ex. **minor** väljs, vidarebefordras mindre aviseringar och aviseringar över den här allvarlighets graden. |
    | **Protokoll** | Som standard väljs alla protokoll.<br><br>Om du vill välja ett särskilt protokoll väljer du **Specific** och väljer det protokoll som regeln tillämpas på. |
    | **Motor** | Som standard ingår alla säkerhets motorer.<br><br>Om du vill välja en speciell säkerhetsmotor som regeln tillämpas på väljer du **speciell** och väljer motorn. |
    | **System meddelanden** | Forward-sensor *online och offline* -status. Det här alternativet är bara tillgängligt om du har loggat in på den lokala hanterings konsolen. |

1. Om du vill att Defender för IoT ska skicka brand Väggs regler till FortiGate väljer du **åtgärd** och väljer sedan **Skicka till Fortigate**.

    :::image type="content" source="media/integration-fortinet/fortigate.png" alt-text="Fönstret Skapa vidarebefordrings regel och välj Skicka till FortiGate":::

1. Så här konfigurerar du regeln för vidarebefordring av FortiGate:

    :::image type="content" source="media/integration-fortinet/configure.png" alt-text="Konfigurera fönstret Skapa vidarebefordrings regel":::

1. I fönstret **åtgärder** ställer du in följande parametrar:

    | Parameter | Beskrivning |
    |--|--|
    | Värd | IP-adress typen för FortiGate-servern. |
    | Port | Port typen för FortiGate-servern. |
    | Användarnamn | Användar namns typen för FortiGate-servern. |
    | API-nyckel | Ange den API-nyckel som du skapade i FortiGate. |
    | Inkommande gränssnitt| Definiera hur blockeringen ska köras:<br /><br />**Efter IP-adress**: skapar alltid spärr av principer i Panorama baserat på IP-adress.<br /><br />**Av fullständigt domän namn eller IP-adress**: skapar spärrnings principer på Panorama baserat på FQDN om det finns, annars IP-adress.| 
    | Utgående gränssnitt |Ange e-postadressen för e-postmeddelandet för princip meddelanden. <br /><br /> **Obs**: kontrol lera att du har konfigurerat en e-postserver i XSense. Om ingen e-postadress har angetts skickar XSense inte något e-postmeddelande.| 
    |Konfigurera| Konfigurera följande alternativ för att tillåta blockering av de misstänkta källorna av FortiGate-brand väggen: <br /><br />**Blockera ogiltiga funktions koder**: protokoll överträdelser-otillåtet fält värde som strider mot ICS-protokollets specifikation (potentiell sårbarhet)<br /><br />**Blockera otillåtna uppdateringar av PLC-programmering/inbyggd program vara**: otillåtna PLC-ändringar<br /><br />**Blockera obehöriga PLC-stopp**: PLC stop (nertid)<br /><br />**Blockera aviseringar som rör skadlig kod**: blockering av de industriella försöken med skadlig kod (Triton, NotPetya osv.). Du kan välja alternativet för **Automatisk blockering**. I så fall körs blockeringen automatiskt och omedelbart.<br /><br />*Blockera obehörig genomsökning*: obehörig genomsökning (potentiell rekognosering)

1. Välj **Skicka**.

## <a name="block-the-suspicious-source"></a>Blockera den misstänkta källan

1. I fönstret **aviseringar** väljer du den avisering som rör Fortinet-integrering.

    :::image type="content" source="media/integration-fortinet/unauthorized.png" alt-text="Den otillåtna PLC-programmerings fönstret":::

1. Om du vill blockera den misstänkta källan automatiskt väljer du **block källa**.

    :::image type="content" source="media/integration-fortinet/confirm.png" alt-text="Bekräftelse fönstret.":::

1. I dialog rutan **Bekräfta** väljer du **OK**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [vidarebefordrar aviserings information](how-to-forward-alert-information-to-partners.md).
