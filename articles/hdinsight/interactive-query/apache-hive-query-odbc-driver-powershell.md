---
title: Fråga Apache Hive med ODBC-drivrutin & PowerShell – Azure HDInsight
description: Använd Microsoft Hive ODBC-drivrutin och PowerShell för att fråga Apache Hive kluster på Azure HDInsight.
keywords: Hive, Hive ODBC, PowerShell
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34ee7eb25b1d106ea8bb53197d69ca5a9d528773
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871613"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Självstudie: fråga Apache Hive med ODBC och PowerShell

Microsoft ODBC-drivrutiner ger ett flexibelt sätt att interagera med olika typer av data källor, inklusive Apache Hive. Du kan skriva kod i skript språk som PowerShell som använder ODBC-drivrutinerna för att öppna en anslutning till Hive-klustret, skicka en fråga som du väljer och visa resultatet.

I den här självstudien ska du utföra följande uppgifter:

> [!div class="checklist"]
> * Hämta och installera Microsoft Hive ODBC-drivrutinen
> * Skapa en Apache Hive ODBC-datakälla som är länkad till klustret
> * Fråga exempel information från klustret med hjälp av PowerShell

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudiekursen behöver du följande:

* Ett interaktivt Query-kluster i HDInsight. Information om hur du skapar ett finns i [Kom igång med Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Välj **interaktiv fråga** som kluster typ.

## <a name="install-microsoft-hive-odbc-driver"></a>Installera Microsoft Hive ODBC-drivrutin

Hämta och installera [Microsoft HIVE ODBC-drivrutinen](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Skapa Apache Hive ODBC-datakälla

Följande steg visar hur du skapar en ODBC-datakälla för Apache Hive.

1. Från Windows navigerar du till **Starta**  >  **Windows Administration Tools**  >  **ODBC data sources (32-bitars)/(64-bitars)**.  En **Administratörs fönstret för ODBC-datakälla** öppnas.

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png " alt-text="OBDC data källans administratör" border="true":::

1. På fliken **användar-DSN** väljer du **Lägg till** för att öppna fönstret **Skapa ny data källa** .

1. Välj **Microsoft HIVE ODBC-drivrutin** och välj sedan **Slutför** för att öppna **installations fönstret för Microsoft Hive ODBC-drivrutin** .

1. Ange eller välj följande värden:

   | Egenskap | Beskrivning |
   | --- | --- |
   |  Namn på datakälla |Namnge din datakälla |
   |  Värd (er) |Ange `CLUSTERNAME.azurehdinsight.net`. Till exempel `myHDICluster.azurehdinsight.net` |
   |  Port |Använd **443**.|
   |  Databas |Använd **standard**. |
   |  Mekanism |Välj **Windows Azure HDInsight-tjänst** |
   |  Användarnamn |Ange HDInsight-kluster HTTP användar namn användar namn. Standardanvändarnamnet är **admin**. |
   |  Lösenord |Ange användar lösen ord för HDInsight-kluster. Markera kryss rutan **Spara lösen ord (krypterad)**.|

1. Valfritt: Välj **Avancerade alternativ**.  

   | Parameter | Beskrivning |
   | --- | --- |
   |  Använd intern fråga |När den är markerad försöker inte ODBC-drivrutinen konvertera TSQL till HiveQL. Använd bara det här alternativet om du är 100% säker på att du skickar in ren HiveQL-instruktioner. När du ansluter till SQL Server eller Azure SQL Database bör du lämna det omarkerat. |
   |  Hämtade rader per block |När du hämtar ett stort antal poster kan du behöva justera den här parametern för att säkerställa optimala prestanda. |
   |  Standard sträng kolumn längd, binär kolumn längd, decimal kolumn skala |Data typens längd och precision kan påverka hur data returneras. De gör att felaktig information returneras på grund av förlust och trunkering. |

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png " alt-text="Avancerade konfigurations alternativ för DSN" border="true":::

1. Testa data källan genom att välja **test** . När data källan har kon figurer ATS korrekt visar test resultatet **lyckades**.  

1. Stäng test fönstret genom att klicka på **OK** .  

1. Välj **OK** för att stänga **installations fönstret för Microsoft Hive ODBC-drivrutin** .  

1. Välj **OK** för att stänga fönstret **ODBC-Administrera data källa** .  

## <a name="query-data-with-powershell"></a>Fråga efter data med PowerShell

Följande PowerShell-skript är en funktion som ODBC kan köra för att fråga ett Hive-kluster.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

Följande kodfragment använder funktionen ovan för att köra en fråga på det interaktiva Query-kluster som du skapade i början av självstudien. Ersätt `DATASOURCENAME` med namnet på den **data källa** som du har angett på skärmen **installations program för Microsoft Hive ODBC-drivrutin** . När du uppmanas att ange autentiseringsuppgifter anger du det användar namn och lösen ord som du angav under **användar namn för kluster inloggning** och **lösen ord för kluster inloggning** när du skapade klustret.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, HDInsight-klustret och lagrings kontot när de inte längre behövs. Det gör du genom att markera resurs gruppen där klustret skapades och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Microsoft Hive ODBC-drivrutin och PowerShell för att hämta data från ditt Azure HDInsight-kluster för interaktiva frågor.

> [!div class="nextstepaction"]
> [Ansluta Excel till Apache Hive med hjälp av ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
