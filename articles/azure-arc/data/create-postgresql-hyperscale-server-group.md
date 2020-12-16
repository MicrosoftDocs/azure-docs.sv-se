---
title: Skapa en Azure Database for PostgreSQL storskalig Server grupp på Azure Arc
description: Skapa en Azure Database for PostgreSQL storskalig Server grupp på Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d432f29e91097491fc4719ec59a11cb96948f431
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609062"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Skapa en Azure-Arc-aktiverad PostgreSQL Hyperskala-servergrupp

Det här dokumentet beskriver stegen för att skapa en PostgreSQL-Server grupp på Azure-bågen.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Komma igång
Om du redan är bekant med avsnitten nedan kan du hoppa över det här stycket.
Det finns viktiga ämnen som du kanske vill läsa innan du går vidare med att skapa:
- [Översikt över Azure Arc-aktiverade data tjänster](overview.md)
- [Anslutningslägen och krav](connectivity.md)
- [Lagrings konfiguration och Kubernetes lagrings koncept](storage-configuration.md)
- [Resurs modell för Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Om du föredrar att testa saker utan att skapa en fullständig miljö själv kan du snabbt komma igång med [Azure Arc-rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på Azure Kubernetes service (AKS), AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator.


## <a name="login-to-the-azure-arc-data-controller"></a>Logga in på data styrenheten för Azure-bågen

Innan du kan skapa en instans måste du först logga in på data styrenheten för Azure-bågen. Om du redan är inloggad på data styrenheten kan du hoppa över det här steget.

```console
azdata login
```

Du uppmanas sedan att ange användar namn, lösen ord och system namn område.  

> Om du använde skriptet för att skapa data styrenheten ska ditt namn område vara **båge**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Preliminärt och tillfälligt steg för OpenShift-användare

Implementera det här steget innan du går vidare till nästa steg. Om du vill distribuera PostgreSQL-storskalig Server grupp till Red Hat OpenShift i ett annat projekt än standardvärdet, måste du köra följande kommandon mot klustret för att uppdatera säkerhets begränsningarna. Det här kommandot ger de behörigheter som krävs för de tjänst konton som ska köra din PostgreSQL-Server grupp för storskalig skalning. SCC- **_SCC_** (Security context constraint) är den som du lade till när du distribuerade data styrenheten för Azure-bågen.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-name** är namnet på den server grupp som du skapar under nästa steg._
   
Mer information om SCCs i OpenShift finns i [OpenShift-dokumentationen](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Nu kan du implementera nästa steg.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Skapa en Azure Database for PostgreSQL Server grupp för storskalig skalning

Om du vill skapa en Azure Database for PostgreSQL storskalig Server grupp på Azure-bågen, använder du följande kommando:

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **Det finns andra tillgängliga kommando rads parametrar.  Se den fullständiga listan med alternativ genom att köra `azdata arc postgres server create --help` .**
> - Lagrings klassen som används för säkerhets kopieringar (_--Storage-Class-backup-SCB_) är standardvärdet för datakontrollantens data lagrings klass om den inte anges.
> - Enheten som godkändes av parametrarna--Volume-* är en Kubernetes Resource Quantity (ett heltal följt av något av dessa SI-värde (T. ex. G, M, m) eller deras effekt av två motsvarigheter (TI, GI, mi, KI)).
> - Namn måste bestå av högst 12 tecken och överensstämmer med DNS-namn konventioner.
> - Du uppmanas att ange lösen ordet för den administrativa _postgres_ -standard användaren.  Du kan hoppa över den interaktiva prompten genom att ställa in `AZDATA_PASSWORD` sessionens miljö variabel innan du kör kommandot CREATE.
> - Om du har distribuerat data styrenheten med AZDATA_USERNAME och AZDATA_PASSWORD sessionens miljövariabler i samma terminalserversession, används värdena för AZDATA_PASSWORD för att distribuera PostgreSQL-Server gruppen. Om du föredrar att använda ett annat lösen ord, antingen (1) uppdatera värdet för AZDATA_PASSWORD eller (2) ta bort AZDATA_PASSWORD-miljövariabeln eller ta bort dess värde uppmanas att ange ett lösen ord interaktivt när du skapar en Server grupp.
> - Namnet på standard administratörs användaren för PostgreSQL-storskalig databas motor är _postgres_ och kan inte ändras nu.
> - Att skapa en PostgreSQL-Server grupp för den storskaliga servern registrerar inte omedelbart resurser i Azure. Som en del av processen för att överföra [resurs lager](upload-metrics-and-logs-to-azure-monitor.md)  -eller [användnings data](view-billing-data-in-azure.md) till Azure skapas resurserna i Azure och du kan se dina resurser i Azure Portal.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Visa en lista över Azure Database for PostgreSQL Server grupper som skapats i din ARC-konfiguration

Använd följande kommando om du vill visa PostgreSQL-ProScale Server-grupper i Azure-bågen:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Hämta slut punkterna för att ansluta till dina Azure Database for PostgreSQL Server grupper

Kör följande kommando för att Visa slut punkterna för en PostgreSQL-instans:

```console
azdata arc postgres endpoint list -n <server group name>
```
Exempel:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Du kan använda PostgreSQL-instansens slut punkt för att ansluta till PostgreSQL-Server gruppen från ditt favorit verktyg:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin osv.

Följ instruktionerna nedan om du använder en virtuell Azure-dator för att testa:

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Särskilda anmärkningar om distributioner av virtuella Azure-datorer

När du använder en virtuell Azure-dator visas inte den _offentliga_ IP-adressen i slut PUNKTens IP-adress. Använd följande kommando för att hitta den offentliga IP-adressen:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Du kan sedan kombinera den offentliga IP-adressen med porten för att upprätta anslutningen.

Du kan också behöva exponera porten för PostgreSQL-Server gruppen via Network Security Gateway (NSG). Om du vill tillåta trafik via (NSG) måste du lägga till en regel som du kan göra med följande kommando:

Om du vill ange en regel måste du känna till namnet på din NSG. Du fastställer NSG med kommandot nedan:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

När du har namnet på NSG kan du lägga till en brand Väggs regel med hjälp av följande kommando. Exempel värden skapar en NSG-regel för port 30655 och tillåter anslutning från **alla** käll-IP-adresser.  Detta är inte en säkerhets metod.  Du kan låsa saker bättre genom att ange värdet a-source-Address-prefix som är speciellt för din klients IP-adress eller ett IP-adressintervall som täcker ditt grupps eller organisations IP-adresser.

Ersätt värdet för parametern--destination-port-Ranges nedan med det port nummer som du fick från kommandot "azdata Arc postgres Server List" ovan.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Ansluta med Azure Data Studio

Öppna Azure Data Studio och Anslut till din instans med den externa slut punktens IP-adress och port nummer ovan, och lösen ordet du angav när du skapade instansen.  Om PostgreSQL inte är tillgängligt i list rutan *Anslutnings typ* kan du installera postgresql-tillägget genom att söka efter postgresql på fliken tillägg.

> [!NOTE]
> Du måste klicka på knappen [Avancerat] på anslutnings panelen för att ange port numret.

Kom ihåg att om du använder en virtuell Azure-dator behöver du den _offentliga_ IP-adress som kan nås via följande kommando:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Anslut med psql

För att få åtkomst till din PostgreSQL-Server grupp, överför den externa slut punkten för den PostgreSQL-Server grupp som du hämtade från ovan:

Nu kan du ansluta antingen psql:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Nästa steg

- Läs begrepp och instruktions guider för Azure Database for PostgreSQL storskalig för att distribuera dina data över flera PostgreSQL storskaliga noder och för att dra nytta av all kraften i Azure Database for PostgreSQL storskalig. :
    * [Noder och tabeller](../../postgresql/concepts-hyperscale-nodes.md)
    * [Fastställa programtyp](../../postgresql/concepts-hyperscale-app-type.md)
    * [Välja en distributionskolumn](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellsamlokalisering](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuera och ändra tabeller](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Utforma en databas för flera innehavare](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Utforma en instrument panel för real tids analys](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* I dokumenten ovan hoppar du över avsnitten **Logga** in på Azure Portal, & **skapa en Azure Database for PostgreSQL-till-skala (citus)**. Implementera de återstående stegen i din Azure Arc-distribution. Dessa avsnitt är speciella för Azure Database for PostgreSQL disscale (citus) som erbjuds som en PaaS-tjänst i Azure-molnet, men andra delar av dokumenten är direkt tillämpliga på den Azure Arc-aktiverade PostgreSQL-skalan.

- [Skala ut din Azure Database for PostgreSQL Hyperscale-servergrupp](scale-out-postgresql-hyperscale-server-group.md)
- [Lagrings konfiguration och Kubernetes lagrings koncept](storage-configuration.md)
- [Expandera beständiga volym anspråk](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Resurs modell för Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)