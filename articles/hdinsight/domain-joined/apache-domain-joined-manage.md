---
title: Hantera Enterprise Security Package kluster – Azure HDInsight
description: Lär dig hur du hanterar Azure HDInsight-kluster med Enterprise Security Package.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: b0f8ba4adfa4b08c23d3f69fa4e2b01f4580bb19
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742219"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Hantera HDInsight-kluster med Enterprise Security Package

Lär dig mer om användare och roller i HDInsight Enterprise Security Package (ESP) och hur du hanterar ESP-kluster.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Använda VSCode för att länka till domänanslutna kluster

Du kan länka ett vanligt kluster med hjälp av Apache Ambari-hanterat användar namn, även länka ett säkerhets Apache Hadoop kluster genom att använda domän användar namn (t. ex.: `user1@contoso.com` ).

1. Öppna [Visual Studio Code](https://code.visualstudio.com/). Se till att tillägget [Spark & Hive tools](../hdinsight-for-vscode.md) är installerat.

1. Följ stegen i [Länka ett kluster](../hdinsight-for-vscode.md#link-a-cluster) för Visual Studio Code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Använda IntelliJ för att länka till domänanslutna kluster

Du kan länka ett vanligt kluster genom att använda Ambari-hanterat användar namn, även länka ett Hadoop-kluster med domän namn (t. ex.: `user1@contoso.com` ).

1. Öppna IntelliJ IDEA. Se till att alla [krav](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) är uppfyllda.

1. Följ stegen i [Länka ett kluster](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) för IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Använda Eclipse för att länka till domänanslutna kluster

Du kan länka ett vanligt kluster genom att använda Ambari-hanterat användar namn, även länka ett Hadoop-kluster med domän namn (t. ex.: `user1@contoso.com` ).

1. Öppna Eclipse. Se till att alla [krav](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) är uppfyllda.

1. Följ stegen i [Länka ett kluster](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) för Sol förmörkelse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Åtkomst till kluster med Enterprise Security Package

Enterprise Security Package (tidigare kallat HDInsight Premium) ger åtkomst till flera användare till klustret, där autentisering görs genom Active Directory och auktorisering av Apache Ranger och lagrings-ACL: er (ADLS ACL: er). Auktorisering ger säkra gränser mellan flera användare och tillåter endast att privilegierade användare får åtkomst till data baserat på Auktoriseringsprinciper.

Säkerhet och användar isolering är viktiga för ett HDInsight-kluster med Enterprise Security Package. För att uppfylla dessa krav stöds SSH-åtkomst till klustret med Enterprise Security Package för den lokala användare som valts i klustrets skapande tid samt användare som är tillgängliga i AAD-DS (t. ex. Kerberos). I följande tabell visas rekommenderade åtkomst metoder för varje kluster typ:

|Arbetsbelastning|Scenario|Åtkomst metod|
|--------|--------|-------------|
|Apache Hadoop|Hive – interaktiva jobb/frågor    |<ul><li>[Beeline](#beeline)</li><li>[Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktiva jobb/frågor, PySpark interaktiva|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin med livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batch-scenarier – Spark-överföring, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktiv fråga (LLAP)|Interaktiv|<ul><li>[Beeline](#beeline)</li><li>[Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Valfri|Installera anpassat program|<ul><li>[Skript åtgärder](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter är inte installerat/stöds i Enterprise Security Package.

Att använda standard-API: er hjälper till med säkerhets perspektiv. Du får också följande fördelar:

- **Hantering** – du kan hantera din kod och automatisera jobb med standard-API: er – LIVY, HS2 osv.
- **Audit** – med SSH finns det inget sätt att granska, vilka användare som använder SSH till klustret. Detta är inte fallet när jobben skapas via standard slut punkter som de skulle köras i samband med användaren.

### <a name="use-beeline"></a><a name="beeline"></a>Använda Beeline

Installera Beeline på datorn och Anslut via det offentliga Internet använder du följande parametrar:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Om du har installerat Beeline lokalt och ansluter via en Azure-Virtual Network använder du följande parametrar:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Om du vill hitta det fullständigt kvalificerade domän namnet för en huvudnoden använder du informationen i hantera HDInsight med hjälp av Ambari REST API-dokumentet.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Användare av HDInsight-kluster med ESP

Ett icke-ESP HDInsight-kluster har två användar konton som skapas när klustret skapas:

- **Ambari-administratör**: det här kontot kallas även för *Hadoop-användare* eller *http-användare*. Detta konto kan användas för att logga in på Ambari på `https://CLUSTERNAME.azurehdinsight.net` . Det kan också användas för att köra frågor på Ambari-vyer, köra jobb via externa verktyg (till exempel PowerShell, Templeton, Visual Studio) och autentisera med Hive ODBC-drivrutin och BI-verktyg (till exempel Excel, Power BI eller Tableau).

Ett HDInsight-kluster med ESP har tre nya användare utöver Ambari-administratören.

- **Ranger-administratör**: det här kontot är det lokala Apache Ranger-administratörskontot. Det är inte en Active Directory-domän användare. Detta konto kan användas för att konfigurera principer och göra andra användare administratörer eller delegerade administratörer (så att dessa användare kan hantera principer). Som standard är användar namnet *administratör* och lösen ordet är detsamma som administratörs lösen ordet för Ambari. Lösen ordet kan uppdateras från sidan Inställningar i Ranger.

- **Kluster administratörs domän användare**: det här kontot är en Active Directory-domän som angetts som Hadoop-kluster administratör, inklusive Ambari och Ranger. Du måste ange användarens autentiseringsuppgifter när du skapar klustret. Den här användaren har följande behörigheter:
    - Anslut datorer till domänen och placera dem i den ORGANISATIONSENHET som du anger när du skapar klustret.
    - Skapa tjänstens huvud namn i den ORGANISATIONSENHET som du anger när klustret skapas.
    - Skapa omvända DNS-poster.

    Observera att övriga AD-användare också har dessa behörigheter.

    Det finns några slut punkter i klustret (till exempel Templeton) som inte hanteras av Ranger, och därför inte är säkra. Dessa slut punkter är låsta för alla användare förutom kluster administratörens domän användare.

- **Normalt**: När klustret skapas kan du ange flera Active Directory-grupper. Användare i dessa grupper synkroniseras med Ranger och Ambari. Dessa användare är domän användare och har åtkomst till endast Ranger-hanterade slut punkter (till exempel Hiveserver2). Alla RBAC-principer och granskningar kommer att gälla för dessa användare.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Roller för HDInsight-kluster med ESP

HDInsight Enterprise Security Package har följande roller:

- Kluster administratör
- Kluster operatör
- Tjänstadministratör
- Tjänst operatör
- Kluster användare

**Så här visar du behörigheterna för dessa roller**

1. Öppna hanterings gränssnittet för Ambari.  Se [Öppna hanterings gränssnittet för Ambari](#open-the-ambari-management-ui).
2. Välj **roller** på den vänstra menyn.
3. Välj det blå frågetecknet för att se behörigheterna:

    ![Behörigheter för ESP HDInsight-roller](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Öppna hanterings gränssnittet för Ambari

1. Navigera till `https://CLUSTERNAME.azurehdinsight.net/` där kluster namn är namnet på klustret.
1. Logga in på Ambari med hjälp av kluster administratörens domän användar namn och lösen ord.
1. Välj List Rute menyn **administratör** i det övre högra hörnet och välj sedan **Hantera Ambari**.

    ![ESP HDInsight hantera Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Användar gränssnittet ser ut så här:

    ![ESP HDInsight Apache Ambari management UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Lista de domän användare som synkroniseras från din Active Directory

1. Öppna hanterings gränssnittet för Ambari.  Se [Öppna hanterings gränssnittet för Ambari](#open-the-ambari-management-ui).
2. Välj **användare** på menyn till vänster. Du ska se alla användare som har synkroniserats från Active Directory till HDInsight-klustret.

    ![ESP HDInsight Ambari management UI List users](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Lista de domän grupper som synkroniserats från din Active Directory

1. Öppna hanterings gränssnittet för Ambari.  Se [Öppna hanterings gränssnittet för Ambari](#open-the-ambari-management-ui).
2. Välj **grupper** på den vänstra menyn. Du ser alla grupper som synkroniserats från Active Directory till HDInsight-klustret.

    ![ESP HDInsight-Ambari hanterings gränssnitt List grupper](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurera Hive-vyer behörigheter

1. Öppna hanterings gränssnittet för Ambari.  Se [Öppna hanterings gränssnittet för Ambari](#open-the-ambari-management-ui).
2. Välj **vyer** på den vänstra menyn.
3. Välj **HIVE** för att visa information.

    ![ESP HDInsight Ambari management UI Hive views](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Välj länken **Hive-vy** om du vill konfigurera Hive-vyer.
5. Rulla ned till avsnittet **behörigheter** .

    ![ESP HDInsight Ambari management UI Hive views Configure Permissions](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Välj **Lägg till användare** eller **Lägg till grupp** och ange sedan de användare eller grupper som kan använda Hive-vyer.

## <a name="configure-users-for-the-roles"></a>Konfigurera användare för rollerna

 Om du vill se en lista över roller och deras behörigheter, se roller för HDInsight-kluster med ESP.

1. Öppna hanterings gränssnittet för Ambari.  Se [Öppna hanterings gränssnittet för Ambari](#open-the-ambari-management-ui).
2. Välj **roller** på den vänstra menyn.
3. Välj **Lägg till användare** eller **Lägg till grupp** för att tilldela användare och grupper till olika roller.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ett HDInsight-kluster med Enterprise Security Package finns i [Konfigurera HDInsight-kluster med ESP](./apache-domain-joined-configure-using-azure-adds.md).
- Om du vill konfigurera Hive-principer och köra Hive-frågor, se [konfigurera Apache Hive principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md).