---
title: Auktorisera användare för Ambari-vyer – Azure HDInsight
description: Hantera Ambari-användare och grupp behörigheter för HDInsight-kluster med ESP aktiverat.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 0b6d4dacf43d80f371a2a4d286af1972198c3be3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942705"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Auktorisera användare för Apache Ambari Views

[Enterprise Security Package (ESP) aktiverade HDInsight-kluster](./domain-joined/hdinsight-security-overview.md) ger funktioner i företags klass, inklusive Azure Active Directory-baserad autentisering. Du kan [synkronisera nya användare](hdinsight-sync-aad-users-to-cluster.md) som har lagts till i Azure AD-grupper som har tilldelats åtkomst till klustret, så att specifika användare kan utföra vissa åtgärder. Att arbeta med användare, grupper och behörigheter i [Apache Ambari](https://ambari.apache.org/) stöds för både ESP HDInsight-kluster och standard HDInsight-kluster.

Active Directory användare kan logga in på klusternoderna med sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera kluster interaktioner med andra godkända slut punkter som [nyans](https://gethue.com/), Ambari vyer, ODBC, JDBC, POWERSHELL och REST-API: er.

> [!WARNING]  
> Ändra inte lösen ordet för Ambari-hdinsightwatchdog () på ditt Linux-baserade HDInsight-kluster. Om du ändrar lösen ordet bryts möjligheten att använda skript åtgärder eller utföra skalnings åtgärder med klustret.

Om du inte redan har gjort det följer du [de här anvisningarna](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) för att etablera ett nytt ESP-kluster.

## <a name="access-the-ambari-management-page"></a>Öppna hanterings sidan för Ambari

Öppna **hanterings sidan för Ambari** i [webb gränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md)genom att bläddra till `https://CLUSTERNAME.azurehdinsight.net` . Ange det användar namn och lösen ord för kluster administratör som du definierade när du skapade klustret. Gå sedan till Ambari-instrumentpanelen och välj **Hantera Ambari** under **Administratörs** menyn:

![Hantera Apache Ambari-instrumentpanel](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Lägga till användare

### <a name="add-users-through-the-portal"></a>Lägg till användare via portalen

1. Välj **användare** på sidan hantering.

    ![Apache Ambari-hanterings sidan användare](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Välj **+ skapa lokal användare**.

1. Ange **användar namn** och **lösen ord**. Välj **Spara**.

### <a name="add-users-through-powershell"></a>Lägg till användare via PowerShell

Redigera variablerna nedan genom att ersätta `CLUSTERNAME` , `NEWUSER` och `PASSWORD` med lämpliga värden.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Lägg till användare via sväng

Redigera variablerna nedan genom att ersätta `CLUSTERNAME` , `ADMINPASSWORD` , `NEWUSER` och `USERPASSWORD` med lämpliga värden. Skriptet är utformat för att köras med bash. Små ändringar krävs för kommando tolken i Windows.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Bevilja behörighet att Apache Hive vyer

Ambari levereras med View-instanser för [Apache Hive](https://hive.apache.org/) och [Apache TEZ](https://tez.apache.org/), bland annat. Om du vill bevilja åtkomst till en eller flera instanser av Hive-vyn går du till **hanterings sidan för Ambari**.

1. På sidan hantering väljer du länken **vyer** under **rubrik menyn till** vänster.

    ![Apache Ambari vyer Visa länkar](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. På sidan vyer expanderar du **HIVE** -raden. Det finns en standard-Hive-vy som skapas när Hive-tjänsten läggs till i klustret. Du kan också skapa fler instanser av Hive-vyer efter behov. Välj en Hive-vy:

    ![HDInsight-vyer – Apache Hive vy](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Rulla nedåt längst ned på sidan Visa. Under avsnittet *behörigheter* har du två alternativ för att ge domän användare behörighet till vyn:

**Bevilja behörighet till dessa användare** ![ Bevilja behörighet till dessa användare](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Bevilja behörighet till dessa grupper** ![ Bevilja behörighet till dessa grupper](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Om du vill lägga till en användare väljer du knappen **Lägg till användare** .

   * Börja skriva användar namnet så visas en listruta med tidigare definierade namn.

     ![Apache Ambari-användare slutförs automatiskt](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Välj eller Skriv klart, användar namnet. Välj knappen **nytt** om du vill lägga till det här användar namnet som en ny användare.

   * Markera **kryss rutan blå** om du vill spara ändringarna.

     ![Apache Ambari bevilja användar behörighet](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Välj knappen **Lägg till grupp** om du vill lägga till en grupp.

   * Börja skriva grupp namnet. Processen att välja ett befintligt grupp namn eller lägga till en ny grupp är samma som för att lägga till användare.
   * Markera **kryss rutan blå** om du vill spara ändringarna.

     ![Apache Ambari Grant-behörighet](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Att lägga till användare direkt i en vy är användbart när du vill tilldela behörigheter till en användare som ska använda vyn, men vill inte att de ska vara medlemmar i en grupp som har ytterligare behörigheter. För att minska den administrativa belastningen kan det vara enklare att tilldela grupper behörigheter.

## <a name="grant-permissions-to-apache-tez-views"></a>Bevilja behörighet till Apache TEZ views

Med [Apache TEZ](https://tez.apache.org/) View-instanserna kan användarna övervaka och felsöka alla TEZ-jobb, som skickas av [Apache Hive](https://hive.apache.org/) frågor och [Apache gris](https://pig.apache.org/) -skript. Det finns en instans av standard-Tez som skapas när klustret har allokerats.

Om du vill tilldela användare och grupper till en instans av Tez, expanderar du raden **Tez** på sidan vyer, enligt beskrivningen ovan.

![HDInsight-vyer – Apache Tez-vy](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Om du vill lägga till användare eller grupper upprepar du steg 3-5 i föregående avsnitt.

## <a name="assign-users-to-roles"></a>Tilldela användare till roller

Det finns fem säkerhets roller för användare och grupper, som visas i ordning för minskning av åtkomst behörigheter:

* Kluster administratör
* Kluster operatör
* Tjänstadministratör
* Tjänst operatör
* Kluster användare

Om du vill hantera roller går du till **hanterings sidan för Ambari** och väljer sedan länken **roller** i meny gruppen *kluster* till vänster.

![Meny länkar för Apache Ambari-roller](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Om du vill se en lista över behörigheter för varje roll, klickar du på det blå frågetecknet bredvid tabell rubriken **roller** på sidan roller.

![Meny länk behörigheter för Apache Ambari roles](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Meny länk behörigheter för Apache Ambari roles")

På den här sidan finns det två olika vyer som du kan använda för att hantera roller för användare och grupper: block och lista.

### <a name="block-view"></a>Blockera vy

I vyn blockera visas varje roll på en egen rad, och den **tilldelas roller till dessa användare** och **tilldelar roller till de här grupp** alternativen enligt beskrivningen ovan.

![Block vy för Apache Ambari roles](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Listvy

Listvyn innehåller snabb redigerings funktioner i två kategorier: användare och grupper.

* Kategorin användare i vyn lista visar en lista över alla användare, så att du kan välja en roll för varje användare i list rutan.

    ![List visning för Apache Ambari-roller – användare](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* Kategorin grupper i listvyn visar alla grupper och rollen som tilldelats till varje grupp. I vårt exempel synkroniseras listan med grupper från de Azure AD-grupper som anges i **användar grupp egenskapen åtkomst** för klustrets domän inställningar. Se [skapa ett HDInsight-kluster med ESP aktiverat](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    ![Vyn Apache Ambari roles List-Groups](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    I bilden ovan tilldelas gruppen "hiveusers" rollen som *kluster användar* roll. Det här är en skrivskyddad roll som gör att användare av gruppen kan visa men inte ändra tjänst konfiguration och kluster mått.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Logga in på Ambari som en användare med endast visning

Vi har tilldelat våra Azure AD-domän användare "hiveuser1"-behörigheter till Hive-och Tez-vyer. När vi startar Ambari-webbgränssnittet och anger användarens domän uppgifter (Azure AD-användarnamnet i e-postformat och lösen ord) omdirigeras användaren till sidan Ambari views. Härifrån kan användaren välja vilken tillgänglig vy som helst. Användaren kan inte besöka någon annan del av webbplatsen, inklusive instrument panelen, tjänster, värdar, aviseringar eller administratörs sidor.

![Apache Ambari-användare med endast vyer](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Logga in på Ambari som en kluster användare

Vi har tilldelat vår Azure AD-domän användare "hiveuser2" till *kluster användar* rollen. Den här rollen har åtkomst till instrument panelen och alla meny alternativ. En kluster användare har färre tillåtna alternativ än en administratör. Hiveuser2 kan till exempel Visa konfigurationer för var och en av tjänsterna, men kan inte redigera dem.

![Apache Ambari-instrumentpanel visas](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache Hive principer i HDInsight med ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Hantera ESP HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md)
* [Använd vyn Apache Hive med Apache Hadoop i HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synkronisera Azure AD-användare till klustret](hdinsight-sync-aad-users-to-cluster.md)
* [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)