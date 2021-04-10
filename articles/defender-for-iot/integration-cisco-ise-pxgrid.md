---
title: Om Cisco ISE-pxGrid-integrering
description: Att överbrygga funktionerna i Defender för IoT med Cisco ISE pxGrid, ger säkerhets teamen en oöverträffad enhets synlighet för företags eko systemet.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784092"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Om Cisco ISE-pxGrid-integrering

Defender for IoT levererar den enda ICS-och IoT cybersäkerhet-plattform som skapats av Blue-team-experter med en spårnings post som skyddar nationell infrastruktur och den enda plattformen med patenterad ICS-medveten hot analys och maskin inlärning. Defender för IoT tillhandahåller:

- Omedelbara insikter om ICS-enheter, sårbarheter och kända och noll dagars hot.

- ICS-medvetna djup inbäddad kunskap om protokoll, enheter, program och deras beteenden.

- En automatiserad teknik för att utforma hot modellering för att förutse de mest sannolika vägarna till riktade ICS-attacker via patentskyddad analys.

## <a name="powerful-device-visibility-and-control"></a>Kraftfull enhets synlighet och kontroll

Defender for IoT-integrering med Cisco ISE pxGrid ger en ny nivå av centraliserad synlighet, övervakning och kontroll för den mittersta landskaps nivån.

Dessa bryggade plattformar möjliggör automatisk synlighet och skydd av enheter till tidigare otillgängliga ICS-och IIoT-enheter.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>IIoT för ICS och enhets synlighet: omfattande och djup

Patenterad Defender för IoT-tekniker garanterar omfattande och djupgående ICS-och IIoT enhets identifiering och inventerings hantering för företags data.

Enhets typer, tillverkare, öppna portar, serie nummer, inbyggda program varu revisioner, IP-adresser och MAC-Datadata med mera uppdateras i real tid. Defender för IoT kan bättre förbättra synlighet, identifiering och analys från den här bas linjen genom att integrera med kritiska företags data källor. Till exempel CMDBs, DNS, brand väggar och webb-API: er.

Dessutom kombinerar Defender för IoT-plattformen passiv övervakning och valfri selektiv söknings teknik för att tillhandahålla den mest exakta och detaljerade inventeringen av enheter i industriella och kritiska infrastruktur organisationer.

### <a name="bridged-capabilities"></a>Bryggade funktioner

Att överbrygga dessa funktioner med Cisco ISE pxGrid, ger säkerhets grupper som inte är överordnad enhets synlighet till företags eko systemet.

Sömlös, robust integrering med Cisco ISE pxGrid garanterar att ingen enhets avupptäcks och ingen enhets information saknas.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Exempel på OUI för slut punkts kategorier.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Exempel slut punkter i systemet":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Skärm bild av de attribut som finns i systemet.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Användnings Falls täckning: ISE-principer baserade på Defender för IoT-attribut

Använd kraftfulla ISE-principer baserade på Defender för IoT djup inlärning för att hantera krav för Internet anslutnings-och IoT-användning.

Genom att arbeta med principer kan du stänga säkerhets cykeln och se till att nätverket efterlevs i real tid.

Kunder kan till exempel använda Defender för IoT-ICS och IoT-attribut för att skapa principer som hanterar följande användnings fall, till exempel:

- Skapa en auktoriseringsprincip för att tillåta kända och auktoriserade enheter i en känslig zon baserat på tillåtna attribut, t. ex. en viss inbyggd version av inbyggd program vara för Rockwell Automation-PLCs.

- Meddela säkerhets team när en ICS-enhet upptäcks i en zon som inte är en annan zon.

- Åtgärda datorer med inaktuella eller inkompatibla leverantörer.

- Placera enheter i karantän och blockera dem efter behov.

- Generera rapporter på PLCs eller RTUs som kör inbyggd program vara med kända sårbarheter (CVEs).

### <a name="about-this-article"></a>Om den här artikeln

Den här artikeln beskriver hur du konfigurerar pxGrid och Defender för IoT-plattformen för att se till att Defender för IoT matar in attribut till Cisco ISE.

### <a name="getting-more-information"></a>Hämta mer information

Mer information om kraven för pxGrid-integrering i Cisco ISE finns i <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>System krav för integrering

I den här artikeln beskrivs kraven för integrerings system:

Defender för IoT-krav

- Defender för IoT version 2,5

Cisco-krav

- pxGrid-version 2,0

- Cisco ISE version 2,4

Nätverkskrav

- Kontrol lera att Defender för IoT-enheten har åtkomst till Cisco ISE Management Interface.

- Kontrol lera att du har CLI-åtkomst till alla Defender för IoT-enheter i företaget.

> [!NOTE]
> Endast enheter med MAC-adresser synkroniseras med Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ISE pxGrid-installation

Den här artikeln beskriver hur du:

- Konfigurera kommunikation med pxGrid

- Prenumerera på avsnittet om slut punkts enhet

- Generera certifikat

- Definiera inställningar för pxGrid

## <a name="set-up-communication-with-pxgrid"></a>Konfigurera kommunikation med pxGrid

Den här artikeln beskriver hur du konfigurerar kommunikation med pxGrid.

Så här konfigurerar du kommunikation:

1. Logga in på Cisco ISE.

1. Välj **administrations** > **system** och **distribution**.

1. Välj noden som krävs. På fliken allmänna inställningar markerar du **kryss rutan pxGrid**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Se till att kryss rutan pxgrid är markerad.":::

1. Välj fliken **profilerings konfiguration** .

1. Markera **kryss rutan pxGrid**. Lägg till en beskrivning.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Skärm bild av Lägg till beskrivning":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Prenumerera på avsnittet om slut punkts enhet

Kontrol lera att pxGrid-noden ISE har prenumererat på avsnittet slut punkts enhet. Gå till **Administration** > **pxGrid Services** > **-webbklienter**. Där kan du kontrol lera att ISE har prenumererat på avsnittet slut punkts enhet.

## <a name="generate-certificates"></a>Generera certifikat

Den här artikeln beskriver hur du skapar certifikat.

För att generera:

1. Välj **Administration**  >  **pxGrid Services** och välj sedan **certifikat**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Välj fliken certifikat för att skapa ett certifikat.":::

1. I fältet **Jag vill använda** väljer du **generera ett enskilt certifikat (utan en certifikat signerings förfrågan)**.

1. Fyll i de återstående fälten och välj **skapa**.

1. Skapa klient certifikat nyckeln och Server certifikatet och konvertera dem sedan till Java-format för nyckel lagring. Du måste kopiera dessa till varje Defender för IoT-sensor i nätverket.

## <a name="define-pxgrid-settings"></a>Definiera inställningar för pxGrid

Så här definierar du inställningar:

1. Välj **Administration**  >  **pxGrid Services** och välj sedan **Inställningar**.

1. Aktivera **Godkänn automatiskt nya certifikatbaserade konton** och **Tillåt skapande av lösenordsbaserade konton.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Se till att båda kryss rutorna är markerade.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Konfigurera Defender för IoT-enheter

Den här artikeln beskriver hur du konfigurerar Defender för IoT-enheter för att kommunicera med pxGrid. Konfigurationen ska utföras på alla Defender för IoT-enheter som kommer att mata in data till Cisco ISE.

Konfigurera utrustning:

1. Logga in på sensor-CLI.

1. Kopiera `trust.jks` och, som tidigare har skapats på sensorn. Kopiera dem till: `/var/cyberx/properties/` .

1. Redigera `/var/cyberx/properties/pxgrid.properties` :

    1. Lägg till en nyckel och ett förtroende och lagra sedan fil namn och lösen ord.

    2. Lägg till värd namnet för pxGrid-instansen.

    3. `Enabled=true`

1. Starta om enheten.

1. Upprepa de här stegen för varje produkt i företaget som ska mata in data.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Visa och hantera identifieringar i Cisco ISE

1. Slut punkts identifieringar visas på fliken för kontroll punkter för ISE-kontext **synlighet**  >   .

1. Välj **princip**  >  **profilering**  >  **Lägg till**  >  **regler**  >  **+ villkor**  >  **Skapa nytt villkor**.

1. Välj **attribut** och Använd IoT-enhetens ord listor för att bygga en profilerings regel baserat på de attribut som matas in. Följande attribut matas in.

    - Enhetstyp

    - Maskin varu revision

    - IP-adress

    - MAC-adress

    - Name

    - Produkt-ID

    - Protokoll

    - Serienummer

    - Program varu revision

    - Leverantör

Endast enheter med MAC-adresser synkroniseras.

## <a name="troubleshooting-and-logs"></a>Fel sökning och loggar

Loggarna finns i:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [vidarebefordrar aviserings information](how-to-forward-alert-information-to-partners.md).
