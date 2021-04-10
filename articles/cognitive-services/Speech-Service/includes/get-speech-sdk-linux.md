---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: cdcb61249f6cb8f7b60c891dd4899d2e5cedbae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612443"
---
:::row:::
    :::column span="3":::
        Tal-SDK: n stöder endast **Ubuntu 16.04/18.04/20.04**, **Debian 9/10**, **Red Hat Enterprise Linux (RHEL) 7/8** och **CentOS 7/8** för följande mål arkitekturer vid användning med Linux:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) och ARM64 (Debian/Ubuntu) för C++-utveckling
- x64, ARM32 (Debian/Ubuntu) och ARM64 (Debian/Ubuntu) för Java
- x64, ARM32 (Debian/Ubuntu) och ARM64 (Debian/Ubuntu) för .NET Core
- x64 för python

> [!IMPORTANT]
> För C# på Linux-ARM64 krävs .NET Core 3. x (dotNet-SDK-3. x-paket).

> [!NOTE]
> Om du vill använda tal-SDK i Alpine Linux skapar du en Debian chroot-miljö som dokumenterad i Alpine Linux wiki på att [köra glibc-program](https://wiki.alpinelinux.org/wiki/Running_glibc_programs)och följer sedan Debian-instruktionerna här.

### <a name="system-requirements"></a>Systemkrav

För ett inbyggt program är tal-SDK: n beroende av `libMicrosoft.CognitiveServices.Speech.core.so` . Kontrol lera att mål arkitekturen (x86, x64) matchar programmet. Beroende på Linux-versionen kan det krävas ytterligare beroenden.

- De delade biblioteken för GNU C-biblioteket (inklusive programmerings biblioteket för POSIX-trådar `libpthreads` )
- OpenSSL-biblioteket ( `libssl.so.1.0.0` eller `libssl.so.1.0.2` )
- Det delade biblioteket för ALSA-program ( `libasound.so.2` )

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> Om libssl 1.0. x inte är tillgängligt installerar du libssl 1.1 i stället.

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> Om libssl 1.0. x inte är tillgängligt installerar du libssl 1.1 i stället.

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 och CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - På RHEL/CentOS 7 följer du anvisningarna för [hur du konfigurerar RHEL/CentOS 7 för tal-SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
