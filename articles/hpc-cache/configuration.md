---
title: Konfigurera inställningar för Azure HPC-cache
description: Förklarar hur du konfigurerar ytterligare inställningar för cachen som MTU och No-root-squash, samt hur du får åtkomst till Express-ögonblicksbilder från Azure Blob Storage-mål.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/21/2020
ms.author: v-erkel
ms.openlocfilehash: 02bf862cdc3b20ef3e5fdb024f474267efa0c70d
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760511"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurera ytterligare inställningar för Azure HPC cache

**Konfigurations** sidan i Azure Portal har alternativ för att anpassa flera inställningar. De flesta användare behöver inte ändra dessa inställningar från standardvärdena.

Den här artikeln beskriver också hur du använder ögonblicks bild funktionen för Azure Blob Storage-mål. Ögonblicks bild funktionen har inga konfigurerbara inställningar.

Om du vill se inställningarna öppnar du cachens **konfigurations** sida i Azure Portal.

![skärm bild av konfigurations sidan i Azure Portal](media/configuration.png)

> [!TIP]
> I [Hantera Azure HPC cache-videon](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) visas konfigurations sidan och dess inställningar.

## <a name="adjust-mtu-value"></a>Justera MTU-värde
<!-- linked from troubleshoot-nas article -->

Du kan välja den maximala storleken för överförings enheter för cacheminnet med hjälp av den nedrullningsbara menyn med namnet **MTU-storlek**.

Standardvärdet är 1500 byte, men du kan ändra det till 1400.

> [!NOTE]
> Om du sänker cachens MTU-storlek kontrollerar du att de klienter och lagrings system som kommunicerar med cachen har samma MTU-inställning eller ett lägre värde.

Att sänka cachens MTU-värde kan hjälpa dig att komma runt paket storleks begränsningar i resten av cacheminnets nätverk. Vissa VPN kan till exempel inte överföra paket med full storlek 1500 byte. Att minska storleken på paket som skickas via VPN kan eliminera det problemet. Observera dock att en lägre cache-MTU-inställning innebär att alla andra komponenter som kommunicerar med cachen, inklusive klienter och lagrings system, måste också ha en lägre MTU-inställning för att undvika kommunikations problem.

Om du inte vill ändra MTU-inställningarna på andra system komponenter bör du inte minska cachens MTU-inställning. Det finns andra lösningar för att undvika storleks begränsningar för VPN-paket. Läs [ändra storleks begränsningar för VPN-paket](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) i fel söknings artikeln för NAS för att lära dig mer om att diagnostisera och lösa det här problemet.

Läs mer om MTU-inställningar i virtuella Azure-nätverk genom [att läsa TCP/IP-prestanda för virtuella Azure-datorer](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Konfigurera rot-squash
<!-- linked from troubleshoot and from access policies -->

Inställningen **Aktivera rot squash** styr hur Azure HPC cache behandlar begär Anden från rot användaren på klient datorer.

När rot-squash har Aktiver ATS mappas rot användare från en klient automatiskt till användaren "ingen" när de skickar förfrågningar via Azure HPC-cachen. Det förhindrar också att klient begär Anden använder set-UID-behörighet bitar.

Om rot-squash är inaktive rad skickas en begäran från klientens rot användare (UID 0) till ett Server dels-NFS-lagrings system som rot. Den här konfigurationen kan tillåta olämplig fil åtkomst.

Genom att ange rot-squash i cacheminnet kan du kompensera för den nödvändiga ``no_root_squash`` inställningen på NAS-system som används som lagrings mål. (Läs mer om [NFS-mål krav för lagring](hpc-cache-prerequisites.md#nfs-storage-requirements).) Det kan också förbättra säkerheten när den används med Azure Blob Storage-mål.

Standardinställningen är **Ja**. (Cacheminnen som skapats före april 2020 kan ha standardvärdet **Nej**.)

> [!TIP]
> Du kan också ange rot-squash för en speciell lagrings export genom [att anpassa klient åtkomst principer](access-policies.md#root-squash).

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

* upp till 20 dagliga ögonblicks bilder
* upp till 8 veckors ögonblicks bilder
* upp till tre månatliga ögonblicks bilder

Få åtkomst till ögonblicks bilderna från `.snapshot` katalogen i Blob Storage-målets namnrymd.
