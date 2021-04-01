---
title: Tillfälliga anslutnings fel – Azure Database for MySQL
description: Lär dig hur du hanterar tillfälliga anslutnings fel och ansluter effektivt till Azure Database for MySQL.
keywords: MySQL-anslutning, anslutnings sträng, anslutnings problem, tillfälligt fel, anslutnings fel, Anslut effektivt
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542617"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Hantera tillfälliga fel och ansluta effektivt till Azure Database for MySQL

Den här artikeln beskriver hur du hanterar tillfälliga fel och ansluter effektivt till Azure Database for MySQL.

## <a name="transient-errors"></a>Tillfälliga fel

Ett tillfälligt fel, även kallat ett tillfälligt fel, är ett fel som kommer att lösa sig självt. Oftast manifestet de här felen som en anslutning till databas servern som tappas bort. Det går inte att öppna nya anslutningar till en server. Tillfälliga fel kan uppstå när ett maskin-eller nätverks fel uppstår. En annan orsak kan vara en ny version av en PaaS-tjänst som distribueras. De flesta av dessa händelser begränsas automatiskt av systemet på mindre än 60 sekunder. En bra metod för att utforma och utveckla program i molnet är att vänta på tillfälliga fel. Anta att de kan ske i vilken komponent som helst och att ha rätt logik på plats för att hantera dessa situationer.

## <a name="handling-transient-errors"></a>Hantering av tillfälliga fel

Tillfälliga fel ska hanteras med logiken för omprövning. Situationer som måste beaktas:

* Ett fel inträffar när du försöker öppna en anslutning
* En inaktiv anslutning bryts på Server sidan. När du försöker utfärda ett kommando kan det inte köras
* En aktiv anslutning som för närvarande kör ett kommando ignoreras.

Det första och det andra fallet är ganska rakt framåt till referensen. Försök att öppna anslutningen igen. När du lyckas har det tillfälliga felet minimerats av systemet. Du kan använda din Azure Database for MySQL igen. Vi rekommenderar att du väntar innan du försöker ansluta igen. Stäng av om de första Återförsöken inte fungerar. På så sätt kan systemet använda alla resurser som är tillgängliga för att lösa fel situationen. Ett lämpligt mönster att följa är:

* Vänta i 5 sekunder innan ditt första försök.
* För varje följande försök ökar vänte tiden exponentiellt, upp till 60 sekunder.
* Ange ett högsta antal återförsök som programmet anser att åtgärden misslyckades.

När en anslutning med en aktiv transaktion Miss lyckas är det svårare att hantera återställningen korrekt. Det finns två fall: om transaktionen var skrivskyddad, är det säkert att öppna anslutningen igen och försöka utföra transaktionen igen. Om transaktionen även skrevs till databasen, måste du bestämma om transaktionen återställdes eller om den lyckades innan det tillfälliga felet uppstod. I så fall kanske du bara inte har fått bekräftelse bekräftelse från databas servern.

Ett sätt att göra detta är att generera ett unikt ID på klienten som används för alla återförsök. Du skickar det här unika ID: t som en del av transaktionen till servern och lagrar den i en kolumn med en unik begränsning. På så sätt kan du på ett säkert sätt försöka utföra transaktionen igen. Det lyckas om den tidigare transaktionen återställdes och det unika ID: t för klienten som skapats ännu inte finns i systemet. Det går inte att ange en dubblett av nyckeln om det unika ID: t tidigare lagrades eftersom den tidigare transaktionen slutfördes.

När ditt program kommunicerar med Azure Database for MySQL via mellanprodukter från tredje part, frågar du leverantören om mellanprodukter innehåller omprövnings logik för tillfälliga fel.

Se till att testa att du testar logiken igen. Försök till exempel att köra koden när du skalar upp eller ned beräknings resurserna för Azure Database for MySQL-servern. Ditt program bör hantera de korta stillestånds tiden som påträffas under den här åtgärden utan problem.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Anslut effektivt till Azure Database for MySQL

Databas anslutningar är en begränsad resurs, så att du effektivt kan använda anslutningspoolen för att komma åt Azure Database for MySQL optimerar prestandan. I avsnittet nedan förklaras hur du använder anslutningspoolen eller beständiga anslutningar till mer effektiv åtkomst Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Få åtkomst till databaser med hjälp av anslutningspoolen (rekommenderas)

Hantering av databas anslutningar kan ha en betydande inverkan på programmets prestanda som helhet. För att optimera prestanda för ditt program bör målet vara att minska antalet gånger som anslutningar upprättas och tid för att upprätta anslutningar i nyckel kods Sök vägar. Vi rekommenderar starkt att du använder anslutningspoolen för databas anslutning eller beständiga anslutningar för att ansluta till Azure Database for MySQL. Anslutningspoolen för databas anslutning hanterar skapande, hantering och allokering av databas anslutningar. När ett program begär en databas anslutning prioriteras tilldelningen av befintliga inaktiva databas anslutningar i stället för att en ny anslutning skapas. När programmet har slutat använda databas anslutningen återställs anslutningen som förberedelse för att användas i stället för att helt enkelt stängas av.

Den här artikeln innehåller [en exempel kod](./sample-scripts-java-connection-pooling.md) som använder Java som exempel för bättre bild. Mer information finns i avsnittet om [apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Servern konfigurerar en timeout-mekanism för att stänga en anslutning som har varit i inaktivt läge under en tid för att frigöra resurser. Se till att konfigurera verifierings systemet för att säkerställa effektiviteten hos beständiga anslutningar när du använder dem. Mer information finns i [Konfigurera verifierings system på klient sidan för att säkerställa effektiviteten hos permanenta anslutningar](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Få åtkomst till databaser med hjälp av beständiga anslutningar (rekommenderas)

Begreppet beständiga anslutningar liknar det för anslutningspoolen. Att ersätta korta anslutningar med permanenta anslutningar kräver bara mindre ändringar i koden, men det har en stor effekt vad gäller att förbättra prestanda i många typiska program scenarier.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Få åtkomst till databaser med hjälp av metoden vänta och försök med korta anslutningar

Om du har resurs begränsningar rekommenderar vi starkt att du använder databas bassänger eller beständiga anslutningar för att få åtkomst till databaser. Om programmet använder korta anslutningar och upplever anslutnings fel när du närmar dig den övre gränsen för antalet samtidiga anslutningar kan du prova att vänta och försöka igen. Du kan ange en lämplig vänte tid med en kortare vänte tid efter det första försöket. Därefter kan du prova att vänta på händelser flera gånger.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Konfigurera verifierings mekanismer i-klienter för att bekräfta effektiviteten hos beständiga anslutningar

Servern konfigurerar en tids gräns för att stänga en anslutning som har varit i inaktivt läge under en tid för att frigöra resurser. När klienten har åtkomst till databasen igen är det detsamma som att skapa en ny anslutningsbegäran mellan klienten och servern. Konfigurera en verifierings funktion på klienten för att säkerställa effektiviteten i anslutningarna under processen med att använda dem. Som du ser i följande exempel kan du använda Tomcat JDBC anslutningspoolen för att konfigurera den här verifierings funktionen.

Genom att ange parametern TestOnBorrow när det finns en ny begäran, verifierar anslutningspoolen automatiskt effektiviteten hos eventuella tillgängliga inaktiva anslutningar. Om en sådan anslutning är effektiv, kommer dess direkt returnerade andra anslutningspoolen att återkalla anslutningen. Anslutningspoolen skapar sedan en ny effektiv anslutning och returnerar den. Den här processen säkerställer att databasen nås effektivt. 

Information om de olika inställningarna finns i JDBC- [anslutningspoolen, officiellt introduktions dokument](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Du måste huvudsakligen ange följande tre parametrar: TestOnBorrow (true), ValidationQuery (inställt på SELECT 1) och ValidationQueryTimeout (inställt på 1). Den speciella exempel koden visas nedan:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka anslutningsproblem till Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
