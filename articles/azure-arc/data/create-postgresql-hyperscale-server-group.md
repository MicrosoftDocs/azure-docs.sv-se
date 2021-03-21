---
title: Skapa en Azure-Arc-aktiverad PostgreSQL Hyperskala-servergrupp
description: Skapa en Azure-Arc-aktiverad PostgreSQL Hyperskala-servergrupp
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687948"
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

Om du föredrar att prova saker utan att skapa en fullständig miljö själv kan du snabbt komma igång med [Azure Arc-rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på Azure Kubernetes service (AKS), AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator.


## <a name="login-to-the-azure-arc-data-controller"></a>Logga in på data styrenheten för Azure-bågen

Innan du kan skapa en instans måste du först logga in på data styrenheten för Azure-bågen. Om du redan är inloggad på data styrenheten kan du hoppa över det här steget.

```console
azdata login
```

Du kommer sedan att uppmanas att ange användar namn, lösen ord och system namn område.  

> Om du använde skriptet för att skapa data styrenheten ska ditt namn område vara **båge**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Preliminärt och tillfälligt steg för OpenShift-användare
Implementera det här steget innan du går vidare till nästa steg. Om du vill distribuera PostgreSQL-storskalig Server grupp till Red Hat OpenShift i ett annat projekt än standardvärdet, måste du köra följande kommandon mot klustret för att uppdatera säkerhets begränsningarna. Det här kommandot ger de behörigheter som krävs för de tjänst konton som ska köra din PostgreSQL-Server grupp för storskalig skalning. SCC-SCC (Security context constraint) är den som du lade till när du distribuerade data styrenheten för Azure-bågen.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-Group-name är namnet på den server grupp som du skapar under nästa steg.**

Mer information om SCCs i OpenShift finns i [OpenShift-dokumentationen](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Nu kan du implementera nästa steg.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Skapa en Azure-Arc-aktiverad PostgreSQL Hyperskala-servergrupp

Om du vill skapa en Azure-båge som är aktive rad PostgreSQL-Server grupp på din ARC-datakontrollant, använder du kommandot `azdata arc postgres server create` som du ska skicka flera parametrar till.

Om du vill ha mer information om alla parametrar som du kan ange när du skapar en gång granskar du kommandots utdata:
```console
azdata arc postgres server create --help
```

Huvud parametrarna bör tänka på:
- **namnet på den server grupp** som du vill distribuera. Ange antingen `--name` eller `-n` följt av ett namn vars längd inte får innehålla fler än 11 tecken.

- **den version av postgresql-motorn** som du vill distribuera: som standard är den version 12. Om du vill distribuera version 12 kan du antingen utelämna den här parametern eller skicka en av följande parametrar: `--engine-version 12` eller `-ev 12` . Om du vill distribuera version 11 anger du `--engine-version 11` eller `-ev 11` .

- **antalet arbetsnoder** som du vill distribuera för att skala ut och eventuellt uppnå bättre prestanda. Innan du fortsätter kan du läsa [begreppen om postgres-skalning](concepts-distributed-postgres-hyperscale.md). Om du vill ange hur många arbetsnoder som ska distribueras använder du parametern `--workers` eller `-w` följt av ett heltal som är större än eller lika med 2. Om du till exempel vill distribuera en Server grupp med två arbetsnoder, anger `--workers 2` eller `-w 2` . Detta skapar tre poddar, en för koordinator-noden/-instansen och två för arbetsnoderna/instanserna (en för varje arbetare).

- **de lagrings klasser** som du vill att din server grupp ska använda. Det är viktigt att du anger lagrings klassen direkt vid den tidpunkt då du distribuerar en Server grupp eftersom detta inte kan ändras efter att du har distribuerat. Om du vill ändra lagrings klassen efter distributionen måste du extrahera data, ta bort Server gruppen, skapa en ny server grupp och importera data. Du kan ange de lagrings klasser som ska användas för data, loggar och säkerhets kopieringar. Om du inte anger lagrings klasser används som standard lagrings klasserna för data styrenheten.
    - Om du vill ange lagrings klass för data anger du parametern `--storage-class-data` eller `-scd` följt av namnet på lagrings klassen.
    - Om du vill ange lagrings klass för loggarna anger du parametern `--storage-class-logs` eller `-scl` följt av namnet på lagrings klassen.
    - för att ställa in lagrings klassen för säkerhets kopieringarna: i den här för hands versionen av Azure Arc-aktiverad PostgreSQL finns det två sätt att ange lagrings klasser beroende på vilka typer av säkerhets kopiering/återställning som du vill göra. Vi arbetar på att förenkla den här upplevelsen. Du kan antingen ange en lagrings klass eller en volym anspråks montering. En volym anspråks montering är ett par med ett befintligt beständigt volym anspråk (i samma namnrymd) och volym typ (och valfria metadata beroende på typ av volym) avgränsade med kolon. Den permanenta volymen monteras i varje Pod för PostgreSQL-Server gruppen.
        - Om du bara vill att hela databasen ska återställas anger du parametern `--storage-class-backups` eller `-scb` följt av namnet på lagrings klassen.
        - Om du planerar att göra både fullständiga återställningar och återställningar av databasen anger du parametern `--volume-claim-mounts` eller `-vcm` följt av namnet på ett volym anspråk och en volym typ.

Observera att när du kör kommandot Skapa uppmanas du att ange lösen ordet för den `postgres` administrativa standard användaren. Namnet på användaren kan inte ändras i den här förhands granskningen. Du kan hoppa över den interaktiva prompten genom att ställa in `AZDATA_PASSWORD` sessionens miljö variabel innan du kör kommandot CREATE.

### <a name="examples"></a>Exempel

**Om du vill distribuera en Server grupp med postgres version 12 med namnet postgres01 med 2 arbetsnoder som använder samma lagrings klasser som data styrenheten kör du följande kommando:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Använd följande steg för att distribuera en Server grupp med postgres version 12 med namnet postgres01 med 2 arbetsnoder som använder samma lagrings klasser som datakontrollanten för data och loggar, men dess speciella lagrings klass för att göra både fullständiga återställningar och återställningar av tidpunkter:**

 I det här exemplet förutsätts att Server gruppen finns i ett Azure Kubernetes service-kluster (AKS). I det här exemplet används azurefile-Premium som lagrings klass namn. Du kan justera exemplet nedan för att matcha din miljö. Observera att **AccessModes ReadWriteMany krävs** för den här konfigurationen.  

Börja med att skapa en YAML-fil som innehåller beskrivningen nedan av säkerhets kopieringens PVC och ge den namnet CreateBackupPVC. yml till exempel:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Skapa sedan en PVC med hjälp av definitionen som lagras i YAML-filen:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Skapa sedan Server gruppen:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Läs de [aktuella begränsningarna för säkerhets kopiering/återställning](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Om du har distribuerat datakontrollanten med hjälp av `AZDATA_USERNAME` `AZDATA_PASSWORD` variablerna och sessionens miljövariabler i samma terminalserversession, kommer värdena för att `AZDATA_PASSWORD` användas för att distribuera postgresql-server gruppen. Om du föredrar att använda ett annat lösen ord, antingen (1) uppdatera värdet för `AZDATA_PASSWORD` eller (2) ta bort `AZDATA_PASSWORD` miljövariabeln eller (3) ta bort dess värde för att uppmanas att ange ett lösen ord interaktivt när du skapar en Server grupp.
> - Att skapa en PostgreSQL-Server grupp för den storskaliga servern registrerar inte omedelbart resurser i Azure. Som en del av processen för att överföra [resurs lager](upload-metrics-and-logs-to-azure-monitor.md)  -eller [användnings data](view-billing-data-in-azure.md) till Azure skapas resurserna i Azure och du kan se dina resurser i Azure Portal.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Visar en lista över de PostgreSQL-Server grupper som distribuerats i din ARC-datakontrollant

Om du vill visa en lista över de PostgreSQL-servergrupper som har distribuerats i data styrenheten för bågen kör du följande kommando:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Hämta slut punkterna för att ansluta till Azure-bågen aktiverade PostgreSQL för storskalig Server grupper

Kör följande kommando för att Visa slut punkterna för en PostgreSQL-Server grupp:

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

Du kan använda PostgreSQL-instansens slut punkt för att ansluta till PostgreSQL-Server gruppen från ditt favorit verktyg:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin osv. När du gör det ansluter du till koordinator-noden/-instansen som tar hand om att dirigera frågan till lämpliga arbetsnoder/instanser om du har skapat distribuerade tabeller. Mer information finns i [begreppen Azure Arc-aktiverad postgresql-skalning](concepts-distributed-postgres-hyperscale.md).

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

- Läs koncepten och instruktions guiderna för Azure Database for PostgreSQL storskalig för att distribuera dina data över flera PostgreSQL storskaliga noder och för att dra nytta av bättre prestanda:
    * [Noder och tabeller](../../postgresql/concepts-hyperscale-nodes.md)
    * [Fastställa programtyp](../../postgresql/concepts-hyperscale-app-type.md)
    * [Välja en distributionskolumn](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellsamlokalisering](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuera och ändra tabeller](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Utforma en databas för flera innehavare](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Utforma en instrument panel för real tids analys](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* I dokumenten ovan hoppar du över avsnitten **Logga** in på Azure Portal, & **skapa en Azure Database for PostgreSQL-till-skala (citus)**. Implementera de återstående stegen i din Azure Arc-distribution. Dessa avsnitt är speciella för Azure Database for PostgreSQL disscale (citus) som erbjuds som en PaaS-tjänst i Azure-molnet, men andra delar av dokumenten är direkt tillämpliga på den Azure Arc-aktiverade PostgreSQL-skalan.

- [Skala ut din Azure-båge som är aktive rad för PostgreSQL-storskalig Server grupp](scale-out-postgresql-hyperscale-server-group.md)
- [Lagrings konfiguration och Kubernetes lagrings koncept](storage-configuration.md)
- [Expandera beständiga volym anspråk](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Resurs modell för Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
