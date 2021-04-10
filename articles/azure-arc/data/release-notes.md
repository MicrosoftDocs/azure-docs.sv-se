---
title: Azure Arc-aktiverade data tjänster – viktig information
description: Senaste versions information
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: conceptual
ms.openlocfilehash: 2f41034331ed21e194fc2b86c2902c5957333313
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010606"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Viktig information – Azure Arc-aktiverade data tjänster (för hands version)

I den här artikeln beskrivs funktioner, funktioner och förbättringar som nyligen har lanserats eller förbättrats för Azure Arc-aktiverade data tjänster. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Mars 2021

Den 6 mars 2021-versionen lanseras den 6 april 2021.

Se begränsningar för den här versionen i [kända problem – Azure Arc-aktiverade data tjänster (för hands version)](known-issues.md).

Versions nummer för Azure Data CLI ( `azdata` ): 20.3.2. Du kan installera `azdata` från [Installera Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

### <a name="data-controller"></a>Datakontrollant

- Distribuera Azure Arc-aktiverade data Services-datakontrollanten i direkt anslutnings läge från portalen. Börja [använda distribuera datakontrollant – Direct Connect-läge-krav](deploy-data-controller-direct-mode-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc Enabled PostgreSQL-skalning

Både anpassade resurs definitioner (CRD) för PostgreSQL har konsoliderats till en enda CRD. Se följande tabell.

|Frisläpp |CRD |
|-----|-----|
|Februari 2021 och tidigare| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|Från och med 2021 mars | postgresqls.arcdata.microsoft.com

Du tar bort den tidigare CRDs när du rensar tidigare installationer. Se [Rensa från tidigare installationer](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### <a name="azure-arc-enabled-managed-instance"></a>Azure Arc-aktiverad hanterad instans

- Nu kan du återställa en databas till SQL-hanterad instans med tre repliker och den läggs automatiskt till i tillgänglighets gruppen. 

- Nu kan du ansluta till en sekundär skrivskyddad slut punkt på SQL-hanterade instanser som distribueras med tre repliker. Används `azdata arc sql endpoint list` för att visa den sekundära skrivskyddade anslutnings slut punkten.

### <a name="known-issues"></a>Kända problem

- I direkt anslutet läge blockeras överföring av användning, mått och loggar med hjälp av `azdata arc dc upload` för tillfället. Användningen laddas upp automatiskt. Överföring för datakontrollant som skapats i läget för indirekt anslutning bör fortsätta att fungera.
- Distribution av datakontrollant i direkt läge kan bara göras från Azure Portal och inte tillgängligt från klient verktyg som azdata, Azure Data Studio eller kubectl.
- Distribution av Azure Arc-aktiverade SQL-hanterade instanser i direkt läge kan bara göras från Azure Portal och inte tillgängligt från verktyg som azdata, Azure Data Studio eller kubectl.
- Distribution av Azure Arc Enabled PostgeSQL-skalning i direkt läge är inte tillgängligt för tillfället.
- Automatisk uppladdning av användnings data i läget för direkt anslutning kommer inte att lyckas om du använder proxy via `–proxy-cert <path-t-cert-file>` .

## <a name="february-2021"></a>Februari 2021

### <a name="new-capabilities-and-features"></a>Nya funktioner och funktioner

Versions nummer för Azure Data CLI ( `azdata` ): 20.3.1. Du kan installera `azdata` från [Installera Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Ytterligare uppdateringar är:

- Azure Arc-aktiverad SQL-hanterad instans
   - Hög tillgänglighet med Always on-tillgänglighetsgrupper

- Azure Arc Enabled PostgreSQL Azure Data Studio: 
   - På sidan Översikt visas nu status för Server grupps elementet per nod
   - Det finns nu en ny egenskaps sida som visar mer information om Server gruppen
   - Konfigurera postgres Engine-parametrar från **noden parametrar** -sida

Problem som är associerade med den här versionen finns i [kända problem – Azure Arc-aktiverade data tjänster (för hands version)](known-issues.md)

## <a name="january-2021"></a>Januari 2021

### <a name="new-capabilities-and-features"></a>Nya funktioner och funktioner

Versions nummer för Azure Data CLI ( `azdata` ): 20.3.0. Du kan installera `azdata` från [Installera Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Ytterligare uppdateringar är:
- Lokaliserad portal tillgänglig för 17 nya språk
- Mindre ändringar i Kube-ursprungliga. yaml-filer
- Nya versioner av Grafana och Kibana
- Problem med python-miljöer när du använder azdata i antecknings böcker i Azure Data Studio löst
- Pg_audit-tillägget är nu tillgängligt för PostgreSQL-storskalig
- Ett säkerhets kopierings-ID krävs inte längre vid en fullständig återställning av en PostgreSQL-storskalig databas
- Status (hälso tillstånd) rapporteras för var och en av de PostgreSQL-instanser som utgör en Server grupp

   I tidigare versioner sammanställdes statusen på Server grupps nivå och inte på PostgreSQL Node-nivå.

- PostgreSQL-distributioner följer nu parametrarna för volym storlek som anges i skapa-kommandon
- Parametrarna för motor versionen är nu påslagna när du redigerar en Server grupp
- Namngivnings konventionen för poddar för Azure Arc-aktiverad PostgreSQL-skalning har ändrats

    Nu är det i formatet: `ServergroupName{c, w}-n` . Till exempel, en Server grupp med tre noder, en koordinator-nod och två arbetsnoder visas som:
   - `Postgres01c-0` (koordinator nod)
   - `Postgres01w-0` (arbetsnoden)
   - `Postgres01w-1` (arbetsnoden)

## <a name="december-2020"></a>December 2020

### <a name="new-capabilities--features"></a>Nya funktioner & funktioner

Versions nummer för Azure Data CLI ( `azdata` ): 20.2.5. Du kan installera `azdata` från [Installera Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Visa slut punkter för SQL-hanterad instans och PostgreSQL-skalning med hjälp av kommandona Azure Data CLI ( `azdata` ) med `azdata arc sql endpoint list` och `azdata arc postgres endpoint list` .

Redigera begär Anden om SQL-hanterad instans resurs (CPU Core och minne) med hjälp av Azure Data Studio.

Azure Arc Enabled PostgreSQL-storskalig stöder nu återställning av återställnings punkt utöver fullständig säkerhets kopiering för både version 11 och 12 av PostgreSQL. Med funktionen för återställning av tidpunkt kan du ange ett visst datum och en viss tid att återställa till.

Namngivnings konventionen för poddar för Azure Arc-aktiverad PostgreSQL-skalning har ändrats. Det finns nu i formatet: ServergroupName {r, s}-_n_. Till exempel, en Server grupp med tre noder, en koordinator-nod och två arbetsnoder visas som:
- `postgres02r-0` (koordinator nod)
- `postgres02s-0` (arbetsnoden)
- `postgres02s-1` (arbetsnoden)

### <a name="breaking-change"></a>Bryta ändring

#### <a name="new-resource-provider"></a>Ny resurs leverantör

Den här versionen presenterar en uppdaterad [resurs leverantör](../../azure-resource-manager/management/azure-services-resource-providers.md) som heter `Microsoft.AzureArcData` . Innan du kan använda den här funktionen måste du registrera den här resurs leverantören. 

Så här registrerar du den här resurs leverantören: 

1. I Azure Portal väljer du **prenumerationer** 
2. Välj din prenumeration
3. Under **Inställningar** väljer du **resurs leverantörer** 
4. Sök efter `Microsoft.AzureArcData` och välj **register** 

Du kan granska detaljerade steg på [Azure-resurs leverantörer och-typer](../../azure-resource-manager/management/resource-providers-and-types.md). Den här ändringen tar också bort alla befintliga Azure-resurser som du har överfört till Azure Portal. För att kunna använda resurs leverantören måste du uppdatera data styrenheten och använda den senaste CLI-versionen `azdata` .  

### <a name="platform-release-notes"></a>Versionsinformation för plattformen

#### <a name="direct-connectivity-mode"></a>Direkt anslutnings läge

Den här versionen presenterar direkt anslutnings läge. Med läget för direkt anslutning kan datastyrenheten automatiskt överföra användnings informationen till Azure. Som en del av användnings uppladdningen skapas Arc-dataenhetens resurs automatiskt i portalen, om den inte redan har skapats via `azdata` överföring.  

Du kan ange direkt anslutning när du skapar data styrenheten. I följande exempel skapas en datakontrollant med `azdata arc dc create` namnet `arc` med direkt anslutnings läge ( `connectivity-mode direct` ). Innan du kör exemplet ersätter du `<subscription id>` med ditt prenumerations-ID.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Kända problem

- I Azure Kubernetes service (AKS) stöds inte Kubernetes version 1.19. x.
- På Kubernetes 1,19 `containerd` stöds inte.
- Data styrenhets resursen i Azure är för närvarande en Azure-resurs. Uppdateringar som ta bort sprids inte tillbaka till Kubernetes-klustret.
- Instans namnen får inte vara längre än 13 tecken
- Ingen uppgradering på plats för Azure Arc-datakontrollanten eller databas instanserna.
- De Arc-aktiverade datatjänstcontaineravbildningarna är inte signerade.  Du kan behöva konfigurera dina Kubernetes-noder så att de tillåter hämtning av osignerade containeravbildningar.  Om du till exempel använder Docker som container runtime, kan du ange miljövariabeln DOCKER_CONTENT_TRUST = 0 och starta om.  Andra containerkörningar har liknande alternativ, som i [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Det går inte att skapa Azure Arc-aktiverade SQL-hanterade instanser eller PostgreSQL för storskaliga Server grupper från Azure Portal.
- Om du använder NFS måste du ange `allowRunAsRoot` till `true` i din distributions profil fil innan du skapar data styrenheten för Azure-bågen.
- Endast SQL-och PostgreSQL-inloggning.  Inget stöd för Azure Active Directory eller Active Directory.
- Att skapa en datakontrollant i OpenShift kräver avslappnad säkerhets begränsningar.  I dokumentationen finns mer information.
- Om du använder Azure Kubernetes service (AKS)-motorn på Azure Stack hubb med Azure Arc-datastyrenheten och databas instanser, stöds inte uppgradering till en nyare Kubernetes-version. Avinstallera Azure Arc data Controller och alla databas instanser innan du uppgraderar Kubernetes-klustret.
- AKS-kluster som sträcker sig över [flera tillgänglighets zoner](../../aks/availability-zones.md) stöds för närvarande inte för Azure Arc-aktiverade data tjänster. Du undviker det här problemet genom att ta bort alla zoner från urvals kontrollen när du skapar AKS-klustret i Azure Portal, om du väljer en region där zoner är tillgängliga. Se följande bild:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Avmarkera kryss rutorna för varje zon för att ange ingen.":::

## <a name="october-2020"></a>Oktober 2020 

Versions nummer för Azure Data CLI ( `azdata` ): 20.2.3. Du kan installera `azdata` från [Installera Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

I den här versionen införs följande ändringar: 

* I PostgreSQL-CRD (Custom Resource definition) `shards` ändras termen till `workers` . Den här termen ( `workers` ) matchar kommando rads parameterns namn.

* `azdata arc postgres server delete` du uppmanas att bekräfta innan du tar bort en postgres-instans.  Använd `--force` för att hoppa över prompten.

### <a name="additional-changes"></a>Ytterligare ändringar

* En ny valfri parameter har lagts till i `azdata arc postgres server create` anropad `--volume-claim mounts` . Värdet är en kommaavgränsad lista över volym anspråks monteringar. En volym anspråks montering är ett par av volym typ och PVC-namn. Den enda volym typ som stöds för närvarande är `backup` .  I PostgreSQL, när volym typen är `backup` , monteras PVC: n på `/mnt/db-backups` .  Detta möjliggör delning av säkerhets kopior mellan PostgresSQL-instanser så att säkerhets kopian av en PostgresSQL-instans kan återställas i en annan instans.

* Nya korta namn för PostgresSQL anpassade resurs definitioner: 
  * `pg11` 
  * `pg12`
* Med telemetri-överföring får användaren antingen:
   * Antal poäng som laddats upp till Azure eller 
   * Om inga data har lästs in i Azure kan du prova att försöka igen.
* `azdata arc dc debug copy-logs` läser nu även från `/var/opt/controller/log` mapp och samlar in postgresql-motor loggar i Linux.
*   Visa en fungerande indikator när du skapar och återställer säkerhets kopia med PostgreSQL-skalning.
* `azdata arc postrgres backup list` innehåller nu information om säkerhets kopierings storlek.
* Egenskapen administratörs namn för SQL-hanterad instans har lagts till i den högra kolumnen i översikts bladet i Azure Portal.
* Azure Data Studio stöder konfiguration av antal arbetsnoder, vCore och minnes inställningar för PostgreSQL-skalning. 
* För hands versionen har stöd för säkerhets kopiering/återställning av postgres version 11 och 12.

## <a name="september-2020"></a>September 2020

Azure Arc-aktiverade data tjänster har publicerats för offentlig för hands version. Med ARC-aktiverade data tjänster kan du hantera data tjänster var som helst.

- SQL-hanterad instans
- PostgreSQL-storskalig

Mer information finns i [Vad är Azure Arc-aktiverade data tjänster?](overview.md)

## <a name="next-steps"></a>Nästa steg

> **Vill du bara testa saker?**  
> Kom igång snabbt med [Azure Arc rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på AKS, AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator.

- [Installera klient verktyg](install-client-tools.md)
- [Skapa data styrenheten för Azure-bågen](create-data-controller.md) (kräver att klient verktygen installeras först)
- [Skapa en Azure SQL-hanterad instans på Azure-bågen](create-sql-managed-instance.md) (du måste först skapa en Azure båg-datakontrollant)
- [Skapa en Azure Database for PostgreSQL skalnings Server grupp på Azure-bågen](create-postgresql-hyperscale-server-group.md) (du måste först skapa en Azure-båge data Controller)
- [Resursleverantörer för Azure-tjänster](../../azure-resource-manager/management/azure-services-resource-providers.md)
