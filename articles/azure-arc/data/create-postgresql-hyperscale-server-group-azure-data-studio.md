---
title: Skapa en Azure Arc-aktiverad PostgreSQL-skalning med Azure Data Studio
description: Skapa en Azure Arc-aktiverad PostgreSQL-skalning med Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310906"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Skapa en Azure Arc-aktiverad PostgreSQL-skalning med Azure Data Studio

Det här dokumentet vägleder dig genom stegen för att använda Azure Data Studio för att etablera Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Anslut till data styrenheten för Azure-bågen

Innan du kan skapa en instans loggar du in på data styrenheten för Azure-bågen om du inte redan är inloggad.

```console
azdata login
```

Du uppmanas sedan att ange namn området där datakontrollanten skapas, användar namnet och lösen ordet för att logga in på kontrollanten.

> Om du behöver validera namn området kan du köra ```kubectl get pods -A``` för att hämta en lista över alla namn områden i klustret.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Preliminärt och tillfälligt steg för OpenShift-användare

Implementera det här steget innan du går vidare till nästa steg. Om du vill distribuera PostgreSQL-storskalig Server grupp till Red Hat OpenShift i ett annat projekt än standardvärdet, måste du köra följande kommandon mot klustret för att uppdatera säkerhets begränsningarna. Det här kommandot ger de behörigheter som krävs för de tjänst konton som ska köra din PostgreSQL-Server grupp för storskalig skalning. SCC- **_SCC_** (Security context constraint) är den som du lade till när du distribuerade data styrenheten för Azure-bågen.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-name** är namnet på den server grupp som du ska distribuera under nästa steg._
   
Mer information om SCCs i OpenShift finns i [OpenShift-dokumentationen](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Nu kan du implementera nästa steg.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Skapa en Azure-Arc-aktiverad PostgreSQL Hyperskala-servergrupp

1. Starta Azure Data Studio
1. På fliken anslutningar klickar du på de tre punkterna längst upp till vänster och väljer "ny distribution"
1. Från distributions alternativen väljer du **postgresql för skalnings Server grupp – Azure Arc**
    >[!NOTE]
    > Du kan uppmanas att installera det [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] här om det inte redan är installerat.
1. Godkänn sekretess-och licens villkoren och klicka på **Välj** längst ned
1. Ange följande information i bladet distribuera PostgreSQL för storskalig Server grupp – Azure Arc:
   - Ange ett namn för Server gruppen
   - Ange och bekräfta ett lösen ord för _postgres_ -administratörens användare i Server gruppen
   - Välj den lagrings klass som passar för data
   - Välj den lagrings klass som passar för loggar
   - Välj den lagrings klass som är lämplig för säkerhets kopieringar
   - Välj antalet arbetsnoder som ska etableras
1. Klicka på knappen **distribuera**

Detta startar skapandet av den PostgreSQL-Server grupp för Azure-bågen som är aktive rad på data styrenheten.

På några minuter bör du skapa en fullständig installation.

## <a name="next-steps"></a>Nästa steg
- [Hantera Server gruppen med hjälp av Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Övervaka Server gruppen](monitor-grafana-kibana.md)
- Läs koncepten och instruktions guiderna för Azure Database for PostgreSQL storskaligt för att distribuera dina data över flera PostgreSQL storskaliga noder och för att dra nytta av all kraften i Azure Database för postgres-skalning. :
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
- [Resurs modell för Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

