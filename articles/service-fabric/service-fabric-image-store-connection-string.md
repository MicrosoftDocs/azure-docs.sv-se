---
title: Anslutnings sträng för Azure Service Fabric image Store
description: Lär dig mer om anslutnings strängen för avbildnings lager, inklusive dess användning och program till ett Service Fabric kluster.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 8fc0239dd18fc7071823a129a7dbc4f102023d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86246205"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Om inställningen ImageStoreConnectionString

I en del av vår dokumentation, nämner vi kortfattat förekomsten av en "ImageStoreConnectionString"-parameter utan att beskriva vad det innebär. När du har gått igenom en artikel som [distribuera och ta bort program med hjälp av PowerShell][10]ser det ut som allt du gör är att kopiera/klistra in värdet som visas i kluster manifestet för mål klustret. Inställningen måste därför kunna konfigureras per kluster, men när du skapar ett kluster via [Azure Portal][11], finns det inget alternativ för att konfigurera den här inställningen och det är alltid "Fabric: avbildnings Arkiv". Vad är syftet med den här inställningen?

![Kluster manifest][img_cm]

Service Fabric startades som en plattform för intern Microsoft-användning av många olika team, så vissa delar av den är mycket anpassningsbara – "Avbildningsarkiv" är en sådan aspekt. Avbildningsarkiv är i stort sett en lagrings Bart lagrings plats för att lagra programpaket. När programmet distribueras till en nod i klustret laddar den noden ned innehållet i programpaketet från Avbildningsarkiv. ImageStoreConnectionString är en inställning som innehåller all nödvändig information för både klienter och noder för att hitta rätt Avbildningsarkiv för ett angivet kluster.

Det finns för närvarande tre möjliga typer av Avbildningsarkiv leverantörer och deras motsvarande anslutnings strängar:

1. Avbildningsarkiv tjänst: "Fabric: avbildnings Arkiv"

2. Fil system: "fil: [sökväg till fil system]"

3. Azure Storage: "xstore: DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Container = [...] "

Den providertyp som används i produktion är Avbildningsarkiv tjänst, som är en tillstånds känslig system tjänst som du kan se från Service Fabric Explorer. 

![Avbildningsarkiv tjänst][img_is]

Om du är värd för Avbildningsarkiv i en system tjänst i själva klustret eliminerar du externa beroenden för paketets lagrings plats och ger oss mer kontroll över lagrings platsen. Framtida förbättringar kring Avbildningsarkiv är sannolikt att Avbildningsarkiv leverantören först, om detta inte är exklusivt. Anslutnings strängen för Avbildningsarkiv tjänst leverantören har ingen unik information sedan klienten redan är ansluten till mål klustret. Klienten behöver bara känna till att protokoll som riktar sig mot system tjänsten ska användas.

Fil system leverantören används i stället för den Avbildningsarkiv tjänsten för lokala kluster i en ruta under utvecklingen för att starta klustret något snabbare. Skillnaden är vanligt vis liten, men det är en bra optimering för de flesta folk under utvecklingen. Det är möjligt att distribuera ett lokalt kluster med en enda värd med de andra typerna av lagringsprovider, men det är vanligt vis ingen anledning att göra det eftersom utveckla/testa arbets flödet förblir detsamma oavsett leverantör. Azure Storage providern finns bara för äldre stöd för gamla kluster som distribuerats innan Avbildningsarkiv tjänst leverantören introducerades.

Dessutom bör inte fil system leverantören eller Azure Storage leverantören användas som en metod för att dela ett Avbildningsarkiv mellan flera kluster – detta leder till att kluster konfigurations data skadas eftersom varje kluster kan skriva motstridiga data till Avbildningsarkiv. Om du vill dela etablerade programpaket mellan flera kluster använder du [sfpkg][12] -filer i stället, som kan överföras till en extern lagrings plats med en nedladdnings-URI.

Så medan ImageStoreConnectionString kan konfigureras använder du bara standardinställningen. När du publicerar till Azure via Visual Studio, anges parametern automatiskt åt dig. För program mässig distribution till kluster som finns i Azure är anslutnings strängen alltid "Fabric: avbildnings Arkiv". Även om det är tveksamt kan värdet alltid verifieras genom att hämta kluster manifestet av [PowerShell](/powershell/module/servicefabric/get-servicefabricclustermanifest), [.net](/previous-versions/azure/reference/mt161375(v=azure.100))eller [rest](/rest/api/servicefabric/get-a-cluster-manifest). Både lokala test-och produktions kluster bör alltid konfigureras för användning av Avbildningsarkiv tjänst leverantören.

### <a name="next-steps"></a>Nästa steg
[Distribuera och ta bort program med PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
