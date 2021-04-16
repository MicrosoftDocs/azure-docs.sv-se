---
title: Mappa en anpassad domän till en Azure Blob Storage slutpunkt
titleSuffix: Azure Storage
description: Mappa en anpassad domän till en Blob Storage eller webbslutpunkt i ett Azure Storage-konto.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 45ae3d80202bfb29074461f899798d278eb0895b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538349"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mappa en anpassad domän till en Azure Blob Storage slutpunkt

Du kan mappa en anpassad domän till en blobtjänstslutpunkt eller en [slutpunkt för en statisk](storage-blob-static-website.md) webbplats. 

> [!NOTE] 
> Den här mappningen fungerar bara för underdomäner (till exempel: `www.contoso.com` ). Om du vill att webbslutpunkten ska vara tillgänglig i rotdomänen (till exempel : ) måste `contoso.com` du använda Azure CDN. Mer information finns i avsnittet [Mappa en anpassad domän med HTTPS aktiverat i](#enable-https) den här artikeln. Eftersom du går till det avsnittet i den här artikeln för att aktivera rotdomänen för din anpassade domän är steget i det avsnittet för att aktivera HTTPS valfritt. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mappa en anpassad domän med endast HTTP aktiverat

Den här metoden är enklare, men aktiverar endast HTTP-åtkomst. Om lagringskontot är konfigurerat för [att kräva säker överföring](../common/storage-require-secure-transfer.md) via HTTPS måste du aktivera HTTPS-åtkomst för din anpassade domän. 

Information om hur du aktiverar HTTPS-åtkomst finns [i avsnittet Mappa en anpassad domän med HTTPS aktiverat](#enable-https) i den här artikeln. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Mappa en anpassad domän

> [!IMPORTANT]
> Din anpassade domän är inte tillgänglig för användarna under tiden du slutför konfigurationen. Om din domän för närvarande stöder ett program med ett serviceavtal (SLA) som [](#zero-down-time) kräver noll driftstopp följer du stegen i avsnittet Mappa en anpassad domän utan avbrottstid i den här artikeln för att säkerställa att användarna kan komma åt din domän medan DNS-mappningen äger rum.

Följ dessa steg om du inte är orolig för att domänen inte är tillgänglig för dina användare.

:heavy_check_mark: Steg 1: Hämta värdnamnet för lagringsslutpunkten.

:heavy_check_mark: Steg 2: Skapa en CNAME-post (kanoniskt namn) med din domänleverantör.

:heavy_check_mark: Steg 3: Registrera den anpassade domänen med Azure. 

:heavy_check_mark: Steg 4: Testa din anpassade domän.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Steg 1: Hämta värdnamnet för lagringsslutpunkten 

Värdnamnet är lagringsslutpunktens URL utan protokoll-ID och avslutande snedstreck. 

1. I [Azure Portal](https://portal.azure.com)du till ditt lagringskonto.

2. I menyfönstret under **Inställningar** väljer du **Egenskaper.**  

3. Kopiera värdet för den primära **blobtjänstslutpunkten eller** slutpunkten **för den primära statiska webbplatsen** till en textfil. 
  
   > [!NOTE]
   > Data Lake-lagringsslutpunkten stöds inte (till exempel: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Ta bort protokollidentifieraren (till exempel `HTTPS` ) och det avslutande snedstrecket från strängen. Följande tabell innehåller exempel.

   | Typ av slutpunkt |  slutpunkt | värdnamn |
   |------------|-----------------|-------------------|
   |blob-tjänst  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statisk webbplats  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Reservera det här värdet för senare.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Steg 2: Skapa en CNAME-post (kanoniskt namn) med din domänleverantör

Skapa en CNAME-post som pekar på värdnamnet. En CNAME-post är en typ Domain Name System (DNS)-post som mappar ett källdomännamn till ett måldomännamn.

1. Logga in på din domänregistrators webbplats och gå sedan till sidan för att hantera DNS-inställningen.

   Du kan hitta sidan i ett avsnitt med **namnet Domännamn,** **DNS** eller **Namnserverhantering.**

2. Leta reda på avsnittet för att hantera CNAME-poster. 

   Du kan behöva gå till en sida med avancerade inställningar och leta efter **CNAME,** **Alias** **eller Underdomäner.**

3. Skapa en CNAME-post. Som en del av posten anger du följande objekt: 

   - Underdomänalias som `www` eller `photos` . Underdomänen krävs, rotdomäner stöds inte. 
      
   - Värdnamnet som du fick i avsnittet [Hämta värdnamnet för lagringsslutpunkten tidigare](#endpoint) i den här artikeln. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Steg 3: Registrera din anpassade domän med Azure

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)du till ditt lagringskonto.

2. I menyfönstret under **Blob Service väljer** du Anpassad **domän.**

   > [!NOTE]
   > Det här alternativet visas inte i konton som har funktionen hierarkisk namnrymd aktiverad. För dessa konton använder du antingen PowerShell eller Azure CLI för att slutföra det här steget.

   ![alternativet anpassad domän](./media/storage-custom-domain-name/custom-domain-button.png "anpassad domän")

   Fönstret **Anpassad** domän öppnas.

3. I **textrutan Domännamn** anger du namnet på din anpassade domän, inklusive underdomänen  
   
   Om din domän till exempel är *contoso.com* och underdomänaliaset är *www anger* du `www.contoso.com` . Om din underdomän är *foton anger* du `photos.contoso.com` .

4. Om du vill registrera den anpassade domänen väljer du **knappen** Spara.

   När CNAME-posten har spridits via DNS (Domain Name Servers) och om användarna har rätt behörigheter kan de visa blobdata med hjälp av den anpassade domänen.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör följande PowerShell-kommando

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- Ersätt `<resource-group-name>` platshållaren med namnet på resursgruppen.

- Ersätt `<storage-account-name>` platshållaren med namnet på lagringskontot.

- Ersätt `<custom-domain-name>` platshållaren med namnet på din anpassade domän, inklusive underdomänen.

  Om din domän till exempel är *contoso.com* och underdomänaliaset är *www anger* du `www.contoso.com` . Om din underdomän är *foton anger* du `photos.contoso.com` .

När CNAME-posten har spridits via DNS (Domain Name Servers) och om användarna har rätt behörigheter kan de visa blobdata med hjälp av den anpassade domänen.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör följande PowerShell-kommando

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- Ersätt `<resource-group-name>` platshållaren med namnet på resursgruppen.

- Ersätt `<storage-account-name>` platshållaren med namnet på lagringskontot.

- Ersätt `<custom-domain-name>` platshållaren med namnet på din anpassade domän, inklusive underdomänen.

  Om din domän till exempel är *contoso.com* och underdomänaliaset är *www anger* du `www.contoso.com` . Om din underdomän är *foton anger* du `photos.contoso.com` .

När CNAME-posten har spridits via DNS (Domain Name Servers) och om användarna har rätt behörigheter kan de visa blobdata med hjälp av den anpassade domänen.

---

#### <a name="step-4-test-your-custom-domain"></a>Steg 4: Testa din anpassade domän

Bekräfta att din anpassade domän är mappad till blobtjänstslutpunkten genom att skapa en blob i en offentlig container i ditt lagringskonto. Öppna sedan bloben i en webbläsare med hjälp av en URI i följande format: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Om du till exempel vill komma åt ett `myforms` webbformulär *i containern i photos.contoso.com* anpassade underdomänen kan du använda följande URI: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mappa en anpassad domän utan avbrottstid

> [!NOTE]
> Om du inte är orolig för att domänen inte är tillgänglig för dina användare kan du överväga att använda stegen i avsnittet [Mappa](#map-a-domain) en anpassad domän i den här artikeln. Det är en enklare metod med färre steg.  

Om din domän för närvarande stöder ett program med ett serviceavtal (SLA) som kräver noll driftstopp, följer du dessa steg för att säkerställa att användarna kan komma åt din domän medan DNS-mappningen äger rum. 

:heavy_check_mark: Steg 1: Hämta värdnamnet för lagringsslutpunkten.

:heavy_check_mark: Steg 2: Skapa en mellanliggande CNAME-post (kanoniskt namn) med din domänleverantör.

:heavy_check_mark: Steg 3: Förregistrera den anpassade domänen med Azure.

:heavy_check_mark: Steg 4: Skapa en CNAME-post med din domänleverantör.

:heavy_check_mark: Steg 5: Testa din anpassade domän.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Steg 1: Hämta värdnamnet för lagringsslutpunkten 

Värdnamnet är lagringsslutpunktens URL utan protokollidentifieraren och avslutande snedstreck. 

1. I [Azure Portal](https://portal.azure.com)du till ditt lagringskonto.

2. I menyfönstret under Inställningar **väljer** du **Egenskaper**.  

3. Kopiera värdet för slutpunkten för **den primära blobtjänsten eller** **slutpunkten för den primära statiska webbplatsen** till en textfil. 

   > [!NOTE]
   > Data Lake-lagringsslutpunkten stöds inte (till exempel: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Ta bort protokollidentifieraren (till exempel `HTTPS` ) och det avslutande snedstrecket från strängen. Följande tabell innehåller exempel.

   | Typ av slutpunkt |  slutpunkt | värdnamn |
   |------------|-----------------|-------------------|
   |blob-tjänst  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statisk webbplats  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ställ in det här värdet åt sidan för senare.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Steg 2: Skapa en mellanliggande CNAME-post (canonical name) med din domänleverantör

Skapa en tillfällig CNAME-post som pekar på värdnamnet. En CNAME-post är en typ av DNS-post som mappar ett källdomännamn till ett måldomännamn.

1. Logga in på domänregistratorns webbplats och gå sedan till sidan för att hantera DNS-inställningen.

   Du kan hitta sidan i ett avsnitt med **namnet Domännamn,** **DNS** eller **Namnserverhantering.**

2. Leta reda på avsnittet för att hantera CNAME-poster. 

   Du kan behöva gå till en sida med avancerade inställningar och leta efter **CNAME,** **Alias** eller **Underdomäner.**

3. Skapa en CNAME-post. Som en del av posten anger du följande objekt: 

   - Underdomänalias som `www` eller `photos` . Underdomänen krävs, rotdomäner stöds inte.

     Lägg till `asverify` underdomänen till aliaset. Till exempel: `asverify.www` eller `asverify.photos` .
       
   - Värdnamnet som du fick i avsnittet [Hämta värdnamnet för lagringsslutpunkten tidigare](#endpoint) i den här artikeln. 

     Lägg till `asverify` underdomänen i värdnamnet. Exempel: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Steg 3: Förregistrera din anpassade domän med Azure

När du förregistrerar din anpassade domän med Azure tillåter du att Azure identifierar din anpassade domän utan att behöva ändra DNS-posten för domänen. På så sätt mappas den till blobslutpunkten utan avbrott när du ändrar DNS-posten för domänen.

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)du till ditt lagringskonto.

2. I menyfönstret under **Blob Service väljer** du Anpassad **domän.**

   > [!NOTE]
   > Det här alternativet visas inte i konton som har funktionen hierarkisk namnrymd aktiverad. För dessa konton använder du antingen PowerShell eller Azure CLI för att slutföra det här steget.

   ![alternativet anpassad domän](./media/storage-custom-domain-name/custom-domain-button.png "anpassad domän")

   Fönstret **Anpassad** domän öppnas.

3. I **textrutan Domännamn** anger du namnet på din anpassade domän, inklusive underdomänen  
   
   Om din domän till exempel är *contoso.com* och underdomänaliaset är *www anger* du `www.contoso.com` . Om din underdomän är *foton anger* du `photos.contoso.com` .

4. Markera kryssrutan **Använd indirekt CNAME-verifiering.**

5. Om du vill registrera den anpassade domänen väljer du **knappen** Spara.
  
   Om registreringen lyckas meddelar portalen dig att ditt lagringskonto har uppdaterats. Din anpassade domän har verifierats av Azure, men trafik till din domän dirigeras ännu inte till ditt lagringskonto förrän du skapar en CNAME-post hos din domänleverantör. Det ska du göra i nästa avsnitt.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kör följande PowerShell-kommando

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- Ersätt `<resource-group-name>` platshållaren med namnet på resursgruppen.

- Ersätt `<storage-account-name>` platshållaren med namnet på lagringskontot.

- Ersätt `<custom-domain-name>` platshållaren med namnet på din anpassade domän, inklusive underdomänen.

  Om din domän till exempel är *contoso.com* underdomänalias är *www* anger du `www.contoso.com` . Om underdomänen är *photos (foton)* anger du `photos.contoso.com` .

Trafik till din domän dirigeras ännu inte till ditt lagringskonto förrän du skapar en CNAME-post hos din domänleverantör. Det ska du göra i nästa avsnitt.

##### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kör följande PowerShell-kommando

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- Ersätt `<resource-group-name>` platshållaren med namnet på resursgruppen.

- Ersätt `<storage-account-name>` platshållaren med namnet på lagringskontot.

- Ersätt `<custom-domain-name>` platshållaren med namnet på din anpassade domän, inklusive underdomänen.

  Om din domän till exempel är *contoso.com* underdomänalias är *www* anger du `www.contoso.com` . Om underdomänen är *photos (foton)* anger du `photos.contoso.com` .

Trafik till din domän dirigeras ännu inte till ditt lagringskonto förrän du skapar en CNAME-post hos din domänleverantör. Det ska du göra i nästa avsnitt.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Steg 4: Skapa en CNAME-post med din domänleverantör

Skapa en tillfällig CNAME-post som pekar på värdnamnet.

1. Logga in på domänregistratorns webbplats och gå sedan till sidan för att hantera DNS-inställningen.

   Du kan hitta sidan i ett avsnitt med **namnet Domännamn,** **DNS** eller **Namnserverhantering.**

2. Leta reda på avsnittet för att hantera CNAME-poster. 

   Du kan behöva gå till en sida med avancerade inställningar och leta efter **CNAME,** **Alias** eller **Underdomäner.**

3. Skapa en CNAME-post. Som en del av posten anger du följande objekt: 

   - Underdomänalias som `www` eller `photos` . Underdomänen krävs, rotdomäner stöds inte.
      
   - Värdnamnet som du fick i avsnittet [Hämta värdnamnet för lagringsslutpunkten tidigare](#endpoint-2) i den här artikeln. 

#### <a name="step-5-test-your-custom-domain"></a>Steg 5: Testa din anpassade domän

Bekräfta att din anpassade domän är mappad till blobtjänstslutpunkten genom att skapa en blob i en offentlig container i ditt lagringskonto. Öppna sedan bloben i en webbläsare med hjälp av en URI i följande format: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Om du till exempel vill komma åt ett `myforms` webbformulär *i containern i photos.contoso.com* anpassade underdomänen kan du använda följande URI: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Ta bort en anpassad domänmappning

Om du vill ta bort en anpassad domänmappning avregistrerar du den anpassade domänen. Använd någon av följande procedurer.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)du till ditt lagringskonto.

2. I menyfönstret under **Blob Service väljer** du Anpassad **domän.**  
   Fönstret **Anpassad** domän öppnas.

3. Rensa innehållet i textrutan som innehåller ditt anpassade domännamn.

4. Välj knappen **Spara**.

När den anpassade domänen har tagits bort visas ett portalmeddelande om att ditt lagringskonto har uppdaterats.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill ta bort en anpassad domänregistrering använder du [PowerShell-cmdleten Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger sedan en tom sträng ( `""` ) för `-CustomDomainName` argumentvärdet.

* Kommandoformat:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Kommandoexempel:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort en anpassad domänregistrering använder du CLI-kommandot [az storage account update](/cli/azure/storage/account) och anger sedan en tom sträng ( ) för `""` `--custom-domain` argumentvärdet.

* Kommandoformat:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Kommandoexempel:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Mappa en anpassad domän med HTTPS aktiverat

Den här metoden omfattar fler steg, men den möjliggör HTTPS-åtkomst. 

Om du inte behöver att användarna kommer åt ditt blob- eller webbinnehåll med hjälp av HTTPS kan du läsa avsnittet Mappa en anpassad domän med [endast HTTP](#enable-http) aktiverat i den här artikeln. 

1. Aktivera [Azure CDN på](../../cdn/cdn-overview.md) din blob- eller webbslutpunkt. 

   En slutpunkt Blob Storage Azure Storage finns i [Integrera ett Azure-lagringskonto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   En slutpunkt för en statisk webbplats finns [i Integrera en statisk webbplats med Azure CDN](static-website-content-delivery-network.md).

2. [Mappa Azure CDN innehåll till en anpassad domän](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Aktivera HTTPS på en Azure CDN anpassad domän.](../../cdn/cdn-custom-ssl.md)

   > [!NOTE] 
   > När du uppdaterar din statiska webbplats måste du rensa cachelagrat innehåll på CDN-gränsservrarna genom att rensa CDN-slutpunkten. Mer information finns i [Purge an Azure CDN endpoint](../../cdn/cdn-purge-endpoint.md) (Rensa en Azure CDN-slutpunkt).

4. (Valfritt) Granska följande vägledning:

   * [SAS-token (signatur för delad åtkomst) med Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [HTTP-till-HTTPS-omdirigering med Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Priser och fakturering när du använder Blob Storage med Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#pricing-and-billing).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om värdtjänster för statiska webbplatser i Azure Blob Storage](storage-blob-static-website.md)