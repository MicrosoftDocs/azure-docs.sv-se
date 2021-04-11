---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 8ac70027f7483fbf0131c31a5ba3631ed1d4ff9a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080375"
---
## <a name="download-and-install-the-osm-client-binary"></a>Hämta och installera OSM-klientens binärfil

I ett PowerShell-baserat gränssnitt i Windows använder `Invoke-WebRequest` du för att ladda ned Istio-versionen och extrahera sedan med `Expand-Archive` enligt följande:

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.8.2"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

`osm`Klientens binärfil körs på klient datorn och gör att du kan hantera OSM-kontrollanten i ditt AKS-kluster. Använd följande kommandon för att installera OSM- `osm` klientens binärfil i ett PowerShell-baserat gränssnitt i Windows. Dessa kommandon kopierar `osm` klientens binärfil till en OSM-mapp och gör den tillgänglig både direkt (i aktuellt gränssnitt) och permanent (mellan omstarter av gränssnittet) via `PATH` . Du behöver inte utökade privilegier (admin) för att köra dessa kommandon och du behöver inte starta om gränssnittet.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
