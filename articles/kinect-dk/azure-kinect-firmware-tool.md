---
title: Azure Kinect Firmware Tool
description: Lär dig hur du frågar och uppdaterar inbyggd enhets program vara med hjälp av den inbyggda Azure Kinect-verktyget.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, inbyggd program vara, uppdatering
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277678"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Verktyget Azure Kinect DK firmware

Verktyget för inbyggd program vara i Azure Kinect kan användas för att fråga och uppdatera enhetens inbyggda program vara för Azure Kinect DK.

## <a name="list-connected-devices"></a>Lista anslutna enheter

Du kan hämta en lista över anslutna enheter med alternativet-l.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>Kontrol lera den inbyggda enhetens version

Du kan kontrol lera de aktuella versionerna av den första anslutna enheten med hjälp av alternativet-q, till exempel `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

Om det finns mer än en ansluten enhet kan du ange vilken enhet du vill fråga genom att lägga till det fullständiga serie numret till kommandot, till exempel:

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>Uppdatera enhetens inbyggda programvara

Den vanligaste användningen av det här verktyget är att uppdatera enhetens inbyggda program vara. Uppdatera genom att anropa verktyget med `-u` alternativet. Det kan ta några minuter att uppdatera den inbyggda program varan, beroende på vilka filer för inbyggd program vara som måste uppdateras.

Steg för steg uppdaterings instruktioner för inbyggd program vara finns i [uppdatering för Azure Kinect firmware](update-device-firmware.md).  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

Om det finns fler än en ansluten enhet kan du ange vilken enhet du vill fråga genom att lägga till det fullständiga serie numret i kommandot.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>Återställ enhet

En ansluten Azure Kinect DK kan återställas med alternativet-r, om du måste få enheten i ett känt tillstånd.

Om det finns fler än en ansluten enhet kan du ange vilken enhet du vill fråga genom att lägga till det fullständiga serie numret i kommandot.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>Kontrol lera inbyggd program vara

Genom att inspektera den inbyggda program varan kan du hämta versions information från en inbyggd fack fil innan du uppdaterar en faktisk enhet.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>Alternativ för uppdaterings verktyget för inbyggd program vara

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Stegvisa instruktioner för uppdatering av enhetens inbyggda program vara](update-device-firmware.md)
