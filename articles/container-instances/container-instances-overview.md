---
title: Server lös behållare i Azure
description: Tjänsten Azure Container Instances är det snabbaste och enklaste sättet att köra isolerade behållare i Azure, utan att behöva hantera virtuella datorer och utan att behöva använda en initierare på högre nivå.
ms.topic: overview
ms.date: 08/10/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 873f7cc51d1b369503a72501ae000000ff06f805
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573021"
---
# <a name="what-is-azure-container-instances"></a>Vad är Azure Container Instances?

Containrar är på väg att bli det bästa sättet att paketera, distribuera och hantera molnprogram. Azure Container Instances erbjuder det snabbaste och enklaste sättet att köra en behållare i Azure, utan att behöva hantera några virtuella datorer och utan att behöva använda en tjänst på högre nivå.

Azure Container Instances är en bra lösning för alla scenarier som kan fungera i isolerade behållare, däribland enkla program, automatisering av uppgifter och att skapa jobb. För scenarier där du behöver fullständig behållar dirigering, inklusive tjänst identifiering över flera behållare, automatisk skalning och koordinerade program uppgraderingar rekommenderar vi [Azure Kubernetes service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Snabba starttider

Med containrar får du betydande startfördelar jämfört med virtuella datorer. Azure Container Instances kan starta en behållare i Azure på några sekunder utan att några virtuella datorer behöver etableras eller hanteras.

Ta Linux-eller Windows container-avbildningar från Docker Hub, ett privat [Azure Container Registry](../container-registry/index.yml)eller ett annat molnbaserad Docker-register. Besök [vanliga frågor och svar](container-instances-faq.md) för att lära dig vilka register som stöds av ACI. Azure Container Instances cachelagrar flera vanliga grundläggande OS-avbildningar, vilket hjälper till att påskynda distributionen av dina anpassade program avbildningar.

## <a name="container-access"></a>Åtkomst till behållare

Azure Container Instances möjliggör exponera dina behållar grupper direkt till Internet med en IP-adress och ett fullständigt kvalificerat domän namn (FQDN). När du skapar en behållarinstans kan du ange en anpassad DNS-namnsetikett, så att programmet kan nås på *customlabel*.*azureregion*. azurecontainer.io.

Azure Container Instances också stöd för att köra ett kommando i en behållare som körs genom att tillhandahålla ett interaktivt gränssnitt som hjälper dig med program utveckling och fel sökning. Åtkomsten tar platser över HTTPS och använder TLS för att skydda klient anslutningar.

> [!IMPORTANT]
> Från och med 13 januari 2020 kräver Azure Container Instances alla säkra anslutningar från servrar och program för att använda TLS 1,2. Stöd för TLS 1,0 och 1,1 kommer att dras tillbaka.

## <a name="compliant-deployments"></a>Kompatibla distributioner

### <a name="hypervisor-level-security"></a>Säkerhet på hypervisornivå

Tidigare har containrar erbjudit isolering av programberoenden och resursstyrning men har inte ansetts vara tillräckligt strikta för fientlig användning med flera innehavare. Azure Container Instances garanterar att ditt program är lika isolerat i en behållare som i en virtuell dator.

### <a name="customer-data"></a>Kundinformation

ACI-tjänsten lagrar lägsta kund information som krävs för att se till att behållar grupper körs som förväntat. Att lagra kund information i en enda region är för närvarande endast tillgängligt i Sydostasien region (Singapore) i regionen Asien och stillahavsområdet Geo-och Brasilien syd (Sao Paulo State) i Brasilien geo. För alla andra regioner lagras kund information på [geo](https://azure.microsoft.com/global-infrastructure/geographies/). Kontakta Azure-supporten för att få mer information.

## <a name="custom-sizes"></a>Anpassade storlekar

Containrar är vanligtvis optimerade för att endast köra ett program, men de specifika behoven för dessa program kan skilja sig åt avsevärt. Azure Container Instances erbjuder optimal användning eftersom det tillåter exakta specifikationer av CPU-kärnor och minne. Du betalar för vad du behöver och faktureras per sekund, så att du kan finjustera dina utgifter utifrån dina faktiska behov.

För beräkningsintensiva jobb, till exempel maskininlärning, kan Azure Container Instances schemalägga Linux-containrar att använda NVIDIA Tesla [GPU-resurser](container-instances-gpu.md) (förhandsversion).

## <a name="persistent-storage"></a>Beständig lagring

För att hämta och bevara tillstånd med Azure Container Instances erbjuder vi direkt [montering av Azure Filess resurser](./container-instances-volume-azure-files.md) som stöds av Azure Storage.

## <a name="linux-and-windows-containers"></a>Linux- och Windows-containrar

Azure Container Instances kan schemalägga både Windows- och Linux-behållare med samma API. Ange typ av operativsystem när du skapar dina [containergrupper](container-instances-container-groups.md).

Vissa funktioner är för närvarande begränsade till Linux-behållare:

* Flera containrar per containergrupp
* Volym montering ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [Secret](container-instances-volume-secret.md))
* [Användnings statistik för resursanvändningen](container-instances-monitor.md) med Azure Monitor
* [Distribution av virtuellt nätverk](container-instances-vnet.md)
* [GPU-resurser](container-instances-gpu.md) (förhands granskning)

För distributioner av Windows-behållare använder du avbildningar baserade på vanliga [Windows Base-avbildningar](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="co-scheduled-groups"></a>Samordna schemalagda grupper

Azure Container Instances stöder schemaläggning av [grupper med flera behållare](container-instances-container-groups.md) som delar en värddator, lokalt nätverk, lagring och livscykel. Det gör att du kan kombinera din huvudprogramcontainer med andra stödrollscontainrar, t.ex. sidecar-filer för loggning.

## <a name="virtual-network-deployment"></a>Distribution av virtuellt nätverk

Azure Container Instances aktiverar [distribution av behållar instanser till ett virtuellt Azure-nätverk](container-instances-vnet.md). När du distribuerar till ett undernät i det virtuella nätverket kan behållar instanser kommunicera säkert med andra resurser i det virtuella nätverket, inklusive de som finns lokalt (via [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Nästa steg

Försök att distribuera en container till Azure med ett enda kommando med hjälp av vår snabbstartsguide:

> [!div class="nextstepaction"]
> [Azure Container Instances Snabbstart](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
