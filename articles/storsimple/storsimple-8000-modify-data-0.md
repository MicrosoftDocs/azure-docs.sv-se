---
title: Ändra DATA 0-inställningar på StorSimple 8000-seriens enhet | Microsoft Docs
description: Lär dig hur du använder Windows PowerShell för StorSimple för att konfigurera om DATA 0-nätverks gränssnittet på din StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 85d7114f419266124d0d23368b24700af025758a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961047"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Ändra inställningarna för nätverks gränssnittet för DATA 0 på din StorSimple 8000-serie enhet

## <a name="overview"></a>Översikt

Din Microsoft Azure StorSimple-enhet har sex nätverks gränssnitt, från DATA 0 till DATA 5. DATA 0-gränssnittet konfigureras alltid via Windows PowerShell-gränssnittet eller serie konsolen och är automatiskt moln aktiverat. Observera att du inte kan konfigurera nätverks gränssnittet för DATA 0 via Azure Portal.

DATA 0-gränssnittet konfigureras först via installations guiden under den första distributionen av StorSimple-enheten. När enheten är i drift läge kan du behöva konfigurera om DATA 0-inställningar. Den här självstudien innehåller två metoder för att ändra nätverks inställningar för DATA 0, både via Windows PowerShell för StorSimple.

När du har läst den här självstudien kommer du att kunna:

* Ändra nätverks inställningen DATA 0 via installations guiden
* Ändra nätverks inställningar för DATA 0 via `Set-HcsNetInterface` cmdleten

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Ändra nätverks inställningar för DATA 0 via installations guiden
Du kan konfigurera om DATA 0 nätverks inställningar genom att ansluta till Windows PowerShell-gränssnittet för StorSimple-enheten och starta en session med installations guiden. Utför följande steg för att ändra inställningarna för DATA 0:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Ändra nätverks inställningar för DATA 0 via installations guiden
1. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**. När du uppmanas ange **enhetens administratörs lösen ord**. Standard lösen ordet är `Password1` .
2. Skriv följande i kommandotolken:
   
    `Invoke-HcsSetupWizard`
3. En installations guide visas för att hjälpa dig att konfigurera enhetens DATA 0-gränssnitt. Ange nya värden för IP-adressen, gatewayen och nät masken.

> [!NOTE]
> De fasta styrenheterna för IP-adresser måste konfigureras om via bladet **nätverks inställningar** på StorSimple-enheten i Azure Portal. Mer information finns i [ändra nätverks gränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Ändra nätverks inställningar för DATA 0 via Set-HcsNetInterface cmdlet
Ett annat sätt att konfigurera om DATA 0-nätverks gränssnittet är genom att använda `Set-HcsNetInterface` cmdleten. Cmdleten körs från Windows PowerShell-gränssnittet på din StorSimple-enhet. När du använder den här proceduren kan styrenhetens fasta IP-adresser också konfigureras här. Utför följande steg för att ändra inställningarna för DATA 0: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Ändra nätverks inställningar för DATA 0 via Set-HcsNetInterface cmdlet
1. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**. När du uppmanas ange enhetens administratörs lösen ord. Standard lösen ordet är `Password1` .
2. Skriv följande i kommandotolken:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    I vinkelparenteser anger du följande värden för DATA 0:
   
   * IPv4-adress
   * IPv4-Gateway
   * IPv4-nätmask
   * Fast IPv4-adress för styrenhet 0
   * Fast IPv4-adress för styrenhet 1
     
     Om du vill ha mer information om hur du använder denna cmdlet går du till [Windows PowerShell för StorSimple cmdlet-referens](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)).

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera andra nätverks gränssnitt än DATA 0 kan du använda [Konfigurera nätverks inställningar i Azure Portal](storsimple-8000-modify-device-config.md). 
* Om du får problem när du konfigurerar nätverks gränssnitten kan du läsa [Felsök distributions problem](./storsimple-8000-troubleshoot-deployment.md).