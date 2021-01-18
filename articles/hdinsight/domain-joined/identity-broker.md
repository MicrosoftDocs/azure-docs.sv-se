---
title: Azure HDInsight ID Broker (HIB)
description: Lär dig mer om Azure HDInsight ID Broker för att förenkla autentisering för domänanslutna Apache Hadoop-kluster.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: b8dfe9a23e5c6697323142212156006cb65d2f9b
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556536"
---
# <a name="azure-hdinsight-id-broker-hib"></a>Azure HDInsight ID Broker (HIB)

Den här artikeln beskriver hur du konfigurerar och använder Azure HDInsight ID Broker-funktionen. Du kan använda den här funktionen för att få modern OAuth-autentisering till Apache Ambari samtidigt som du har tvingande autentisering med multifaktorautentisering utan att behöva äldre hashvärden i Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Översikt

HDInsight ID-Broker fören klar avancerade konfigurations inställningar i följande scenarier:

* Din organisation använder federationen för att autentisera användare för åtkomst till moln resurser. Om du tidigare vill använda HDInsight Enterprise Security Package-kluster var du tvungen att aktivera synkronisering av lösen ord från din lokala miljö till Azure Active Directory (Azure AD). Detta krav kan vara svårt eller olämpligt för vissa organisationer.
* Organisationen vill tvinga multifaktor-autentisering för webbaserad eller HTTP-baserad åtkomst till Apache Ambari och andra kluster resurser.

HDInsight ID Broker tillhandahåller den infrastruktur för autentisering som möjliggör protokoll över gång från OAuth (modern) till Kerberos (äldre) utan att behöva synkronisera lösen ords hashar till Azure AD DS. Den här infrastrukturen består av komponenter som körs på en virtuell Windows Server-dator (VM) med noden HDInsight ID Broker aktive rad, tillsammans med klusternoder för klusternoder.

Använd följande tabell för att fastställa det bästa alternativet för autentisering baserat på organisationens behov.

|Autentiseringsalternativ |HDInsight-konfiguration | Faktorer att överväga |
|---|---|---|
| Fullständigt OAuth | Enterprise Security Package-ID-Broker för HDInsight | Säkraste alternativet. (Multifaktorautentisering stöds.) Passion hash Sync krävs *inte* . Ingen SSH/kinit/keytab-åtkomst för lokala konton, som inte har något lösen ords-hash i Azure AD DS. Moln konton kan fortfarande vara SSH/kinit/keytab. Webbaserad åtkomst till Ambari via OAuth. Kräver att äldre appar uppdateras (till exempel JDBC/ODBC) för att stödja OAuth.|
| OAuth + Basic-autentisering | Enterprise Security Package-ID-Broker för HDInsight | Webbaserad åtkomst till Ambari via OAuth. Äldre appar fortsätter att använda grundläggande autentisering. Multifaktorautentisering måste inaktive ras för grundläggande åtkomst till autentisering. Passion hash Sync krävs *inte* . Ingen SSH/kinit/keytab-åtkomst för lokala konton, som inte har något lösen ords-hash i Azure AD DS. Moln konton kan fortfarande vara SSH-/kinit. |
| Fullständigt grundläggande autentisering | Enterprise Security Package | Liknar lokala installationer. Hash-synkronisering av lösen ord till Azure AD DS krävs. Lokala konton kan SSH-kinit eller använda keytab. Multifaktorautentisering måste inaktive ras om lagrings utrymmet Azure Data Lake Storage Gen2. |

Följande diagram visar det moderna OAuth-baserade autentiseringsschemat för alla användare, inklusive federerade användare, efter att HDInsight ID Broker har Aktiver ATS:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagram som visar Authentication Flow med HDInsight ID-Broker.":::

I det här diagrammet måste klienten (dvs. en webbläsare eller app) Hämta OAuth-token först. Sedan visar den token till gatewayen i en HTTP-begäran. Om du redan har loggat in på andra Azure-tjänster, till exempel Azure Portal, kan du logga in på ditt HDInsight-kluster med enkel inloggning.

Det kan fortfarande finnas många äldre program som endast stöder grundläggande autentisering (det vill säga användar namn och lösen ord). I dessa scenarier kan du fortfarande använda HTTP Basic-autentisering för att ansluta till kluster-gatewayerna. I den här installationen måste du se till att nätverks anslutningen från Gateway-noderna till Active Directory Federation Services (AD FS) (AD FS) slut punkten för att säkerställa en direkt rad syn från Gateway-noder.

Följande diagram visar det grundläggande autentiseringsschemat för federerade användare. Först försöker gatewayen att slutföra autentiseringen med hjälp av [ROPC Flow](../../active-directory/develop/v2-oauth-ropc.md). Om det inte finns några hash-hashvärden som är synkroniserade med Azure AD, går det tillbaka till att identifiera AD FS slut punkten och slutför autentiseringen genom att komma åt AD FS-slutpunkten.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagram som visar arkitektur med grundläggande autentisering.":::


## <a name="enable-hdinsight-id-broker"></a>Aktivera HDInsight ID-Broker

Så här skapar du ett Enterprise Security Package kluster med HDInsight ID Broker aktiverat:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Följ de grundläggande stegen för att skapa Enterprise Security Package-kluster. Mer information finns i [skapa ett HDInsight-kluster med Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Välj **Aktivera HDInsight ID Broker**.

HDInsight ID Broker-funktionen lägger till en extra virtuell dator i klustret. Den här virtuella datorn är noden HDInsight ID Broker och innehåller Server komponenter som stöd för autentisering. HDInsight ID Broker-noden är domän ansluten till Azure AD DS-domänen.

![Diagram som visar alternativ för att aktivera HDInsight ID Broker.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

Om du lägger till en ny roll `idbrokernode` som heter med följande attribut i beräknings profilen för din mall, skapas klustret med noden HDInsight ID Broker aktive rad:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 2,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

Om du vill se ett komplett exempel på en ARM-mall kan du se mallen som publicerats [här](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).


## <a name="tool-integration"></a>Verktygs integrering

HDInsight-verktyg har uppdaterats till internt stöd för OAuth. Använd de här verktygen för modern OAuth-baserad åtkomst till klustren. HDInsight [IntelliJ-plugin-programmet](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) kan användas för Java-baserade program, till exempel Scala. [Spark-och Hive-verktyg för Visual Studio Code](../hdinsight-for-vscode.md) kan användas för PySpark-och Hive-jobb. Verktygen stöder både batch-och interaktiva jobb.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-åtkomst utan hash för lösen ord i Azure AD DS

|SSH-alternativ |Faktorer att överväga |
|---|---|
| Lokalt VM-konto (till exempel sshuser) | Du angav det här kontot när klustret skapades. Det finns ingen Kerberos-autentisering för det här kontot. |
| Endast moln konto (till exempel alice@contoso.onmicrosoft.com ) | Lösen ordets hash är tillgängligt i Azure AD DS. Kerberos-autentisering är möjlig via SSH Kerberos. |
| Lokalt konto (till exempel alice@contoso.com ) | SSH Kerberos-autentisering är bara möjlig om en lösen ords-hash är tillgänglig i Azure AD DS. Annars kan den här användaren inte använda SSH till klustret. |

För SSH till en domänansluten virtuell dator eller för att köra `kinit` kommandot måste du ange ett lösen ord. SSH Kerberos-autentisering kräver att hash är tillgängligt i Azure AD DS. Om du bara vill använda SSH i administrativa scenarier kan du skapa ett enda moln konto och använda det för SSH till klustret. Andra lokala användare kan fortfarande använda Ambari-eller HDInsight-verktyg eller HTTP Basic-autentisering utan att lösen ordet hash är tillgängligt i Azure AD DS.

Om din organisation inte synkroniserar lösen ords-hashar till Azure AD DS, bör du skapa en endast molnbaserad användare i Azure AD. Tilldela den sedan en kluster administratör när du skapar klustret och Använd det i administrations syfte. Du kan använda den för att få rot åtkomst till de virtuella datorerna via SSH.

Information om hur du felsöker problem med autentisering finns i [den här guiden](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Klienter som använder OAuth för att ansluta till en HDInsight-Gateway med HDInsight ID Broker

I HDInsight ID Broker-installationen kan anpassade appar och klienter som ansluter till gatewayen uppdateras för att först hämta den begärda OAuth-token. Följ stegen i [det här dokumentet](../../storage/common/storage-auth-aad-app.md) för att hämta token med följande information:

*    URI för OAuth-resurs: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*    Behörighet: (namn: cluster. ReadWrite, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

När du har skaffat OAuth-token använder du den i Authorization-huvudet för HTTP-begäran till kluster-gatewayen (till exempel https:// <clustername> -int.azurehdinsight.net). Ett exempel på en spiral-kommando till Apache livy-API: t kan se ut så här:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

För att använda Beeline och livy kan du även följa de exempel koder som anges [här](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) för att konfigurera klienten att använda OAuth och ansluta till klustret.

## <a name="faq"></a>Vanliga frågor
### <a name="what-app-is-created-by-hdinsight-in-aad"></a>Vilken app skapas av HDInsight i AAD?
För varje kluster registreras ett program från tredje part i AAD med kluster-URI som identifierar (t `https://clustername.azurehdinsight.net` . ex.).

### <a name="why-are-users-prompted-for-consent-before-using-hib-enabled-clusters"></a>Varför uppmanas användarna att ange medgivande innan de använder HIB-aktiverade kluster?
I AAD krävs medgivande för alla program från tredje part innan det kan autentisera användare eller komma åt data.

### <a name="can-the-consent-be-approved-programatically"></a>Kan medgivande godkännas program mässigt?
Microsoft Graph API gör att du kan automatisera medgivande, se [API-dokumentationen](/graph/api/resources/oauth2permissiongrant) som sekvensen för att automatisera godkännandet är:

* Registrera en app och bevilja Application. ReadWrite. alla behörigheter till appen för att få åtkomst till Microsoft Graph
* Efter att ett kluster har skapats frågar du efter kluster appen baserat på ID-URI: n
* Registrera medgivande för appen

När klustret tas bort tar HDInsight bort appen och det finns inget behov av att rensa ett medgivande.

 


## <a name="next-steps"></a>Nästa steg

* [Konfigurera ett HDInsight-kluster med Enterprise Security Package med Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synkronisera Azure Active Directory-användare med ett HDInsight-kluster](../hdinsight-sync-aad-users-to-cluster.md)
* [Övervaka klusterprestanda](../hdinsight-key-scenarios-to-monitor.md)