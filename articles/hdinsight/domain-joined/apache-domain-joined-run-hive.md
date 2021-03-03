---
title: Apache Hive principer i Apache Ranger – Azure HDInsight
description: Lär dig hur du konfigurerar Apache Ranger-principer för Hive i en Azure HDInsight-tjänst med Enterprise Security Package.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: fbe4361033321f2ba81478c41a138937f70b5498
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705712"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurera Apache Hive-principer i HDInsight med Enterprise Security Package

Lär dig hur du konfigurerar Apache Ranger-principer för Apache Hive. I den här artikeln skapar du två Ranger-principer för att begränsa åtkomsten till hivesampletable. Hivesampletable medföljer HDInsight-kluster. När du har konfigurerat principerna använder du Excel och ODBC-drivrutinen för att ansluta till Hive-tabeller i HDInsight.

## <a name="prerequisites"></a>Förutsättningar

* Ett HDInsight-kluster med Enterprise Security Package. Se [Konfigurera HDInsight-kluster med ESP](./apache-domain-joined-configure-using-azure-adds.md).
* En arbets station med Microsoft 365 appar för företag, Office 2016, Office 2013 Professional Plus, Excel 2013 fristående eller Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Anslut till Apache Ranger Admin-gränssnittet
**Så här ansluter du till Ranger Admin-gränssnittet**

1. Från en webbläsare navigerar du till gränssnittet Ranger admin på `https://CLUSTERNAME.azurehdinsight.net/Ranger/` där kluster namn är namnet på klustret.

   > [!NOTE]  
   > Ranger använder andra autentiseringsuppgifter än Apache Hadoop kluster. Om du vill förhindra att webbläsare använder cachelagrade Hadoop-autentiseringsuppgifter använder du nytt InPrivate-webbläsarfönster för att ansluta till användar gränssnittet Ranger.

2. Logga in med klusteradministratörens domänanvändarnamn och lösenord:

    ![Start sida för HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    För närvarande fungerar Ranger bara med Yarn och Hive.

## <a name="create-domain-users"></a>Skapa domänanvändare

Information om hur du skapar hiveruser1 och hiveuser2 finns i [skapa ett HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). Du använder de två användar kontona i den här artikeln.

## <a name="create-ranger-policies"></a>Skapa Ranger-principer

I det här avsnittet skapar du två Ranger-principer för att få åtkomst till hivesampletable. Du kan ge select-behörighet för olika uppsättningar med kolumner. Båda användarna skapades med att [skapa ett HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). I nästa avsnitt ska du testa de två principerna i Excel.

**Skapa Ranger-principer**

1. Öppna Ranger Admin-gränssnittet. Se Anslut till Apache Ranger Admin-gränssnittet.
2. Välj **CLUSTERNAME_Hive** under **Hive**. Du bör se två förkonfigurerade principer.
3. Välj **Lägg till ny princip** och ange sedan följande värden:

    |Egenskap |Värde |
    |---|---|
    |Principnamn|Read-hivesampletable – alla|
    |Hive-databas|standard|
    |tabell|hivesampletable|
    |Hive-kolumn|*|
    |Välj användare|hiveuser1|
    |Behörigheter|välj|

    ![HDInsight ESP Ranger-principer för Hive konfigurera](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Om en domänanvändare inte är ifylld i Välj användare, vänta en stund för att Ranger ska synkronisera med AAD.

4. Välj **Lägg till** för att spara principen.

5. Upprepa de två sista stegen för att skapa en annan princip med följande egenskaper:

    |Egenskap |Värde |
    |---|---|
    |Principnamn|Read-hivesampletable-devicemake|
    |Hive-databas|standard|
    |tabell|hivesampletable|
    |Hive-kolumn|ClientID, devicemake|
    |Välj användare|hiveuser2|
    |Behörigheter|välj|

## <a name="create-hive-odbc-data-source"></a>Skapa Hive ODBC-datakälla

Du hittar anvisningarna i [Skapa Hive ODBC-datakällan](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Egenskap  |Beskrivning |
 | --- | --- |
 | Namn på datakälla | Namnge din datakälla |
 | Värd | Ange CLUSTERNAME.azurehdinsight.net. Till exempel myHDICluster.azurehdinsight.net |
 | Port | Använd **443**. (Den här porten har ändrats från 563 till 443.) |
 | Databas | Använd **standard**. |
 | Hive-servertyp | Välj **Hive Server 2** |
 | Mekanism | Välj **Azure HDInsight-tjänst** |
 | HTTP-sökväg | Lämna tomt. |
 | Användarnamn | Ange hiveuser1@contoso158.onmicrosoft.com. Uppdatera domän namnet om det är annorlunda. |
 | Lösenord | Ange lösenordet för hiveuser1. |

Se till att klicka på **Test** innan du sparar datakällan.

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight

I det sista avsnittet har du konfigurerat två principer.  hiveuser1 har select-behörighet för alla kolumner och hiveuser2 har select-behörighet på två kolumner. I det här avsnittet personifierar du de två användarna för att importera data till Excel.

1. Öppna en ny eller befintlig arbetsbok i Excel.

1. Från fliken **data** navigerar du till **Hämta data**  >  **från andra källor**  >  **från ODBC** för att starta **från ODBC-** fönstret.

    ![Guiden öppna data anslutning](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. I list rutan väljer du namnet på den data källa som du skapade i det sista avsnittet och väljer sedan **OK**.

1. För den första användningen öppnas en **ODBC-drivrutins** dialog ruta. Välj **fönster** på den vänstra menyn. Välj **Anslut** för att öppna fönstret **navigatör** .

1. Vänta tills dialogrutan **Markera databas och tabell** öppnas. Det kan ta några sekunder.

1. Välj **hivesampletable** och välj sedan **Nästa**.

1. Välj **Slutför**.

1. I dialogrutan **Importera data** kan du ändra eller specificera frågan. Det gör du genom att välja **Egenskaper**. Det kan ta några sekunder.

1. Välj fliken **definition** . Kommando texten är:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   Enligt Ranger-principerna du definierade har hiveuser1 select-behörighet för alla kolumner.  Den här frågan fungerar med hiveuser1's-autentiseringsuppgifter, men den här frågan fungerar inte med autentiseringsuppgifterna hiveuser2-autentiseringsuppgifter.

1. Välj **OK** för att stänga dialog rutan anslutnings egenskaper.

1. Stäng dialog rutan **Importera data** genom att klicka på **OK** .  

1. Ange lösenordet för hiveuser1 igen och klicka sedan på **OK**. Det tar några sekunder innan data har importerats till Excel. När den är färdig visas 11 kolumner med data.

Om du vill testa den andra principen (Read-hivesampletable-devicemake) skapade du i det sista avsnittet

1. Lägg till ett nytt kalkylblad i Excel.
2. Följ föregående procedur för att importera data.  Den enda ändring du gör är att använda autentiseringsuppgifterna hiveuser2-autentiseringsuppgifter i stället för hiveuser1's. Detta Miss lyckas eftersom hiveuser2 endast har behörighet att visa två kolumner. Du bör se följande fel:

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. Följ samma procedur för att importera data. Den här gången använder du autentiseringsuppgifterna för hiveuser2 och ändrar också select-uttrycket från:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    till:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    När den är färdig visas två kolumner med importerade data.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar ett HDInsight-kluster med Enterprise Security Package finns i [Konfigurera HDInsight-kluster med ESP](./apache-domain-joined-configure-using-azure-adds.md).
* Information om hur du hanterar HDInsight-kluster med ESP finns i [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md).
* Information om hur du kör Hive-frågor med SSH på HDInsight-kluster med ESP finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* För att ansluta Hive med Hive-JDBC, se [Anslut till Apache Hive på Azure HDInsight med HIVE JDBC-drivrutinen](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Information om hur du ansluter Excel till Hadoop med Hive ODBC finns i [ansluta Excel till Apache Hadoop med Microsoft HIVE ODBC-enhet](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Information om hur du ansluter Excel till Hadoop med hjälp av Power Query finns i [ansluta Excel till Apache Hadoop med Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)