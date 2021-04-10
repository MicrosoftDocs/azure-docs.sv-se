---
title: Felsöka anslutnings problem – Azure Database for MySQL-flexibel Server
description: Lär dig hur du felsöker anslutnings problem till Azure Database for MySQL flexibel Server.
keywords: MySQL-anslutning, anslutnings sträng, anslutnings problem, beständigt fel, anslutnings fel
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: bdd52ba77fd9a65ce27985ff3c86a93fc887ddf9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109999"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Felsöka anslutnings problem till Azure Database for MySQL-flexibel Server

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Anslutnings problem kan orsakas av olika saker, inklusive:

* Brandväggsinställningar
* Anslutnings-timeout
* Felaktig inloggnings information
* Max gränsen har nåtts för några Azure Database for MySQL flexibla server resurser

I den här artikeln diskuterar vi hur du kan felsöka några av de vanliga felen och stegen för att lösa de här felen.

## <a name="troubleshoot-common-errors"></a>Felsöka vanliga fel

Om programmet inte kan ansluta till Azure Database for MySQL flexibel Server, tyder det vanligt vis på ett problem med något av följande:

* Krypterad anslutning med TLS/SSL: flexibel Server stöder bara krypterade anslutningar med hjälp av Transport Layer Security (TLS 1,2) och alla **inkommande anslutningar med tls 1,0 och tls 1,1 kommer att nekas**. Det går inte att inaktivera eller ändra TLS-versionen. Läs mer om [krypterad anslutning med Transport Layer Security (TLS 1,2) i Azure Database for MySQL-flexibel Server](./how-to-connect-tls-ssl.md).
- Flexibel server i *privat åtkomst (VNet-integrering)*: se till att du ansluter inifrån samma virtuella nätverk som den flexibla servern. Referera till [Virtual Network i Azure Database for MySQL flexibel server]<!--(./concepts-networking-virtual-network.md)-->
- Flexibel server med *offentlig åtkomst (tillåtna IP-adresser)* kontrol lera att brand väggen är konfigurerad för att tillåta anslutningar från klienten. Se [skapa och hantera flexibla Server brand Väggs regler med hjälp av Azure Portal](./how-to-manage-firewall-portal.md).
* Konfiguration av klient brand väggen: brand väggen på klienten måste tillåta anslutningar till din databas server. IP-adresser och portar på den server som du inte kan tillåta måste vara tillåtna samt program namn som MySQL i vissa brand väggar.
* Användar fel: du kan ha felangede anslutnings parametrar, till exempel Server namnet i anslutnings strängen.

### <a name="resolve-connectivity-issues"></a>Lösa anslutnings problem

* Läs mer om krypterade anslutningar [med hjälp av Transport Layer Security (TLS 1,2) i Azure Database for MySQL-flexibla Server](./how-to-connect-tls-ssl.md) ->.
* Om du använder **offentlig åtkomst (tillåtna IP-adresser)** måste du konfigurera [brand Väggs regler](./how-to-manage-firewall-portal.md) som tillåter klientens IP-adress. För temporär testning kan du konfigurera en brand Väggs regel med 0.0.0.0 som första IP-adress och använda 255.255.255.255 som sista IP-adress. Då öppnas servern med alla IP-adresser. Om detta löser problemet med anslutningen tar du bort den här regeln och skapar en brand Väggs regel för en lämplig, begränsad IP-adress eller ett IP-adressintervall.
* Kontrol lera att port 3306 är öppen för utgående anslutningar på alla brand väggar mellan klienten och Internet.
* Kontrol lera anslutnings strängen och andra anslutnings inställningar. Se de fördefinierade anslutnings strängarna på sidan **anslutnings strängar** som är tillgängliga för servern i Azure Portal för vanliga språk.

## <a name="next-steps"></a>Nästa steg
- [Använd MySQL Workbench för att ansluta och fråga efter data i Azure Database for MySQL flexibel Server](./connect-workbench.md).
- [Använd php för att ansluta och fråga efter data i Azure Database for MySQL flexibel Server](./connect-php.md).
- [Använd python för att ansluta och fråga efter data i Azure Database for MySQL flexibel Server](./connect-python.md).
