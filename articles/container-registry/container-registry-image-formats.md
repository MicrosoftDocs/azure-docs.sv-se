---
title: Innehålls format som stöds
description: Lär dig mer om innehålls format som stöds av Azure Container Registry, inklusive Docker-kompatibla behållar avbildningar, Helm-diagram, OCI-avbildningar och OCI-artefakter.
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 218d98f3f16e8d0ca76a24692afbb2b69606564b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223072"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Innehålls format som stöds i Azure Container Registry

Använd ett privat lager i Azure Container Registry för att hantera något av följande innehålls format. 

## <a name="docker-compatible-container-images"></a>Docker-kompatibla behållar avbildningar

Följande versioner av Docker-behållar avbildningar stöds:

* [Docker-avbildnings manifest v2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-avbildnings manifest v2, schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) – innehåller manifest listor som tillåter register för att lagra [avbildningar med flera arkitektur](push-multi-architecture-images.md) under en enda `image:tag` referens

## <a name="oci-images"></a>OCI-avbildningar

Azure Container Registry stöder avbildningar som uppfyller specifikationen för [avbildnings formatet öppen container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md), inklusive specifikationen för valfria [avbildnings index](https://github.com/opencontainers/image-spec/blob/master/image-index.md) . Paket formaten innehåller ett [bild format för bilder (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>OCI-artefakter

Azure Container Registry stöder [OCI-distributions specifikationen](https://github.com/opencontainers/distribution-spec), en leverantörs oberoende, molnbaserad oberoende-specifikation för att lagra, dela, säkra och distribuera behållar avbildningar och andra innehålls typer (artefakter). Specifikationen gör det möjligt för ett register att lagra en mängd olika artefakter utöver behållar avbildningar. Du kan använda verktyg som är lämpliga för artefakten för att skicka och ta emot artefakter. Ett exempel finns i [skicka och hämta en OCI-artefakt med ett Azure Container Registry](container-registry-oci-artifacts.md).

Mer information om OCI-artefakter finns i [OCI-registret as Storage (ORAS)](https://github.com/deislabs/oras) lagrings platsen och [OCI-artefakterna](https://github.com/opencontainers/artifacts) lagrings platsen på GitHub.

## <a name="helm-charts"></a>Helm-diagram

Azure Container Registry kan vara värd för databaser för [Helm-diagram](https://helm.sh/), ett paket format som används för att snabbt hantera och distribuera program för Kubernetes. [Helm-klient](https://docs.helm.sh/using_helm/#installing-helm) version 3 rekommenderas. Se [push-och pull-Helm diagram till ett Azure Container Registry](container-registry-helm-repos.md).

## <a name="next-steps"></a>Nästa steg

* Se hur du [push-överför och hämtar](container-registry-get-started-docker-cli.md) bilder med Azure Container Registry.

* Använd [ACR-uppgifter](container-registry-tasks-overview.md) för att bygga och testa behållar avbildningar. 

* Använd [Moby-BuildKit](https://github.com/moby/buildkit) för att bygga och paketera behållare i OCI-format.

* Konfigurera en [Helm-lagringsplats](container-registry-helm-repos.md) som finns i Azure Container Registry. 


