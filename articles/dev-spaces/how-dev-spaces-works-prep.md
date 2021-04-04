---
title: Så här förbereder du ett projekt för Azure dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver hur du förbereder ditt projekt med Azure dev Spaces
keywords: azds. yaml, Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: d2da69dd8a8c2683ff584dfd0ffc61cb023f2ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91968165"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Så här förbereder du ett projekt för Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Med Azure dev Spaces får du flera sätt att snabbt iterera och felsöka Kubernetes-program och samar beta med ditt team i ett Azure Kubernetes service-kluster (AKS). Dev Spaces kan generera Dockerfiles-och Helm-diagram för ditt projekt. Dev Spaces skapar och använder också en konfigurations fil för att distribuera, köra och felsöka dina Kubernetes-program i AKS. Alla dessa filer finns i program koden och kan läggas till i versions kontroll systemet.

I den här artikeln beskrivs vad som händer när du förbereder projektet för att köras i AKS med dev Spaces.

## <a name="prepare-your-code"></a>Förbered din kod

För att kunna köra programmet i ett dev-utrymme måste den vara containerd och du måste definiera hur det ska distribueras till Kubernetes. För att Använd ditt program behöver du en Dockerfile. Om du vill definiera hur programmet distribueras till Kubernetes behöver du ett [Helm-diagram](https://docs.helm.sh/). För att hjälpa till med att skapa både Dockerfile-och Helm-diagrammet för ditt program innehåller verktyg på klient sidan `prep` kommandot:

```cmd
azds prep --enable-ingress
```

`prep`Kommandot tittar på filerna i projektet och försöker skapa Dockerfile-och Helm-diagrammet för att köra programmet i Kubernetes. För närvarande `prep` kommer kommandot att generera ett Dockerfile-och Helm-diagram med följande språk:

* Java
* Node.js
* .NET Core

Du *måste* köra `prep` kommandot från en katalog som innehåller käll koden. Genom att köra `prep` kommandot från rätt katalog kan verktyget på klient sidan identifiera språket och skapa en lämplig Dockerfile för att Använd ditt program. Du kan också köra `prep` kommandot från en katalog som innehåller en *pom.xml* -fil för Java-projekt.

Om du kör `prep` kommandot från en katalog som inte innehåller någon käll kod genererar klient sidans verktyg inget Dockerfile. Det visar också ett fel som säger: *Dockerfile kunde inte genereras på grund* av ett språk som inte stöds. Det här felet uppstår även om verktyg på klient sidan inte känner igen projekt typen.

När du kör `prep` kommandot har du möjlighet att ange `--enable-ingress` flaggan. Den här flaggan anger att kontrollanten ska skapa en tillgänglig slut punkt för Internet för den här tjänsten. Om du inte anger den här flaggan är tjänsten bara tillgänglig i klustret eller med hjälp av den localhost-tunnel som skapats av klient sidans verktyg. Du kan aktivera eller inaktivera det här beteendet när du har kört `prep` kommandot genom att uppdatera det genererade Helm-diagrammet.

`prep`Kommandot kommer inte att ersätta några befintliga Dockerfiles-eller Helm-diagram som du har i projektet. Om ett befintligt Dockerfile-eller Helm-diagram använder samma namngivnings konvention som de filer som genereras av `prep` kommandot `prep` kommer kommandot att hoppa över genereringen av filerna. Annars `prep` genererar kommandot ett eget Dockerfile-eller Helm-diagram längs de befintliga filerna.

> [!IMPORTANT]
> I Azure dev Spaces används Dockerfile-och Helm-diagrammet för ditt projekt för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet skapas och körs.

`prep`Kommandot genererar även en `azds.yaml` fil i projektets rot. Azure dev Spaces använder den här filen för att skapa, installera, konfigurera och köra ditt program. Den här konfigurations filen visar platsen för ditt Dockerfile-och Helm-diagram och ger även ytterligare konfiguration ovanpå dessa artefakter.

Här är ett exempel på en azds. yaml-fil som skapats med [.net Core-exempel program](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`azds.yaml`Filen som genereras av `prep` kommandot är avsedd att fungera för ett enkelt scenario med en enda projekt utveckling. Om det aktuella projektet har ökad komplexitet kan du behöva uppdatera filen när du har kört `prep` kommandot. Ditt projekt kan till exempel kräva vissa ändringar i din build-eller start process utifrån dina utvecklings-eller fel söknings behov. Du kan också ha flera program i projektet, vilket kräver flera Bygg processer eller ett annat build-innehåll.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kör din kod i ditt utvecklings utrymme finns i [så här kör du din kod med Azure dev Spaces][how-it-works-up].

[how-it-works-up]: how-dev-spaces-works-up.md