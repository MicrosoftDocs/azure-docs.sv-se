---
title: Vanliga frågor och svar om Azure HDInsight
description: Vanliga frågor och svar om HDInsight
keywords: Vanliga frågor och svar om vanliga frågor och svar
author: Ramakoni1
ms.author: ramakoni
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 13282962886d8682b6056d10f8f0cc5a7f626d60
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946016"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Vanliga frågor och svar

Den här artikeln innehåller svar på några av de vanligaste frågorna om hur du kör [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Skapa eller ta bort HDInsight-kluster

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Hur gör jag för att etablera ett HDInsight-kluster?

Om du vill granska HDInsight-kluster typerna och etablerings metoderna, se [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](./hdinsight-hadoop-provision-linux-clusters.md).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hur gör jag för att ta bort ett befintligt HDInsight-kluster?

Om du vill veta mer om hur du tar bort ett kluster när det inte längre används, se [ta bort ett HDInsight-kluster](hdinsight-delete-cluster.md).

Försök att lämna minst 30 till 60 minuter mellan åtgärderna skapa och ta bort. Annars kan åtgärden Miss lyckas med följande fel meddelande:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hur gör jag för att välja rätt antal kärnor eller noder för min arbets belastning?

Lämpligt antal kärnor och andra konfigurations alternativ beror på olika faktorer.

Mer information finns i [kapacitets planering för HDInsight-kluster](./hdinsight-capacity-planning.md).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Vilka är de olika typerna av noder i ett HDInsight-kluster?

Se [resurs typer i Azure HDInsight-kluster](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>Vilka är de bästa metoderna för att skapa stora HDInsight-kluster?

1. Rekommendera att konfigurera HDInsight-kluster med en [anpassad Ambari-databas](./hdinsight-custom-ambari-db.md) för att förbättra klustrets skalbarhet.
2. Använd [Azure Data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) för att skapa HDInsight-kluster för att dra nytta av högre bandbredd och andra prestanda egenskaper för Azure Data Lake Storage Gen2.
3. Huvudnoderna bör vara tillräckligt stor för att rymma flera huvud tjänster som körs på dessa noder.
4. Vissa arbets belastningar, till exempel interaktiva frågor, behöver också större Zookeeper-noder. Kontrol lera att det finns minst 8 kärn virtuella datorer.
5. Vid Hive och Spark använder du [externa Hive-metaarkiv](./hdinsight-use-external-metadata-stores.md).

## <a name="individual-components"></a>Enskilda komponenter

### <a name="can-i-install-additional-components-on-my-cluster"></a>Kan jag installera ytterligare komponenter i mitt kluster?

Ja. Om du vill installera ytterligare komponenter eller anpassa kluster konfigurationen använder du:

- Skript under eller efter skapandet. Skript anropas via [skript åtgärd](./hdinsight-hadoop-customize-cluster-linux.md). Skript åtgärd är ett konfigurations alternativ som du kan använda från Azure Portal, HDInsight Windows PowerShell-cmdletar eller HDInsight .NET SDK. Det här konfigurations alternativet kan användas från Azure Portal, HDInsight Windows PowerShell-cmdletar eller HDInsight .NET SDK.

- [HDInsight-programplattform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) för att installera program.

En lista över komponenter som stöds finns i [vilka Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](./hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Kan jag uppgradera de enskilda komponenterna som är förinstallerade i klustret?

Om du uppgraderar inbyggda komponenter eller program som är förinstallerade i klustret kommer den resulterande konfigurationen inte att stödjas av Microsoft. Dessa system konfigurationer har inte testats av Microsoft. Försök att använda en annan version av HDInsight-klustret som redan har den uppgraderade versionen av komponenten som är installerad.

Det finns till exempel inte stöd för att uppgradera Hive som en enskild komponent. HDInsight är en hanterad tjänst och många tjänster är integrerade med Ambari-servern och testas. Att uppgradera en Hive på egen hand leder till att indexerade binärfiler för andra komponenter ändras och kan orsaka komponent integrerings problem i klustret.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Kan Spark och Kafka köras i samma HDInsight-kluster?

Nej, det går inte att köra Apache Kafka och Apache Spark i samma HDInsight-kluster. Skapa separata kluster för Kafka och Spark för att undvika problem med resurs konflikter.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hur gör jag för att ändra tidszon i Ambari?

1. Öppna Ambari-webbgränssnittet på `https://CLUSTERNAME.azurehdinsight.net` , där kluster namn är namnet på klustret.
2. I det övre högra hörnet väljer du admin | Autentiseringsinställningar. 

   ![Ambari-inställningar](media/hdinsight-faq/ambari-settings.png)

3. I fönstret användar inställningar väljer du den nya tids zonen från List rutan tidszon och klickar sedan på Spara.

   ![Ambari användar inställningar](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metaarkiv

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>Hur kan jag migrera från den befintliga metaarkiv till Azure SQL Database? 

Information om hur du migrerar från SQL Server till Azure SQL Database finns i [Självstudier: migrera SQL Server till en enskild databas eller en databas i poolen Azure SQL Database offline med DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Tas Hive-metaarkiv bort när klustret tas bort?

Det beror på vilken typ av metaarkiv som klustret är konfigurerat för att använda.

För en standard-metaarkiv: standard-metaarkiv är en del av kluster livs cykeln. När du tar bort ett kluster raderas även motsvarande metaarkiv och metadata.

För en anpassad metaarkiv: livs cykeln för metaarkiv är inte knuten till ett klusters livs cykel. Så du kan skapa och ta bort kluster utan att förlora metadata. Metadata, till exempel Hive-scheman, behålls även efter att du tagit bort och återskapat HDInsight-klustret.

Mer information finns i [använda externa metadata butiker i Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migrerar en Hive-metaarkiv även standard principerna för Ranger-databasen?

Nej, princip definitionen finns i Ranger-databasen, så migreringen av Ranger-databasen migrerar sin princip.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Kan du migrera en Hive-metaarkiv från ett Enterprise Security Package-kluster (ESP) till ett icke-ESP-kluster och det andra sättet?

Ja, du kan migrera en Hive-metaarkiv från en ESP till ett icke-ESP-kluster.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hur kan jag uppskatta storleken på en Hive-metaarkiv databas?

En Hive-metaarkiv används för att lagra metadata för data källor som används av Hive-servern. Storleks kraven beror delvis på antalet och komplexiteten för dina Hive-datakällor. Dessa objekt kan inte uppskattas överst. Som beskrivs i [Hive-metaarkiv rikt linjer](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)kan du börja med en S2-nivå. Nivån ger 50 DTU och 250 GB lagring, och om du ser en Flask hals skalar du upp databasen.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Stöder du andra databaser än Azure SQL Database som externa metaarkiv?

Nej, Microsoft stöder bara Azure SQL Database som en extern anpassad metaarkiv.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Kan jag dela en metaarkiv över flera kluster?

Ja, du kan dela anpassade metaarkiv över flera kluster så länge de använder samma version av HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Anslutningar och virtuella nätverk  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Vad är konsekvenserna av att blockera portarna 22 och 23 i mitt nätverk?

Om du blockerar portarna 22 och port 23 kommer du inte att ha SSH-åtkomst till klustret. Dessa portar används inte av HDInsight-tjänsten.

Mer information finns i följande dokument:

- [Portar som används av Apache Hadoop Services i HDInsight](./hdinsight-hadoop-port-settings-for-services.md)

- [Skydda inkommande trafik till HDInsight-kluster i ett virtuellt nätverk med privat slut punkt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [IP-adresser för HDInsight-hantering](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Kan jag distribuera en ytterligare virtuell dator i samma undernät som ett HDInsight-kluster?

Ja, du kan distribuera en ytterligare virtuell dator i samma undernät som ett HDInsight-kluster. Följande konfigurationer är möjliga:

- Edge-noder: du kan lägga till en annan Edge-nod i klustret, enligt beskrivningen i [använda tomma Edge-noder i Apache Hadoop kluster i HDInsight](hdinsight-apps-use-edge-node.md).

- Fristående noder: du kan lägga till en fristående virtuell dator i samma undernät och komma åt klustret från den virtuella datorn med hjälp av den privata slut punkten `https://<CLUSTERNAME>-int.azurehdinsight.net` . Mer information finns i [kontrol lera nätverks trafik](./control-network-traffic.md).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Ska jag lagra data på den lokala hård disken på en Edge-nod?

Nej, det är inte en bra idé att lagra data på en lokal disk. Om noden Miss lyckas kommer alla data som lagras lokalt att gå förlorade. Vi rekommenderar att du lagrar data i Azure Data Lake Storage Gen2 eller Azure Blob Storage, eller genom att montera en Azure Files resurs för lagring av data.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Kan jag lägga till ett befintligt HDInsight-kluster i ett annat virtuellt nätverk?

Nej, du kan inte. Det virtuella nätverket ska anges vid tidpunkten för etableringen. Om inget virtuellt nätverk anges under etableringen skapar distributionen ett internt nätverk som inte är tillgängligt utanför. Mer information finns i [lägga till HDInsight i ett befintligt virtuellt nätverk](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Säkerhet och certifikat

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Vilka är rekommendationerna för skydd mot skadlig kod på Azure HDInsight-kluster?

Information om skydd mot skadlig kod finns i [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hur gör jag för att skapa en keytab för ett HDInsight ESP-kluster?

Skapa en Kerberos-keytab för ditt domän användar namn. Du kan senare använda den här keytab för att autentisera till fjärranslutna domänanslutna kluster utan att ange ett lösen ord. Domän namnet är versaler:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Kan jag använda en befintlig Azure Active Directory-klient för att skapa ett HDInsight-kluster med ESP?

Aktivera Azure Active Directory Domain Services (Azure AD DS) innan du kan skapa ett HDInsight-kluster med ESP. Hadoop med öppen källkod förlitar sig på Kerberos för autentisering (till skillnad från OAuth).

Om du vill ansluta virtuella datorer till en domän måste du ha en domänkontrollant. Azure AD DS är den hanterade domänkontrollanten och betraktas som en utökning av Azure Active Directory. Azure AD DS innehåller alla Kerberos-krav för att bygga ett säkert Hadoop-kluster på ett hanterat sätt. HDInsight som en hanterad tjänst integreras med Azure AD DS för att ge säkerhet.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Kan jag använda ett självsignerat certifikat i en AAD-DS säker LDAP-installation och etablera ett ESP-kluster?

Vi rekommenderar att du använder ett certifikat som utfärdats av en certifikat utfärdare. Men det finns också stöd för att använda ett självsignerat certifikat på ESP. Mer information finns i:

- [Aktivera Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Självstudie: Konfigurera säker LDAP för en Azure Active Directory Domain Services hanterad domän](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hur kan jag hämta inloggnings aktivitet som visas i Ranger?

För gransknings krav rekommenderar Microsoft att du aktiverar Azure Monitor loggar enligt beskrivningen i [använda Azure Monitor loggar för att övervaka HDInsight-kluster](./hdinsight-hadoop-oms-log-analytics-tutorial.md).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Kan jag inaktivera `Clamscan` i mitt kluster?

`Clamscan` är antivirus programmet som körs i HDInsight-klustret och används av Azure Security (azsecd) för att skydda dina kluster från virus angrepp. Microsoft rekommenderar starkt att användarna inte gör några ändringar i standard `Clamscan` konfigurationen.

Den här processen stör inte eller tar några cykler bort från andra processer. Den kommer alltid att ge till gång till andra processer. PROCESSOR toppar från `Clamscan` bör bara visas när systemet är inaktivt.  

I scenarier där du måste kontrol lera schemat kan du använda följande steg:

1. Inaktivera automatisk körning med följande kommando:
   
  sudo `usr/local/bin/azsecd config -s clamav -d Disabled` sudo service azsecd restart 
   
1. Lägg till ett cron-jobb som kör följande kommando som rot:
   
   `/usr/local/bin/azsecd manual -s clamav`

Mer information om hur du konfigurerar och kör ett cron-jobb finns i [Hur gör jag för att konfigurera ett cron-jobb](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Varför är LLAP tillgängligt i Spark ESP-kluster?
LLAP har Aktiver ATS av säkerhets skäl (Apache Ranger), inte prestanda. Använd större virtuella noder för virtuella datorer för resursanvändning för LLAP (till exempel minsta D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Hur kan jag lägga till ytterligare AAD-grupper efter att ha skapat ett ESP-kluster?
Det finns två sätt att uppnå det här målet: 1 – du kan återskapa klustret och lägga till ytterligare grupper när klustret skapas. Om du använder omfångs synkronisering i AAD-DS ser du till att grupp B ingår i den omfångs synkroniseringen.
2 – Lägg till gruppen som en kapslad under grupp för den tidigare gruppen som användes för att skapa ESP-klustret. Om du till exempel har skapat ett ESP-kluster med en grupp `A` kan du senare använda Lägg till grupp `B` som en kapslad under grupp av `A` och efter ungefär en timme kommer det att synkroniseras och vara tillgängligt i klustret automatiskt. 

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Kan jag lägga till en Azure Data Lake Storage Gen2 till ett befintligt HDInsight-kluster som ett ytterligare lagrings konto?

Nej, det är för närvarande inte möjligt att lägga till ett Azure Data Lake Storage Gen2 lagrings konto till ett kluster som har blob-lagring som primär lagring. Mer information finns i [jämföra lagrings alternativ](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hur hittar jag den för närvarande länkade tjänstens huvud namn för ett Data Lake lagrings konto?

Du kan hitta inställningarna i **data Lake Storage gen1 åtkomst** under kluster egenskaperna i Azure Portal. Mer information finns i [Verifiera kluster konfiguration](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Hur kan jag beräkna användningen av lagrings konton och blob-behållare för mina HDInsight-kluster?

Gör något av följande:

- [Använda PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Hitta storleken på */User/Hive/. Pappers korgen/* mapp i HDInsight-klustret med följande kommando rad:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hur kan jag konfigurera granskning för mitt Blob Storage-konto?

Om du vill granska Blob Storage-konton konfigurerar du övervakning med hjälp av proceduren i [övervaka ett lagrings konto i Azure Portal](../storage/common/storage-monitor-storage-account.md). En HDFS – Gransknings logg innehåller bara gransknings information för det lokala HDFS-filsystemet (hdfs://mycluster).  Den omfattar inte åtgärder som utförs på Fjärrlagring.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hur kan jag överföra filer mellan en BLOB-behållare och en HDInsight-Head-nod?

Kör ett skript som liknar följande Shell-skript på Head-noden:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Filen *filenames.txt* kommer att ha den absoluta sökvägen till filerna i BLOB-behållarna.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Finns det några Ranger-plugin-program för lagring?

För närvarande finns det inget Ranger-plugin-program för Blob Storage och Azure Data Lake Storage Gen1 eller Gen2. För ESP-kluster bör du använda Azure Data Lake Storage. Du kan minst ange detaljerade behörigheter manuellt på fil system nivå med HDFS-verktyg. När du använder Azure Data Lake Storage kommer ESP-kluster också att göra en del av åtkomst kontrollen för fil system med Azure Active Directory på kluster nivå. 

Du kan tilldela data åtkomst principer till dina användares säkerhets grupper med hjälp av Azure Storage Explorer. Mer information finns i:

- [Hur gör jag för att ställer du in behörigheter för Azure AD-användare att fråga efter data i Data Lake Storage Gen2 med hjälp av Hive eller andra tjänster?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Ange behörigheter på fil- och katalognivå med hjälp av Azure Storage Explorer med Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Kan jag öka HDFS-lagringen i ett kluster utan att öka disk storleken för arbetsnoder?

Nej. Du kan inte öka disk storleken för alla arbetsnoder. Det enda sättet att öka disk storleken är att släppa klustret och återskapa det med större virtuella datorer i arbets gruppen. Använd inte HDFS för att lagra någon av dina HDInsight-data eftersom data tas bort om du tar bort klustret. Lagra dina data i stället i Azure. Att skala upp klustret kan också lägga till ytterligare kapacitet i ditt HDInsight-kluster.

## <a name="edge-nodes"></a>Gränsnoder

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Kan jag lägga till en Edge-nod efter att klustret har skapats?

Se [använda tomma Edge-noder på Apache Hadoop kluster i HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Hur kan jag ansluta till en Edge-nod?

När du har skapat en Edge-nod kan du ansluta till den med hjälp av SSH på port 22. Du kan hitta namnet på Edge-noden från kluster portalen. Namnen slutar vanligt vis med *-Ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Varför körs inte de sparade skripten automatiskt på nyskapade Edge-noder?

Du använder sparade skript för att anpassa nya arbetsnoder som läggs till i klustret genom skalnings åtgärder. Bestående skript gäller inte för Edge-noder.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Vad är REST API anrop för att hämta en Tez från klustret?

Du kan använda följande REST-slutpunkter för att hämta nödvändig information i JSON-format. Använd grundläggande autentiseringsscheman för att göra begär Anden.

- `Tez Query View`: *https: \/ / \<cluster name> . azurehdinsight.net/WS/v1/Timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https: \/ / \<cluster name> . azurehdinsight.net/WS/v1/Timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hur gör jag för att hämta konfigurations information från HDI-kluster med hjälp av en Azure Active Directory användare?

Om du vill förhandla rätt autentiserings-token med din AAD-användare går du igenom gatewayen med hjälp av följande format:

* https:// `<cluster dnsname>` . azurehdinsight.NET/API/v1/Clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hur gör jag för att använda Ambari RESTful API för att övervaka garn prestanda?

Om du anropar kommandot spiral i samma virtuella nätverk eller ett peer-kopplat virtuellt nätverk är kommandot:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Om du anropar kommandot från utanför det virtuella nätverket eller från ett icke-peer-kopplat virtuellt nätverk är kommando formatet:

- För ett icke-ESP-kluster:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- För ett ESP-kluster:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Du uppmanas att ange ett lösen ord. Du måste ange ett giltigt lösen ord för användar namnet för kluster inloggning.

## <a name="billing"></a>Fakturering

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Hur mycket kostar det att distribuera ett HDInsight-kluster?

Mer information om priser och vanliga frågor och svar om fakturering finns på pris sidan för [Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) .

### <a name="when-does-hdinsight-billing-start--stop"></a>När ska HDInsight-faktureringen starta & stoppa?

Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Faktureringen beräknas proportionellt per minut.

### <a name="how-do-i-cancel-my-subscription"></a>Hur gör jag för att avbryta min prenumeration?

Information om hur du avbryter din prenumeration finns i [avbryta din Azure-prenumeration](../cost-management-billing/manage/cancel-azure-subscription.md).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Vad händer om du betalar per användning, vad händer när jag har avbrutit min prenumeration?

Information om din prenumeration när den har avbrutits finns i [Vad händer när jag har avbrutit min prenumeration?](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Varför visas Hive-versionen som 1.2.1000 i stället för 2,1 i Ambari-ANVÄNDARGRÄNSSNITTET trots att jag kör ett HDInsight 3,6-kluster?

Även om 1,2 visas i Ambari-ANVÄNDARGRÄNSSNITTET innehåller HDInsight 3,6 både Hive 1,2 och Hive 2,1.

## <a name="other-faq"></a>Andra vanliga frågor och svar

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>Vad erbjuder HDInsight för real tids data ström bearbetnings funktioner?

Information om integrations funktioner i Stream-bearbetning finns i [välja en teknik för ström bearbetning i Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Finns det något sätt att dynamiskt avsluta klustrets huvud nod när klustret är inaktivt under en viss period?

Det går inte att utföra den här åtgärden med HDInsight-kluster. Du kan använda Azure Data Factory för dessa scenarier.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Vilka efterlevnadsprinciper erbjuder HDInsight?

Information om kompatibilitet finns i [Microsoft Trust Center](https://www.microsoft.com/trust-center) och [Översikt över Microsoft Azure efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
