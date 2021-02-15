---
title: Konfigurera SNMP MIB-övervakning
description: Du kan utföra hälso övervakning av sensorer med hjälp av SNMP. Sensorn svarar på SNMP-frågor som skickas från en auktoriserad övervaknings Server.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 051ce1be66f91d60f719ca3695f15e6c8001b20f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523829"
---
# <a name="set-up-snmp-mib-monitoring"></a>Konfigurera SNMP MIB-övervakning

Du kan utföra hälso övervakning av sensorer genom att använda Simple Network Management Protocol (SNMP). Sensorn svarar på SNMP-frågor som skickas från en auktoriserad övervaknings Server. SNMP-övervakaren avsöker sensor-OID med jämna mellanrum (upp till 50 gånger en sekund).

De SNMP-versioner som stöds är SNMP v2 eller SNMP v3. SNMP använder UDP som transport protokoll med port 161 (SNMP).

Innan du börjar konfigurera SNMP-övervakning måste du öppna port UDP 161 i brand väggen.

## <a name="oids"></a>OID

| Hanterings konsol och sensor | OID | Format | Beskrivning |
|--|--|--|--|
| Installations namn | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | Installations namn för den lokala hanterings konsolen |
| Leverantör | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Microsoft Support (support.microsoft.com) |
| Plattform | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | Sensor eller lokal hanterings konsol |
| Serienummer | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | Sträng som licensen använder |
| Program varu version | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Xsense full versions sträng och fullständig hanterings sträng för hantering |
| CPU-användning | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | Indikation på noll till 100 |
| CPU-temperatur | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | Celsius som anger noll till 100 baserat på Linux-indatatyper |
| Minnesanvändning | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | Indikation på noll till 100 |
| Disk användning | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | Indikation på noll till 100 |
| Tjänststatus | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | Online eller offline om någon av de fyra viktiga komponenterna är nere |
| Bandbredd | Omfattas inte av 2,4 |  | Bandbredden som tas emot på varje övervaknings gränssnitt i Xsense |

   - Icke-befintliga nycklar svarar med null, HTTP 200, baserat på [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - Maskinvarubaserad MIB (CPU-användning, CPU-temperatur, minnes användning, disk användning) bör testas på alla arkitekturer och fysiska sensorer. CPU-temperatur på virtuella datorer förväntas inte gälla.

Du kan hämta loggen som innehåller alla SNMP-frågor som sensorn tar emot, inklusive anslutnings data och rå data från paketet.

Definiera SNMP v2-hälso övervakning:

1. På menyn på sidan väljer du **Systeminställningar**.

2. I fönstret **aktiv identifiering** väljer du **SNMP MIB-övervakning** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Redigera SNMP-fönstret.":::

3. I avsnittet **tillåtna värdar** väljer du **Lägg till värd** och anger IP-adressen för servern som utför system hälso övervakning.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Ange IP-adressen för tillåtna värdar.":::

4. I avsnittet **autentisering** i rutan **SNMP 2 grupp sträng** anger du strängen. SNMP-gruppsträngen kan innehålla upp till 32 tecken och innehålla en kombination av alfanumeriska tecken (versaler, gemener och siffror). Blanksteg är inte tillåtna.

5. Välj **Spara**.

Definiera SNMP v3-hälso övervakning:

1. På menyn på sidan väljer du **Systeminställningar**.

2. I fönstret **aktiv identifiering** väljer du **SNMP MIB-övervakning** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Redigera SNMP-fönstret.":::

3. I avsnittet **tillåtna värdar** väljer du **Lägg till värd** och anger IP-adressen för servern som utför system hälso övervakning.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Ange IP-adressen för tillåtna värdar.":::

4. I avsnittet **autentisering** anger du följande parametrar:

    | Parameter | Beskrivning |
    |--|--|
    | **Användarnamn** | SNMP-användarnamnet får innehålla upp till 32 tecken och innehålla en kombination av alfanumeriska tecken (versaler, gemener och siffror). Blanksteg är inte tillåtna. <br /> <br />Användar namnet för SNMP v3-autentisering måste konfigureras i systemet och på SNMP-servern. |
    | **Lösenord** | Ange ett lösen ord för Skift läges känsligt autentisering. Lösen ordet för autentisering kan innehålla 8 till 12 tecken och innehålla en kombination av alfanumeriska tecken (versaler, gemener och siffror). <br /> <br/>Användar namnet för SNMP v3-autentisering måste konfigureras i systemet och på SNMP-servern. |
    | **Autentiseringstyp** | Välj MD5 eller SHA. |
    | **Kryptering** | Välj DES eller AES. |
    | **Hemlig nyckel** | Nyckeln måste innehålla exakt åtta tecken och innehålla en kombination av alfanumeriska tecken (versaler, gemener och siffror). |

5. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Exportera fel söknings loggar](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
