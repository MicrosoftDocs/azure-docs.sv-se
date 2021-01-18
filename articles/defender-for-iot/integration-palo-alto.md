---
title: Palo--integrering
titleSuffix: Azure Defender for IoT
description: Defender för IoT har integrerat sin kontinuerliga brand vägg för brand Väggs skydd med Palo-världens nästa generations brand väggar för att möjliggöra blockering av kritiska hot, snabbare och mer effektivt.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 85a7622223861f857ce75b8136b509ba279f3d96
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558665"
---
# <a name="about-the-palo-alto-integration"></a>Om Palo--integrering

Defender för IoT har integrerat sin kontinuerliga brand vägg för brand Väggs skydd med Palo-världens nästa generations brand väggar för att möjliggöra blockering av kritiska hot, snabbare och mer effektivt.

Följande integrerings typer är tillgängliga:

- Automatiskt spärrnings alternativ: direkt Defender för IoT-Palo-och-integrering

- Skicka rekommendationer för blockering till det centrala hanterings systemet: Defender för IoT-Panorama-integrering

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Konfigurera omedelbar blockering av angiven Palo--brand vägg

I kritiska fall, till exempel aviseringar som är relaterade till skadlig kod, kan du aktivera automatisk blockering. Detta görs genom att konfigurera en vidarebefordrings regel i Defender för IoT som skickar spärrnings kommando direkt till en speciell Palo-brandvägg-brandvägg.

När Defender för IoT identifierar ett kritiskt hot skickar den en avisering som innehåller ett alternativ för att blockera den infekterade källan. Om du väljer **block källa** i aviseringens information aktive ras vidarebefordrings regeln, som skickar spärrnings kommandot till den angivna Palo--brand väggen.

Konfigurera:

1. I det vänstra fönstret väljer du **vidarebefordra**.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="Skärmen vidarebefordra avisering.":::

1. Välj **skapa vidarebefordrings regel**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Skapa vidarebefordrings regel":::

1. Så här konfigurerar du vidarebefordrings regeln för Palo-NGFW:  
 
   - Definiera standard regel parametrarna och välj **Skicka till Palo NGFW** i list rutan **åtgärder** .
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Redigera regeln för vidarebefordran.":::

1. I fönstret åtgärder ställer du in följande parametrar:

   - **Värd**: Ange IP-adressen för NGFW-servern.
   - **Port**: Ange NGFW-Server porten.
   - **Användar namn**: Ange NGFW-serverns användar namn.
   - **Lösen ord**: Ange lösen ordet för NGFW-servern.
   - **Konfigurera**: Konfigurera följande alternativ för att tillåta blockering av de misstänkta källorna av Palo-brand väggen:
     - **Blockera ogiltiga funktions koder**: protokoll överträdelser-otillåtet fält värde bryter mot ICS-protokoll specifikationen (potentiell sårbarhet).
     - **Blockera otillåtna uppdateringar av PLC-programmering/inbyggd program vara**: otillåtna PLC-ändringar.
     - **Blockera obehöriga PLC-stopp**: PLC stop (nedtid).
     - **Blockera aviseringar som rör skadlig kod**: blockering av industriella malware-försök (Triton, NotPetya osv.). Du kan välja alternativet för **Automatisk blockering**. I så fall körs blockeringen automatiskt och omedelbart.
     - **Blockera obehörig genomsökning**: obehörig sökning (potentiell rekognosering).
     
1. Välj **Skicka**.

Så här blockerar du den misstänkta källan: 

1. I rutan **aviseringar** väljer du den avisering som rör Palo--integrering. Dialog rutan **aviserings information** visas.
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Aviserings information":::

1. Om du vill blockera den misstänkta källan automatiskt väljer du **block källa**. Dialog rutan **Bekräfta** visas.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Bekräfta blockering på bekräfta-skärmen.":::

1. I dialog rutan **Bekräfta** väljer du **OK**. Den misstänkta källan blockeras av Palo-brand väggen.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Sändning av spärrnings principer till Palo--Panorama

Defender för IoT-och Palo-nätverk har en inaktive rad integrering som automatiskt skapar nya principer i Palo-nätverk NMS, Panorama. Den här integrationen kräver bekräftelse av Panorama administratören och tillåter inte automatisk blockering.

Integrationen är avsedd för följande incidenter:

- **Otillåtna PLC-ändringar:** En uppdatering av steg logiken eller den inbyggda program varan för en enhet. Detta kan representera en legitim aktivitet eller ett försök att kompromettera enheten. Kompromissen kan inträffa genom att infoga skadlig kod, t. ex. en trojansk (fjärråtkomst) eller parametrar som orsakar den fysiska processen, till exempel en snurrande turbin, för att köras på ett osäkert sätt.

- **Protokoll fel:** En paket struktur eller ett fält värde som strider mot protokoll specifikationen. Detta kan representera ett felkonfigurerat program eller ett skadligt försök att kompromettera enheten. Exempelvis orsaka ett buffertspill i mål enheten.

- **PLC stopp:** Ett kommando som gör att enheten slutar fungera och därmed riskerar den fysiska processen som styrs av PLC.

- **Industriell skadlig kod hittades i ICS-nätverket:** Skadlig kod som manipulerar ICS-enheter med sina ursprungliga protokoll, till exempel TRITON och Industroyer. Defender för IoT identifierar även skadlig kod som har flyttats senare till ICS-och SCADA-miljön, till exempel Conficker, WannaCry och NotPetya.

- **Genomsök skadlig kod:** Rekognosering-verktyg som samlar in data om system konfiguration i en för hands attack fas. Havex-trojanen söker till exempel i industriella nätverk efter enheter som använder OPC, vilket är ett standard protokoll som används av Windows-baserade SCADA-system för att kommunicera med ICS-enheter.

## <a name="the-process"></a>Processen

När Defender för IoT identifierar ett förkonfigurerat användnings fall läggs knappen **block källa** till i aviseringen. När **CyberX** -användaren sedan väljer knappen **block källa** skapar Defender för IoT principer i panoramat genom att skicka den fördefinierade regeln för vidarebefordran.

Principen tillämpas endast när Panorama administratören push-överför den till relevanta NGFW i nätverket.

I IT-nätverk kan det finnas dynamiska IP-adresser. Därför måste principen baseras på FQDN (DNS-namn) och inte IP-adressen för dessa undernät. Defender for IoT utför omvänd sökning och matchar enheter med dynamisk IP-adress till sitt fullständiga domän namn (DNS-namn) med ett konfigurerat antal timmar.

Dessutom skickar Defender för IoT ett e-postmeddelande till relevant Panorama användare för att meddela att en ny princip som skapats av Defender för IoT väntar på godkännande. I bilden nedan presenteras Defender för IoT-Panorama integrations arkitektur.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Integrerings arkitektur för CyberX-Panorama":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Skapa blockerings principer för Panorama i Defender för IoT-konfiguration

### <a name="to-configure-dns-lookup"></a>Konfigurera DNS-sökning

1. I det vänstra fönstret väljer du **Systeminställningar**.

1. I fönstret **system inställningar** väljer du **DNS-inställningar** :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Konfigurera DNS-inställningarna.":::

1. I dialog rutan **Redigera DNS-inställningar** anger du följande parametrar:

   - **Status**: status för DNS-matcharen.

   - **DNS-serveradress**: Ange IP-adressen eller FQDN för nätverks-DNS-servern.
   - **DNS-server port**: Ange den port som används för att fråga DNS-servern.
   - **Undernät**: Ange det dynamiska IP-adressens under näts intervall. Intervallet som Defender för IoT omvändt söker efter sin IP-adress på DNS-servern för att matcha deras aktuella FQDN-namn.
   - **Schemalägg omvänd sökning**: definiera alternativ för schemaläggning enligt följande:
     - Vid vissa tidpunkter: Ange när den omvända sökningen ska utföras dagligen.
     - Efter fasta intervall (i timmar): ange frekvensen för att utföra omvänd sökning.
   - **Antal etiketter**: instruera Defender for IoT att automatiskt lösa nätverks-IP-adresser till enhets-FQDN. <br />Om du vill konfigurera DNS-FQDN-matchning lägger du till antalet domän etiketter som ska visas. Upp till 30 tecken visas från vänster till höger.
1. Välj **Spara**.
1. För att se till att dina DNS-inställningar är korrekta väljer du **Lookup-test**. Testet ser till att DNS-serverns IP-adress och DNS-serverns port är korrekt inställda.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Konfigurera en vidarebefordrings regel för att blockera misstänkt trafik med Palo-brand väggen

1. I det vänstra fönstret väljer du **vidarebefordra**. Fönstret vidarebefordran visas.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="Sidan vidarebefordran.":::

1. I fönstret **vidarebefordra** väljer du **skapa vidarebefordrings regel**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Skapa vidarebefordrings regel":::

1. Så här konfigurerar du vidarebefordrings regeln för Palo--Panorama:

   Definiera standard regel parametrarna och i list rutan **åtgärder** väljer du **Skicka till Palo-**. Fönstret åtgärds information visas.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Välj åtgärd":::

1. I fönstret åtgärder ställer du in följande parametrar:

   - **Värd**: Ange IP-adressen för Panorama servern.

   - **Port**: Ange port för Panorama servern.
   - **Användar namn**: Ange användar namnet för Panorama servern.
   - **Lösen ord**: Ange lösen ordet för Panorama servern.
   - **Rapport adress**: definiera hur blockeringen ska utföras enligt följande:
   
     - **Efter IP-adress**: skapar alltid spärr av principer i Panorama baserat på IP-adressen.
     
     - **Av FQDN eller IP-adress**: skapar spärrnings principer på Panorama baserat på FQDN om det finns, annars av IP-adressen.
     
   - **E-post**: Ange e-postadressen för e-post för princip meddelanden
     > [!NOTE]
     > Kontrol lera att du har konfigurerat en e-postserver i Defender for IoT. Om ingen e-postadress har angetts skickar inte Defender för IoT ett e-postmeddelande om ett e-postmeddelande.
   - **Köra en DNS-sökning vid varnings identifiering (kryss ruta)**: när alternativet via FQDN eller IP-adress har angetts i rapport adressen. Den här kryss rutan är markerad som standard. Om endast IP-adressen har angetts är det här alternativet inaktiverat...
   - **Konfigurera**: Ställ in följande alternativ för att tillåta blockering av de misstänkta källorna av Palo:
   
     - **Blockera ogiltiga funktions koder**: protokoll överträdelser-otillåtet fält värde som bryter mot ICS, protokoll specifikation (potentiell sårbarhet).
     
     - **Blockera otillåtna uppdateringar av PLC-programmering/inbyggd program vara**: otillåtna PLC-ändringar.
     
     - **Blockera obehöriga PLC-stopp**: PLC stop (nedtid).
     
     - **Blockera aviseringar som rör skadlig kod**: blockering av industriella malware-försök (Triton, NotPetya osv.). Du kan välja alternativet för **Automatisk blockering**. I så fall körs blockeringen automatiskt och omedelbart.
     
     - **Blockera obehörig genomsökning**: obehörig sökning (potentiell rekognosering).
     
1. Välj **Skicka**.

### <a name="to-block-the-suspicious-source"></a>Så här blockerar du den misstänkta källan

1. I rutan **aviseringar** väljer du den avisering som rör Palo--integrering. Dialog rutan **information om aviseringen** visas.

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Aviserings information":::        

1. Om du vill blockera den misstänkta källan automatiskt väljer du **block källa**.

1. I dialog rutan **Bekräfta** väljer du **OK.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirm":::

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [vidarebefordrar aviserings information](how-to-forward-alert-information-to-partners.md).
