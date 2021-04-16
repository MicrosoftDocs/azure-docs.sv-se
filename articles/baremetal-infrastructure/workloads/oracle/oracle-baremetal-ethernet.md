---
title: Ethernet-konfiguration av BareMetal för Oracle
description: Lär dig mer om konfigurationen av Ethernet-gränssnitt på BareMetal-instanser för Oracle-arbetsbelastningar.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: e98af51075d5193cab14d18f1cdb1f431c8fa892
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559319"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Ethernet-konfiguration av BareMetal för Oracle

I den här artikeln tittar vi på konfigurationen av Ethernet-gränssnitt på BareMetal-instanser för Oracle-arbetsbelastningar.

Varje etablerad BareMetal-instans för Oracle är förkonfigurerad med uppsättningar av Ethernet-gränssnitt. Ethernet-gränssnitten är indelade i fyra typer:

- Används för eller av klientåtkomst.
- Används för kommunikation från nod till nod. Det här gränssnittet konfigureras på alla servrar oavsett vilken topologi som efterfrågas. Det används endast för utskalningsscenarier.
- Används för nod-till-lagring-anslutning.
- Används för haveriberedskapskonfiguration och anslutning till global räckvidd för anslutning mellan regioner.

## <a name="architecture"></a>Arkitektur

Följande diagram illustrerar arkitekturen för de förkonfigurerade Ethernet-gränssnitten för BareMetal Infrastructure. 

[![Diagram som visar arkitekturen för de förkonfigurerade Ethernet-gränssnitten för Oracle-arbetsbelastningar.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

Standardkonfigurationen levereras med ett klient-IP-gränssnitt (eth1) som ansluter från Azure Virtual Network (VNET) där du kan använda Secure Shell (SSH) för att komma åt en BareMetal-instans.

> [!NOTE]
> För ett annat klientgränssnitt (eth10) från ett annat azure-VNET kontaktar du din Microsoft CSA för att skicka en tjänstbegäran. Till exempel om du vill utveckla/testa samt produktions-/DR-miljöer.

| **Logiskt NIC-gränssnitt** | **Namn med RHEL OS** | **Användningsfall** |
| --- | --- | --- |
| A | eth1.tenant | Klient till BareMetal-instans |
| C | eth2.tenant | Nod-till-lagring; stöder samordning och åtkomst till lagringskontrollanterna för hantering av lagringsmiljön. |
| B | eth3.tenant | Nod-till-nod (privat sammankoppling) |
| C | eth4.tenant | Reserverad/iSCSI |
| C | eth5.tenant | Reserverad/loggsäkerhetskopia |
| C | eth6.tenant | Säkerhetskopiering från nod till storage_Data (RMAN, ögonblicksbild) |
| C | eth7.tenant | Nod-till-storage_dNFS-Pri; ger anslutning till NetApp-lagringsmatrisen. |
| C | eth8.tenant | Nod-till-storage_dNFS-sek; ger anslutning till NetApp-lagringsmatrisen. |
| D | eth9.tenant | DR-anslutning för global räckvidd för åtkomst till BMI i en annan region. |
| A | \*eth10.tenant | \* Klient till BareMetal-instans
 |

Om det behövs kan du definiera fler nätverkskort på egen hand. Konfigurationerna för befintliga nätverkskort *kan dock inte* ändras.

## <a name="usage-rules"></a>Användningsregler

För BareMetal-instanser har standardvärdet nio tilldelade IP-adresser på de fyra logiska nätverkskorten. Följande användningsregler gäller:

- Ethernet "A" bör ha en tilldelad IP-adress som ligger utanför det ip-adressintervall för server-IP-pool som du skickade till Microsoft. Den här IP-adressen ska inte underhållas i katalogen etc/hosts i operativsystemet.
- Ethernet "B" bör underhållas exklusivt i katalogen etc/hosts för kommunikation mellan de olika instanserna. Behåll dessa IP-adresser i utskalningskonfigurationer för Oracle Real Application Clusters (RAC) som IP-adresser som används för konfigurationen mellan noder.
- Ethernet "C" bör ha en tilldelad IP-adress som används för kommunikation till NFS-lagring. Den här typen av adress ska inte underhållas i katalogen etc/hosts.
- Ethernet "D" ska endast användas för global reach-konfiguration för åtkomst till BareMetal-instanser i din DR-region.

## <a name="next-step"></a>Nästa steg

Läs mer om BareMetal-infrastruktur för Oracle-arkitektur.

> [!div class="nextstepaction"]
> [Arkitektur för BareMetal-infrastruktur för Oracle](oracle-baremetal-architecture.md)
