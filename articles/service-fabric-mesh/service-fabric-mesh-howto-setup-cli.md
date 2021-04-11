---
title: Konfigurera Azure Service Fabric maskor CLI
description: Service Fabric Mesh-kommandoradsgränssnittet (CLI) krävs för att distribuera och hantera resurser lokalt och i Azure Service Fabric Mesh. Så här konfigurerar du det.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: f8a9c26e65ef911ad85806c72c7946947379ab72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613349"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Konfigurera Service Fabric Mesh CLI

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Service Fabric Mesh-kommandoradsgränssnittet (CLI) krävs för att distribuera och hantera resurser lokalt och i Azure Service Fabric Mesh. Så här konfigurerar du det.

Det finns tre typer av CLI som kan användas och de sammanfattas i tabellen nedan.

| CLI-modul | Målmiljö |  Beskrivning | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Primärt CLI som gör att du kan distribuera dina program och hantera resurser mot Azure Service Fabric Mesh-miljön. 
| sfctl | Lokala kluster | Service Fabric-CLI som möjliggör distribution och testning av Service Fabric-resurser mot lokala kluster.  
| Maven CLI | Lokala kluster och Azure Service Fabric Mesh | En omslutning `az mesh` `sfctl` som gör att Java-utvecklare kan använda en välbekant kommando rads upplevelse för lokal och Azure-utvecklings miljö.  

För förhandsversionen är Azure Service Fabric Mesh CLI skrivet som ett tillägg till Azure CLI. Du kan installera det i Azure Cloud Shell eller en lokal installation av Azure CLI. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.67 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Installera Azure Service Fabric Mesh CLI

Om du inte redan har gjort det installerar du modulen Azure Service Fabrics mask CLI-tillägg med följande kommando: 
 
```azurecli-interactive
az extension add --name mesh
```

Om den redan är installerad uppdaterar du din befintliga Azure Service Fabric nät CLI-modul med följande kommando:

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>Installera Service Fabric CLI (sfctl) 

Följ instruktionerna i [Konfigurera Service Fabric CLI](../service-fabric/service-fabric-cli.md). Modulen **sfctl** kan användas för distribution av program baserat på resursmodellen mot Service Fabric-kluster på den lokala datorn. 

## <a name="install-the-maven-cli"></a>Installera Maven CLI 

För att kunna använda Maven CLI måste följande vara installerat på datorn: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh) – För Azure Service Fabric Mesh som mål 
* SFCTL (sfctl) – För lokala kluster som mål 

Maven CLI för Service Fabric är fortfarande i förhandsversion. 

Om du vill använda Maven-pluginprogrammet i Maven Java-appen lägger du till följande kodavsnitt i filen pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Läs avsnittet med [Maven CLI-referensen](service-fabric-mesh-reference-maven.md) om du vill veta mer om detaljerad användning.

## <a name="next-steps"></a>Nästa steg

Du kan även konfigurera [Windows-utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Få svar på [vanliga frågor och problem](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
