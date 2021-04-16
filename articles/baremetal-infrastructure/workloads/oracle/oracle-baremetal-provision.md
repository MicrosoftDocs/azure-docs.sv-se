---
title: Etablera BareMetal för Oracle
description: Lär dig mer om etablering av BareMetal-infrastrukturen för Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559218"
---
# <a name="provision-baremetal-for-oracle"></a>Etablera BareMetal för Oracle

I den här artikeln tittar vi på hur du etablerar dina BareMetal Infrastructure-instanser för Oracle-arbetsbelastningar. 

Det första steget för att etablera Dina BareMetal-instanser är att arbeta med Din Microsoft CSA. De hjälper dig baserat på dina specifika arbetsbelastningsbehov och den arkitektur som du distribuerar, oavsett om det är en enskild instans, En nod-RAC eller RAC. Mer information om dessa topologier finns i Architecture of BareMetal Infrastructure for Oracle ( Arkitektur [för BareMetal-infrastruktur för Oracle).](oracle-baremetal-architecture.md)

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * En aktiv Azure-prenumeration
> * Microsoft Premier Support-avtal
> * Licenser för Red Hat Enterprise Linux 7.6
> * Oracle-supportavtal 
> * Licenser och programinstallationskomponenter för Oracle
> * ExpressRoute ansluten lokalt till Azure ( Om **du vill** kan du konfigurera **ExpressRoute-Global Reach** för direktanslutning från lokal plats till Oracle Database)   
> * Virtuellt nätverk
> * Skapa en gateway
> * Virtuella datorer (VM) i det virtuella nätverket (hopprutor)

## <a name="information-to-provide-microsoft-operations"></a>Information för att tillhandahålla Microsoft Operations

Du måste ange följande information till din CSA:

1. Adressutrymme för virtuellt nätverk. Det här intervallet måste vara /24-undernätet. till exempel 10.11.0.0/24.
2. P2P-intervall. Det här intervallet måste vara ett /29-undernät. till exempel 10.12.0.0/29.
3. Serverns IP-adresspool. Det rekommenderade intervallet är /24; till exempel 10.13.0.0/24.
4. Serverns IP-adress. Välj en IP-adress från serverns IP-adresspool.

    > [!Note] 
    > De första 30 IP-adresserna är reserverade för Konfiguration av Microsoft-infrastruktur. I det här exemplet blir din första tillgängliga IP-adress för ett blad 10.13.0.30.

5. Den Azure-region som krävs; till exempel USA, västra 2.
6. BareMetal Infrastructure SKU krävs; till exempel S192 (två datorer).

## <a name="storage-requirements"></a>Lagringskrav

Arbeta med din CSA-representant för dina lagringsbehov under etableringsbegäran, inklusive förväntade lagringsbehov baserat på framtida tillväxt. Lagringsutrymmet har lagts till i steg om 1 TB.

För volymer följer vi Oracles [OFA-standard (Optimal Flexible Architecture)](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)med Basic-nivån och Enterprise-konfiguration. Om du har andra lagringskrav än standardformatet "T-shirt", gör du din anpassade begäran via din CSA.

| Grundläggande konfiguration (POC/testning) | Description | Liten | Medel | Stor |
| --- | --- | --- | --- | --- |
| /u01 | Oracle-binärfiler | 500 GB | 500 GB | 500 GB |
| /u02 | Läsintensiv/administratör | 500 GB | 1 TB | 5 TB |
| /u03 | Skrivintensiva/loggar | 500 GB | 1 TB | 5 TB |
| /u09 | Backup | 5 TB | 10 TB | 15 TB |

| Företagskonfiguration | Description | Liten | Medel | Stor | Extra stor |
| --- | --- | --- | --- | --- | --- |
| /u01 | Oracle-binärfiler | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | Administratör | 100 GB | 100 GB | 100 GB | 100 GB |
| /u10 till /u59 | Läsintensiv | 500 GB | 5 TB | 10 TB | 20 TB |
| /u60 till /u89 | Skrivintensiva | 500 GB | 5 TB | 10 TB | 20 TB |
| /u90 till /u91 | Gör om loggar | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | Arkiv | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | Backup | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>Nästa steg

Läs mer om BareMetal Infrastructure for Oracle.

> [!div class="nextstepaction"]
> [Vad är BareMetal-infrastrukturen i Azure?](../../concepts-baremetal-infrastructure-overview.md)
