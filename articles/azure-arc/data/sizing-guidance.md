---
title: Vägledning för dimensionering
description: Planera för storleken på en distribution av Azure Arc-aktiverade data tjänster.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e7f2e445c3e4e8df7420c0587e156968f3a2c92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542685"
---
# <a name="sizing-guidance"></a>Vägledning för storleks kontroll

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Översikt över storleks vägledning

När du planerar distributionen av Azure Arc-datatjänster bör du planera för rätt mängd data bearbetning, minne och lagrings utrymme som krävs för att köra Azure Arc-datakontrollanten och för antalet SQL-hanterade instanser och PostgreSQL-Server grupper som du ska distribuera.  Eftersom Azure Arc-aktiverade data tjänster har distribuerats på Kubernetes har du flexibiliteten att lägga till ytterligare kapacitet till ditt Kubernetes-kluster med tiden genom att lägga till ytterligare Compute-noder eller lagring.  Den här guiden ger vägledning om minimi krav och ger vägledning om rekommenderade storlekar för några vanliga krav.

## <a name="general-sizing-requirements"></a>Allmänna storleks krav

> [!NOTE]
> Om du inte är bekant med begreppen i den här artikeln kan du läsa mer om [Kubernetes Resource styrning](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) och [Kubernetes size notation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Kärnornas nummer måste vara ett heltal som är större än eller lika med ett.

När du använder azdata för distribution ska du ange minnes värden i en potens av två tal – d.v.s. använda suffixen: KI, mi eller GI.

Limit-värden måste alltid vara större än värdet för begäran, om det har angetts.

Gräns värden för kärnor är det fakturerbara måttet på SQL-hanterad instans och PostgreSQL för storskaliga Server grupper.

## <a name="minimum-deployment-requirements"></a>Minimi krav för distribution

En minsta storlek för Azure Arc-aktiverade data tjänster kan anses vara Azure Arc-datakontrollanten plus en SQL-hanterad instans plus en PostgreSQL storskalig Server grupp med två arbetsnoder.  För den här konfigurationen behöver du minst 16 GB RAM-minne och 4 kärnor med _tillgänglig_ kapacitet på ditt Kubernetes-kluster.  Du bör se till att du har en minsta Kubernetes för 8 GB RAM-minne och 4 kärnor och en total total kapacitet på 16 GB RAM-minne tillgängligt för alla dina Kubernetes-noder.  Du kan till exempel ha 1 nod med 32 GB RAM-minne och 4 kärnor, eller så kan du ha 2 noder med 16 GB RAM-minne och 4 kärnor vardera.

Se artikeln [lagrings konfiguration](storage-configuration.md) för information om lagrings storlek.

## <a name="data-controller-sizing-details"></a>Storleks information om data kontrollant

Datakontrollanten är en samling poddar som distribueras till ditt Kubernetes-kluster för att tillhandahålla ett API, styrenhets tjänsten, start program och övervaknings databaser och instrument paneler.  I den här tabellen beskrivs standardvärdena för minnes-och CPU-begäranden och-gränser.

|Pod namn|PROCESSOR förfrågan|Minnes förfrågan|PROCESSOR gräns|Minnes gräns|Kommentarer|
|---|---|---|---|---|---|
|**Start program**|100 miljoner|100Mi|200m|200Mi||
|**reglering**|400m|2Gi|1800|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10 miljoner|100Mi|100 miljoner|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100 miljoner|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100 miljoner|200Mi|200m|300Mi|Metricsdc är en daemonset som skapas på varje Kubernetes-nod i klustret.  Siffrorna i tabellen här är _per nod_. Om du anger allowNodeMetricsCollection = false i din distributions profil fil innan du skapar datakontrollanten skapas inte metricsdc-daemonset.|
|**metricsui**|20 miljoner|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

Du kan åsidosätta standardinställningarna för controldb och kontrollen poddar i din distributions profil fil eller datacontroller YAML-fil.  Exempel:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Se artikeln [lagrings konfiguration](storage-configuration.md) för information om lagrings storlek.

## <a name="sql-managed-instance-sizing-details"></a>Storleks information för SQL-hanterad instans

Varje SQL-hanterad instans måste ha följande minsta resurs begär Anden:
- Minne: 2Gi
- Kärnor: 1

Varje SQL-hanterad instans Pod som skapas har tre behållare:

|Containerns namn|PROCESSOR förfrågan|Minnes förfrågan|PROCESSOR gräns|Minnes gräns|Kommentarer|
|---|---|---|---|---|---|
|fluentbit|100 miljoner|100Mi|Inte angivet|Inte angivet|Fluentbit behållar resurs begär Anden är _utöver_ de begär Anden som anges för SQL-hanterad instans.||
|båge – sqlmi|Användaren har angetts eller inte angetts.|Användaren har angetts eller inte angetts.|Användaren har angetts eller inte angetts.|Användaren har angetts eller inte angetts.||
|insamlade|Inte angivet|Inte angivet|Inte angivet|Inte angivet||

Standard volym storleken för alla beständiga volymer är 5Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>Storleks information för PostgreSQL-ProScale Server-grupp

Varje PostgreSQL för storskalig Server grupp måste ha följande minsta resurs begär Anden:
- Minne: 256Mi
- Kärnor: 1

Varje PostgreSQL-Server grupp koordinator eller arbets Pod som skapas har tre behållare:

|Containerns namn|PROCESSOR förfrågan|Minnes förfrågan|PROCESSOR gräns|Minnes gräns|Kommentarer|
|---|---|---|---|---|---|
|fluentbit|100 miljoner|100Mi|Inte angivet|Inte angivet|Fluentbit behållar resurs begär Anden gäller _utöver_ de begär Anden som anges för noderna i postgresql-server gruppen.|
|postgres|Användaren har angetts eller inte angetts.|Användardefinierad eller 256Mi (standard).|Användaren har angetts eller inte angetts.|Användaren har angetts eller inte angetts.||
|teleympkvistar|Inte angivet|Inte angivet|Inte angivet|Inte angivet||

## <a name="cumulative-sizing"></a>Ackumulerad storlek

Den övergripande storleken på en miljö som krävs för Azure Arc-aktiverade data tjänster är i första hand en funktion av antalet och storleken på databas instanserna som ska skapas.  Den totala storleken kan vara svår att förutsäga vid tiden att veta att antalet instanser kommer att växa och minska och hur mycket resurser som krävs för varje databas instans som ska ändras.

Bas linje storleken för en specifik Azure Arc-aktiverad data tjänst miljö är storleken på datakontrollanten som kräver 4 kärnor och 16 GB RAM-minne.  Där kan du lägga till högst upp det sammanlagda antalet kärnor och minne som krävs för databas instanserna.  För SQL-hanterad instans är antalet poddar lika med antalet SQL-hanterade instanser som skapas.  För PostgreSQL-Server grupper är antalet poddar detsamma som antalet arbetsnoder plus en för koordinator-noden.  Om du till exempel har en PostgreSQL-Server grupp med tre arbetsnoder blir det totala antalet poddar 4.

Förutom de kärnor och det minne som du begär för varje databas instans bör du lägga till 250m kärnor och 250Mi RAM-minne för agent behållarna.

Följande är ett exempel på en storleks beräkning.

Krav:

- **"SQL1"**: 1 SQL-hanterad instans med 16 GB RAM, 4 kärnor
- **"SQL2"**: 1 SQL-hanterad instans med 256 GB RAM-minne, 16 kärnor
- **"Postgres1"**: 1 postgresql storskalig Server grupp med 4 arbetare vid 12 GB RAM, 4 kärnor

Storleks beräkningar:

- Storleken på "SQL1" är: 1 Pod * ([16 GI RAM, 4 kärnor] + [250Mi RAM, 250m kärnor]) för agenterna per POD = 16,25 GI RAM, 4,25 kärnor.
- Storleken på "SQL2" är: 1 Pod * ([256 GI RAM, 16 kärnor] + + [250Mi RAM, 250m kärnor]) för agenterna per POD = 256,25 GI RAM, 16,25 kärnor.
- Den totala storleken på SQL 1 och SQL 2 är: (16,25 GB + 256,25 GI) = 272,5 GB RAM, (4,25 kärnor + 16,25 kärnor) = 20,5 kärnor.

- Storleken på "Postgres1" är: (4 Worker poddar + 1 koordinator Pod) * ([12 GB RAM, 4 kärnor] + [250Mi RAM, 250m kärnor]) för agenterna per POD = 61,25 GB RAM, 21,25 kärnor.

- Den totala kapaciteten som krävs för databas instanserna är: 272,5 GB RAM, 20,5 kärnor för SQL + 61,25 GB RAM, 21,25 kärnor för PostgreSQL Scale = 333,75 GB RAM, 42,5 kärnor.

- Den totala kapaciteten som krävs för databas instanserna plus data styrenheten är: 333,75 GB RAM, 42,5 kärnor för databas instanserna + 16 GB RAM, 4 kärnor för data styrenheten = 349,75 GB RAM, 46,5 kärnor.

Se artikeln [lagrings konfiguration](storage-configuration.md) för information om lagrings storlek.

## <a name="other-considerations"></a>Ytterligare överväganden

Tänk på att en specifik databas instans storleks förfrågan för kärnor eller RAM-minne inte kan överskrida den tillgängliga kapaciteten för Kubernetes-noderna i klustret.  Om den största Kubernetes-noden som du har i ditt Kubernetes-kluster till exempel är 256 GB RAM-minne och 24 kärnor, kan du inte skapa en databas instans med en begäran på 512 GB RAM-minne och 48-kärnor.  

Det är en bra idé att underhålla minst 25% av tillgänglig kapacitet över Kubernetes-noderna så att Kubernetes effektivt kan schemalägga poddar som ska skapas och för att möjliggöra elastisk skalning och längre långsiktiga tillväxt på begäran.  

I dina storleks beräkningar glömmer du inte att lägga till i resurs kraven för Kubernetes system poddar och andra arbets belastningar som kan dela kapaciteten med Azure Arc-aktiverade data tjänster i samma Kubernetes-kluster.

För att upprätthålla hög tillgänglighet under planerat underhåll och haveri kontinuitet bör du planera för att minst en av Kubernetes-noderna i klustret ska vara otillgänglig vid en viss tidpunkt.  Kubernetes kommer att försöka schemalägga om poddar som kördes på en specifik nod som togs ned för underhåll eller på grund av ett haveri.  Om det inte finns någon tillgänglig kapacitet på de återstående noderna, kommer poddar inte att omplaneras för skapande förrän det finns tillgänglig kapacitet igen.  Var extra försiktig med stora databas instanser.  Om det till exempel bara finns en Kubernetes-nod som är tillräckligt stor för att uppfylla resurs kraven för en stor databas instans och noden Miss lyckas, kommer Kubernetes inte att kunna schemalägga att databas instansen Pod till en annan Kubernetes-nod.

Behåll de [maximala gränserna för en Kubernetes kluster storlek](https://kubernetes.io/docs/setup/best-practices/cluster-large/) i åtanke.

Kubernetes-administratören kan ha konfigurerat [resurs kvoter](https://kubernetes.io/docs/concepts/policy/resource-quotas/) på ditt namnrymd/projekt.  Tänk på detta när du planerar databas instansens storlekar.
