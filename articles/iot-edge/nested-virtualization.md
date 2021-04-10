---
title: Kapslad virtualisering för Azure IoT Edge för Linux i Windows | Microsoft Docs
description: Lär dig mer om hur du navigerar i kapslad virtualisering i Azure IoT Edge för Linux i Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610039"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Kapslad virtualisering för Azure IoT Edge för Linux i Windows
Det finns två former av kapslad virtualisering som är kompatibel med Azure IoT Edge för Linux i Windows. Användare kan välja att distribuera via en lokal virtuell dator eller virtuell Azure-dator. Den här artikeln ger användarna klarhet om vilka alternativ som passar bäst för ditt scenario och ger inblick i konfigurations kraven.

> [!NOTE]
>
> Se till att aktivera ett [netowrking-alternativ](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) för kapslad virtualisering. Om du inte gör det leder det till EFLOW-installations fel. 

## <a name="deployment-on-local-vm"></a>Distribution på lokal virtuell dator
Detta är den grundläggande metoden för virtuella Windows-datorer som är värdar för Azure IoT Edge för Linux i Windows. För det här fallet måste kapslad virtualisering aktive ras innan du påbörjar distributionen. Läs [köra Hyper-V i en virtuell dator med kapslad virtualisering](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) för mer information om hur du konfigurerar det här scenariot.

Om du använder Windows Server kontrollerar du att du [installerar Hyper-V-rollen](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Distribution på virtuella Azure-datorer
Observera att det inte finns någon extern växel enligt design om du väljer att distribuera på virtuella Azure-datorer. Azure IoT Edge för Linux på Windows är inte heller kompatibelt på en virtuell Azure-dator som kör Server-SKU, om inte ett speciellt skript körs som tar upp en standard växel. Mer information om hur du hämtar en standard växel finns i [avsnittet Windows Server](#windows-server) nedan. 

> [!NOTE]
>
> Alla virtuella Azure-datorer som ska vara värdar för EFLOW måste vara en virtuell dator som [stöder kapslad virtualisering](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Begränsad användning av extern växel
I alla situationer där den virtuella datorn inte kan erhålla en IP-adress via en extern växel använder distributions funktionen automatiskt den interna standard växeln för distributionen. Det innebär att hanteringen av Azure IoT Edge för virtuella Linux-datorer bara kan köras på själva mål enheten (d.v.s. att ansluta till Azure IoT Edge för virtuella Linux-datorer via WAC PowerShell SSH-tillägget är bara möjlig på localhost).

## <a name="windows-server"></a>Windows Server
Observera att Azure IoT Edge för Linux i Windows inte automatiskt stöder standard växeln för Windows Server-användare.

* Konsekvenser för den lokala virtuella datorn: se till att den virtuella datorn EFLOW kan hämta en IP-adress via den externa växeln.

* Konsekvenser för virtuell Azure-dator: eftersom det inte finns någon extern växel på virtuella Azure-datorer går det inte att distribuera EFLOW innan du konfigurerar en intern växel på servern.

Det finns ingen standard växel på Server-SKU: er som standard (oavsett om Server-SKU: n körs på en virtuell Azure-dator eller inte). När du distribuerar på en virtuell Azure-dator där den externa växeln inte kan användas, måste standard växeln konfigureras och konfigureras manuellt innan Azure IoT Edge för Linux på Windows-distribution. Vår distributions funktion täcker detta eftersom det krävs IP-konfiguration för den interna växeln, en NAT-konfiguration och installation och konfiguration av en DHCP-server. Vår distributions funktion i offentlig för hands version säger att den inte FIDDLE med de här inställningarna, inte att försämra nätverkskonfigurationer på produktiva distributioner.

* Relevant information om hur du ställer in standard växeln manuellt finns här: [så här aktiverar du kapslad virtualisering i Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* Dokumentation om hur du konfigurerar en DHCP-server för det här scenariot finns här: [distribuera DHCP med Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
