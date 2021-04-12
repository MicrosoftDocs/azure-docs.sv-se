---
title: System krav för Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Lär dig mer om program-och nätverks kraven för din Azure Stack Edge Pro R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: ef81494b6c93f2941cc41b367e566332d6bc7cc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831350"
---
# <a name="azure-stack-edge-pro-r-system-requirements"></a>System krav för Azure Stack Edge Pro R

I den här artikeln beskrivs viktiga system krav för din Azure Stack Edge Pro R-lösning och för klienter som ansluter till Azure Stack Edge Pro R. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Azure Stack Edge Pro R. Du kan referera tillbaka till den här informationen vid behov under distributionen och efterföljande åtgärder.

System kraven för Azure Stack Edge Pro R inkluderar:

- **Program varu krav för värdar** – beskriver plattformar som stöds, webbläsare för det lokala konfigurations gränssnittet, SMB-klienter och eventuella ytterligare krav för klienter som har åtkomst till enheten.
- **Nätverks krav för enheten** – ger information om eventuella nätverks krav för den fysiska enhetens drift.

## <a name="supported-os-for-clients-connected-to-device"></a>Operativ system som stöds för klienter som är anslutna till enheten

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protokoll som stöds för klienter som ansluter till enheten

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-tiered-storage-accounts"></a>Lagrings konton som stöds

Vid hantering från Azure Stack stöds följande skiktade lagrings konton med SMB/NFS/REST-gränssnitt.

|Typ  |Lagringskonto  |Kommentarer  |
|---------|---------|---------|
|Standard     |GPv1: Block-Blob         |         |
|    |  Blob Storage: Block-Blob       | Stöds endast för NAS     |

* Page blobbar och Azure Files stöds för närvarande inte i Azure Stack.
* * Frekvent och kall nivå finns inte i Azure Stack. Använd Azure PowerShell för att flytta data till Arkiv nivån när data har överförts. Stegvisa instruktioner finns i [använda Azure PowerShell för att ställa in BLOB-nivån]()

## <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Webbläsare som stöds för lokalt webb gränssnitt

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Krav för nätverks port

### <a name="port-requirements-for-azure-stack-edge-pro-r"></a>Port krav för Azure Stack Edge Pro R

I följande tabell visas de portar som måste öppnas i brand väggen för att tillåta SMB-, moln-eller hanterings trafik. I den här tabellen avser *i* eller *inkommande* den riktning från vilken inkommande klient begär åtkomst till din enhet. *Ut* eller *utgående* avser i vilken riktning din Azure Stack Edge Pro R-enhet skickar data externt, utöver distributionen, till exempel utgående till Internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Port krav för IoT Edge

Azure IoT Edge tillåter utgående kommunikation från en lokal Edge-enhet till Azure-molnet med IoT Hub protokoll som stöds. Inkommande kommunikation krävs endast för vissa scenarier där Azure IoT Hub behöver skicka meddelanden till Azure IoT Edge-enheten (till exempel molnet till enhets meddelanden).

Använd följande tabell för port konfiguration för servrar som är värdar för Azure IoT Edge Runtime:

| Port nr. | In eller ut | Port omfång | Obligatorisk | Vägledning |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Ut       | WAN        | Ja      | Utgående öppen för IoT Edge etablering. Den här konfigurationen krävs när du använder manuella skript eller DPS (Azure IoT Device Provisioning service).|

Fullständig information finns i [brand Väggs-och port konfigurations regler för IoT Edge distribution](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>URL-mönster för brand Väggs regler

Nätverks administratörer kan ofta konfigurera avancerade brand Väggs regler baserat på URL-mönster för att filtrera inkommande och utgående trafik. Din Azure Stack Edge Pro R-enhet och tjänsten är beroende av andra Microsoft-program som Azure Service Bus, Azure Active Directory Access Control, lagrings konton och Microsoft Update-servrar. URL-mönstren som är kopplade till dessa program kan användas för att konfigurera brand Väggs regler. Det är viktigt att förstå att URL-mönstren som är kopplade till dessa program kan ändras. Dessa ändringar kräver att nätverks administratören övervakar och uppdaterar brand Väggs regler för din Azure Stack Edge Pro R som och när det behövs.

Vi rekommenderar att du ställer in brand Väggs regler för utgående trafik, baserat på Azure Stack Edge Pro R fasta IP-adresser, i de flesta fall. Du kan dock använda informationen nedan för att ange avancerade brand Väggs regler som behövs för att skapa säkra miljöer.

> [!NOTE]
> - IP-adresserna för enheten (källa) ska alltid anges till alla molnbaserade nätverks gränssnitt.
> - Mål-IP-adresser ska anges till [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>URL-mönster för gateway-funktion

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-mönster för beräknings funktion

| URL-mönster                      | Komponent eller funktion                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.io | Microsoft container Registry (krävs)               |
| https:// \* . azurecr.io                     | Personliga och tredje parts behållar register (valfritt) | 
| https:// \* . Azure-Devices.net              | IoT Hub åtkomst (krävs)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL-mönster för gateway för Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL-mönster för beräkning för Azure Government

| URL-mönster                      | Komponent eller funktion                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.com | Microsoft container Registry (krävs)               |
| https:// \* . Azure-Devices.us              | IoT Hub åtkomst (krävs)           |
| https:// \* . azurecr.us                    | Personliga och tredje parts behållar register (valfritt) | 

## <a name="internet-bandwidth"></a>Internet bandbredd

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Överväganden vid beräkning av storlek

Använd din upplevelse när du utvecklar och testar din lösning för att se till att det finns tillräckligt med kapacitet på din Azure Stack Edge Pro R-enhet och du får optimala prestanda från din enhet.

Faktorer som du bör ta hänsyn till:

- **Behållar information** – Tänk på följande.

    - Hur många behållare finns i arbets belastningen? Du kan ha många enkla behållare jämfört med några resurs intensiva.
    - Vilka resurser allokeras till dessa behållare jämfört med vilka resurser de använder?
    - Hur många lager gör behållarna till delar?
    - Finns det oanvända behållare? En stoppad behållare tar fortfarande upp disk utrymme.
    - I vilket språk är dina behållare skrivna?
- **Storleken på de data som bearbetas** – hur mycket data kommer behållarna att bearbeta? Kommer dessa data att förbruka disk utrymme eller så bearbetas data i minnet?
- **Förväntade prestanda** – vilka är de önskade prestanda egenskaperna för lösningen? 

Om du vill förstå och förfina lösningens prestanda kan du använda:

- Beräknings måtten som är tillgängliga i Azure Portal. Gå till din Azure Stack Edge Pro R-resurs och gå sedan till **övervakning > mått**. Titta på processor **användningen för Edge Compute-Memory** och **Edge Compute-procent** för att förstå de tillgängliga resurserna och hur är resurserna förbrukade.
- De [övervaknings kommandon som är tillgängliga via PowerShell-gränssnittet på enheten](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Till sist kontrollerar du att du validerar din lösning på din data uppsättning och kvantifierar prestanda på Azure Stack Edge Pro R innan du distribuerar i produktionen.

## <a name="next-step"></a>Nästa steg

- [Distribuera Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md)