---
title: Brand Väggs regler – Azure Database for MySQL
description: Lär dig mer om att använda brand Väggs regler för att aktivera anslutningar till din Azure Database for MySQL-server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: eb178e38001e54ac39a269c8b8cdef12c77d74cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96901957"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Brandväggsregler för Azure Database for MySQL-server
Brand väggar förhindrar all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Brand väggen beviljar åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran.

Konfigurera en brand vägg genom att skapa brand Väggs regler som anger intervall för acceptabla IP-adresser. Du kan skapa brand Väggs regler på server nivå.

**Brand Väggs regler:** Dessa regler gör det möjligt för klienter att komma åt hela Azure Database for MySQL server, det vill säga alla databaser på samma logiska Server. Brand Väggs regler på server nivå kan konfigureras med hjälp av Azure Portal-eller Azure CLI-kommandon. Du måste vara prenumerations ägare eller en prenumerations deltagare för att skapa brand Väggs regler på server nivå.

## <a name="firewall-overview"></a>Översikt över brandväggar
All databas åtkomst till din Azure Database for MySQL Server blockeras som standard av brand väggen. Om du vill börja använda servern från en annan dator måste du ange en eller flera brand Väggs regler på server nivå för att aktivera åtkomst till servern. Använd brand Väggs reglerna för att ange vilka IP-adressintervall från Internet som ska tillåtas. Åtkomst till den Azure Portal webbplatsen påverkas inte av brand Väggs reglerna.

Anslutnings försök från Internet och Azure måste först passera brand väggen innan de kan komma åt din Azure Database for MySQL databas, som du ser i följande diagram:

:::image type="content" source="./media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Exempel flöde för hur brand väggen fungerar":::

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
Brand Väggs regler på server nivå gäller för alla databaser på Azure Database for MySQL-servern.

Om IP-adressen för begäran är inom ett intervall som anges i brand Väggs reglerna på server nivå, beviljas anslutningen.

Om IP-adressen för begäran ligger utanför de intervall som anges i brand Väggs reglerna på databas-eller server nivå, så Miss lyckas anslutningsbegäran.

## <a name="connecting-from-azure"></a>Ansluta från Azure
Vi rekommenderar att du hittar den utgående IP-adressen för alla program och tjänster och uttryckligen tillåter åtkomst till dessa enskilda IP-adresser eller intervall. Du kan till exempel hitta den utgående IP-adressen för en Azure App Service eller använda en offentlig IP-adress som är kopplad till en virtuell dator eller annan resurs (se nedan för information om hur du ansluter till en virtuell dators privata IP-adresser över tjänst slut punkter). 

Om en fast utgående IP-adress inte är tillgänglig för din Azure-tjänst kan du överväga att aktivera anslutningar från alla Azure-datacenter-IP-adresser. Du kan aktivera den här inställningen från Azure Portal genom att ange alternativet **Tillåt åtkomst till Azure-tjänster** till **på** i fönstret **anslutnings säkerhet** och sedan trycka på **Spara**. Från Azure CLI motsvarar en brand Väggs regel inställning med start-och slut adress lika med 0.0.0.0. Om anslutnings försöket inte är tillåtet når begäran inte Azure Database for MySQL servern.

> [!IMPORTANT]
> Alternativet **Tillåt åtkomst till Azure-tjänster** konfigurerar brand väggen så att alla anslutningar från Azure, inklusive anslutningar från andra kunders prenumerationer. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

:::image type="content" source="./media/concepts-firewall-rules/allow-azure-services.png" alt-text="Konfigurera Tillåt åtkomst till Azure-tjänster i portalen":::

### <a name="connecting-from-a-vnet"></a>Ansluta från ett virtuellt nätverk
Överväg att använda [VNet-tjänstens slut punkter](./concepts-data-access-and-security-vnet.md)för att ansluta säkert till din Azure Database for MySQL-server från ett VNet. 

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Förutom Azure Portal kan brand Väggs regler hanteras program mässigt med hjälp av Azure CLI. Se även [skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Felsöka brand Väggs problem
Tänk på följande när åtkomst till Microsoft Azure databasen för MySQL Server-tjänsten inte fungerar som förväntat:

* **Ändringar i listan över tillåtna har inte börjat att fungera än:** Det kan finnas en fördröjning på fem minuter för ändringar i Azure Database for MySQL serverns brand Väggs konfiguration som börjar gälla.

* **Inloggningen är inte auktoriserad eller så användes ett felaktigt lösen ord:** Om en inloggning inte har behörighet på Azure Database for MySQL-servern eller om det lösen ord som används är felaktigt nekas anslutningen till Azure Database for MySQL-servern. En brandväggsinställning ger endast klienter möjlighet att försöka ansluta till din server. Varje klient måste fortfarande ange nödvändiga säkerhetsreferenser.

* **Dynamisk IP-adress:** Om du har en Internet anslutning med dynamisk IP-adressering och du har problem med att komma åt brand väggen kan du prova någon av följande lösningar:

   * Be Internet leverantören (ISP) om IP-adressintervallet som har tilldelats till de klient datorer som har åtkomst till Azure Database for MySQL-servern och Lägg sedan till IP-adressintervallet som en brand Väggs regel.

   * Använd statisk IP-adressering i stället för dina klientdatorer och lägg sedan till IP-adresserna som brandväggsregler.

* **Serverns IP-adress verkar vara offentlig:** Anslutningar till Azure Database for MySQL-servern dirigeras via en offentligt tillgänglig Azure-Gateway. Den faktiska server-IP-adressen skyddas dock av brandväggen. Mer information finns i [artikeln om anslutningsarkitektur](concepts-connectivity-architecture.md). 

* **Det går inte att ansluta från Azure-resursen med tillåtet IP:** Kontrol lera om **Microsoft. SQL** -tjänstens slut punkt har Aktiver ATS för under nätet som du ansluter från. Om **Microsoft. SQL** har Aktiver ATS anger det att du bara vill använda [slut punkts regler för VNet-tjänsten](concepts-data-access-and-security-vnet.md) i det under nätet.

   Du kan till exempel se följande fel om du ansluter från en virtuell Azure-dator i ett undernät där **Microsoft. SQL** är aktiverat men saknar motsvarande VNet-regel:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* **Brand Väggs regeln är inte tillgänglig för IPv6-format:** Brand Väggs reglerna måste vara i IPv4-format. Om du anger brand Väggs regler i IPv6-format visas validerings felet.

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure Portal](./howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)
* [VNet-tjänstens slut punkter i Azure Database for MySQL](./concepts-data-access-and-security-vnet.md)
