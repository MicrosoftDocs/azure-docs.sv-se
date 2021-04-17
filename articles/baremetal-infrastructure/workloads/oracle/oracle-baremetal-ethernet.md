---
title: Ethernet-konfiguration av BareMetal för Oracle
description: Lär dig mer om konfigurationen av Ethernet-gränssnitt på BareMetal-instanser för Oracle-arbetsbelastningar.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c57cbc86d17090d6960a334c2790d80b43420aca
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588895"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Ethernet-konfiguration av BareMetal för Oracle

I den här artikeln tittar vi på konfigurationen av Ethernet-gränssnitt på BareMetal-instanser för Oracle-arbetsbelastningar.

Varje etablerad BareMetal-instans för Oracle är förkonfigurerad med uppsättningar ethernet-gränssnitt. Ethernet-gränssnitten är kategoriserade i fyra typer:

- Används för eller av klientåtkomst.
- Används för nod-till-nod-kommunikation. Det här gränssnittet är konfigurerat på alla servrar oavsett vilken topologi som efterfrågas. Det används endast för utskalningsscenarier.
- Används för nod-till-lagring-anslutning.
- Används för haveriberedskapskonfiguration och anslutning till global räckvidd för anslutning mellan regioner.

## <a name="architecture"></a>Arkitektur

Följande diagram illustrerar arkitekturen för de förkonfigurerade Ethernet-gränssnitten för BareMetal Infrastructure. 

[![Diagram som visar arkitekturen för de förkonfigurerade Ethernet-gränssnitten för Oracle-arbetsbelastningar.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

Standardkonfigurationen levereras med ett klient-IP-gränssnitt (eth1) som ansluter från Azure Virtual Network (VNET) med vilket du kan använda Secure Shell (SSH) för att få åtkomst till en BareMetal-instans.

> [!NOTE]
> För ett annat klientgränssnitt (eth10) från ett annat azure-VNET kontaktar du din Microsoft CSA för att skicka en tjänstbegäran. Till exempel om du vill utveckla/testa samt produktions-/DR-miljöer.

| **Logiskt NIC-gränssnitt** | **Namn med RHEL OS** | **Användningsfall** |
| --- | --- | --- |
| A | net1.tenant | Klient till BareMetal-instans |
| C | net2.tenant | Nod-till-lagring; stöder samordning och åtkomst till lagringskontrollanterna för hantering av lagringsmiljön. |
| B | net3.tenant | Nod-till-nod (privat sammankoppling) |
| C | net4.tenant | Reserverad/iSCSI |
| C | net5.tenant | Reserverad/loggsäkerhetskopia |
| C | net6.tenant | Node-to-storage_Data Backup (RMAN, Snapshot) |
| C | net7.tenant | Nod-till-storage_dNFS-Pri; ger anslutning till NetApp-lagringsmatrisen. |
| C | net8.tenant | Nod-till-storage_dNFS-sek; ger anslutning till NetApp-lagringsmatrisen. |
| D | net9.tenant | DR-anslutning för global räckvidd för åtkomst till BMI i en annan region. |
| A | \*net10.tenant | \* Klient till BareMetal-instans
 |

Om det behövs kan du definiera fler nätverkskort på egen hand. Konfigurationerna för befintliga nätverkskort *kan dock inte* ändras.

## <a name="usage-rules"></a>Användningsregler

För BareMetal-instanser har standardvärdet nio tilldelade IP-adresser på de fyra logiska nätverkskorten. Följande användningsregler gäller:

- Ethernet "A" bör ha en tilldelad IP-adress som ligger utanför det ip-adressintervall för server-IP-pool som du skickade till Microsoft. Den här IP-adressen ska inte underhållas i katalogen etc/hosts i operativsystemet.
- Ethernet "B" bör underhållas exklusivt i katalogen etc/hosts för kommunikation mellan de olika instanserna. Behåll dessa IP-adresser i skalningsbaserade RAC-konfigurationer (Oracle Real Application Clusters) som IP-adresser som används för konfigurationen mellan noder.
- Ethernet "C" bör ha en tilldelad IP-adress som används för kommunikation till NFS-lagring. Den här typen av adress ska inte underhållas i katalogen etc/hosts.
- Ethernet "D" ska endast användas för global reach-konfiguration för åtkomst till BareMetal-instanser i din DR-region.

## <a name="next-step"></a>Nästa steg

Läs mer om BareMetal-infrastruktur för Oracle-arkitektur.

> [!div class="nextstepaction"]
> [Arkitektur för BareMetal-infrastruktur för Oracle](oracle-baremetal-architecture.md)
