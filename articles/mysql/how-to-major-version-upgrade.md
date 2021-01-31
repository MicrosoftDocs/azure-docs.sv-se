---
title: Huvud versions uppgradering i Azure Database for MySQL-enskild server
description: Den här artikeln beskriver hur du kan uppgradera huvud versionen för Azure Database for MySQL-en server
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 62faaed3672f721b26587d1bca3ddb0947f733e7
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220844"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Huvud versions uppgradering i Azure Database for MySQL enskild server

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tas den bort från den här artikeln.
>

> [!IMPORTANT]
> Huvud versions uppgradering för Azure Database för MySQL Single Server finns i en offentlig för hands version.

Den här artikeln beskriver hur du kan uppgradera din MySQL-huvudversion på plats i Azure Database for MySQL enskild server.

Den här funktionen gör det möjligt för kunder att utföra uppgraderingar på plats av sina MySQL 5,6-servrar till MySQL 5,7 med ett klick på knappen utan någon data förflyttning eller behovet av eventuella anslutnings strängar för program.

> [!Note]
> * Huvud versions uppgradering är bara tillgänglig för en större versions uppgradering från MySQL 5,6 till MySQL 5,7.
> * Servern kommer inte att vara tillgänglig under uppgraderings åtgärden. Vi rekommenderar därför att du utför uppgraderingar under det planerade underhålls fönstret. Du kan överväga att [utföra minimal stillestånds tid för stor versions uppgradering från MySQL 5,6 till mysql 5,7 med hjälp av Läs replik.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Utför större versions uppgradering från MySQL 5,6 till MySQL 5,7 med Azure Portal

Följ de här stegen för att utföra större versions uppgradering för din Azure Database of MySQL 5,6-server med Azure Portal

> [!IMPORTANT]
> Vi rekommenderar att du först uppgraderar till en återställd kopia av servern i stället för att uppgradera produktion direkt. Se [hur du utför en tidpunkts återställning](howto-restore-server-portal.md#point-in-time-restore).

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL 5,6-Server.

2. På sidan **Översikt** klickar du på knappen **Uppgradera** i verktygsfältet.

3. I avsnittet **Uppgradera** väljer du **OK** för att uppgradera Azure Database for MySQL 5,6 server till 5,7 Server.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-översikt – uppgradering":::

4. Ett meddelande bekräftar att uppgraderingen har slutförts.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Utför större versions uppgradering från MySQL 5,6 till MySQL 5,7 med Azure CLI

Följ de här stegen för att utföra större versions uppgradering för din Azure Database of MySQL 5,6-server med Azure CLI

> [!IMPORTANT]
> Vi rekommenderar att du först uppgraderar till en återställd kopia av servern i stället för att uppgradera produktion direkt. Se [hur du utför en tidpunkts återställning](howto-restore-server-cli.md#server-point-in-time-restore).

1. Installera [Azure CLI för Windows](/cli/azure/install-azure-cli) eller Använd Azure cli i [Azure Cloud Shell](../cloud-shell/overview.md) för att köra uppgraderings kommandona. 
 
   Den här uppgraderingen kräver version 2.16.0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. Kör az version om du vill hitta versionen och de beroende bibliotek som är installerade. Om du vill uppgradera till den senaste versionen kör du az upgrade.

2. När du har loggat in kör du kommandot [AZ MySQL server Upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) :

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   Kommando tolken visar meddelandet "-körs". När det här meddelandet inte längre visas är versions uppgraderingen slutförd.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Utför större versions uppgradering från MySQL 5,6 till MySQL 5,7 på Read Replica med hjälp av Azure Portal

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL 5,6-läsa replik Server.

2. På sidan **Översikt** klickar du på knappen **Uppgradera** i verktygsfältet.

3. I avsnittet **Uppgradera** väljer du **OK** för att uppgradera Azure Database för MySQL 5,6-läsa replik server till 5,7 Server.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-översikt – uppgradering":::

4. Ett meddelande bekräftar att uppgraderingen har slutförts.

5. På sidan **Översikt** bekräftar du att Azure Database for MySQL-versionen av Server versionen är 5,7.

6. Gå nu till den primära servern och [utför stor versions uppgradering](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) .

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Utför minimal stillestånds tid för stor versions uppgradering från MySQL 5,6 till MySQL 5,7 med hjälp av Läs repliker

Du kan utföra minimal stillestånds tid för stor versions uppgradering från MySQL 5,6 till MySQL 5,7 genom att använda Läs repliker. Idén är att uppgradera den skrivskyddade repliken av servern till 5,7 först och sedan redundansväxla ditt program så att det pekar på Läs replik och gör det till en ny primär.

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL 5,6.

2. Skapa en [Läs replik](https://docs.microsoft.com/azure/mysql/concepts-read-replicas#create-a-replica) från den primära servern.

3. [Uppgradera din Läs replik](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) till version 5,7.

4. När du har bekräftat att replik servern körs på version 5,7 stoppar du ditt program från att ansluta till den primära servern.
 
5. Kontrol lera replikeringsstatus och se till att repliken har fångats upp med primärt, så att alla data synkroniseras och att inga nya åtgärder utförs i primär.

   Anropa [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) kommandot på replik servern för att visa replikeringsstatus.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Om statusen `Slave_IO_Running` och `Slave_SQL_Running` är "Ja" och värdet för `Seconds_Behind_Master` är "0" fungerar replikeringen bra. `Seconds_Behind_Master` anger hur sen repliken är. Om värdet inte är "0" innebär det att repliken bearbetar uppdateringar. När du `Seconds_Behind_Master` har bekräftat är "0" det är säkert att stoppa replikeringen.

6. Flytta upp din Läs replik till primär genom att [stoppa replikeringen](https://docs.microsoft.com/azure/mysql/howto-read-replicas-portal#stop-replication-to-a-replica-server).

7. Peka ditt program till den nya primära (tidigare replik) som kör Server 5,7. Varje server har en unik anslutnings sträng. Uppdatera programmet så att det pekar på den (tidigare) repliken i stället för källan.

> [!Note]
> Det här scenariot kommer att ha drift stopp under steg 4, 5 och 6.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>När kommer den här uppgraderings funktionen att vara GA när vi har MySQL v 5.6 i vår produktions miljö som vi behöver uppgradera?

GA för den här funktionen är planerad innan MySQL v 5.6 upphör. Funktionen är dock produktions klar och stöds fullt ut av Azure, så du bör köra den med förtroende för din miljö. Som rekommenderad praxis rekommenderar vi starkt att du kör och testar det först på en återställd kopia av servern så att du kan beräkna stillestånds tiden under uppgraderingen och utföra test av programkompatibilitet innan du kör det på produktion. Mer information finns i [så här utför du](howto-restore-server-portal.md#point-in-time-restore) tidpunkts återställning för att skapa en tidpunkts kopia av servern. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Kommer detta att orsaka drift stopp av servern och i så fall hur lång tid?

Ja, servern kommer inte att vara tillgänglig under uppgraderings processen så vi rekommenderar att du utför den här åtgärden under det planerade underhålls fönstret. Den uppskattade stillestånds tiden beror på databasens storlek, den allokerade lagrings storleken (IOPs etablerad) och antalet tabeller i databasen. Uppgraderings tiden är direkt proportionell till antalet tabeller på servern. Uppgraderingar av Basic SKU-servrar förväntas ta längre tid eftersom de är på standard plattform för lagring. Vi rekommenderar att du först uppgraderar till en återställd kopia av servern för att beräkna stillestånds tiden för din server miljö.  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>Det noteras att det inte stöds på replik servern ännu. Vad betyder det konkreta?

För närvarande stöds inte högre versions uppgradering för replik servern, vilket innebär att du inte bör köra den för servrar som ingår i replikeringen (antingen käll-eller replik servern). Om du vill testa uppgraderingen av servrarna som ingår i replikeringen innan vi lägger till replik stödet för uppgraderings funktionen rekommenderar vi följande steg:

1. Under planerat underhåll stoppar du [replikeringen och tar bort replik servern](howto-read-replicas-portal.md) efter att du har samlat in dess namn och all konfigurations information (brand Väggs inställningar, Server parameter konfiguration om den skiljer sig från käll servern).
2. Utför uppgraderingen av käll servern.
3. Etablera en ny Läs replik server med samma namn och konfigurations inställningar som du hämtade i steg 1. Den nya replik servern kommer att finnas på v 5.7 automatiskt efter att käll servern har uppgraderats till v 5.7.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Vad händer om vi inte väljer att uppgradera vår MySQL v 5.6-server före den 5 februari 2021?

Du kan fortfarande fortsätta att köra MySQL v 5.6-servern som tidigare. Azure genomför **aldrig** Force-uppgraderingen på servern. De begränsningar som dokumenteras i [Azure Database for MySQL versions policy](concepts-version-policy.md) gäller dock.

## <a name="next-steps"></a>Nästa steg

Läs mer om [principer för Azure Database for MySQL versioner](concepts-version-policy.md).
