---
title: Om Forescout-integrering
description: Azure Defender för IoT-integrering med Forescout-plattformen ger en ny nivå av centraliserad synlighet, övervakning och kontroll för IoT och landskap.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784075"
---
# <a name="about-the-forescout-integration"></a>Om Forescout-integrering

Azure Defender för IoT levererar en ICS-och IoT cybersäkerhet-plattform som skapats av blå grupp experter med en spårnings post för den försvarande viktiga nationella infrastrukturen. Defender för IoT är den enda plattformen med patenterad ICS-medveten hot analys och maskin inlärning. Defender för IoT tillhandahåller:

- Omedelbar insikter om ICS är enhets landskapet och innehåller en mängd information om attribut.
- ICS-medvetna djup inbäddad kunskap om protokoll, enheter, program och deras beteenden.
- Omedelbara insikter om sårbarheter och kända hot och noll dagar.
- En automatiserad teknik för att utforma hot modellering för att förutse de mest sannolika vägarna till riktade ICS-attacker via patentskyddad analys.

Med Defender for IoT-integrering med Forescout-plattformen får du en ny nivå av centraliserad synlighet, övervakning och kontroll för IoT och landskap.

Dessa bryggade plattformar möjliggör automatisk synlighet och hantering av enheter till tidigare otillgängliga ICS-enheter och silode arbets flöden.

Integrationen tillhandahåller SOC-analytiker med detaljerad insyn i andra protokoll som distribueras i industriella miljöer. Information finns tillgänglig för objekt som inbyggd program vara, enhets typer, operativ system och risk analys resultat baserat på patentskyddade Azure Defender för IoT-tekniker.

> [!Note]
> Referenser till CyberX hänvisar till Azure Defender för IoT.
## <a name="devices"></a>Enheter

### <a name="device-visibility-and-management"></a>Synlighet och hantering av enheter

Enhetens lager berikas med kritiska attribut som identifieras av Defender för IoT-plattformen. Det innebär att du:

- Få heltäckande och kontinuerlig insyn i landskaps enhetens landskap från ett enda fönster i glaset.
- Få information i real tid om andra risker.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Enhets inventering":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Information om enhet":::

### <a name="device-control"></a>Enhetskontroll

Forescout-integreringen bidrar till att minska tiden som krävs för organisationer för industriella och kritiska infrastrukturer för att identifiera, undersöka och agera på cyberhot-hot.

- Använd Azure Defender för IoT-enhets information för att stänga säkerhets cykeln genom att utlösa Forescout princip åtgärder. Du kan till exempel automatiskt skicka e-postaviseringar till SOC-administratörer när vissa protokoll identifieras eller när information om inbyggd program vara ändras.

- Korrelera Defender för IoT-information med andra *Forescout eyeExtended* -moduler som övervakar övervakning, incident hantering och enhets kontroll.

## <a name="system-requirements"></a>Systemkrav

- Azure Defender för IoT version 2,4 eller senare
- Forescout version 8,0 eller senare
- En licens för *Forescout eyeExtend* -modulen för Azure Defender för IoT-plattformen.

### <a name="getting-more-forescout-information"></a>Få mer Forescout-information

Mer information om Forescout-plattformen finns i [Forescout Resource Center](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Systeminstallation

Den här artikeln beskriver hur du konfigurerar kommunikation mellan Defender för IoT-plattformen och Forescout-plattformen.

### <a name="set-up-the-defender-for-iot-platform"></a>Konfigurera Defender för IoT-plattformen

För att säkerställa kommunikation från Defender för IoT till Forescout, genererar du en åtkomsttoken i Defender för IoT.

Med åtkomsttoken kan externa system komma åt data som identifieras av Defender för IoT och utföra åtgärder med dessa data med hjälp av den externa REST API över SSL-anslutningar. Du kan generera åtkomsttoken för att komma åt Azure Defender för IoT REST API.

Så här genererar du en token:

1. Logga in på den Defender for IoT-sensor som ska frågas av Forescout.

1. Välj **Systeminställningar** och välj sedan **åtkomsttoken** i avsnittet **Allmänt** . Dialog rutan **åtkomsttoken** öppnas.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Åtkomsttokens":::
1. Välj **Skapa ny token** i **dialog rutan åtkomsttoken.**
1. Ange en beskrivning av token i dialog rutan **ny åtkomsttoken** .
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Ny åtkomsttoken":::
1. Välj **Nästa**. Token visas i dialog rutan. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Visa token":::
   > [!NOTE]
   > *Registrera token på en säker plats. Du behöver den när du konfigurerar Forescout-plattformen*.
1. Välj **Slutför**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Slutför tillägg av token":::

### <a name="set-up-the-forescout-platform"></a>Konfigurera Forescout-plattformen

Du kan konfigurera Forescout-plattformen för att kommunicera med en Defender för IoT-sensor.

Konfigurera:

1. Installera *Forescout eyeExtend-modulen för CyberX* på Forescout-plattformen.

1. Logga in på den motverkande konsolen och välj **alternativ** på **verktyg** -menyn. Dialog rutan **alternativ** öppnas.

1. Navigera till och välj mappen **moduler** .

1. I fönstret **moduler** väljer du **CyberX-plattform**. Fönstret Defender för IoT-plattform öppnas.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Inställningar för Azure Defender för IoT-modulen":::

   Ange följande information:

   - **Server adress** – ange IP-adressen för den Defender for IoT-sensor som ska frågas av Forescout-enheten.
   - **Åtkomsttoken – ange** den åtkomsttoken som skapats för den Defender för IoT-sensor som ska ansluta till Forescout-enheten. Information om hur du skapar en token finns i [Konfigurera Defender för IoT-plattformen](#set-up-the-defender-for-iot-platform).

1. Välj **Använd**.

Om du vill att Forescout-plattformen ska kommunicera med en annan sensor:

1. Skapa en ny åtkomsttoken i relevant Defender för IoT-sensor.

1. I dialog rutan **Forescout modules**  >  **CyberX Platform** :

   - Ta bort den information som visas.
   
   - Ange den nya sensor-IP-adressen och den nya informationen om åtkomsttoken.

### <a name="verify-communication"></a>Verifiera kommunikation

När du har konfigurerat Defender för IoT och Forescout öppnar du dialog rutan för sensorns åtkomsttoken i Defender för IoT.

Om **N/A** visas i fältet **används** för denna token fungerar inte anslutningen mellan sensorn och Forescout-enheten.

**Används** anger sista gången ett externt anrop med denna token togs emot.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Verifierar att token har tagits emot":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Visa Defender för IoT-identifieringar i Forescout

Så här visar du en enhets attribut:

1. Logga in på Forescout-plattformen och navigera sedan till **till gångs lagret**.

1. Navigera till **CyberX-plattformen**. Följande enhets attribut visas för enhets Visa enheter som identifieras av Defender för IoT.

   | Objekt | Beskrivning |
   |--|--|
   | Auktoriserad av Azure Defender för IoT | En enhet som har identifierats i nätverket av Defender för IoT under nätverks inlärnings perioden. |
   | Inbyggd programvara | Enhetens information om inbyggd program vara. Till exempel modell och versions information. |
   | Name | Namnet på enheten. |
   | Operativsystem | Enhetens operativ system. |
   | Typ | Typ av enhet. Till exempel en PLC-, historian-eller ingenjörs Station. |
   | Leverantör | Enhetens tillverkare. Till exempel Rockwell Automation. |
   | Risk nivå | Risk nivån beräknad av Defender för IoT. |
   | Protokoll | De protokoll som identifierades i trafiken som genereras av enheten. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Visa attributen för inbyggd program vara.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Visa attribut för leverantörer.":::

### <a name="viewing-more-details"></a>Visa mer information

Visa extra enhets information för enheter som dirigeras av Defender för IoT. Till exempel Forescout efterlevnad och princip information.

För att åstadkomma detta högerklickar du på en enhet från avsnittet **enhets inventerings värdar** . Dialog rutan värd information öppnas med ytterligare information.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Värd information":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Skapa Azure Defender för IoT-principer i Forescout

Forescout-principer kan användas för att automatisera kontroll och hantering av enheter som identifieras av Defender för IoT. Exempel:

- E-posta SOC-administratörer automatiskt när vissa versioner av den inbyggda program varan identifieras.

- Lägg till vissa Defender för IoT identifierade enheter i en Forescout-grupp för ytterligare hantering i arbets flöden för incidenter och säkerhet, till exempel med andra SIEM-integrationer.

Skapa en anpassad princip för Forescout med Defender för IoT med villkors egenskaper.

För att komma åt Defender för IoT-egenskaper:

1. Navigera till **egenskaps trädet** från dialog rutan **princip villkor** .

1. Expandera mappen **CyberX Platform** i **egenskaps trädet**. Det finns följande egenskaper för Defender for IoT.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Egenskaper":::

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [vidarebefordrar aviserings information](how-to-forward-alert-information-to-partners.md).
