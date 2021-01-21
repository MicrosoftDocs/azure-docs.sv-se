---
title: Huvud versions uppgradering i Azure Database for MySQL-enskild server
description: Den här artikeln beskriver hur du kan uppgradera huvud versionen för Azure Database for MySQL-en server
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 1/13/2021
ms.openlocfilehash: b62f4ebc61ac27478788d8b2bae5e4145f87ac8b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630208"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Huvud versions uppgradering i Azure Database for MySQL enskild server

> [!IMPORTANT]
> Huvud versions uppgradering för Azure Database för MySQL Single Server finns i en offentlig för hands version.

Den här artikeln beskriver hur du kan uppgradera din MySQL-huvudversion på plats i Azure Database for MySQL enskild server.

Den här funktionen gör det möjligt för kunder att utföra uppgraderingar på plats av sina MySQL 5,6-servrar till MySQL 5,7 med ett klick på knappen utan någon data förflyttning eller behovet av eventuella anslutnings strängar för program.

> [!Note]
> * Huvud versions uppgradering är bara tillgänglig för uppgradering av högre version från MySQL 5,6 till MySQL 5,7<br>
> * Det finns inte stöd för att uppgradera större versioner på replik servern ännu.
> * Servern kommer inte att vara tillgänglig under uppgraderings åtgärden. Vi rekommenderar därför att du utför uppgraderingar under det planerade underhålls fönstret.

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
