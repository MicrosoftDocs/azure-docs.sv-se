---
title: Det går inte att fjärr skrivbord till Azure Virtual Machines på grund av statisk IP | Microsoft Docs
description: Lär dig hur du felsöker RDP-problem som orsakas av statisk IP i Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: bf19a6f77a87f2424f9e7b889e48119d57d1e2e5
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97820990"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Det går inte att fjärr skrivbord till Azure Virtual Machines på grund av statisk IP

I den här artikeln beskrivs ett problem där du inte kan fjärrans luta till Azure Virtuella Windows-datorer (VM) när en statisk IP-adress har kon figurer ATS på den virtuella datorn.


## <a name="symptoms"></a>Symtom

När du gör en RDP-anslutning till en virtuell dator i Azure får du följande fel meddelande:

**Fjärr skrivbord kan inte ansluta till fjärrdatorn av något av följande skäl:**

1. **Fjärråtkomst till servern är inte aktive rad**

2. **Fjärrdatorn är avstängd**

3. **Fjärrdatorn är inte tillgänglig i nätverket**

**Kontrol lera att fjärrdatorn är påslagen och ansluten till nätverket och att fjärråtkomst har Aktiver ATS.**

När du kontrollerar skärm bilden i [startdiagnostiken](../troubleshooting/boot-diagnostics.md) i Azure Portal ser du att den virtuella datorn startar normalt och väntar på autentiseringsuppgifter på inloggnings skärmen.

## <a name="cause"></a>Orsak

Den virtuella datorn har en statisk IP-adress som definieras i nätverks gränssnittet i Windows. Den här IP-adressen skiljer sig från den adress som har definierats i Azure Portal.

## <a name="solution"></a>Lösning

Innan du följer de här stegen ska du ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

Lös problemet genom att använda seriell kontroll för att aktivera DHCP eller [återställa nätverks gränssnittet](reset-network-interface.md) för den virtuella datorn.

### <a name="use-serial-control"></a>Använd seriell kontroll

1. Anslut till [serie konsolen och öppna cmd-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om serie konsolen inte är aktive rad på den virtuella datorn, se [Återställ nätverks gränssnitt](reset-network-interface.md).
2. Kontrol lera om DHCP är inaktiverat i nätverks gränssnittet:

    ```console
    netsh interface ip show config
    ```

3. Om DHCP är inaktive rad återställer du konfigurationen av nätverks gränssnittet för att använda DHCP:

    ```console
    netsh interface ip set address name="<NIC Name>" source=dhcp
    ```

    Om du till exempel arbetar med gränssnittet "Ethernet 2" kör du följande kommando:

    ```console
    netsh interface ip set address name="Ethernet 2" source=dhcp
    ```

4. Fråga IP-konfigurationen igen för att se till att nätverks gränssnittet nu är rätt konfigurerat. Den nya IP-adressen ska matcha den som tillhandahålls av Azure.

    ```console
    netsh interface ip show config
    ```

    Du behöver inte starta om den virtuella datorn just nu. Den virtuella datorn kommer att gå att komma åt.

Om du vill konfigurera den statiska IP-adressen för den virtuella datorn kan du läsa mer i [Konfigurera statiska IP-adresser för en virtuell dator](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).
