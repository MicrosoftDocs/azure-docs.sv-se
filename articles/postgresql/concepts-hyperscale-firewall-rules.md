---
title: Brand Väggs regler – storskalig (citus)-Azure Database for PostgreSQL
description: I den här artikeln beskrivs brand Väggs regler för Azure Database for PostgreSQL-storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90902086"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Brand Väggs regler i Azure Database for PostgreSQL-storskalig (citus)
Azure Database for PostgreSQL-serverns brand vägg förhindrar all åtkomst till din citus-Coordinator-nod tills du anger vilka datorer som har behörighet. Brand väggen beviljar åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran.
Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brand Väggs regler på server nivå.

**Brand Väggs regler:** Dessa regler gör att klienter kan komma åt din citus-koordinator-nod, det vill säga alla databaser på samma logiska Server. Brand Väggs regler på server nivå kan konfigureras med hjälp av Azure Portal. Du måste vara prenumerations ägare eller en prenumerations deltagare för att skapa brand Väggs regler på server nivå.

## <a name="firewall-overview"></a>Översikt över brandväggar
All databas åtkomst till koordinator-noden blockeras som standard av brand väggen. Om du vill börja använda servern från en annan dator måste du ange en eller flera brand Väggs regler på server nivå för att aktivera åtkomst till servern. Använd brand Väggs reglerna för att ange vilka IP-adressintervall från Internet som ska tillåtas. Åtkomst till den Azure Portal webbplatsen påverkas inte av brand Väggs reglerna.
Anslutnings försök från Internet och Azure måste först passera brand väggen innan de kan komma åt din PostgreSQL-databas, som du ser i följande diagram:

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="Exempel flöde för hur brand väggen fungerar":::

## <a name="connecting-from-the-internet-and-from-azure"></a>Ansluta från Internet och från Azure

En citus-kontroll för Server grupps brand väggar som kan ansluta till gruppens koordinator nod. Brand väggen bestämmer åtkomsten genom att konsultera en konfigurerbar lista med regler. Varje regel är en IP-adress eller ett adress intervall som tillåts i.

När brand väggen blockerar anslutningar kan det orsaka program fel. Genom att använda PostgreSQL JDBC-drivrutinen, till exempel, genererar vi ett fel som detta:

> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. postgresql. util. PSQLException: allvarligt: ingen PG \_ HBA. conf-post för värden "123.45.67.890", User "citus", Database "citus", SSL

Se [skapa och hantera brand Väggs regler](howto-hyperscale-manage-firewall-using-portal.md) för att lära dig hur reglerna definieras.

## <a name="troubleshooting-the-database-server-firewall"></a>Felsöka databas serverns brand vägg
När åtkomst till Microsoft Azure-databasen för PostgreSQL-tjänsten (citus) inte fungerar som förväntat, bör du tänka på följande punkter:

* **Ändringar i listan över tillåtna har inte börjat att fungera än:** Det kan ta upp till fem minuter innan ändringar i citus-brandväggens brand Väggs konfiguration börjar gälla.

* **Användaren är inte auktoriserad eller ett felaktigt lösen ord användes:** Om en användare inte har behörighet på servern eller om lösen ordet som används är felaktigt nekas anslutningen till servern. Att skapa en brand Väggs inställning ger bara klienter möjlighet att försöka ansluta till servern. varje klient måste ändå ange nödvändiga säkerhets uppgifter.

Om du till exempel använder en JDBC-klient kan följande fel uppstå.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException: org. postgresql. util. PSQLException: OÅTERKALLELIGt: lösenordsautentiseringen misslyckades för användaren "YOURUSERNAME"

* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att passera brandväggen kan du prova någon av följande lösningar:

* Be Internet leverantören (ISP) om IP-adressintervallet som har tilldelats till de klient datorer som har åtkomst till noden för citus-koordinatorn () och Lägg sedan till IP-adressintervallet som en brand Väggs regel.

* Hämta statiska IP-adresser i stället för dina klient datorer och Lägg sedan till den statiska IP-adressen som en brand Väggs regel.

## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar brand Väggs regler på server nivå och databas nivå finns i:
* [Skapa och hantera Azure Database for PostgreSQL brand Väggs regler med hjälp av Azure Portal](howto-hyperscale-manage-firewall-using-portal.md)
