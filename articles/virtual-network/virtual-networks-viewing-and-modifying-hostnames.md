---
title: Visa och ändra värdnamn | Microsoft Docs
description: Visa och ändra värd namn för virtuella Azure-datorer, webb-och arbets roller för namn matchning
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ed250e3f32965fc450102fb14b93b93d6753ab3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222793"
---
# <a name="viewing-and-modifying-hostnames"></a>Visa och ändra värdnamn
Om du vill att roll instanserna ska refereras av värd namnet måste du ange värdet för värd namnet i tjänst konfigurations filen för varje roll. Det gör du genom att lägga till det önskade värd namnet i **vmName** -attributet för **roll** elementet. Värdet för attributet **vmName** används som bas för värd namnet för varje roll instans. Om **vmName** till exempel är *webrole* och det finns tre instanser av rollen, kommer värd namnen för instanserna att vara *webrole0*, *webrole1* och *webrole2*. Du behöver inte ange ett värdnamn för virtuella datorer i konfigurations filen, eftersom värd namnet för en virtuell dator är ifyllt baserat på namnet på den virtuella datorn. Mer information om hur du konfigurerar en Microsoft Azure-tjänst finns i [konfigurations schema för Azure-tjänsten (. cscfg-fil)](/previous-versions/azure/reference/ee758710(v=azure.100))

## <a name="viewing-hostnames"></a>Visa värdnamn
Du kan visa värd namnen för virtuella datorer och roll instanser i en moln tjänst genom att använda något av verktygen nedan.

### <a name="service-configuration-file"></a>Tjänst konfigurations fil
Du kan hämta tjänst konfigurations filen för en distribuerad tjänst från tjänstens **konfigurations blad i** Azure Portal. Du kan sedan leta efter attributet **vmName** för elementet **roll namn** för att se värd namnet. Tänk på att det här värd namnet används som bas för värd namnet för varje roll instans. Om **vmName** till exempel är *webrole* och det finns tre instanser av rollen, kommer värd namnen för instanserna att vara *webrole0*, *webrole1* och *webrole2*.

### <a name="remote-desktop"></a>Fjärrskrivbord
När du har aktiverat anslutning till fjärr skrivbord (Windows), Windows PowerShell-fjärrkommunikation (Windows) eller SSH (Linux och Windows) till dina virtuella datorer eller roll instanser, kan du Visa värd namnet från en aktiv anslutning till fjärr skrivbord på olika sätt:

* Ange hostname i kommando tolken eller SSH-terminalen.
* Skriv ipconfig/all i kommando tolken (endast Windows).
* Visa dator namnet i systeminställningarna (endast Windows).

### <a name="azure-service-management-rest-api"></a>Azure Service Management-REST API
Följ dessa instruktioner från en REST-klient:

1. Se till att du har ett klient certifikat för att ansluta till Azure Portal. Hämta ett klient certifikat genom att följa stegen i så här [: Hämta och importera publicerings inställningar och prenumerations information](/previous-versions/dynamicsnav-2013/dn385850(v=nav.70)). 
2. Ange en sidhuvud post med namnet x-MS-version med värdet 2013-11-01.
3. Skicka en begäran i följande format: https: \/ /Management.Core.Windows.net/ \<subscrition-id\> /Services/hostedservices/ \<service-name\> ? embed-detail = True
4. Leta efter **hostname** -elementet för varje **RoleInstance** -element.

> [!WARNING]
> Du kan också visa det interna domänsuffixet för moln tjänsten från REST-anropet genom att kontrol lera **InternalDnsSuffix** -elementet eller genom att köra ipconfig/all från en kommando tolk i en fjärrskrivbordssession (Windows) eller genom att köra Cat-/etc/resolv.conf från en SSH-Terminal (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Ändra ett värdnamn
Du kan ändra värd namnet för en virtuell dator eller roll instans genom att överföra en ändrad tjänst konfigurations fil eller genom att byta namn på datorn från en fjärrskrivbordssession.

## <a name="next-steps"></a>Nästa steg
[Namn matchning (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Konfigurations schema för Azure-tjänsten (. cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100))

[Konfigurations schema för Azure Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100))

[Ange DNS-inställningar med hjälp av konfigurationsfiler för nätverk](/previous-versions/azure/virtual-network/virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file)