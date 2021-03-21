---
title: Check lista för för distribution för att distribuera Azure Stack Edge Pro R-enhet | Microsoft Docs
description: Den här artikeln beskriver den information som kan samlas in innan du distribuerar din Azure Stack Edge Pro R-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2021
ms.author: alkohli
ms.openlocfilehash: eca26934605ff70ecb26c10604fc9b493e88f2cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727452"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-r-device"></a>Distributions check lista för din Azure Stack Edge Pro R-enhet  

Den här artikeln beskriver den information som kan samlas in före den faktiska distributionen av din Azure Stack Edge Pro R-enhet. 

Använd följande check lista för att se till att du har den här informationen när du har placerat en order för en Azure Stack Edge Pro R-enhet och innan du har fått enheten. 

## <a name="deployment-checklist"></a>Check lista för distribution 

| Fas                             | Parameter                                                                                                                                                                                                                           | Information                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Enhets hantering               | <li>Azure-prenumeration</li><li>Registrerade resurs leverantörer</li><li>Azure-lagringskonto</li>|<li>Aktive rad för Azure Stack Edge Pro/Data Box Gateway, ägare eller deltagar åtkomst.</li><li>I Azure Portal går du till **start > prenumerationer > dina prenumerations > resurs leverantörer**. Sök efter `Microsoft.DataBoxEdge` och registrera. Upprepa för `Microsoft.Devices` om du distribuerar IoT-arbetsbelastningar.</li><li>Behöver autentiseringsuppgifter för åtkomst</li> |
| Enhets installation               | Ström kablar i paketet. <br>För oss kommer en SVE 18/3-kabel som klassats för 125 V och 15 ampere med en NEMA på 5-15P till C13 (indata till utdata) att skickas. | Mer information finns i listan över [ström sladdar som stöds efter land](azure-stack-edge-technical-specifications-power-cords-regional.md)  |
|                                   | <li>Minst 1 X 1 – GbE RJ-45-nätverks kabel för port 1  </li><li> Minst 1 X 25-GbE SFP + koppar kabel för Port 3, Port 4</li>| Kunden måste köpa dessa kablar.<br>En fullständig lista över nätverks kablar, växlar och Sänd tagare som stöds för enhets nätverkskort finns i [Cavium FastlinQ 41000-serien Interoperabilitets mat ris](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) och [Mellanox dual port 25G ConnectX-4 Channel-kompatibla produkter](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Första gången enhets anslutning      | <li>Bärbar dator vars IPv4-inställningar kan ändras. Den här bärbara datorn ansluter till port 1 via en växel eller USB till Ethernet-adapter.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Enhets inloggning                      | Enhets administratörs lösen ord, mellan 8 och 16 tecken, inklusive tre av följande tecken typer: versaler, gemener, siffror och specialtecken.                                            | Standard lösen ordet är *Password1*, som upphör att gälla vid första inloggningen.                                                     |
| Nätverksinställningar                  | Enheten levereras med 2 x 1 – GbE, 4 x 25-GbE-nätverks portar. <li>Port 1 används endast för att konfigurera hanterings inställningar. En eller flera data portar kan vara anslutna och konfigurerade. </li><li> Minst ett data nätverks gränssnitt från port 2-port 6 måste vara anslutet till Internet (med anslutning till Azure).</li><li> DHCP och statisk IPv4-konfiguration stöds. | Statisk IPv4-konfiguration kräver IP, DNS-server och standard-gateway.   |
| Beräkna nätverks inställningar     | <li>Kräv 2 kostnads fria, statiska, sammanhängande IP-adresser för Kubernetes-noder och 1 statisk IP för IoT Edge-tjänsten.</li><li>Kräv en ytterligare IP-adress för varje extra tjänst eller modul som du ska distribuera.</li>| Endast statisk IPv4-konfiguration stöds.|
| Valfritt Webbproxy-inställningar     | <li>Webb-proxyserverns IP/FQDN, port </li><li>Webbproxy-användarnamn, lösen ord</li> |  |
| Brand vägg och port inställningar        | Om du använder brand vägg kontrollerar du att de [listade URL: erna och portarna](azure-stack-edge-system-requirements.md#networking-port-requirements) är tillåtna för enhetens IP-adresser. |  |
| Rekommenderas Tids inställningar       | Konfigurera tids zon, primär NTP-server, sekundär NTP-server. | Konfigurera den primära och sekundära NTP-servern i det lokala nätverket.<br>Om den lokala servern inte är tillgänglig kan offentliga NTP-servrar konfigureras.                                                    |
| Valfritt Uppdatera Server inställningar | <li>Kräv IP-adress för uppdaterings server i lokalt nätverk, sökväg till WSUS-server. </li> | Som standard används den offentliga Windows Update-servern.|
| Enhetsinställningar | <li>Fullständigt kvalificerat domän namn för enheten (FQDN) </li><li>DNS-domän</li> | |
| Valfritt Intyg  | Om du tar med dina egna certifikat, inklusive signerings kedjan, [lägger du till certifikat](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#bring-your-own-certificates) i lämpligt format.| Konfigurera bara certifikat om du ändrar enhets namn och/eller DNS-domän. |
| VPN  | <!--Need VPN certificate, VPN gateway, firewall setup in Azure,  passphrase and region info VPN scripts. -->   | |
| Kryptering vid vila  | Rekommendera att använda automatiskt genererad krypterings nyckel.   |Om du använder en egen nyckel behöver du en 32 tecken lång bas-64-kodad nyckel.  |
| Aktivering  | Kräv aktiverings nyckel från Azure Stack Edge Pro/Data Box Gateway-resursen.    | När den har genererats går nyckeln ut om tre dagar. |

<!--
| (Optional) MAC Address            | If MAC address needs to be approved, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Nästa steg

Förbered för att distribuera din [Azure Stack Edge Pro-enhet](azure-stack-edge-pro-r-deploy-prep.md).
