---
title: 'Snabb start: utveckla Defender Micro-agenten från käll koden (för hands version)'
description: I den här snabb starten får du lära dig om Micro-agenten som innehåller en infrastruktur som kan användas för att anpassa distributionen.
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: a3a8f82989d6abbaf2657e4b45638c77b3b2f704
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384605"
---
# <a name="quickstart-build-the-defender-micro-agent-from-source-code-preview"></a>Snabb start: utveckla Defender Micro-agenten från käll koden (för hands version)

Micro-agenten innehåller en infrastruktur som kan användas för att anpassa distributionen. Om du vill visa en lista över tillgängliga konfigurations parametrar tittar du på `configs/LINUX_BASE.conf` filen.

Ändra bas filen för en enskild distribution `.conf` . 

Om du behöver mer än en distribution kan du ärva från bas konfigurationen och åsidosätta dess värden. 

För att åsidosätta värdena:

1. Skapa en ny `.dist` fil.

1. Lägg till `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` överst.
 
1. Definiera nya värden för det du behöver, exempel: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Ge `.dist` filen en referens när du skapar den. Exempel: 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="prerequisites"></a>Krav

1. Kontakta din konto ansvarige för att be om åtkomst till Defender för IoT-källkod.
 
1. Klona eller extrahera käll koden till en mapp på disken.

1. Navigera till den katalogen.

1. Hämta undermodulerna med följande kod:

    ```bash
    git submodule update --init
    ```
    
1. Hämta sedan undermodulerna för Azure IoT SDK med följande kod: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Lägg till en körnings behörighet och kör installations skriptet för utvecklings miljön:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Skapa en katalog för build-utdata: 

    ```bash
    mkdir cmake 
    ```

1. Valfritt Hämta och installera [VSCode](https://code.visualstudio.com/download ) 

1. Valfritt Installera [C/C++-tillägget](https://code.visualstudio.com/docs/languages/cpp ) för VSCode.-None

## <a name="baseline-configuration-signing"></a>Signering av bas linje konfiguration 

Agenten verifierar äktheten hos konfigurationsfiler som är placerade på disken för att minimera manipulering, som standard.

Du kan stoppa den här processen genom att definiera Preprocessor-flaggan `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` .

Vi rekommenderar inte att du inaktiverar signatur kontrollen för produktions miljöer. 

Kontakta Defender för IoT-teamet om du behöver en annan konfiguration för produktions scenarier. 

## <a name="building-the-defender-iot-micro-agent"></a>Skapa Defender IoT Micro-agenten 

1. Öppna katalogen med VSCode 

1. Navigera till `cmake` katalogen. 

1. Kör följande kommando: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Till exempel: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Nästa steg

[Konfigurera din Azure Defender för IoT-lösning](quickstart-configure-your-solution.md).
