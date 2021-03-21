---
title: Logisk avkodning-Azure Database for PostgreSQL-enskild server
description: Beskriver logisk avkodning och wal2json för registrering av ändrings data i Azure Database for PostgreSQL-enskild server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 0ea58050c5dc952392df56b4fb556a0998eef165
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938910"
---
# <a name="logical-decoding"></a>Logisk avkodning

[Med logisk avkodning i postgresql](https://www.postgresql.org/docs/current/logicaldecoding.html) kan du strömma data ändringar till externa konsumenter. Logisk avkodning används ofta för händelse strömning och ändring av data insamlings scenarier.

Med den logiska avkodningen används ett output-pluginprogram för att konvertera postgres Write Ahead-logg (WAL) till ett läsbart format. Azure Database for PostgreSQL tillhandahåller plugin- [wal2json](https://github.com/eulerto/wal2json), [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) och pgoutput. pgoutput görs tillgänglig av PostgreSQL från PostgreSQL version 10 och senare.

En översikt över hur postgres logiska avkodning fungerar [finns på vår blogg](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421). 

> [!NOTE]
> Logisk replikering med PostgreSQL-publikation/-prenumeration stöds inte med Azure Database for PostgreSQL-Single-Server.


## <a name="set-up-your-server"></a>Konfigurera servern 
Logiska avkodnings-och [Läs repliker](concepts-read-replicas.md) beror både på postgres Write Ahead-loggen (Wal) för information. De här två funktionerna behöver olika loggnings nivåer från postgres. Logisk avkodning kräver en högre loggnings nivå än Läs repliker.

Om du vill konfigurera rätt loggnings nivå använder du parametern Azure Replication support. Support för Azure-replikering har tre inställnings alternativ:

* **Off** – lägger till minst information i Wal. Den här inställningen är inte tillgänglig på de flesta Azure Database for PostgreSQL-servrar.  
* **Replik** – mer utförligt än **.** Detta är den lägsta loggnings nivå som krävs för att [läsa repliker](concepts-read-replicas.md) ska fungera. Den här inställningen är standard på de flesta servrar.
* **Logisk** – mer utförlig än **replik**. Detta är den lägsta loggnings nivån för logisk avkodning att arbeta. Läs repliker fungerar också med den här inställningen.


### <a name="using-azure-cli"></a>Använda Azure CLI

1. Ange azure.replication_support till `logical` .
   ```azurecli-interactive
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. Starta om servern för att tillämpa ändringen.
   ```azurecli-interactive
   az postgres server restart --resource-group mygroup --name myserver
   ```
3. Om du kör postgres 9,5 eller 9,6 och använder offentlig nätverks åtkomst lägger du till brand Väggs regeln för att inkludera den offentliga IP-adressen för den klient som du ska köra den logiska replikeringen från. Brand Väggs regelns namn måste innehålla **_replrule**. Till exempel *test_replrule*. Om du vill skapa en ny brand Väggs regel på servern kör du kommandot [AZ postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) . 

### <a name="using-azure-portal"></a>Använda Azure Portal

1. Ange Azure Replication-stöd till **logiska**. Välj **Spara**.

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Azure Database for PostgreSQL replikering – stöd för Azure-replikering":::

2. Starta om servern för att tillämpa ändringen genom att välja **Ja**.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Azure Database for PostgreSQL-replikering-bekräfta omstart":::

3. Om du kör postgres 9,5 eller 9,6 och använder offentlig nätverks åtkomst lägger du till brand Väggs regeln för att inkludera den offentliga IP-adressen för den klient som du ska köra den logiska replikeringen från. Brand Väggs regelns namn måste innehålla **_replrule**. Till exempel *test_replrule*. Klicka sedan på **Spara**.

   :::image type="content" source="./media/concepts-logical/client-replrule-firewall.png" alt-text="Azure Database for PostgreSQL-replikering – Lägg till brand Väggs regel":::

## <a name="start-logical-decoding"></a>Starta logisk avkodning

Logisk avkodning kan användas via direkt uppspelnings protokoll eller SQL-gränssnitt. Båda metoderna använder [replikerings-platser](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). En plats representerar en data ström med ändringar från en enskild databas.

Om du använder en replikerings plats krävs postgres. Vid det här tillfället är replik privilegiet bara tillgängligt för serverns administratörs användare. 

### <a name="streaming-protocol"></a>Strömnings protokoll
Användning av ändringar med streaming-protokollet är ofta bättre. Du kan skapa en egen konsument/anslutning eller använda ett verktyg som [Debezium](https://debezium.io/). 

Besök wal2json-dokumentationen för [ett exempel som använder streaming-protokollet med pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>SQL-gränssnitt
I exemplet nedan använder vi SQL-gränssnittet med wal2json-plugin-programmet.
 
1. Skapa en plats.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Utfärda SQL-kommandon. Exempel:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Förbrukar ändringarna.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Resultatet kommer att se ut så här:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Ta bort facket när du är färdig med den.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Övervaknings platser

Du måste övervaka logisk avkodning. Eventuell oanvänd replikerings plats måste släppas. Platser är kvar på postgres WAL-loggar och relevanta system kataloger tills ändringar har lästs av en konsument. Om din konsument Miss lyckas eller inte har kon figurer ATS korrekt, kommer de oanvända loggarna att stapla och fylla lagringen. Oanvända loggar ökar också risken för transaktions-ID wraparound. Båda situationerna kan orsaka att servern blir otillgänglig. Därför är det viktigt att logiska replikerings platser konsumeras kontinuerligt. Om ingen logisk replikerings plats används tar du bort den direkt.

Kolumnen "aktiv" i vyn pg_replication_slots visar om det finns en konsument som är ansluten till en plats.
```SQL
SELECT * FROM pg_replication_slots;
```

[Ange aviseringar](howto-alert-on-metric.md) för *använt lagrings utrymme* och *högsta fördröjning i repliker* mått för att meddela dig när värdena ökar de tidigare tröskelvärdena för normala perioder. 

> [!IMPORTANT]
> Du måste ta bort oanvända replikerings-platser. Om du inte gör det kan det leda till att servern inte är tillgänglig.

## <a name="how-to-drop-a-slot"></a>Så här släpper du en plats
Om du inte aktivt konsumerar en replikerings plats bör du släppa den.

Så här släpper du en replikerings-kortplats `test_slot` med namnet med hjälp av SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Om du slutar använda logisk avkodning ändrar du azure.replication_support tillbaka till `replica` eller `off` . WAL-informationen som behålls av `logical` är mer utförlig och bör inaktive ras när logisk avkodning inte används. 

 
## <a name="next-steps"></a>Nästa steg

* Besök postgres-dokumentationen om du vill [veta mer om logisk avkodning](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html).
* Kontakta [vårt team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) om du har frågor om logisk avkodning.
* Läs mer om [Läs repliker](concepts-read-replicas.md).

