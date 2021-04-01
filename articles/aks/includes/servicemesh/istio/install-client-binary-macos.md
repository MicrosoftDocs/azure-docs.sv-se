---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 5dc9686e4a9994a085cc9f4a4631e66b05d7949d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666741"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Hämta och installera Istio istioctl-klientens binärfil

I ett bash-baserat gränssnitt på macOS använder `curl` du för att hämta Istio-versionen och extraherar sedan med `tar` enligt följande:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

`istioctl`Klientens binärfil körs på klient datorn och gör att du kan installera och hantera Istio i ditt AKS-kluster. Använd följande kommandon för att installera Istio- `istioctl` klientens binärfil i ett bash-baserat gränssnitt på MacOS. Dessa kommandon kopierar `istioctl` klientens binärfil till standard platsen för användar program i `PATH` .

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Om du vill att kommando rads komplettering ska slutföras för Istio `istioctl` -klientens binärfil, så gör du det på följande sätt:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```