---
title: 'Azure VPN Gateway: generera & export certifikat för P2S: MakeCert'
description: Skapa ett självsignerat rot certifikat, exportera den offentliga nyckeln och generera klient certifikat med hjälp av MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 55e22ebec5853d6b4f10b53be8e24f4dbebe4e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659785"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Skapa och exportera certifikat för punkt-till-plats-anslutningar med MakeCert

Punkt-till-plats-anslutningar använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rot certifikat och genererar klient certifikat med MakeCert. Om du letar efter olika certifikat instruktioner, se [certifikat – PowerShell](vpn-gateway-certificates-point-to-site.md) eller [certifikat – Linux](vpn-gateway-certificates-point-to-site-linux.md).

Vi rekommenderar att du använder [Windows 10 PowerShell-stegen](vpn-gateway-certificates-point-to-site.md) för att skapa dina certifikat, och vi tillhandahåller dessa MakeCert-instruktioner som en valfri metod. De certifikat som du genererar med någon av metoderna kan installeras på [alla klient operativ system som stöds](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). MakeCert har dock följande begränsning:

* MakeCert är föråldrad. Det innebär att verktyget kan tas bort när som helst. Alla certifikat som du redan har genererat med MakeCert påverkas inte när MakeCert inte längre är tillgängligt. MakeCert används bara för att generera certifikaten, inte som en verifierande mekanism.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Skapa ett självsignerat rot certifikat

Följande steg visar hur du skapar ett självsignerat certifikat med MakeCert. De här stegen är inte distributions modell som är speciell. De är giltiga för både Resource Manager och klassisk.

1. Hämta och installera [MakeCert](/windows/win32/seccrypto/makecert).
2. Efter installationen kan du vanligt vis hitta makecert.exe-verktyget under denna sökväg: ' C:\Program Files (x86) \Windows Kits\10\bin \<arch> '. Även om det är möjligt är det möjligt att det har installerats på en annan plats. Öppna en kommando tolk som administratör och navigera till platsen för MakeCert-verktyget. Du kan använda följande exempel för att justera efter rätt plats:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Skapa och installera ett certifikat i det personliga certifikat arkivet på datorn. I följande exempel skapas en motsvarande *. cer* -fil som du överför till Azure när du konfigurerar P2s. Ersätt "P2SRootCert" och "P2SRootCert. cer" med det namn som du vill använda för certifikatet. Certifikatet finns i "certificates-Current User\Personal\Certificates".

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Exportera den offentliga nyckeln (. cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Den exporterade CER-filen måste överföras till Azure. Instruktioner finns i [Konfigurera en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Information om hur du lägger till ytterligare ett betrott rot certifikat finns i [det här avsnittet](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) i artikeln.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportera det självsignerade certifikatet och den privata nyckeln för att lagra det (valfritt)

Du kanske vill exportera det självsignerade rot certifikatet och lagra det på ett säkert sätt. Om det behövs kan du senare installera det på en annan dator och generera fler klient certifikat eller exportera en annan. cer-fil. Om du vill exportera det självsignerade rot certifikatet som en. pfx väljer du rot certifikatet och använder samma steg som beskrivs i [Exportera ett klient certifikat](#clientexport).

## <a name="create-and-install-client-certificates"></a>Skapa och installera klient certifikat

Du installerar inte det självsignerade certifikatet direkt på klient datorn. Du måste generera ett klient certifikat från det självsignerade certifikatet. Sedan kan du exportera och installera klient certifikatet på klient datorn. Följande steg är inte distributions modell som är speciell. De är giltiga för både Resource Manager och klassisk.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Generera ett klientcertifikat

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Du genererar ett klient certifikat från det självsignerade rot certifikatet och exporterar och installerar sedan klient certifikatet. Autentiseringen Miss lyckas om klient certifikatet inte är installerat. 

Följande steg beskriver hur du skapar ett klient certifikat från ett självsignerat rot certifikat. Du kan generera flera klient certifikat från samma rot certifikat. När du genererar klient certifikat med stegen nedan installeras klient certifikatet automatiskt på den dator som du använde för att generera certifikatet. Om du vill installera ett klient certifikat på en annan klient dator kan du exportera certifikatet.
 
1. Öppna en kommando tolk som administratör på samma dator som du använde för att skapa det självsignerade certifikatet.
2. Ändra och kör exemplet för att generera ett klient certifikat.
   * Ändra *"P2SRootCert"* till namnet på den självsignerade roten som du genererar klient certifikatet från. Kontrol lera att du använder namnet på rot certifikatet, vilket innebär att värdet "CN =" är det som du angav när du skapade den självsignerade roten.
   * Ändra *P2SChildCert* till det namn som du vill skapa ett klient certifikat för.

   Om du kör följande exempel utan att ändra det, är resultatet ett klient certifikat med namnet P2SChildcert i ditt personliga certifikat arkiv som genererades från rot certifikat P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Exportera ett klient certifikat

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Installera ett exporterat klientcertifikat

Information om hur du installerar ett klient certifikat finns i [Installera ett klient certifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Nästa steg

Fortsätt med din punkt-till-plats-konfiguration. 

* Anvisningar för distributions modellen i **Resource Manager** finns i [Konfigurera P2s med intern Azure-certifikatautentisering](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* De **klassiska** stegen för distributions modellen finns i [Konfigurera en punkt-till-plats-VPN-anslutning till ett VNet (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Information om P2S-felsökning finns i [Felsöka punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).