---
title: Konfigurera aver vFXT Storage – Azure
description: Lär dig hur du lägger till ett Server dels lagrings system för ett kluster i AVERT vFXT för Azure. Om du har skapat en Azure Blob-behållare med klustret är den redo att användas.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271149"
---
# <a name="configure-storage"></a>Konfigurera lagring

Det här steget konfigurerar ett Server dels lagrings system för ditt vFXT-kluster.

> [!TIP]
> Om du har skapat en ny Azure Blob-behållare tillsammans med det Avera vFXT-klustret har den här behållaren redan kon figurer ATS och är redo att användas.

Följ dessa anvisningar om du inte skapade en ny BLOB-behållare med klustret, eller om du vill lägga till ytterligare ett maskin varu-eller molnbaserad lagrings system.

Det finns två huvudsakliga uppgifter:

1. [Skapa en kärn](#create-a-core-filer)-filer som ansluter ditt vFXT-kluster till ett befintligt lagrings system eller en Azure Storage konto behållare.

1. [Skapa en Knut punkt för namn område](#create-a-junction), som definierar den sökväg som klienterna ska montera.

I de här stegen används kontroll panelen aver. Läs [åtkomst till vFXT-klustret](avere-vfxt-cluster-gui.md) för att lära dig hur du använder det.

## <a name="create-a-core-filer"></a>Skapa en kärn-filer

"Kärn filer" är en vFXT term för ett Server dels lagrings system. Lagringen kan vara en maskinvaru-NAS-apparat som NetApp eller Isilon, eller så kan det vara ett moln objekts lager. Mer information om core-filer finns i [Guide för kluster inställningar i aver](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Om du vill lägga till en Core-filer väljer du någon av de två huvud typerna av Core-:

* [NAS Core](#nas-core-filer) -filer – beskriver hur du lägger till en NAS core-filer
* [Azure Storage Cloud Core](#azure-blob-storage-cloud-core-filer) -filer – beskriver hur du lägger till en Azure Blob Storage-behållare som en Cloud core-filer

### <a name="nas-core-filer"></a>NAS-kärnor

En NAS core-filer kan vara en lokal NetApp eller Isilon-apparat eller en NAS-slutpunkt i molnet. Lagrings systemet måste ha en tillförlitlig höghastighets anslutning till AVERT vFXT-kluster, till exempel en 1GBps ExpressRoute-anslutning (inte en VPN) – och den måste ge kluster rot åtkomst till de NAS-exporter som används.

Följ dessa steg om du vill lägga till en NAS core-filer:

1. Klicka på fliken **Inställningar** längst upp i kontroll panelen aver.

1. Klicka på **Core**  >  **filer hantera Core** -till vänster.

1. Klicka på **Skapa**.

   ![Skärm bild av sidan Lägg till ny core-filer med en markör över knappen Skapa](media/avere-vfxt-add-core-filer-start.png)

1. Fyll i den information som krävs i guiden:

   * Namnge dina kärnor.
   * Ange ett fullständigt kvalificerat domän namn (FQDN) om det är tillgängligt. Annars anger du en IP-adress eller ett värdnamn som matchar dina kärnor.
   * Välj din filer-klass från listan. Om du är osäker väljer du **annat**.

     ![Skärm bild av sidan Lägg till ny core-filer med namnet på kärn filen och det fullständigt kvalificerade domän namnet](media/avere-vfxt-add-core-filer.png)
  
   * Klicka på **Nästa** och välj en princip för cache.
   * Klicka på **Lägg till**filer.
   * Mer detaljerad information finns i lägga till [en ny NAS Core](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) -filer i guiden för kluster inställningar i AVERT.

Fortsätt sedan att [skapa en Knut](#create-a-junction)punkt.  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage Cloud core-filer

Om du vill använda Azure Blob Storage som vFXT-klusters backend-lagring behöver du en tom behållare för att lägga till som en kärn post.

Följande uppgifter krävs för att lägga till Blob Storage i klustret:

* Skapa ett lagrings konto (steg 1 nedan)
* Skapa en tom BLOB-behållare (steg 2-3)
* Lägg till lagrings åtkomst nyckeln som en moln autentiseringsuppgift för vFXT-klustret (steg 4-6)
* Lägg till BLOB-behållaren som en kärn-filer för vFXT-klustret (steg 7-9)
* Skapa en namn områdes Knut som klienter använder för att montera kärn filer ([skapa en Knut](#create-a-junction)punkt, samma för både maskin vara och moln lagring)

> [!TIP]
> Om du skapar en ny BLOB-behållare när du skapar ett AVERT-vFXT för Azure-kluster, konfigurerar distributions mal len automatiskt behållaren som en kärn-filer. (Detta gäller även om du använder skriptet för att skapa, som är tillgängligt på begäran.) Du behöver inte konfigurera kärn filerna efteråt.
>
> Verktyget för att skapa kluster gör dessa konfigurations åtgärder åt dig:
>
> * Skapar en ny BLOB-behållare på det angivna lagrings kontot
> * Definierar containern som en kärn-filer
> * Skapar en namn områdes Knut punkt till behållaren
> * Skapar en lagrings tjänst slut punkt i klustrets virtuella nätverk

Följ dessa steg om du vill lägga till Blob Storage när klustret har skapats.

1. Skapa ett allmänt-syfte v2-lagrings konto med följande inställningar:

   * **Prenumerationen** – samma som vFXT-klustret
   * **Resurs grupp** – samma som kluster gruppen vFXT (valfritt)
   * **Plats** -samma som vFXT-klustret
   * **Prestanda** – standard (Premium Storage stöds inte)
   * **Konto typ** – allmänt-syfte v2 (StorageV2)
   * **Replikering** – lokalt redundant lagring (LRS)
   * **Åtkomst nivå** – frekvent
   * **Säker överföring krävs** -inaktivera det här alternativet (inte standardvärdet)
   * **Virtuella nätverk** – inte obligatoriskt

   Du kan använda Azure Portal eller klicka på knappen "distribuera till Azure" nedan.

   [![knapp för att skapa lagrings konto](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. När kontot har skapats bläddrar du till sidan lagrings konto.

   ![Nytt lagrings konto i Azure Portal](media/avere-vfxt-new-storage-acct.png)

1. Skapa en ny BLOB-behållare: Klicka på **behållare** på sidan Översikt och klicka sedan på **+ container**. Använd alla behållar namn och se till att åtkomst är inställt på **privat**.

   ![Sidan Storage blobbar med knappen + container inringad och en ny behållare skapas på en popup-sida](media/avere-vfxt-new-blob.png)

1. Hämta Azure Storage konto nyckeln genom att klicka på **åtkomst nycklar** under **Inställningar**. Kopiera en av de angivna nycklarna.

   ![Azure Portal GUI för att kopiera nyckeln](media/avere-vfxt-copy-storage-key.png)

1. Öppna kontroll panelen aver för klustret. Klicka på **Inställningar**och öppna **Cluster**  >  **autentiseringsuppgifter** för kluster moln i det vänstra navigerings fönstret. På sidan autentiseringsuppgifter för moln klickar du på **Lägg till autentiseringsuppgift**.

   ![Klicka på knappen Lägg till autentiseringsuppgift på sidan konfiguration av moln autentiseringsuppgifter](media/avere-vfxt-new-credential-button.png)

1. Fyll i följande information för att skapa en autentiseringsuppgift för Cloud core-filer:

   | Fält | Värde |
   | --- | --- |
   | Namn på autentiseringsuppgifter | ett beskrivande namn |
   | Typ av tjänst | (Välj Azure Storage åtkomst nyckel) |
   | Klientorganisation | lagringskontots namn |
   | Prenumeration | subscription ID |
   | Lagrings åtkomst nyckel | Nyckel för Azure Storage-konto (kopieras i föregående steg) |

   Klicka på **Skicka**.

   ![Formulär för slutförd moln behörighet i AVERT-Kontrollpanelen](media/avere-vfxt-new-credential-submit.png)

1. Skapa sedan core-filer. Klicka på **Core**-  >   **filer hantera Core**-på vänster sida av kontroll panelen aver.

1. Klicka på knappen **skapa** på sidan **Hantera inställningar för Core-Filskydd** .

1. Fyll i guiden:

   * Välj typ av filtyp- **moln**.
   * Namnge den nya Core-filen och klicka på **Nästa**.
   * Godkänn standard principen för cache och fortsätt till den tredje sidan.
   * I **tjänst typ**väljer du **Azure Storage**.
   * Välj de autentiseringsuppgifter som skapades tidigare.
   * Ange **Bucket-innehåll** som **tomt**
   * Ändra **certifikat verifiering** till **inaktive rad**
   * Ändra **komprimerings läge** till **ingen**
   * Klicka på **Nästa**.
   * På den fjärde sidan anger du namnet på behållaren i **Bucket-namn** som *storage_account_name* / *container_name*.
   * Du kan också ange **krypterings typ** till **ingen**.  Azure Storage krypteras som standard.
   * Klicka på **Lägg till**filer.

   Mer detaljerad information finns i [lägga till en ny Cloud Core](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) -filer i konfigurations guiden för AVERT kluster.

Sidan uppdateras eller så kan du uppdatera sidan för att visa dina nya kärnor.

Därefter måste du [skapa en Knut](#create-a-junction)punkt.

## <a name="create-a-junction"></a>Skapa en Knut punkt

En Knut punkt är en sökväg som du skapar för-klienter. Klienter monterar sökvägen och kommer till den plats du väljer.

Du kan till exempel skapa `/vfxt/files` för att mappa till din NetApp Core- `/vol0/data` filexport och under `/project/resources` katalogen.

Mer information om Knut punkter finns i [avsnittet namnrymd i konfigurations guiden för AVERT kluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Följ de här stegen i gränssnittet AVERT på kontroll panelen:

* Klicka på **vserver**-  >  **namnrymd** i det övre vänstra hörnet.
* Ange en namn områdes Sök väg som börjar med/(snedstreck), t ``/vfxt/data`` . ex..
* Välj dina kärnor.
* Välj kärn-filer exporteras.
* Klicka på **Nästa**.

  ![Skärm bild av sidan "Lägg till ny koppling" med fälten slutförda för koppling, kärn filer och export](media/avere-vfxt-add-junction.png)

Knut punkten kommer att visas efter några sekunder. Skapa ytterligare Knut punkter efter behov.

När Knut punkten har skapats använder klienterna namn områdes Sök vägen för att komma åt filerna från lagrings systemet.

## <a name="next-steps"></a>Nästa steg

* [Montera Avere vFXT-klustret](avere-vfxt-mount-clients.md)
* Lär dig effektiva sätt att [Flytta data till en ny BLOB-behållare](avere-vfxt-data-ingest.md)
