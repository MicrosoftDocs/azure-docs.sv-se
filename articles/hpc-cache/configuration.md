---
title: Konfigurera inställningar för Azure HPC-cache
description: Förklarar hur du konfigurerar ytterligare inställningar för cachen som MTU, anpassad NTP-och DNS-konfiguration samt hur du får åtkomst till Express-ögonblicksbilder från Azure Blob Storage-mål.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 06feefe3a934d1ee02793fab442852e5ef40899a
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563392"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurera ytterligare inställningar för Azure HPC cache

Sidan **nätverk** i Azure Portal har alternativ för att anpassa flera inställningar. De flesta användare behöver inte ändra dessa inställningar från standardvärdena.

Den här artikeln beskriver också hur du använder ögonblicks bild funktionen för Azure Blob Storage-mål. Ögonblicks bild funktionen har inga konfigurerbara inställningar.

Om du vill se inställningarna öppnar du sidan för cachens **nätverk** i Azure Portal.

![skärm bild av sidan nätverk i Azure Portal](media/networking-page.png)

> [!NOTE]
> En tidigare version av den här sidan innehöll en rot squash-inställning på cache nivå, men den här inställningen har flyttats till [klient åtkomst principer](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>Justera MTU-värde
<!-- linked from troubleshoot-nas article -->

Du kan välja den maximala storleken för överförings enheter för cacheminnet med hjälp av den nedrullningsbara menyn med namnet **MTU-storlek**.

Standardvärdet är 1500 byte, men du kan ändra det till 1400.

> [!NOTE]
> Om du sänker cachens MTU-storlek kontrollerar du att de klienter och lagrings system som kommunicerar med cachen har samma MTU-inställning eller ett lägre värde.

Att sänka cachens MTU-värde kan hjälpa dig att komma runt paket storleks begränsningar i resten av cacheminnets nätverk. Vissa VPN kan till exempel inte överföra paket med full storlek 1500 byte. Att minska storleken på paket som skickas via VPN kan eliminera det problemet. Observera dock att en lägre cache-MTU-inställning innebär att alla andra komponenter som kommunicerar med cachen, inklusive klienter och lagrings system, måste också ha en lägre MTU-inställning för att undvika kommunikations problem.

Om du inte vill ändra MTU-inställningarna på andra system komponenter bör du inte minska cachens MTU-inställning. Det finns andra lösningar för att undvika storleks begränsningar för VPN-paket. Läs [ändra storleks begränsningar för VPN-paket](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) i fel söknings artikeln för NAS för att lära dig mer om att diagnostisera och lösa det här problemet.

Läs mer om MTU-inställningar i virtuella Azure-nätverk genom [att läsa TCP/IP-prestanda för virtuella Azure-datorer](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="customize-ntp"></a>Anpassa NTP

Din cache använder Azure-baserade Time Server-time.microsoft.com som standard. Om du vill att din cache ska använda en annan NTP-server anger du den i avsnittet **NTP-konfiguration** . Använd ett fullständigt kvalificerat domän namn eller en IP-adress.

## <a name="set-a-custom-dns-configuration"></a>Ange en anpassad DNS-konfiguration

> [!CAUTION]
> Ändra inte cache-DNS-konfigurationen om du inte behöver det. Konfigurations misstag kan ha ödesdigra-konsekvenser. Om konfigurationen inte kan matcha Azure-tjänst namn, blir HPC cache-instansen permanent otillgänglig.

Azure HPC cache konfigureras automatiskt för att använda det säkra och smidiga Azure DNS systemet. Några ovanliga konfigurationer kräver dock att cachen använder ett separat lokalt DNS-system i stället för Azure-systemet. Avsnittet **DNS-konfiguration** på sidan **nätverk** används för att ange den här typen av system.

Kontakta Azure-representanterna eller kontakta Microsoft service och support för att avgöra om du behöver använda en anpassad cache-DNS-konfiguration.

Om du konfigurerar ditt eget lokala DNS-system för att Azure HPC-cache ska använda, måste du se till att konfigurationen kan matcha namn på Azure-slutpunkter för Azure-tjänster. Du måste konfigurera din anpassade DNS-miljö så att den vidarebefordrar vissa namn matchnings begär anden till Azure DNS eller till en annan server efter behov.

Kontrol lera att DNS-konfigurationen kan lösa dessa objekt innan du använder den för en Azure HPC-cache:

* ``*.core.windows.net``
* Hämtning av listor över återkallade certifikat (CRL) och kontroll tjänster online Certificate Status Protocol (OCSP). En partiell lista finns i [objektet brand Väggs regler](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) i slutet av den här [Azure TLS-artikeln](../security/fundamentals/tls-certificate-changes.md), men du bör kontakta en teknisk representant från Microsoft för att förstå alla krav.
* Det fullständigt kvalificerade domän namnet för din NTP-server (time.microsoft.com eller en anpassad server)

Om du behöver ange en anpassad DNS-server för cacheminnet använder du de angivna fälten:

* **DNS-sökdomän** (valfritt) – Ange din Sök domän, till exempel ``contoso.com`` . Ett enda värde är tillåtet, eller så kan du lämna det tomt.
* **DNS** -servrar – Ange upp till tre DNS-servrar. Ange dem efter IP-adress.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

### <a name="refresh-storage-target-dns"></a>Uppdatera lagrings målets DNS

Om DNS-servern uppdaterar IP-adresser blir de tillhör ande NFS-målen tillfälligt otillgängliga. Läs hur du uppdaterar dina anpassade DNS-systemip-adresser i [Redigera lagrings mål](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Visa ögonblicks bilder för Blob Storage-mål

Azure HPC cache sparar automatiskt lagrings ögonblicks bilder för Azure Blob Storage-mål. Ögonblicks bilder tillhandahåller en snabb referens punkt för innehållet i backend storage-behållaren.

Ögonblicks bilder är inte en ersättning för säkerhets kopiering av data, och de innehåller ingen information om statusen för cachelagrade data.

> [!NOTE]
> Den här ögonblicks bild funktionen skiljer sig från ögonblicks bild funktionen som ingår i NetApp eller Isilon Storage-programvara. De här ögonblicks bilds implementeringarna tömmer ändringar från cachen till Server delens lagrings system innan du tar ögonblicks bilden.
>
> För att vara effektiv tömmer Azure HPC cache-ögonblicksbilden ändringar först och registrerar bara data som har skrivits till BLOB-behållaren. Den här ögonblicks bilden representerar inte statusen för cachelagrade data, så den kanske inte innehåller de senaste ändringarna.

Den här funktionen är endast tillgänglig för Azure Blob Storage-mål och dess konfiguration kan inte ändras.

Ögonblicks bilder tas var åttonde timme, kl. UTC 0:00, 08:00 och 16:00.

Azure HPC-cache lagrar dagliga, veckovis och månatliga ögonblicks bilder tills de ersätts av nya. Gränserna är:

* Upp till 20 dagliga ögonblicks bilder
* Upp till 8 veckors ögonblicks bilder
* Upp till tre månatliga ögonblicks bilder

Få åtkomst till ögonblicks bilderna från `.snapshot` katalogen i Blob Storage-målets namnrymd.
