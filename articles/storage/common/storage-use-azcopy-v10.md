---
title: Kopiera eller flytta data till Azure Storage med hjälp av AzCopy v10 | Microsoft Docs
description: AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera data till, från eller mellan lagringskonton. Den här artikeln hjälper dig att ladda ned AzCopy, ansluta till ditt lagringskonto och sedan överföra filer.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34d3bd45d2c0bf0260a4f8524cff6f8ac03b746c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501727"
---
# <a name="get-started-with-azcopy"></a>Kom igång med AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att ladda ned AzCopy, ansluta till ditt lagringskonto och sedan överföra filer.

> [!NOTE]
> AzCopy **V10** är den version av AzCopy som stöds för närvarande.
>
> Om du behöver använda en tidigare version av AzCopy kan du läsa avsnittet [Använda den tidigare versionen av AzCopy](#previous-version) i den här artikeln.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Ladda ned AzCopy

Ladda först ned den körbara AzCopy V10-filen till valfri katalog på datorn. AzCopy V10 är bara en körbar fil, så det finns inget att installera.

- [Windows 64-bitars](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bitars](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

De här filerna komprimeras som en zip-fil (Windows och Mac) eller en tar-fil (Linux). Om du vill ladda ned och dekomprimera tar-filen i Linux kan du läsa dokumentationen för din Linux-distribution.

> [!NOTE]
> Om du vill kopiera data till och från [Azure Table Storage-tjänsten](../tables/table-storage-overview.md) installerar du [AzCopy version 7.3.](https://aka.ms/downloadazcopynet)

## <a name="run-azcopy"></a>Köra AzCopy

Det blir enklare om du lägger till katalogplatsen för den körbara AzCopy-filen i din systemsökväg. På så sätt kan du `azcopy` skriva från valfri katalog i systemet.

Om du väljer att inte lägga till AzCopy-katalogen i sökvägen måste du ändra sökvägen till platsen för din körbara AzCopy-fil och skriva `azcopy` eller Windows PowerShell kommandotolken. `.\azcopy`

Som ägare till ditt Azure Storage-konto tilldelas du inte automatiskt behörighet att komma åt data. Innan du kan göra något meningsfullt med AzCopy måste du bestämma hur du ska ange autentiseringsuppgifter för lagringstjänsten. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Auktorisera AzCopy

Du kan ange autentiseringsuppgifter med hjälp Azure Active Directory (AD) eller med hjälp av en SAS-token (signatur för delad åtkomst).

Använd den här tabellen som vägledning:

| Lagringstyp | Metod för auktorisering som stöds för närvarande |
|--|--|
|**Blob Storage** | Azure AD och SAS |
|**Blob Storage (hierarkisk namnrymd)** | Azure AD och SAS |
|**File Storage** | Endast SAS |

#### <a name="option-1-use-azure-active-directory"></a>Alternativ 1: Använd Azure Active Directory

Det här alternativet är endast tillgängligt för bloblagring. Genom att Azure Active Directory kan du ange autentiseringsuppgifter en gång i stället för att behöva lägga till en SAS-token i varje kommando.  

> [!NOTE]
> Om du planerar att kopiera blobar mellan lagringskonton i den aktuella versionen måste du lägga till en SAS-token till varje käll-URL. Du kan utelämna endast SAS-token från mål-URL:en. Exempel finns i Kopiera [blobar mellan lagringskonton.](#transfer-data)

Information om hur du auktoriserar åtkomst med hjälp av Azure AD finns i Authorize [access to blobs with AzCopy and Azure Active Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md)(Auktorisera åtkomst till blobar med AzCopy och Azure Active Directory (Azure AD).

#### <a name="option-2-use-a-sas-token"></a>Alternativ 2: Använda en SAS-token

Du kan lägga till en SAS-token till varje käll- eller mål-URL som använder i dina AzCopy-kommandon.

Det här exempelkommandot kopierar rekursivt data från en lokal katalog till en blobcontainer. En fiktiv SAS-token läggs till i slutet av container-URL:en.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Mer information om SAS-token och hur du hämtar en finns i [Använda signaturer för delad åtkomst (SAS).](./storage-sas-overview.md)

> [!NOTE]
> Inställningen [Säker överföring krävs för](storage-require-secure-transfer.md) ett lagringskonto avgör om anslutningen till ett lagringskonto skyddas med Transport Layer Security (TLS). Den här inställningen är aktiverad som standard.   

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Överföra data

När du har auktoriserat din identitet eller fått en SAS-token kan du börja överföra data.

Exempelkommandon finns i någon av dessa artiklar.

| Tjänst | Artikel |
|--------|-----------|
|Azure Blob Storage|[Ladda upp filer till Azure Blob Storage](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Storage|[Ladda ned blobar från Azure Blob Storage](storage-use-azcopy-blobs-download.md)|
|Azure Blob Storage|[Kopiera blobar mellan Azure Storage-konton](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Storage|[Synkronisera med Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)|
|Amazon S3|[Kopiera data från Amazon S3 till Azure Storage](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Kopiera data från Google Cloud Storage till Azure Storage (förhandsversion)](storage-use-azcopy-google-cloud.md)|
|Azure Stack lagring|[Överföra data med AzCopy och Azure Stack storage](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>Hämta kommandohjälp

Om du vill se en lista över kommandon skriver `azcopy -h` du och trycker sedan på RETUR-tangenten.

Om du vill veta mer om ett visst kommando inkluderar du bara namnet på kommandot (till exempel: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Infogade hjälp](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>Lista över kommandon

I följande tabell visas alla AzCopy v10-kommandon. Varje kommando länkar till en referensartikel. 

|Kommando|Beskrivning|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|Kör ett prestandatest genom att ladda upp eller ned testdata till eller från en angiven plats.|
|[azcopy kopiera](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|Kopierar källdata till en målplats|
|[azcopy dok](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Genererar dokumentation för verktyget i Markdown-format.|
|[azcopy miljö](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|Visar de miljövariabler som kan konfigurera AzCopy-beteendet.|
|[azcopy jobb](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|Underkommandon som rör hantering av jobb.|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|Ta bort alla logg- och planfiler för alla jobb.|
|[azcopy jobblista](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|Visar information om alla jobb.|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|Ta bort alla filer som är associerade med det angivna jobb-ID:t.|
|[azcopy jobb-CV](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|Återupptar det befintliga jobbet med det angivna jobb-ID:t.|
|[azcopy jobbvisning](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|Visar detaljerad information om det angivna jobb-ID:t.|
|[azcopy load](storage-ref-azcopy-load.md)|Underkommandon som rör överföring av data i specifika format.|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|Överför lokala data till en container och lagrar dem i Microsofts CLFS-format (Avere Cloud FileSystem).|
|[azcopy lista](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|Visar entiteterna i en viss resurs.|
|[azcopy inloggning](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Loggar in på Azure Active Directory åtkomst till Azure Storage resurser.|
|[azcopy utloggning](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|Loggar ut användaren och avslutar åtkomsten till Azure Storage resurser.|
|[azcopy skapa](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|Skapar en container eller filresurs.|
|[azcopy ta bort](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Ta bort blobar eller filer från ett Azure Storage-konto.|
|[azcopy synkronisering](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|Replikerar källplatsen till målplatsen.|

## <a name="use-in-a-script"></a>Använda i ett skript

#### <a name="obtain-a-static-download-link"></a>Hämta en statisk nedladdningslänk

Med tiden pekar [Nedladdningslänken för](#download-and-install-azcopy) AzCopy på nya versioner av AzCopy. Om skriptet laddar ned AzCopy kan skriptet sluta fungera om en nyare version av AzCopy ändrar funktioner som skriptet är beroende av.

Undvik dessa problem genom att hämta en statisk (oföränderelse) länk till den aktuella versionen av AzCopy. På så sätt laddar skriptet ned samma exakta version av AzCopy varje gång det körs.

Hämta länken genom att köra det här kommandot:

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> För Linux tar kommandot bort mappen på den översta nivån som innehåller versionsnamnet och extraherar i stället den binära filen `--strip-components=1` direkt till den aktuella `tar` mappen. Detta gör att skriptet kan uppdateras med en ny version av `azcopy` genom att endast uppdatera `wget` URL:en.

URL:en visas i kommandots utdata. Skriptet kan sedan ladda ned AzCopy med hjälp av url:en.

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Escape-specialtecken i SAS-token

I batchfiler som har `.cmd` tillägget måste du undvika de tecken som visas i `%` SAS-token. Du kan göra det genom att lägga till ytterligare `%` ett tecken bredvid befintliga tecken i `%` SAS-tokensträngen.

#### <a name="run-scripts-by-using-jenkins"></a>Köra skript med Jenkins

Om du planerar att [använda Jenkins](https://jenkins.io/) för att köra skript ska du placera följande kommando i början av skriptet.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Använd i Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) använder AzCopy för att utföra alla dataöverföringsåtgärder. Du kan [använda Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om du vill utnyttja prestandafördelarna med AzCopy, men du föredrar att använda ett grafiskt användargränssnitt i stället för kommandoraden för att interagera med dina filer.

Storage Explorer använder din kontonyckel för att utföra åtgärder, så när du har loggat in Storage Explorer behöver du inte ange ytterligare autentiseringsuppgifter.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Konfigurera, optimera och åtgärda

Se någon av följande resurser:

- [AzCopy-konfigurationsinställningar](storage-ref-azcopy-configuration-settings.md)

- [Optimera prestanda för AzCopy](storage-use-azcopy-optimize.md)

- [Felsöka Problem med AzCopy V10 i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Använda en tidigare version

Om du behöver använda den tidigare versionen av AzCopy kan du se någon av följande länkar:

- [AzCopy i Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy på Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Nästa steg

Om du har frågor, problem eller allmän feedback kan du skicka dem [på GitHub-sidan.](https://github.com/Azure/azure-storage-azcopy)
