---
title: Uppdatera inbyggd Azure Kinect DK-programvara
description: Lär dig hur du uppdaterar den inbyggda program varan för Azure Kinects DK med hjälp av Azure Kinects inbyggda program vara.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, inbyggd program vara, uppdatering, återställning
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94356234"
---
# <a name="update-azure-kinect-dk-firmware"></a>Uppdatera inbyggd Azure Kinect DK-programvara

Det här dokumentet ger vägledning om hur du uppdaterar inbyggd program vara på din Azure Kinect DK.

Azure Kinect DK uppdaterar inte den inbyggda program varan automatiskt. Du kan använda [Azure Kinect-verktyget för inbyggd program](azure-kinect-firmware-tool.md) vara för att uppdatera inbyggd program vara manuellt till den senaste tillgängliga versionen.

## <a name="prepare-for-firmware-update"></a>Förbered för uppdatering av inbyggd program vara

1. [Hämta SDK](sensor-sdk-download.md).
2. Installera SDK:n.
3. På platsen för SDK-installation under (SDK-installations plats) \tools\ du söka:

    - AzureKinectFirmwareTool.exe
    - En inbyggd program vara. bin-fil i mappen inbyggd program vara, till exempel *AzureKinectDK_Fw_1.5.926614. bin*.

4. Anslut din enhet till värd datorn och slå sedan på den.

> [!IMPORTANT]
> Håll USB-och strömförsörjningen ansluten under uppdateringen av den inbyggda program varan. Om du tar bort anslutningen under uppdateringen kan den inbyggda program varan vara i ett skadat tillstånd.

## <a name="update-device-firmware"></a>Uppdatera enhetens inbyggda programvara

1. Öppna en kommando tolk i mappen (SDK-installations plats) \tools\.
2. Uppdatera inbyggd program vara med verktyget för inbyggd program vara i Azure Kinect

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Exempel:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Vänta tills uppdateringen av den inbyggda program varan har slutförts. Det kan ta några minuter beroende på bild storleken.

### <a name="verify-device-firmware-version"></a>Verifiera den inbyggda enhetens version

1. Kontrol lera att den inbyggda program varan har uppdaterats.

    `AzureKinectFirmwareTool.exe -q`

2. Visa följande exempel.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Om du ser ovanstående utdata uppdateras den inbyggda program varan.

4. Efter uppdateringen av den inbyggda program varan kan du köra [Azure Kinect Viewer](azure-kinect-viewer.md) för att kontrol lera att alla sensorer fungerar som förväntat.

## <a name="troubleshooting"></a>Felsökning

Uppdateringar av inbyggd program vara kan inte utföras av flera orsaker. När en uppdatering av den inbyggda program varan Miss lyckas kan du prova följande åtgärder för minskning:

1. Försök att köra uppdaterings kommandot för inbyggd program vara en andra gång.

2. Bekräfta att enheten fortfarande är ansluten genom att fråga efter versionen för den inbyggda program varan.        AzureKinectFirmareTool.exe

3. Om allt annat Miss lyckas följer du [återställnings](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) stegen för att återställa den inbyggda program varan och försöka igen.

Ytterligare information finns i [Microsofts support sidor](./index.yml)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Inbyggd Azure Kinect-verktyg](azure-kinect-firmware-tool.md)