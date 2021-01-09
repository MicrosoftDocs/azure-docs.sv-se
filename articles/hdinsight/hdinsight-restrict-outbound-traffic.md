---
title: Konfigurera begränsning av utgående nätverks trafik – Azure HDInsight
description: Lär dig hur du konfigurerar begränsning av utgående nätverks trafik för Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 4c703fc1ddac4af2e3cf8716764a21da7e870b19
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048682"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurera utgående nätverks trafik för Azure HDInsight-kluster med hjälp av brand vägg

Den här artikeln innehåller anvisningar för att skydda utgående trafik från ditt HDInsight-kluster med hjälp av Azure-brandväggen. I stegen nedan förutsätter vi att du konfigurerar en Azure-brandvägg för ett befintligt kluster. Om du distribuerar ett nytt kluster bakom en brand vägg måste du först skapa ditt HDInsight-kluster och undernät. Följ sedan stegen i den här guiden.

## <a name="background"></a>Bakgrund

HDInsight-kluster distribueras vanligt vis i ett virtuellt nätverk. Klustret har beroenden för tjänster utanför det virtuella nätverket.

Inkommande hanterings trafik kan inte skickas via en brand vägg. Du kan använda NSG service-taggar för inkommande trafik som dokumenteras [här](./hdinsight-service-tags.md). 

De utgående trafik beroendena för HDInsight är nästan helt definierade med FQDN. Som inte har statiska IP-adresser bakom dem. Bristen på statiska adresser innebär att nätverks säkerhets grupper (NSG: er) inte kan låsa utgående trafik från ett kluster. IP-adresserna är ofta tillräckligt många det går inte att ställa in regler som baseras på den aktuella namn matchningen och användningen.

Säkra utgående adresser med en brand vägg som kan styra utgående trafik baserat på FQDN. Azure-brandväggen begränsar utgående trafik baserat på FQDN för mål-eller FQDN- [taggarna](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurera Azure-brandvägg med HDInsight

En sammanfattning av stegen för att låsa bort från din befintliga HDInsight med Azure Firewall är:

1. Skapa ett undernät.
1. Skapa en brand vägg.
1. Lägg till program regler i brand väggen
1. Lägg till nätverks regler i brand väggen.
1. Skapa en routningstabell.

### <a name="create-new-subnet"></a>Skapa nytt undernät

Skapa ett undernät med namnet **AzureFirewallSubnet** i det virtuella nätverk där klustret finns.

### <a name="create-a-new-firewall-for-your-cluster"></a>Skapa en ny brand vägg för klustret

Skapa en brand vägg med namnet **test-FW01** med hjälp av stegen i **distribuera brand väggen** från [självstudie: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurera brand väggen med program regler

Skapa en program regel samling som gör det möjligt för klustret att skicka och ta emot viktig kommunikation.

1. Välj den nya brand Väggs **test-FW01** från Azure Portal.

1. Gå till **Inställningar**  >  **regler**  >  **program regel samling**  >  **+ Lägg till program regel samling**.

    ![Rubrik: Lägg till program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Ange följande information på skärmen **Lägg till program regel samling** :

    **Övre delen**

    | Egenskap|  Värde|
    |---|---|
    |Namn| FwAppRule|
    |Prioritet|200|
    |Åtgärd|Tillåt|

    **Avsnittet FQDN-Taggar**

    | Namn | Käll adress | FQDN-tagg | Anteckningar |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate och HDInsight | Krävs för HDI-tjänster |

    **Avsnittet mål-FQDN**

    | Namn | Käll adresser | Protokoll:Port | Mål-FQDN | Anteckningar |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Tillåt Windows inloggnings aktivitet |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Tillåt Windows inloggnings aktivitet |
    | Rule_4 | * | https: 443, http: 80 | storage_account_name. blob. Core. Windows. net | Ersätt `storage_account_name` med det faktiska lagrings konto namnet. Om du bara vill använda HTTPS-anslutningar kontrollerar du att ["säker överföring krävs"](../storage/common/storage-require-secure-transfer.md) är aktiverat på lagrings kontot. Om du använder privat slut punkt för att komma åt lagrings konton behövs inte det här steget och lagrings trafiken vidarebefordras inte till brand väggen.|

   ![Rubrik: Ange information om program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Välj **Lägg till**.

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurera brand väggen med nätverks regler

Skapa nätverks reglerna för att konfigurera HDInsight-klustret på rätt sätt.

1. Fortsätt från föregående steg, gå till **regel samling för nätverk**  >  **+ Lägg till nätverks regel samling**.

1. Ange följande information på skärmen **Lägg till regel samling för nätverk** :

    **Övre delen**

    | Egenskap|  Värde|
    |---|---|
    |Namn| FwNetRule|
    |Prioritet|200|
    |Åtgärd|Tillåt|

    **Avsnittet service Tags**

    | Namn | Protokoll | Källadresser | Tjänsttaggar | Mål portar | Anteckningar |
    | --- | --- | --- | --- | --- | --- |
    | Rule_5 | TCP | * | SQL | 1433 | Om du använder standard-SQL-servrarna som tillhandahålls av HDInsight konfigurerar du en nätverks regel i avsnittet service Tags för SQL som gör att du kan logga och granska SQL-trafik. Om du inte har konfigurerat tjänst slut punkter för SQL Server i HDInsight-undernätet, vilket kringgår brand väggen. Om du använder anpassad SQL Server för Ambari, Oozie, ranger och Hive metastores behöver du bara tillåta trafiken till dina egna anpassade SQL-servrar.|
    | Rule_6 | TCP | * | Azure Monitor | * | valfritt Kunder som planerar att använda funktionen för automatisk skalning bör lägga till den här regeln. |
    
   ![Rubrik: Ange program regel samling](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Välj **Lägg till**.

### <a name="create-and-configure-a-route-table"></a>Skapa och konfigurera en routningstabell

Skapa en routningstabell med följande poster:

* Alla IP-adresser från [hälso-och hanterings tjänster](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) med nästa hopp typ av **Internet**. Den bör innehålla 4 IP-adresser för de allmänna regionerna samt två IP-adresser för din speciella region. Den här regeln behövs bara om ResourceProviderConnection är inställt på *inkommande*. Om ResourceProviderConnection är inställt på *utgående* behövs inte dessa IP-adresser i UDR. 

* En virtuell enhets väg för IP-adressen 0.0.0.0/0 med nästa hopp som din Azure Firewall-privata IP-adress.

Om du till exempel vill konfigurera routningstabellen för ett kluster som skapats i regionen USA, östra, använder du följande steg:

1. Välj din Azure Firewall **test-FW01**. Kopiera den **privata IP-adressen** som visas på sidan **Översikt** . I det här exemplet ska vi använda en **exempel adress för 10.0.2.4**.

1. Navigera sedan till **alla tjänster**  >  **nätverks**  >  **flödes tabeller** och **skapa routningstabell**.

1. Från din nya väg går du till **Inställningar**  >  **vägar**  >  **+ Lägg till**. Lägg till följande vägar:

| Vägnamn | Adressprefix | Nästa hopptyp | Nexthop-adress |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Virtuell installation | 10.0.2.4 |

Slutför konfigureringen av routningstabellen:

1. Tilldela routningstabellen som du skapade i ditt HDInsight-undernät genom att välja **undernät** under **Inställningar**.

1. Välj **+ associera**.

1. På skärmen **associera undernät** väljer du det virtuella nätverk som klustret skapades i. Och **under nätet** som du använde för ditt HDInsight-kluster.

1. Välj **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-Node eller anpassad program trafik

Ovanstående steg gör att klustret kan köras utan problem. Du måste fortfarande konfigurera beroenden för att anpassa dina anpassade program som körs på Edge-noderna, om tillämpligt.

Program beroenden måste identifieras och läggas till i Azure-brandväggen eller i routningstabellen.

Vägar måste skapas för att program trafiken ska undvika problem med asymmetrisk routning.

Om dina program har andra beroenden måste de läggas till i din Azure-brandvägg. Skapa program regler för att tillåta HTTP/HTTPS-trafik och nätverks regler för allt annat.

## <a name="logging-and-scale"></a>Loggning och skalning

Azure-brandväggen kan skicka loggar till några olika lagrings system. Anvisningar om hur du konfigurerar loggning för brand väggen finns i [Självstudier: övervaka Azure Firewall-loggar och-mått](../firewall/firewall-diagnostics.md).

När du har slutfört loggnings konfigurationen, om du använder Log Analytics, kan du Visa blockerad trafik med en fråga som:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Att integrera Azure-brandväggen med Azure Monitor loggar är användbart när du först får ett program att fungera. Särskilt när du inte känner till alla program beroenden. Du kan lära dig mer om Azure Monitor loggar från [analysera loggdata i Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Mer information om skalnings gränserna för Azure-brandväggen och begär Anden ökar finns i [det här](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) dokumentet eller [vanliga frågor och svar](../firewall/firewall-faq.yml).

## <a name="access-to-the-cluster"></a>Åtkomst till klustret

När brand väggen har kon figurer ATS kan du använda den interna slut punkten ( `https://CLUSTERNAME-int.azurehdinsight.net` ) för att komma åt Ambari inifrån det virtuella nätverket.

Om du vill använda den offentliga slut punkten ( `https://CLUSTERNAME.azurehdinsight.net` ) eller SSH-slutpunkten ( `CLUSTERNAME-ssh.azurehdinsight.net` ) kontrollerar du att du har rätt vägar i ROUTNINGSTABELLEN och NSG-regler för att undvika problemet med asymmetrisk routning som beskrivs [här](../firewall/integrate-lb.md). I detta fall måste du tillåta klientens IP-adress i reglerna för inkommande NSG och även lägga till den i den användardefinierade routningstabellen med nästa hopp uppsättning som `internet` . Om routningen inte är korrekt konfigurerad visas ett tids gräns fel.

## <a name="next-steps"></a>Nästa steg

* [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)
* [Konfigurera virtuell nätverksinstallation](./network-virtual-appliance.md)
