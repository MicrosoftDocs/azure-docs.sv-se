---
title: Generera trender och statistik rapporter
description: Få insyn i nätverks aktivitet, statistik och trender genom att använda Defender för IoT-trender och statistik-widgetar.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8bfbacc77843c7978bcb1d364bd93f5e09381144
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811729"
---
# <a name="sensor-trends-and-statistics-reports"></a>Sensor trender och statistik rapporter

## <a name="about-sensor-trends-and-statistics-reports"></a>Om sensor trender och statistik rapporter

Du kan skapa widgets grafer och cirkel diagram för att få insyn i nätverks trender och statistik. Widgetar kan grupperas under användardefinierade instrument paneler.

> [!NOTE]
> Administratörer och säkerhets analyser kan skapa trender och statistik rapporter.

Instrument panelen består av widgetar som grafiskt beskriver följande typer av information:

- Trafik efter port
- Kanal bandbredd
- Total bandbredd
- Aktiv TCP-anslutning
- Enheter:
  - Nya enheter
  - Upptagna enheter
  - Enheter efter leverantör
  - Enheter per operativ system
  - Frånkopplade enheter
- Anslutnings släpp per timme
- Aviseringar för incidenter efter typ
- Åtkomst till databas tabell
- Widgetar protokoll avsnitt
- Ethernet-och IP-adress:
  - Ethernet-och IP-CIP av tjänster
  - Ethernet-och IP-datatrafik per CIP-klass
  - Ethernet-och IP-adress trafik via kommando
- OPC
  - OPC topp hanterings åtgärder
  - OPC främsta I/O-åtgärder
- Siemens S7:
  - S7 trafik efter kontroll funktion
  - S7 trafik per underfunktion
- SRTP:
  - SRTP trafik per service kod
  - SRTP fel per dag
- SuiteLink:
  - SuiteLink-Taggar med översta frågan
  - SuiteLink för numerisk tagg
- IEC-60870-trafik efter ASDU
- DNP3 trafik efter funktion
- MMS-trafik per tjänst
- Modbus trafik efter funktion
- OPC-UA-trafik per tjänst

> [!NOTE]
>  Tiden i widgeten anges enligt sensor tiden.

## <a name="create-reports"></a>Skapa rapporter

Så här visar du instrument paneler och widgetar:

Välj **trender & statistik** på sido menyn.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Skärm bild av en undersökning.":::

Som standard visas resultat för identifieringar under de senaste 7 dagarna. Du kan använda filter verktyg för att ändra det här intervallet. Till exempel en texts ökning i fritext.

## <a name="see-also"></a>Se även

[Rapportering](how-to-create-risk-assessment-reports.md) 
 om riskbedömning Frågor om sensor [Data utvinning](how-to-create-data-mining-queries.md) 
 [Rapportering om angrepps vektor](how-to-create-attack-vector-reports.md)