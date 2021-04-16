---
title: Kapslad virtualisering för Azure IoT Edge för Linux i Windows | Microsoft Docs
description: Lär dig mer om hur du navigerar i kapslad virtualisering Azure IoT Edge för Linux i Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4f01362fd9342c1f508f165b34e121a11e8d07e2
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496596"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Kapslad virtualisering för Azure IoT Edge för Linux i Windows
Det finns två former av kapslad virtualisering som är kompatibel med Azure IoT Edge för Linux i Windows. Användare kan välja att distribuera via en lokal virtuell dator eller virtuell Azure-dator. Den här artikeln ger användarna tydlighet om vilket alternativ som passar bäst för deras scenario och ger inblick i konfigurationskraven.

> [!NOTE]
>
> Se till att aktivera ett [nätverksalternativ](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) för kapslad virtualisering. Om du inte gör det resulterar det i EFLOW-installationsfel. 

## <a name="deployment-on-local-vm"></a>Distribution på lokal virtuell dator
Det här är baslinjen för alla virtuella Windows-datorer som är Azure IoT Edge för Linux i Windows. I det här fallet måste kapslad virtualisering aktiveras innan distributionen startas. Mer [information om hur du konfigurerar det här scenariot finns i Köra Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) på en virtuell dator med kapslad virtualisering.

Om du använder Windows Server kontrollerar du att du [installerar Hyper-V-rollen](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Distribution på virtuella Azure-datorer
Observera att det inte finns någon extern växel som är design om du väljer att distribuera på virtuella Azure-datorer. Azure IoT Edge för Linux i Windows är inte heller kompatibelt på en virtuell Azure-dator som kör server-SKU:n, såvida inte ett specifikt skript körs som skapar en standardväxel. Mer information om hur du använder en standardväxel finns i [avsnittet Windows Server](#windows-server) nedan. 

> [!NOTE]
>
> Alla virtuella Azure-datorer som ska vara värdar för EFLOW måste vara en virtuell dator som stöder [kapslad virtualisering](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Begränsad användning av extern växel
I alla scenarier där den virtuella datorn inte kan hämta en IP-adress via en extern växel använder distributionsfunktionen automatiskt den interna standardväxeln för distributionen. Det innebär att hanteringen av Azure IoT Edge för virtuella Linux-datorer endast kan köras på själva målenheten (dvs. det går bara att ansluta till Azure IoT Edge för virtuella Linux-datorer via WAC PowerShell SSH-tillägget på localhost).

## <a name="windows-server"></a>Windows Server
Observera för Windows Server-användare att Azure IoT Edge för Linux i Windows inte automatiskt stöder standardväxeln.

* Konsekvenser för lokal virtuell dator: Se till att den virtuella EFLOW-datorn kan hämta en IP-adress via den externa växeln.

* Konsekvenser för virtuella Azure-datorer: Eftersom det inte finns någon extern växel på virtuella Azure-datorer går det inte att distribuera EFLOW innan du ställer in en intern växel på servern.

Det finns ingen standardväxel på server-SKU:er som standard (oavsett om server-SKU:n körs på en virtuell Azure-dator eller inte). När du distribuerar på en virtuell Azure-dator där den externa växeln inte kan användas måste standardväxeln konfigureras manuellt innan du Azure IoT Edge för Linux på Windows-distribution. Vår distributionsfunktioner omfattar detta eftersom det kräver IP-konfiguration för den interna växeln, en NAT-konfiguration och installation och konfiguration av en DHCP-server. Vår distributionsfunktion i den offentliga förhandsversionen säger att den inte är begränsad till de här inställningarna för att inte försämra nätverkskonfigurationer på produktiva distributioner.

* Relevant information om hur du ställer in standardväxeln manuellt finns här: Så här aktiverar du [kapslad virtualisering i Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* Dokumentation om hur du ställer in en DHCP-server för det här scenariot finns här: [Distribuera DHCP med Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
