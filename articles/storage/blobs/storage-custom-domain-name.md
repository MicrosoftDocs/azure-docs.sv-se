---
title: Mappa en anpassad domän till en Azure Blob Storage-slutpunkt
titleSuffix: Azure Storage
description: Mappa en anpassad domän till en Blob Storage eller en webb slut punkt i ett Azure Storage-konto.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 52fc7b9c1229421fd46b8110857a0a7a8a4f916a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520433"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mappa en anpassad domän till en Azure Blob Storage-slutpunkt

Du kan mappa en anpassad domän till en BLOB service-slutpunkt eller en [statisk webbplats](storage-blob-static-website.md) slut punkt. 

> [!NOTE] 
> Den här mappningen fungerar bara för under domäner (till exempel: `www.contoso.com` ). Om du vill att webb slut punkten ska vara tillgänglig på rot domänen (till exempel: `contoso.com` ) måste du använda Azure CDN. Vägledning finns i avsnittet [Mappa en anpassad domän med https aktiverat](#enable-https) i den här artikeln. Eftersom du kommer till det avsnittet i den här artikeln för att aktivera rot domänen för din anpassade domän, är steget i avsnittet för att aktivera HTTPS valfritt. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mappa en anpassad domän med endast HTTP aktiverat

Den här metoden är enklare, men aktiverar endast HTTP-åtkomst. Om lagrings kontot har kon figurer ATS för att [kräva säker överföring](../common/storage-require-secure-transfer.md) över HTTPS måste du Aktivera HTTPS-åtkomst för din anpassade domän. 

Information om hur du aktiverar HTTPS-åtkomst finns i avsnittet [Mappa en anpassad domän med https aktiverat](#enable-https) i den här artikeln. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Mappa en anpassad domän

> [!IMPORTANT]
> Den anpassade domänen är en kort användare som inte är tillgänglig för användarna när du har slutfört konfigurationen. Om din domän för närvarande har stöd för ett program med ett service nivå avtal som kräver noll avbrotts tid följer du stegen i avsnittet [Mappa en anpassad domän med noll stillestånds](#zero-down-time) tid i den här artikeln för att se till att användarna kan komma åt din domän när DNS-mappningen äger rum.

Om du inte är orolig över att domänen inte är tillgänglig för användarna följer du de här stegen.

: heavy_check_mark: steg 1: Hämta värd namnet för din lagrings slut punkt.

: heavy_check_mark: steg 2: skapa en post för kanoniskt namn (CNAME) med din domän leverantör.

: heavy_check_mark: steg 3: registrera den anpassade domänen med Azure. 

: heavy_check_mark: steg 4: testa din anpassade domän.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Steg 1: Hämta värd namnet för din lagrings slut punkt 

Värd namnet är URL: en för lagrings slut punkt utan protokoll-ID och avslutande snedstreck. 

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.

2. I meny fönstret, under **Inställningar**, väljer du **Egenskaper**.  

3. Kopiera värdet för den **primära BLOB service-slutpunkten** eller den **primära statiska webbplats slut punkten** till en textfil. 
  
   > [!NOTE]
   > Data Lake lagrings slut punkten stöds inte (till exempel: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Ta bort protokoll identifieraren (till exempel: `HTTPS` ) och avslutande snedstreck från den strängen. Följande tabell innehåller exempel.

   | Typ av slut punkt |  slutpunkt | värdnamn |
   |------------|-----------------|-------------------|
   |BLOB service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statisk webbplats  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ange det här värdet undan för senare.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Steg 2: skapa en post för kanoniskt namn (CNAME) med din domän leverantör

Skapa en CNAME-post för att peka på värd namnet. En CNAME-post är en typ av Domain Name System (DNS-post) som mappar ett käll domän namn till ett mål domän namn.

1. Logga in på din domän registrators webbplats och gå sedan till sidan för att hantera DNS-inställningar.

   Du kan hitta sidan i ett avsnitt med namnet **domän namn**, **DNS** eller **namn server hantering**.

2. Hitta avsnittet för att hantera CNAME-poster. 

   Du kanske måste gå till sidan Avancerade inställningar och leta efter **CNAME**, **alias** eller under **domäner**.

3. Skapa en CNAME-post. Ange följande objekt som en del av posten: 

   - Under domänens alias, till exempel `www` eller `photos` . Under domänen måste anges. rot domäner stöds inte. 
      
   - Det värdnamn som du hämtade i avsnittet [Hämta värd namnet för din lagrings slut punkt](#endpoint) tidigare i den här artikeln. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Steg 3: registrera din anpassade domän med Azure

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.

2. I meny fönstret, under **BLOB service**, väljer du **anpassad domän**.

   > [!NOTE]
   > Det här alternativet visas inte i konton som har funktionen hierarkiskt namn område aktive rad. För dessa konton använder du antingen PowerShell eller Azure CLI för att slutföra det här steget.

   ![alternativ för anpassad domän](./media/storage-custom-domain-name/custom-domain-button.png "anpassad domän")

   Fönstret **anpassad domän** öppnas.

3. I text rutan **domän namn** anger du namnet på din anpassade domän, inklusive under domänen  
   
   Om din domän till exempel är *contoso.com* och ditt under domän Ali Aset är *www*, anger du `www.contoso.com` . Om din under domän är *foton*, anger du `photos.contoso.com` .

4. Registrera den anpassade domänen genom att välja knappen **Spara** .

   När CNAME-posten har spridits via DNS-servrar (Domain Name servers), och om dina användare har rätt behörigheter, kan de Visa BLOB-data med hjälp av den anpassade domänen.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör följande PowerShell-kommando

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- Ersätt `<resource-group-name>` plats hållaren med namnet på resurs gruppen.

- Ersätt `<storage-account-name>` plats hållaren med namnet på lagrings kontot.

- Ersätt `<custom-domain-name>` plats hållaren med namnet på din anpassade domän, inklusive under domänen.

  Om din domän till exempel är *contoso.com* och ditt under domän Ali Aset är *www*, anger du `www.contoso.com` . Om din under domän är *foton*, anger du `photos.contoso.com` .

När CNAME-posten har spridits via DNS-servrar (Domain Name servers), och om dina användare har rätt behörigheter, kan de Visa BLOB-data med hjälp av den anpassade domänen.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör följande PowerShell-kommando

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- Ersätt `<resource-group-name>` plats hållaren med namnet på resurs gruppen.

- Ersätt `<storage-account-name>` plats hållaren med namnet på lagrings kontot.

- Ersätt `<custom-domain-name>` plats hållaren med namnet på din anpassade domän, inklusive under domänen.

  Om din domän till exempel är *contoso.com* och ditt under domän Ali Aset är *www*, anger du `www.contoso.com` . Om din under domän är *foton*, anger du `photos.contoso.com` .

När CNAME-posten har spridits via DNS-servrar (Domain Name servers), och om dina användare har rätt behörigheter, kan de Visa BLOB-data med hjälp av den anpassade domänen.

---

#### <a name="step-4-test-your-custom-domain"></a>Steg 4: testa din anpassade domän

För att bekräfta att din anpassade domän är mappad till BLOB service-slutpunkten skapar du en BLOB i en offentlig behållare i ditt lagrings konto. I en webbläsare öppnar du sedan bloben med hjälp av en URI i följande format: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Om du till exempel vill komma åt ett webb formulär i `myforms` behållaren i den anpassade under domänen *photos.contoso.com* kan du använda följande URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mappa en anpassad domän med noll avbrotts tid

> [!NOTE]
> Om du inte är bekymrad om att domänen inte är tillgänglig för dina användare kan du överväga att följa stegen i avsnittet [Mappa en anpassad domän](#map-a-domain) i den här artikeln. Det är en enklare metod med färre steg.  

Om din domän för närvarande har stöd för ett program med ett service nivå avtal som kräver noll avbrotts tid följer du de här stegen för att se till att användarna kan komma åt din domän när DNS-mappningen äger rum. 

: heavy_check_mark: steg 1: Hämta värd namnet för din lagrings slut punkt.

: heavy_check_mark: steg 2: skapa en CNAME-post (intermediat kanoniskt namn) med din domän leverantör.

: heavy_check_mark: steg 3: för att registrera den anpassade domänen med Azure.

: heavy_check_mark: steg 4: skapa en CNAME-post med din domän leverantör.

: heavy_check_mark: steg 5: testa din anpassade domän.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Steg 1: Hämta värd namnet för din lagrings slut punkt 

Värd namnet är URL: en för lagrings slut punkt utan protokoll-ID och avslutande snedstreck. 

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.

2. I meny fönstret, under **Inställningar**, väljer du **Egenskaper**.  

3. Kopiera värdet för den **primära BLOB service-slutpunkten** eller den **primära statiska webbplats slut punkten** till en textfil. 

   > [!NOTE]
   > Data Lake lagrings slut punkten stöds inte (till exempel: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Ta bort protokoll identifieraren (till exempel: `HTTPS` ) och avslutande snedstreck från den strängen. Följande tabell innehåller exempel.

   | Typ av slut punkt |  slutpunkt | värdnamn |
   |------------|-----------------|-------------------|
   |BLOB service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statisk webbplats  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ange det här värdet undan för senare.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Steg 2: skapa en CNAME-post (intermediat kanoniskt namn) med din domän leverantör

Skapa en tillfällig CNAME-post för att peka på värd namnet. En CNAME-post är en typ av DNS-post som mappar ett källdomännamn till ett måldomännamn.

1. Logga in på din domän registrators webbplats och gå sedan till sidan för att hantera DNS-inställningar.

   Du kan hitta sidan i ett avsnitt med namnet **domän namn**, **DNS** eller **namn server hantering**.

2. Hitta avsnittet för att hantera CNAME-poster. 

   Du kanske måste gå till sidan Avancerade inställningar och leta efter **CNAME**, **alias** eller under **domäner**.

3. Skapa en CNAME-post. Ange följande objekt som en del av posten: 

   - Under domänens alias, till exempel `www` eller `photos` . Under domänen måste anges. rot domäner stöds inte.

     Lägg till under `asverify` domänen i aliaset. Till exempel: `asverify.www` eller `asverify.photos` .
       
   - Det värdnamn som du hämtade i avsnittet [Hämta värd namnet för din lagrings slut punkt](#endpoint) tidigare i den här artikeln. 

     Lägg till under domänen `asverify` till värd namnet. Exempel: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Steg 3: för att registrera din anpassade domän med Azure

När du förregistrerar din anpassade domän med Azure tillåter du att Azure känner igen din anpassade domän utan att behöva ändra DNS-posten för domänen. På så sätt kommer den att mappas till BLOB-slutpunkten utan avbrott när du ändrar DNS-posten för domänen.

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.

2. I meny fönstret, under **BLOB service**, väljer du **anpassad domän**.

   > [!NOTE]
   > Det här alternativet visas inte i konton som har funktionen hierarkiskt namn område aktive rad. För dessa konton använder du antingen PowerShell eller Azure CLI för att slutföra det här steget.

   ![alternativ för anpassad domän](./media/storage-custom-domain-name/custom-domain-button.png "anpassad domän")

   Fönstret **anpassad domän** öppnas.

3. I text rutan **domän namn** anger du namnet på din anpassade domän, inklusive under domänen  
   
   Om din domän till exempel är *contoso.com* och ditt under domän Ali Aset är *www*, anger du `www.contoso.com` . Om din under domän är *foton*, anger du `photos.contoso.com` .

4. Markera kryss rutan **Använd indirekt CNAME-validering** .

5. Registrera den anpassade domänen genom att välja knappen **Spara** .
  
   Om registreringen lyckas meddelar portalen dig att ditt lagrings konto har uppdaterats. Din anpassade domän har verifierats av Azure, men trafik till din domän har inte dirigerats till ditt lagrings konto förrän du har skapat en CNAME-post med din domän leverantör. Det gör du i nästa avsnitt.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör följande PowerShell-kommando

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- Ersätt `<resource-group-name>` plats hållaren med namnet på resurs gruppen.

- Ersätt `<storage-account-name>` plats hållaren med namnet på lagrings kontot.

- Ersätt `<custom-domain-name>` plats hållaren med namnet på din anpassade domän, inklusive under domänen.

  Om din domän till exempel är *contoso.com* och ditt under domän Ali Aset är *www*, anger du `www.contoso.com` . Om din under domän är *foton*, anger du `photos.contoso.com` .

Trafik till din domän dirigeras inte ännu till ditt lagrings konto förrän du skapar en CNAME-post med din domän leverantör. Det gör du i nästa avsnitt.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör följande PowerShell-kommando

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- Ersätt `<resource-group-name>` plats hållaren med namnet på resurs gruppen.

- Ersätt `<storage-account-name>` plats hållaren med namnet på lagrings kontot.

- Ersätt `<custom-domain-name>` plats hållaren med namnet på din anpassade domän, inklusive under domänen.

  Om din domän till exempel är *contoso.com* och ditt under domän Ali Aset är *www*, anger du `www.contoso.com` . Om din under domän är *foton*, anger du `photos.contoso.com` .

Trafik till din domän dirigeras inte ännu till ditt lagrings konto förrän du skapar en CNAME-post med din domän leverantör. Det gör du i nästa avsnitt.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Steg 4: skapa en CNAME-post med din domän leverantör

Skapa en tillfällig CNAME-post för att peka på värd namnet.

1. Logga in på din domän registrators webbplats och gå sedan till sidan för att hantera DNS-inställningar.

   Du kan hitta sidan i ett avsnitt med namnet **domän namn**, **DNS** eller **namn server hantering**.

2. Hitta avsnittet för att hantera CNAME-poster. 

   Du kanske måste gå till sidan Avancerade inställningar och leta efter **CNAME**, **alias** eller under **domäner**.

3. Skapa en CNAME-post. Ange följande objekt som en del av posten: 

   - Under domänens alias, till exempel `www` eller `photos` . Under domänen måste anges. rot domäner stöds inte.
      
   - Det värdnamn som du hämtade i avsnittet [Hämta värd namnet för din lagrings slut punkt](#endpoint-2) tidigare i den här artikeln. 

#### <a name="step-5-test-your-custom-domain"></a>Steg 5: testa din anpassade domän

För att bekräfta att din anpassade domän är mappad till BLOB service-slutpunkten skapar du en BLOB i en offentlig behållare i ditt lagrings konto. I en webbläsare öppnar du sedan bloben med hjälp av en URI i följande format: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Om du till exempel vill komma åt ett webb formulär i `myforms` behållaren i den anpassade under domänen *photos.contoso.com* kan du använda följande URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Ta bort en anpassad domän mappning

Om du vill ta bort en anpassad domän mappning avregistrerar du den anpassade domänen. Använd någon av följande procedurer.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.

2. I meny fönstret, under **BLOB service**, väljer du **anpassad domän**.  
   Fönstret **anpassad domän** öppnas.

3. Ta bort innehållet i text rutan som innehåller ditt anpassade domän namn.

4. Välj knappen **Spara**.

När den anpassade domänen har tagits bort visas ett Portal meddelande om att ditt lagrings konto har uppdaterats.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill ta bort en anpassad domän registrering använder du PowerShell-cmdleten [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger sedan en tom sträng ( `""` ) för `-CustomDomainName` argumentvärdet.

* Kommando format:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Kommando exempel:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort en anpassad domän registrering använder du kommandot [AZ Storage Account Update](/cli/azure/storage/account) CLI och anger sedan en tom sträng ( `""` ) för `--custom-domain` argumentvärdet.

* Kommando format:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Kommando exempel:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Mappa en anpassad domän med HTTPS aktiverat

Den här metoden omfattar fler steg, men det aktiverar HTTPS-åtkomst. 

Om du inte behöver användare åtkomst till ditt BLOB-eller webb innehåll med hjälp av HTTPS kan du läsa avsnittet [Mappa en anpassad domän med endast http aktiverat](#enable-http) i den här artikeln. 

1. Aktivera [Azure CDN](../../cdn/cdn-overview.md) på BLOB-eller webb slut punkten. 

   En Blob Storage slut punkt finns i [integrera ett Azure Storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   För en statisk webbplats slut punkt, se [integrera en statisk webbplats med Azure CDN](static-website-content-delivery-network.md).

2. [Mappa Azure CDN innehåll till en anpassad domän](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Aktivera HTTPS på en Azure CDN anpassad domän](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > När du uppdaterar din statiska webbplats måste du ta bort det cachelagrade innehållet på CDN Edge-servrarna genom att rensa CDN-slutpunkten. Mer information finns i [Purge an Azure CDN endpoint](../../cdn/cdn-purge-endpoint.md) (Rensa en Azure CDN-slutpunkt).

4. Valfritt Läs följande rikt linjer:

   * [Token för signatur för delad åtkomst (SAS) med Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [Http-till-https-omdirigering med Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Priser och fakturering när du använder Blob Storage med Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om den statiska webbplats som är värd för Azure Blob Storage](storage-blob-static-website.md)