---
title: Microsoft Azure Data Box Gateway system krav | Microsoft Docs
description: Läs om program-och nätverks kraven för din Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: e7c8653b39a3e0333ff6e98783a6e9a1437dba22
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739219"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Azure Data Box Gateway system krav

I den här artikeln beskrivs viktiga system krav för din Microsoft Azure Data Box Gateway-lösning och för klienter som ansluter till Azure Data Box Gateway. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box Gateway och sedan går tillbaka till den vid behov under distributionen och efterföljande åtgärder. 

System kraven för den virtuella Data Box Gateway-enheten är:

- **Program varu krav för värdar** – beskriver plattformar som stöds, webbläsare för det lokala konfigurations gränssnittet, SMB-klienter och eventuella ytterligare krav för de värdar som ansluter till enheten.
- **Nätverks krav för enheten** – ger information om eventuella nätverks krav för den virtuella enhetens drift.


## <a name="specifications-for-the-virtual-device"></a>Specifikationer för den virtuella enheten

Det underliggande värd systemet för Data Box Gateway kan dedicera följande resurser för att etablera den virtuella enheten:

| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Virtuella processorer (kärnor)   | Minst 4 |
| Minne  | Minst 8 GB. Vi rekommenderar starkt minst 16 GB. |
| Tillgänglighet|Enkel nod|
| Diskar| OS-disk: 250 GB <br> Datadisk: minst 2 TB, tunt allokerad och måste backas upp av SSD-enheter|
| Nätverksgränssnitt|Minst 1 virtuellt nätverksgränssnitt|


## <a name="supported-os-for-clients-connected-to-device"></a>Operativ system som stöds för klienter som är anslutna till enheten

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protokoll som stöds för klienter som ansluter till enheten

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Virtualization-plattformar som stöds för enhet

| **Operativ system/plattform**  |**Versioner**   |**Kommentarer**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |VMware-verktyg stöds inte.         |


## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Webbläsare som stöds för lokalt webb gränssnitt

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Krav för nätverks port

I följande tabell visas de portar som måste öppnas i brand väggen för att tillåta SMB-, moln-eller hanterings trafik. I den här tabellen avser *i* eller *inkommande* den riktning från vilken inkommande klient begär åtkomst till din enhet. *Out* eller *utgående* avser i vilken riktning din data Box gateway enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brand Väggs regler

Nätverks administratörer kan ofta konfigurera avancerade brand Väggs regler baserat på URL-mönster för att filtrera inkommande och utgående trafik. Din Data Box Gateway-enhet och tjänsten Data Box Gateway är beroende av andra Microsoft-program, till exempel Azure Service Bus, Azure Active Directory Access Control, lagrings konton och Microsoft Update-servrar. URL-mönstren som är kopplade till dessa program kan användas för att konfigurera brand Väggs regler. Det är viktigt att förstå att URL-mönstren som är kopplade till dessa program kan ändras. På så sätt måste nätverks administratören övervaka och uppdatera brand Väggs regler för din Data Box Gateway som och när det behövs.

Vi rekommenderar att du ställer in brand Väggs regler för utgående trafik, baserat på Data Box Gateway fasta IP-adresser, i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brand Väggs regler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - IP-adresserna för enheten (källa) ska alltid anges till alla molnbaserade nätverks gränssnitt.
> - Mål-IP-adresser ska anges till [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>URL-mönster för Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internet bandbredd

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Nästa steg

* [Distribuera Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

