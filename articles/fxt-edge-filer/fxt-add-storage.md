---
title: 'Självstudie: Lägg till lagring i ett Azure FXT Edge-kluster'
description: Så här konfigurerar du Server dels lagring och klient-och pseudonamespace för Azure FXT Edge-filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 7d10c6c1ce440b2ffe964dc78379ef3ab108e78e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217532"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Självstudie: Lägg till Server dels lagring och konfigurera det virtuella namn området

I den här självstudien beskrivs hur du lägger till lagring på Server sidan för din cache och hur du konfigurerar klientens virtuella fil system.

Klustret ansluter till Server dels lagrings system för att få åtkomst till begäran om data klienter och för att lagra ändringar mer permanent än i cachen.

Namn området är det klientbaserade pseudo-fil systemet som gör att du kan byta ut backend-lagring utan att ändra arbets flöden på klient sidan.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Så här lägger du till Server dels lagring i Azure FXT Edge-kluster
> * Så här definierar du sökvägen till klienten för lagring

## <a name="about-back-end-storage"></a>Om Server dels lagring

Azure FXT Edge-kluster använder en definition av *kärn* filer för att länka ett Server dels lagrings system till FXT-klustret.

Azure FXT Edge-filer är kompatibla med flera populära NAS-maskinvarusystem och kan använda tomma behållare från Azure Blob eller annan moln lagring.

Moln lagrings behållare måste vara tomma när de läggs till så att operativ systemet FXT kan hantera alla data i moln lagrings volymen fullständigt. Du kan flytta dina befintliga data till moln behållaren när du har lagt till behållaren i klustret som en kärn post.

Använd kontroll panelen för att lägga till en kärn-filer i systemet.

> [!NOTE]
>
> Om du vill använda Amazon AWS eller Google Cloud Storage måste du installera en FlashCloud<sup>TM</sup> -funktions licens. Kontakta din Microsoft-representant om du vill ha en licens nyckel och följ sedan anvisningarna i den äldre konfigurations guiden för [att lägga till eller ta bort funktions licenser](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
>
> Stöd för Azure Blob Storage ingår i licensen för Azure FXT Edge-program.

Mer detaljerad information om hur du lägger till Core-filläsare finns i följande avsnitt i guiden kluster konfiguration:

* Mer information om att välja och förbereda för att lägga till en kärn-filer finns i [arbeta med Core](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview)-.
* Detaljerade krav och stegvisa anvisningar finns i följande artiklar:

  * [Lägga till en ny NAS core-filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Lägga till en ny Cloud core-filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

När du har lagt till en kärn fil fil kan du uppdatera dess inställningar på sidan core-filer för information inställningar.

## <a name="add-a-core-filer"></a>Lägg till en kärn-filer

Definiera en kärna genom att klicka på knappen **skapa** på sidan **Core**-  >  **filer hantera Core-Filskydd** .

![Klicka på knappen Skapa ovanför listan över Core-på sidan Hantera core-filer](media/fxt-cluster-config/create-core-filer-button.png)

Guiden **Lägg till nya kärnor** vägleder dig genom processen att skapa en kärn-filer som länkar till Server dels lagringen. Kluster konfigurations guiden innehåller stegvisa beskrivningar av processen, som skiljer sig för NFS/NAS-lagring och för moln lagring (länkarna ovan).

Under aktiviteter är:

* Ange typ av kärn filer (NAS eller moln)

  ![Första sidan i guiden Ny Core-åtkomst för maskin vara. Alternativet för "Cloud core-filer" är inaktiverat och visar ett fel meddelande om licensen som saknas.](media/fxt-cluster-config/new-nas-1.png)

* Ange namnet på kärn filen. Välj ett namn som hjälper kluster administratörer att förstå vilket lagrings system den representerar.

* För NAS Core-anger du det fullständigt kvalificerade domän namnet (FQDN) eller IP-adressen. FQDN rekommenderas för alla core-filer och krävs för SMB-åtkomst.

* Välj en princip för cachelagring – den andra sidan i guiden listar tillgängliga cache-principer för de nya kärnorna. Mer information finns i [avsnittet om cache-principer i guiden kluster konfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html).

  ![Andra sidan i en maskinvaru-NAS ny Core-filguide; List rutan cacheinställningar är öppen, visar flera inaktiverade alternativ och tre giltiga alternativ för cache-princip (kringgå, läsbegäran och läsning/skrivning av cachelagring).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* För moln lagring måste du ange moln tjänsten och autentiseringsuppgifter för åtkomst, bland andra parametrar. Mer information finns i [moln tjänst och protokoll](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) i guiden kluster konfiguration.

  ![Cloud Core-information i guiden nya kärnor](media/fxt-cluster-config/new-core-filer-cloud3.png)
  
  Om du redan har lagt till autentiseringsuppgifter för moln åtkomst för det här klustret visas de i listan. Uppdatera och Lägg till autentiseringsuppgifter på sidan Inställningar för **kluster**  >  **molnets autentiseringsuppgifter** .

När du har fyllt i alla nödvändiga inställningar i guiden klickar du på knappen **Lägg till** filer för att skicka ändringen.

Efter en liten stund visas lagrings systemet i listan **instrument panel** core-filer och kan nås via sidor med inställningar för kärn filer.

![Core-filer "Flurry-NAS" på sidan Hantera Core-inställningar, där vyn information visas](media/fxt-cluster-config/core-filer-in-manage-page.png)

Kärnorna i den här skärm bilden saknar en vserver. Du måste länka kärn filer till en vserver och skapa en Knut punkt så att klienterna kan komma åt lagringen. De här stegen beskrivs nedan i [Konfigurera namn området](#configure-the-namespace).

## <a name="configure-the-namespace"></a>Konfigurera namn området

Azure FXT Edge-kluster skapar ett virtuellt fil system med namnet *kluster namn området* som fören klar klient åtkomsten till data som lagras på olika Server dels system. Eftersom klienter begär filer med hjälp av en virtuell sökväg kan du lägga till eller ersätta lagrings system utan att behöva ändra klient arbets flödet.

Kluster namn området gör det också möjligt att presentera moln-och NAS-lagringssystem i en liknande fil struktur.

Klustrets vservers upprätthåller namn området och betjänar innehåll till klienter. Det finns två steg för att skapa kluster namn området:

1. Skapa en vserver
1. Konfigurera Knut punkter mellan server dels lagrings system och sökvägar för klient-Facing-filsystem

### <a name="create-a-vserver"></a>Skapa en vserver

VServers är virtuella fil servrar som styr hur data flödar mellan klienten och klustrets kärn-filer:

* VServers IP-adresser för värd klienten
* VServers skapa namn området och definiera Knut punkter som mappar den klient-riktade virtuella katalog strukturen till export på backend-slutpunkt
* VServers tillämpa fil åtkomst kontroller, inklusive kärn filer för export och användar verifierings system
* VServers tillhandahåller SMB-infrastruktur

Innan du börjar konfigurera ett kluster vserver läser du den länkade dokumentationen och kontaktar din Microsoft-representant om du vill ha hjälp med att förstå namnrymd och vservers. Om du använder VLAN [skapar du dem](fxt-configure-network.md#adjust-network-settings) innan du skapar vserver.

I de här avsnitten i guiden kluster konfiguration kan du bekanta dig med funktionerna för FXT-vserver och globala namn områden:

* [Skapa och arbeta med VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Använda ett globalt namn område](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Skapa en VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

Du behöver minst en vserver för klustret.

Om du vill skapa en ny vserver behöver du följande information:

* Det namn som ska anges för vserver

* Intervallet för klient-IP-adresser som vserver ska hantera

  Du måste ange en enda uppsättning sammanhängande IP-adresser när du skapar vserver. Du kan lägga till fler adresser senare genom att använda sidan klient inställningar för **nätverks åtkomst** .

* Om nätverket har VLAN, vilket VLAN som ska användas för den här vserver

Använd sidan **vserver**  >  **Hantera VServers** -inställningar om du vill skapa en ny vserver. Mer information finns i [skapa en vserver](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) i guiden kluster konfiguration.

![popup-fönster för att skapa en ny vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Skapa en Knut punkt

En *Knut* punkt mappar en lagrings Sök väg på Server sidan till det synliga namn området för klienter.

Du kan använda det här systemet för att förenkla den sökväg som används i klient monterings punkter och skala kapaciteten sömlöst eftersom en virtuell sökväg kan hantera lagring från flera core-Filskydd.

![Guiden Lägg till ny Knut sida med inställningar ifyllda](media/fxt-cluster-config/add-junction-full.png)

Mer information om hur du skapar en namn områdes Knut punkt finns i [ **vserver**-  >  **namnrymden**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) i kluster konfigurations guiden.

![Sidan VServer > namn områdes inställningar som visar information om en förgrening](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Konfigurera export regler

När du har både en vserver och en kärn fil, bör du anpassa export reglerna och export principerna som styr hur klienter kan komma åt filer på kärn filerna.

Använd först sidan **vserver**  >  **export regler** om du vill lägga till nya regler, ändra standard principen eller skapa en egen anpassad export princip.

Sedan använder du sidan **vserver**  >  **export policys** för att tillämpa den anpassade principen på dina kärn-filers exporter vid åtkomst via den vserver.

Mer information finns i artikeln kluster konfigurations guiden för att [kontrol lera åtkomst till viktiga](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) filer.

## <a name="next-steps"></a>Nästa steg

När du har lagt till lagring och konfigurerat klientens namn område, slutför du klustrets första konfiguration: 

> [!div class="nextstepaction"]
> [Konfigurera klustrets nätverksinställningar](fxt-configure-network.md)
