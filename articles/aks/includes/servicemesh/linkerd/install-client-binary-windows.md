---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81736812"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Hämta och installera Linkerd Linkerd-klientens binärfil

I ett PowerShell-baserat gränssnitt i Windows använder `Invoke-WebRequest` du för att ladda ned Linkerd-versionen på följande sätt:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

`linkerd`Klientens binärfil körs på klient datorn och gör att du kan interagera med Linkerd-tjänstens nät. Använd följande kommandon för att installera Linkerd- `linkerd` klientens binärfil i ett PowerShell-baserat gränssnitt i Windows. Dessa kommandon kopierar `linkerd` klientens binärfil till en Linkerd-mapp och gör den tillgänglig både direkt (i aktuellt gränssnitt) och permanent (mellan omstarter av gränssnittet) via `PATH` . Du behöver inte utökade privilegier (admin) för att köra dessa kommandon och du behöver inte starta om gränssnittet.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```