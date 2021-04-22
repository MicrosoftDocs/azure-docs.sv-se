---
title: Använda IoT Plug and Play på begränsade enheter | Microsoft Docs
description: Lär dig hur du kan implementera IoT-Plug and Play på begränsade enheter med antingen SDK för Embedded C eller Azure RTOS.
author: EliotSeattle
ms.author: eliotgra
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6c792fbbb44b7dc769e7cdc56850684bd69ea40b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864145"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementera IoT Plug and Play på begränsade enheter

Om du utvecklar för begränsade enheter kan du använda IoT-Plug and Play med Azure SDK för [Embedded C IoT-klientbibliotek](https://aka.ms/embeddedcsdk) eller [med Azure RTOS](/azure/rtos/overview-rtos). Den här artikeln innehåller länkar och resurser för dessa begränsade scenarier.

## <a name="use-the-sdk-for-embedded-c"></a>Använda SDK för Embedded C

SDK för Embedded C erbjuder en enkel lösning för att ansluta begränsade enheter till Azure IoT-tjänster, inklusive [användning av IoT Plug and Play konventionerna](concepts-convention.md). Följande länkar innehåller exempel för verkliga enheter och för utbildnings- och felsökningsändamål.

### <a name="use-a-real-device"></a>Använda en riktig enhet

En fullständig självstudie från start till slut med SDK för Embedded C, Device Provisioning Service och IoT Plug and Play på en verklig enhet finns i [Repurpose PIC-IoT Wx Development Board to Connect to Azure through IoT Hub Device Provisioning Service](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Introduktionsexempel

SDK för Embedded C-lagringsplatsen [innehåller flera exempel](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) som visar hur du använder IoT-Plug and Play:

> [!NOTE]
> De här exemplen visas i Windows och Linux i utbildnings- och felsökningssyfte. I ett produktionsscenario är exemplen endast avsedda för begränsade enheter.

- [Termostatexempel med SDK för Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Exempel på temperaturkontrollant med SDK för Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Använda Azure RTOS

Azure RTOS ett enkelt lager som lägger till inbyggd anslutning till Azure IoT-molntjänster. Det här lagret ger en enkel mekanism för att ansluta begränsade enheter till Azure IoT med hjälp av de avancerade funktionerna i Azure RTOS. Mer information finns i Vad [är Microsoft Azure RTOS.](/azure/rtos/overview-rtos)

### <a name="toolchains"></a>Verktygskedjor

De Azure RTOS exemplen tillhandahålls med olika typer av kombinationer av IDE och verktygskedja, till exempel:

- IAR: IAR:s [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake: Bygg ovanpå [CMake-byggsystemet](https://cmake.org/) med öppen källkod och [GNU Arm Embedded-verktygskedja](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: NXP:s [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicro automatik s [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: Microchips [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>Exempel

Exempel som visar hur du kommer igång på olika enheter med Azure RTOS och IoT Plug and Play finns i följande tabell:

Tillverkare | Enhet | Exempel |
| --- | --- | --- |
| Mikrochip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| Nxp | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| Stmicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| Stmicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| Stmicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om alternativen för att implementera IoT-Plug and Play på begränsade enheter föreslår vi att du går vidare med att lära dig mer om [IoT Plug and Play konventionerna](concepts-convention.md).