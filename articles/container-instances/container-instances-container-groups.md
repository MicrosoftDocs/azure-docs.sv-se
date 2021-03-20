---
title: Introduktion till behållar grupper
description: Lär dig mer om behållar grupper i Azure Container Instances, en samling instanser som delar en livs cykel och resurser, till exempel processorer, lagring och nätverk
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 72ebe6186da179bc5a1effddcc14327455eb7557
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89612412"
---
# <a name="container-groups-in-azure-container-instances"></a>Behållargrupper i Azure Container Instances

Resursen på den översta nivån i Azure Container Instances är *behållar gruppen*. I den här artikeln beskrivs vilka behållar grupper som är och vilka typer av scenarier de aktiverar.

## <a name="what-is-a-container-group"></a>Vad är en behållar grupp?

En behållar grupp är en samling behållare som är schemalagda på samma värddator. Behållarna i en behållar grupp delar en livs cykel, resurser, lokalt nätverk och lagrings volymer. Det liknar begreppet *Pod* i [Kubernetes][kubernetes-pod].

Följande diagram visar ett exempel på en behållar grupp som innehåller flera behållare:

![Diagram över container grupper][container-groups-example]

Den här exempel behållar gruppen:

* Har schemalagts på en dator med en enda värd.
* Har tilldelats en DNS-benämning.
* Exponerar en enskild offentlig IP-adress med en exponerad port.
* Består av två behållare. En behållare lyssnar på port 80, medan den andra lyssnar på port 5000.
* Innehåller två Azure-filresurser som volym monteringar och varje behållare monterar en av resurserna lokalt.

> [!NOTE]
> Grupper med flera behållare stöder för närvarande endast Linux-behållare. För Windows-behållare stöder Azure Container Instances endast distribution av en enda behållar instans. Medan vi arbetar för att hämta alla funktioner till Windows-behållare kan du hitta aktuella plattforms skillnader i tjänst [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Distribution

Här följer två vanliga sätt att distribuera en grupp med flera behållare: Använd en [Resource Manager-mall][resource-manager template] eller en [yaml-fil][yaml-file]. En Resource Manager-mall rekommenderas när du behöver distribuera ytterligare Azure-tjänst resurser (till exempel en [Azure Files resurs][azure-files]) när du distribuerar behållar instanserna. På grund av YAML-formatets mer koncisa typ rekommenderas en YAML-fil när distributionen endast innehåller behållar instanser. Mer information om egenskaper som du kan ange finns i [referens dokumentationen för Resource Manager-mallen](/azure/templates/microsoft.containerinstance/containergroups) eller [yaml](container-instances-reference-yaml.md) .

Om du vill bevara en behållar grupps konfiguration kan du exportera konfigurationen till en YAML-fil med hjälp av Azure CLI-kommandot [AZ container export][az-container-export]. Med export kan du lagra konfigurationerna för behållar grupper i versions kontroll för "konfiguration som kod". Du kan också använda den exporterade filen som en start punkt när du utvecklar en ny konfiguration i YAML.



## <a name="resource-allocation"></a>Resursallokering

Azure Container Instances allokerar resurser som processorer, minne och alternativt [GPU][gpus] (för hands version) till en grupp med flera behållare genom att lägga till [resurs begär Anden][resource-requests] för instanserna i gruppen. Att ta processor resurser som exempel, om du skapar en behållar grupp med två behållar instanser, varje begäran 1 processor, allokeras behållar gruppen 2 processorer.

### <a name="resource-usage-by-container-instances"></a>Resursanvändning efter container instances

Varje behållar instans i en grupp tilldelas resurserna som anges i resurs förfrågan. De maximala resurserna som används av en behållar instans i en grupp kan dock vara olika om du konfigurerar den valfria [resurs begränsnings][resource-limits] egenskapen. Resurs gränsen för en behållar instans måste vara större än eller lika med den obligatoriska [resurs förfrågnings][resource-requests] egenskapen.

* Om du inte anger en resurs gräns, är behållar instansens maximala resursanvändning samma som resurs förfrågan.

* Om du anger en gräns för en behållar instans kan instansens maximala användning vara större än begäran, upp till den gräns du angav. På motsvarande sätt kan resursanvändning från andra behållar instanser i gruppen minska. Den maximala resurs gränsen som du kan ange för en behållar instans är det totala antalet resurser som har allokerats till gruppen.
    
I en grupp med två behållar instanser varje begäran 1 processor kan en av dina behållare köra en arbets belastning som kräver att fler processorer körs än det andra.

I det här scenariot kan du ange en resurs gräns på upp till 2 processorer för behållar instansen. Den här konfigurationen gör att behållar instansen kan använda upp till 2 processorer om den är tillgänglig.

> [!NOTE]
> En liten del av en behållar grupps resurser används av tjänstens underliggande infrastruktur. Behållarna kommer att kunna komma åt de flesta men inte alla resurser som har allokerats till gruppen. Därför bör du planera en liten resurs buffert när du begär resurser för behållare i gruppen.

### <a name="minimum-and-maximum-allocation"></a>Lägsta och högsta allokering

* Allokera **minst** 1 processor och 1 GB minne till en behållar grupp. Enskilda behållar instanser i en grupp kan tillhandahållas med mindre än 1 processor och 1 GB minne. 

* För **maximala** resurser i en behållar grupp, se [resurs tillgänglighet][region-availability] för Azure Container instances i distributions regionen.

## <a name="networking"></a>Nätverk

Behållar grupper kan dela en extern IP-adress, en eller flera portar på den IP-adressen och en DNS-etikett med ett fullständigt kvalificerat domän namn (FQDN). Om du vill att externa klienter ska kunna komma åt en behållare i gruppen måste du exponera porten på IP-adressen och från behållaren. En behållar grupps IP-adress och FQDN släpps när behållar gruppen tas bort. 

I en behållar grupp kan behållar instanser komma åt varandra via localhost på vilken port som helst, även om dessa portar inte exponeras externt på gruppens IP-adress eller från behållaren.

Du kan också distribuera behållar grupper till ett [virtuellt Azure-nätverk][virtual-network] så att behållare kan kommunicera säkert med andra resurser i det virtuella nätverket.

## <a name="storage"></a>Storage

Du kan ange externa volymer som ska monteras i en behållar grupp. Volymer som stöds är:
* [Azure-filresurs][azure-files]
* [Icke][secret]
* [Tom katalog][empty-directory]
* [Klonad git-lagrings platsen][volume-gitrepo]

Du kan mappa dessa volymer till specifika sökvägar inom de enskilda behållarna i en grupp. 

## <a name="common-scenarios"></a>Vanliga scenarier

Grupper med flera behållare är användbara i de fall där du vill dela upp en enda funktions aktivitet i ett litet antal behållar avbildningar. Dessa bilder kan sedan levereras av olika team och ha separata resurs krav.

Exempel på användning kan vara:

* En behållare som hanterar ett webb program och en behållare hämtar det senaste innehållet från käll kontroll.
* En program behållare och en loggnings behållare. Loggnings behållaren samlar in loggar och mät resultat från det huvudsakliga programmet och skriver dem till långsiktig lagring.
* En program behållare och en övervaknings behållare. Övervaknings behållaren skickar regelbundet en begäran till programmet för att säkerställa att den körs och svarar korrekt, och genererar en avisering om den inte är det.
* En klient dels behållare och en backend-behållare. Klient delen kan betjäna ett webb program, med Server delen som kör en tjänst för att hämta data. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en container grupp med flera behållare med en Azure Resource Manager-mall:

> [!div class="nextstepaction"]
> [Distribuera en behållar grupp][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
