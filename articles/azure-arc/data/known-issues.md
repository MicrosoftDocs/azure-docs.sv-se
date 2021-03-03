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
ms.openlocfilehash: d4667e8fa3a5624dddc3cb0dd792fc73ea812332
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693231"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Kända problem – Azure Arc-aktiverade data tjänster (för hands version)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Februari 2021


- Anslutet kluster läge har inaktiverats
- Azure Arc Enabled PostgreSQL-skalning returnerar ett felaktigt fel meddelande när det inte går att återställa till den relativa tidpunkt som du anger. Om du till exempel har angett en tidpunkt för återställning som är äldre än vad dina säkerhets kopior innehåller, Miss lyckas återställningen med ett fel meddelande som: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}` . När detta händer startar du om kommandot efter att ha angett en tidpunkt som ligger inom det datum intervall som du har säkerhets kopior för. Ta reda på intervallet genom att lista dina säkerhets kopior och titta på de datum då de togs.
- Ett säkerhets kopierings-ID krävs vid fullständig återställning. Om du inte anger ett säkerhets kopierings-ID kommer den senaste säkerhets kopian att användas som standard. Detta fungerar inte i den här versionen.

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

### <a name="postgresql"></a>PostgreSQL

- Azure Arc Enabled PostgreSQL-skalning returnerar ett felaktigt fel meddelande när det inte går att återställa till den relativa tidpunkt som du anger. Om du till exempel har angett en tidpunkt för att återställa som är äldre än vad dina säkerhets kopior innehåller, kommer återställningen att Miss lyckas med ett fel meddelande som: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
När detta händer startar du om kommandot efter att ha angett en tidpunkt som ligger inom det datum intervall som du har säkerhets kopior för. Du fastställer intervallet genom att ange dina säkerhets kopior och titta på de datum då de vidtogs.
- Återställning av tidpunkt stöds bara i Server grupper. Mål servern för en återställnings punkt för en tidpunkt kan inte vara den server som du använde för säkerhets kopieringen. Den måste vara en annan server grupp. Fullständig återställning stöds dock i samma server grupp.
- Ett säkerhets kopierings-ID krävs vid fullständig återställning. Om du inte anger ett säkerhets kopierings-ID kommer den senaste säkerhets kopian att användas som standard. Detta fungerar inte i den här versionen.

## <a name="next-steps"></a>Nästa steg

> **Vill du bara testa saker?**  
> Kom igång snabbt med [Azure Arc rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på AKS, AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator.

- [Installera klient verktyg](install-client-tools.md)
- [Skapa data styrenheten för Azure-bågen](create-data-controller.md) (kräver att klient verktygen installeras först)
- [Skapa en Azure SQL-hanterad instans på Azure-bågen](create-sql-managed-instance.md) (du måste först skapa en Azure båg-datakontrollant)
- [Skapa en Azure Database for PostgreSQL skalnings Server grupp på Azure-bågen](create-postgresql-hyperscale-server-group.md) (du måste först skapa en Azure-båge data Controller)
- [Resursleverantörer för Azure-tjänster](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Viktig information – Azure Arc-aktiverade data tjänster (för hands version)](release-notes.md)
