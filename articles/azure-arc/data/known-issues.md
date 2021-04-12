---
title: Kända problem med Azure Arc Enabled Data Services
description: Senaste kända problem
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029513"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Kända problem – Azure Arc-aktiverade data tjänster (för hands version)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Mars 2021

### <a name="data-controller"></a>Datakontrollant

- Du kan skapa en datakontrollant i direkt anslutnings läge med Azure Portal. Distribution med andra Azure Arc-aktiverade data Services-verktyg stöds inte. Mer specifikt kan du inte distribuera en data styrenhet i Direct Connect-läge med något av följande verktyg under den här versionen.
   - Azure Data Studio
   - Azure Data CLI ( `azdata` )
   - Inbyggda Kubernetes-verktyg

   [Distribuera Azure Arc data Controller | Direct Connect-läget](deploy-data-controller-direct-mode.md) förklarar hur du skapar datakontrollanten i portalen. 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc Enabled PostgreSQL-skalning

- Det finns inte stöd för att distribuera en Azure-båge som är aktive rad i postgres i en ARC-datakontrollant som är aktive rad för Direct Connect-läge.
- Om du skickar ett ogiltigt värde till `--extensions` parametern när du redigerar konfigurationen för en Server grupp för att aktivera ytterligare tillägg återställs listan över aktiverade tillägg på ett felaktigt sätt till den som var i den tidpunkt då Server gruppen skapades, och hindrar användaren från att skapa ytterligare tillägg. Den enda lösning som är tillgänglig när det händer är att ta bort Server gruppen och distribuera om den.

## <a name="february-2021"></a>Februari 2021

### <a name="data-controller"></a>Datakontrollant

- Kluster läget för direkt anslutning har inaktiverats

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc Enabled PostgreSQL-skalning

- Återställning vid tidpunkt stöds inte för tillfället på NFS-lagring.
- Det går inte att aktivera och konfigurera pg_cron-tillägget samtidigt. Du måste använda två kommandon för detta. Ett kommando för att aktivera det och ett kommando för att konfigurera det. 

   Exempel:
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   Det första kommandot kräver en omstart av Server gruppen. Innan du kör det andra kommandot måste du därför kontrol lera att Server gruppens tillstånd har gått över från uppdateringen till klar. Om du kör det andra kommandot innan omstarten har slutförts kommer det att Miss klar. Om så är fallet väntar du bara på en stund och kör sedan det andra kommandot igen.

## <a name="introduced-prior-to-february-2021"></a>Infördes före februari 2021

### <a name="data-controller"></a>Datakontrollant

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


## <a name="next-steps"></a>Nästa steg

> **Vill du bara testa saker?**  
> Kom igång snabbt med [Azure Arc rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på AKS, AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator.

- [Installera klient verktyg](install-client-tools.md)
- [Skapa data styrenheten för Azure-bågen](create-data-controller.md) (kräver att klient verktygen installeras först)
- [Skapa en Azure SQL-hanterad instans på Azure-bågen](create-sql-managed-instance.md) (du måste först skapa en Azure båg-datakontrollant)
- [Skapa en Azure Database for PostgreSQL skalnings Server grupp på Azure-bågen](create-postgresql-hyperscale-server-group.md) (du måste först skapa en Azure-båge data Controller)
- [Resursleverantörer för Azure-tjänster](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Viktig information – Azure Arc-aktiverade data tjänster (för hands version)](release-notes.md)
