---
title: Översikt över ett Oracle Disaster Recovery-scenario i Azure-miljön | Microsoft Docs
description: Ett katastrof återställnings scenario för en Oracle Database 12C-databas i din Azure-miljö
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 68b5b9dfd205628c9d7c430df4c0230267752e01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669944"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Haveri beredskap för en Oracle Database 12C-databas i en Azure-miljö

## <a name="assumptions"></a>Antaganden

- Du har en förståelse för Oracle data Guard-design och Azure-miljöer.


## <a name="goals"></a>Mål
- Utforma topologin och konfigurationen som uppfyller dina DR-krav (Disaster Recovery).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenario 1: primära och DR-platser på Azure

En kund har konfigurerat en Oracle-databas på den primära platsen. En DR-plats finns i en annan region. Kunden använder Oracle data Guard för snabb återställning mellan dessa platser. Den primära platsen har också en sekundär databas för rapportering och annan användning. 

### <a name="topology"></a>Topologi

Här är en sammanfattning av Azure-installationen:

- Två platser (en primär plats och en DR-plats)
- Två virtuella nätverk
- Två Oracle-databaser med data Guard (primär och vänte läge)
- Två Oracle-databaser med gyllene grind eller data skydd (endast primär plats)
- Två program tjänster, en primär plats och en på DR-platsen
- En *tillgänglighets uppsättning* som används för databasen och program tjänsten på den primära platsen
- En hoppsida på varje plats, som begränsar åtkomsten till det privata nätverket och tillåter endast inloggning av en administratör
- En hopp, program tjänst, databas och VPN-gateway på separata undernät
- NSG framtvingas på program-och databas under nät

![Diagram som visar primära och DR-platser på Azure.](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenario 2: lokal plats för lokal och DR-webbplats på Azure

En kund har en lokal installation av Oracle Database (primär plats). En DR-webbplats finns på Azure. Oracle data Guard används för snabb återställning mellan dessa platser. Den primära platsen har också en sekundär databas för rapportering och annan användning. 

Det finns två metoder för den här installationen.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Metod 1: direkta anslutningar mellan lokala platser och Azure som kräver öppna TCP-portar i brand väggen 

Vi rekommenderar inte direkta anslutningar eftersom de exponerar TCP-portarna för världen utanför.

#### <a name="topology"></a>Topologi

Följande är en sammanfattning av Azure-installationen:

- En DR-plats 
- Ett virtuellt nätverk
- En Oracle-databas med data Guard (aktiv)
- En program tjänst på DR-platsen
- En hoppsida som begränsar åtkomsten till det privata nätverket och tillåter endast inloggning av en administratör
- En hopp, program tjänst, databas och VPN-gateway på separata undernät
- NSG framtvingas på program-och databas under nät
- En NSG-princip/-regel som tillåter inkommande TCP-port 1521 (eller en användardefinierad port)
- En NSG-princip/-regel som begränsar enbart IP-adress/adresser lokalt (databas eller program) för åtkomst till det virtuella nätverket

![Diagram som visar direkta anslutningar mellan lokala platser och Azure, vilket kräver öppna TCP-portar i brand väggen.](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Metod 2: VPN för plats-till-plats
VPN för plats-till-plats är ett bättre tillvägagångs sätt. Mer information om hur du konfigurerar en VPN-anslutning finns i [skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med CLI](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md).

#### <a name="topology"></a>Topologi

Följande är en sammanfattning av Azure-installationen:

- En DR-plats 
- Ett virtuellt nätverk 
- En Oracle-databas med data Guard (aktiv)
- En program tjänst på DR-platsen
- En hoppsida som begränsar åtkomsten till det privata nätverket och tillåter endast inloggning av en administratör
- En hopp, program tjänst, databas och VPN-gateway finns i separata undernät
- NSG framtvingas på program-och databas under nät
- Plats-till-plats-VPN-anslutning mellan lokalt och Azure

![Skärm bild av sidan DR-topologi](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Mer att läsa

- [Utforma och implementera en Oracle-databas på Azure](oracle-design.md)
- [Konfigurera Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurera den gyllene Oracle-grinden](configure-oracle-golden-gate.md)
- [Säkerhets kopiering och återställning i Oracle](./oracle-overview.md)


## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa virtuella datorer med hög tillgänglighet](../../linux/create-cli-complete.md)
- [Utforska Azure CLI-exempel för VM-distribution](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)