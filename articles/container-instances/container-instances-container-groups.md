---
title: Introduktion till containergrupper
description: Lär dig mer om containergrupper i Azure Container Instances, en samling instanser som delar en livscykel och resurser som processorer, lagring och nätverk
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: a2cb3eac5baa5b1035749d28b9fb99bbb45b9ee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790895"
---
# <a name="container-groups-in-azure-container-instances"></a>Behållargrupper i Azure Container Instances

Den översta resursen i Azure Container Instances är *containergruppen*. I den här artikeln beskrivs vilka containergrupper som är och vilka typer av scenarier de möjliggör.

## <a name="what-is-a-container-group"></a>Vad är en containergrupp?

En containergrupp är en samling containrar som schemaläggs på samma värddator. Containrarna i en containergrupp delar en livscykel, resurser, lokalt nätverk och lagringsvolymer. Det liknar begrepp i en *podd i* [Kubernetes][kubernetes-pod].

Följande diagram visar ett exempel på en containergrupp som innehåller flera containrar:

![Diagram över containergrupper][container-groups-example]

Det här exemplet på en containergrupp:

* Schemaläggs på en enda värddator.
* Tilldelas en DNS-namnetikett.
* Exponerar en enskild offentlig IP-adress, med en exponerad port.
* Består av två containrar. En container lyssnar på port 80 medan den andra lyssnar på port 5000.
* Innehåller två Azure-filresurser som volymmonteringar och varje container monterar en av filresurser lokalt.

> [!NOTE]
> Grupper med flera containrar stöder för närvarande endast Linux-containrar. För Windows-containrar Azure Container Instances endast distribution av en enda containerinstans. Medan vi arbetar med att ta med alla funktioner i Windows-containrar kan du hitta de aktuella plattformsskillnaderna i [översikten över tjänsten](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Distribution

Här är två vanliga sätt att distribuera en grupp med flera containrar: använda [en Resource Manager eller][resource-manager template] en [YAML-fil][yaml-file]. En Resource Manager-mall rekommenderas när du behöver distribuera ytterligare Azure-tjänstresurser (till exempel en [Azure Files resurs)][azure-files]när du distribuerar containerinstanserna. På grund av YAML-formatets mer koncisa natur rekommenderas en YAML-fil när distributionen endast innehåller containerinstanser. Mer information om egenskaper som du kan ange finns i Resource Manager [mallreferens eller](/azure/templates/microsoft.containerinstance/containergroups) [YAML-referensdokumentation.](container-instances-reference-yaml.md)

Om du vill bevara en containergrupps konfiguration kan du exportera konfigurationen till en YAML-fil med hjälp av Azure CLI-kommandot [az container export][az-container-export]. Med Exportera kan du lagra dina konfigurationer för containergrupper i versionskontroll för "konfiguration som kod". Du kan också använda den exporterade filen som utgångspunkt när du utvecklar en ny konfiguration i YAML.



## <a name="resource-allocation"></a>Resursallokeringen

Azure Container Instances tilldelar resurser som processorer, minne och eventuellt GPU:er [(förhandsversion)][gpus] till en [][resource-requests] grupp med flera containrar genom att lägga till resursbegäranden för instanserna i gruppen. Om du till exempel tar CPU-resurser och skapar en containergrupp med två containerinstanser, där var och en begär 1 processor, allokeras containergruppen 2 processorer.

### <a name="resource-usage-by-container-instances"></a>Resursanvändning efter containerinstanser

Varje containerinstans i en grupp tilldelas de resurser som anges i dess resursbegäran. Det maximala antalet resurser som används av en containerinstans i en grupp kan dock vara annorlunda om du konfigurerar dess valfria [resursgränsegenskap.][resource-limits] Resursgränsen för en containerinstans måste vara större än eller lika med den obligatoriska egenskapen [för resursbegäran.][resource-requests]

* Om du inte anger en resursgräns är containerinstansens maximala resursanvändning samma som dess resursbegäran.

* Om du anger en gräns för en containerinstans kan instansens maximala användning vara större än begäran, upp till den gräns som du har angett. På motsvarande sätt kan resursanvändningen av andra containerinstanser i gruppen minska. Den högsta resursgräns som du kan ange för en containerinstans är det totala antalet resurser som allokerats till gruppen.
    
I en grupp med två containerinstanser som var och en begär 1 PROCESSOR kan en av dina containrar till exempel köra en arbetsbelastning som kräver fler processorer att köra än den andra.

I det här scenariot kan du ange en resursgräns på upp till 2 processorer för containerinstansen. Med den här konfigurationen kan containerinstansen använda upp till 2 processorer om det är tillgängligt.

> [!NOTE]
> En liten mängd av en containergrupps resurser används av tjänstens underliggande infrastruktur. Dina containrar kommer att kunna komma åt de flesta men inte alla resurser som har allokerats till gruppen. Därför bör du planera en liten resursbuffert när du begär resurser för containrar i gruppen.

### <a name="minimum-and-maximum-allocation"></a>Lägsta och högsta allokering

* Allokera minst **1** PROCESSOR och 1 GB minne till en containergrupp. Enskilda containerinstanser i en grupp kan etableras med mindre än 1 CPU och 1 GB minne. 

* Maximalt **antal** resurser i en containergrupp finns i [resurstillgänglighet][region-availability] för Azure Container Instances i distributionsregionen.

## <a name="networking"></a>Nätverk

Containergrupper kan dela en extern IP-adress, en eller flera portar på den IP-adressen och en DNS-etikett med ett fullständigt kvalificerat domännamn (FQDN). Om du vill göra det möjligt för externa klienter att nå en container i gruppen måste du exponera porten på IP-adressen och från containern. En containergrupps IP-adress och FQDN släpps när containergruppen tas bort. 

I en containergrupp kan containerinstanser nå varandra via localhost på valfri port, även om dessa portar inte exponeras externt på gruppens IP-adress eller från containern.

Du kan också distribuera containergrupper i ett [virtuellt Azure-nätverk][virtual-network] så att containrar kan kommunicera säkert med andra resurser i det virtuella nätverket.

## <a name="storage"></a>Storage

Du kan ange externa volymer som ska monteras i en containergrupp. Volymer som stöds är:
* [Azure-filresurs][azure-files]
* [Hemliga][secret]
* [Tom katalog][empty-directory]
* [Klonad git-lagringsplatsen][volume-gitrepo]

Du kan mappa volymerna till specifika sökvägar i de enskilda containrarna i en grupp. 

## <a name="common-scenarios"></a>Vanliga scenarier

Grupper med flera containrar är användbara i fall där du vill dela upp en enda funktionell uppgift i ett litet antal containeravbildningar. Dessa avbildningar kan sedan levereras av olika team och ha separata resurskrav.

Exempel på användning kan vara:

* En container som betjänar ett webbprogram och en container som drar det senaste innehållet från källkontrollen.
* En programcontainer och en loggningscontainer. Loggningscontainern samlar in loggar och mått från huvudprogrammet och skriver dem till långsiktig lagring.
* En programcontainer och en övervakningscontainer. Övervakningscontainern skickar regelbundet en begäran till programmet för att säkerställa att den körs och svarar korrekt, och skickar en avisering om den inte gör det.
* En frontend-container och en backend-container. Frontend kan hantera ett webbprogram, där serverdelen kör en tjänst för att hämta data. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du distribuerar en containergrupp med flera containrar med Azure Resource Manager mall:

> [!div class="nextstepaction"]
> [Distribuera en containergrupp][resource-manager template]

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
[az-container-export]: /cli/azure/container#az_container_export
