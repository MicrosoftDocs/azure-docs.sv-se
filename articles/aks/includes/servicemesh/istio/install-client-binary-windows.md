---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: a02c17013a205ccc0da85536b491d467ef72fa48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666736"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Hämta och installera Istio istioctl-klientens binärfil

I ett PowerShell-baserat gränssnitt i Windows använder `Invoke-WebRequest` du för att ladda ned Istio-versionen och extrahera sedan med `Expand-Archive` enligt följande:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.7.3"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-win.zip" -OutFile "istioctl-$ISTIO_VERSION.zip"
Expand-Archive -Path "istioctl-$ISTIO_VERSION.zip" -DestinationPath .
```

`istioctl`Klientens binärfil körs på klient datorn och gör att du kan installera och hantera Istio i ditt AKS-kluster. Använd följande kommandon för att installera Istio- `istioctl` klientens binärfil i ett PowerShell-baserat gränssnitt i Windows. Dessa kommandon kopierar `istioctl` klientens binärfil till en Istio-mapp och gör den tillgänglig både direkt (i aktuellt gränssnitt) och permanent (mellan omstarter av gränssnittet) via `PATH` . Du behöver inte utökade privilegier (admin) för att köra dessa kommandon och du behöver inte starta om gränssnittet.

```powershell
# Copy istioctl.exe to C:\Istio
New-Item -ItemType Directory -Force -Path "C:\Istio"
Move-Item -Path .\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```