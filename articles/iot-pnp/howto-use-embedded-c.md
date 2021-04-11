---
title: Använda IoT Plug and Play på begränsade enheter | Microsoft Docs
description: Lär dig hur du kan implementera IoT Plug and Play på begränsade enheter med antingen SDK för Embedded C eller Azure återställnings tider.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cfa71e02688d5bf63d894ae42dabab7da1d4174c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057322"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementera IoT Plug and Play på begränsade enheter

Om du utvecklar för *begränsade enheter* kan du använda IoT plug and Play med [Azure SDK: n för inbäddade klient bibliotek för C IoT](https://aka.ms/embeddedcsdk) eller med [Azure återställnings tider](/azure/rtos/overview-rtos). Den här artikeln innehåller länkar och resurser för dessa begränsade scenarier.

## <a name="use-the-sdk-for-embedded-c"></a>Använd SDK för Embedded C

SDK för Embedded C erbjuder en Lightweight-lösning för att ansluta begränsade enheter till Azure IoT-tjänster, inklusive användning av [IoT plug and Play konventioner](concepts-convention.md). Följande länkar innehåller exempel på en riktig enhet och för utbildning och fel sökning.

### <a name="use-a-real-device"></a>Använda en riktig enhet

En fullständig självstudie från slut punkt till slut punkt med SDK för Embedded C, Device Provisioning-tjänsten och IoT-Plug and Play på en riktig enhet finns i [syfte PIC-IoT wx Development Board att ansluta till Azure via IoT Hub Device Provisioning service](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Inledande exempel

SDK för Embedded C-lagringsplatsen innehåller [flera exempel](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) som visar hur du använder IoT-plug and Play:

> [!NOTE]
> De här exemplen visas i Windows och Linux för utbildning och fel sökning. I ett produktions scenario är exemplen endast avsedda för begränsade enheter.

- [Termostat-exempel med SDK för Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Exempel på temperatur styrenhet med SDK för Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Använda Azure-återställnings tider

Azure återställnings tider innehåller ett lätt lager som lägger till inbyggd anslutning till Azure IoT Cloud Services. Det här lagret är en enkel mekanism för att ansluta begränsade enheter till Azure IoT samtidigt som du använder avancerade funktioner i Azure återställnings tider. Läs mer i [Microsoft Azure återställnings tider](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Verktygs kedjor

Azure återställnings tider-exemplen tillhandahålls av olika typer av IDE-och verktygskedjan-kombinationer, till exempel:

- IAR: IAR: s [inbäddade Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake: Bygg ovanpå [cmake](https://cmake.org/) build system och [GNU arm Embedded verktygskedjan](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: NXP s [MCUXPRESSO IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicroelectronic s [STM32CUBE IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: mikrochip: s [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>Exempel

Exempel som visar hur du kommer igång på olika enheter med Azure återställnings tider och IoT Plug and Play finns i följande tabell:

Tillverkare | Enhet | Exempel |
| --- | --- | --- |
| Mikrochip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [Gcc/cmake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [Gcc/cmake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E – IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [Gcc/cmake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I – IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [Gcc/cmake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om alternativen för att implementera IoT Plug and Play på begränsade enheter är ett föreslaget nästa steg att lära dig mer om [iot plug and Play-konventionerna](concepts-convention.md).