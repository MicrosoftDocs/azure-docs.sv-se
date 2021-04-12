---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080374"
---
## <a name="download-and-install-the-osm-client-binary"></a>Hämta och installera OSM-klientens binärfil

Använd för att hämta OSM-versionen i ett bash-baserat gränssnitt på Linux-eller [Windows-undersystem för Linux][install-wsl] `curl` och extrahera sedan med `tar` enligt följande:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

`osm`Klientens binärfil körs på klient datorn och gör att du kan hantera OSM i ditt AKS-kluster. Använd följande kommandon för att installera OSM- `osm` klientens binärfil i ett bash-baserat gränssnitt på Linux-eller [Windows-undersystem för Linux][install-wsl]. Dessa kommandon kopierar `osm` klientens binärfil till standard platsen för användar program i `PATH` .

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Du kan kontrol lera att `osm` klient biblioteket har lagts till korrekt i sökvägen och dess versions nummer med följande kommando.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
