---
title: Kopiera eller flytta data till Azure Storage med AzCopy v10 | Microsoft Docs
description: AzCopy är ett kommando rads verktyg som du kan använda för att kopiera data till, från eller mellan lagrings konton. Den här artikeln hjälper dig att ladda ned AzCopy, ansluta till ditt lagringskonto och sedan överföra filer.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: ab3cb403a8757f424a141206f99f9c29bf3e0b49
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108156"
---
# <a name="get-started-with-azcopy"></a>Kom igång med AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att ladda ned AzCopy, ansluta till ditt lagringskonto och sedan överföra filer.

> [!NOTE]
> AzCopy **v10** är den version av AzCopy som stöds för tillfället.
>
> Om du behöver använda en tidigare version av AzCopy kan du läsa avsnittet [använda den tidigare versionen av AzCopy](#previous-version) i den här artikeln.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Ladda ned AzCopy

Börja med att ladda ned den körbara filen AzCopy v10 till valfri katalog på datorn. AzCopy v10 är bara en körbar fil, så det finns inget att installera.

- [Windows 64-bitars](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bitars](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Filerna komprimeras som en zip-fil (Windows och Mac) eller en tar-fil (Linux). Information om hur du laddar ned och dekomprimerar filen tar i Linux finns i dokumentationen för din Linux-distribution.

> [!NOTE]
> Om du vill kopiera data till och från [Azure Table Storage](../tables/table-storage-overview.md) -tjänsten installerar du [AzCopy version 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Köra AzCopy

Det blir enklare om du lägger till katalogplatsen för den körbara AzCopy-filen i din systemsökväg. På så sätt kan du skriva `azcopy` från valfri katalog i systemet.

Om du väljer att inte lägga till katalogen AzCopy i sökvägen måste du ändra katalogerna till platsen för din AzCopy-körbara fil och skriva `azcopy` eller `.\azcopy` i Windows PowerShell-Kommandotolken.

Om du vill se en lista över kommandon skriver du `azcopy -h` och trycker sedan på RETUR-tangenten.

Om du vill veta mer om ett speciellt kommando inkluderar du bara namnet på kommandot (till exempel: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Infogad hjälp](media/storage-use-azcopy-v10/azcopy-inline-help.png)


För att hitta detaljerad referens dokumentation för varje kommando och kommando parameter, se [AzCopy](storage-ref-azcopy.md)

> [!NOTE] 
> Som ägare till ditt Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data. Innan du kan göra allt meningsfullt med AzCopy måste du bestämma hur du ska ange autentiseringsuppgifter för lagrings tjänsten. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>Auktorisera AzCopy

Du kan ange autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (AD) eller genom att använda en SAS-token (signatur för delad åtkomst).

Använd den här tabellen som en guide:

| Lagringstyp | För närvarande stöds metoden för auktorisering |
|--|--|
|**Blob Storage** | Azure AD och SAS |
|**Blob Storage (hierarkiskt namn område)** | Azure AD och SAS |
|**File Storage** | Endast SAS |

#### <a name="option-1-use-azure-active-directory"></a>Alternativ 1: Använd Azure Active Directory

Det här alternativet är endast tillgängligt för Blob Storage. Med hjälp av Azure Active Directory kan du ange autentiseringsuppgifter en gång i stället för att behöva lägga till en SAS-token i varje kommando.  

> [!NOTE]
> Om du planerar att kopiera blobbar mellan lagrings konton i den aktuella versionen måste du lägga till en SAS-token till varje käll-URL. Du kan bara utelämna SAS-token från mål-URL: en. Exempel finns i [Kopiera blobbar mellan lagrings konton](storage-use-azcopy-blobs.md).

Information om hur du auktoriserar åtkomst med hjälp av Azure AD finns i [bevilja åtkomst till blobbar med AzCopy och Azure Active Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Alternativ 2: Använd en SAS-token

Du kan lägga till en SAS-token för varje käll-eller mål-URL som används i dina AzCopy-kommandon.

Det här exempel kommandot kopierar data rekursivt från en lokal katalog till en BLOB-behållare. En fiktiv SAS-token läggs till i slutet av behållar-URL: en.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Mer information om SAS-token och hur du hämtar en finns i [använda signaturer för delad åtkomst (SAS)](./storage-sas-overview.md).

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Överföra data

När du har auktoriserat din identitet eller fått en SAS-token kan du börja överföra data.

Du hittar exempel kommandon i någon av de här artiklarna.

| Tjänst | Artikel |
|--------|-----------|
|Azure Blob Storage |[Ladda upp filer till Azure Blob Storage](storage-use-azcopy-blobs-upload.md)<br><br>[Ladda ned blobbar från Azure Blob Storage](storage-use-azcopy-blobs-download.md)<br><br>[Kopiera blobbar mellan Azure Storage-konton](storage-use-azcopy-blobs-copy.md)<br><br>[Synkronisera med Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)|
|Amazon S3|[Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)|
|Azure Stack lagring|[Överföra data med AzCopy och Azure Stack Storage](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>Använd i ett skript

#### <a name="obtain-a-static-download-link"></a>Hämta en statisk nedladdnings länk

Med tiden kommer [nedladdnings länken](#download-and-install-azcopy) för AzCopy att peka på nya versioner av AzCopy. Om skriptet laddar ned AzCopy kan skriptet sluta fungera om en nyare version av AzCopy ändrar de funktioner som skriptet är beroende av.

Undvik dessa problem genom att hämta en statisk (oförändrad) länk till den aktuella versionen av AzCopy. På så sätt laddar skriptet ned samma exakta version av AzCopy varje gång den körs.

Hämta länken genom att köra det här kommandot:

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> För Linux `--strip-components=1` `tar` tar kommandot bort den översta mappen som innehåller versions namnet för Linux och extraherar i stället binärfilen direkt till den aktuella mappen. Detta gör att skriptet kan uppdateras med en ny version av `azcopy` genom att bara uppdatera `wget` URL: en.

URL: en visas i kommandots utdata. Skriptet kan sedan hämta AzCopy med hjälp av den URL: en.

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Escape-specialtecken i SAS-token

I kommandofiler med `.cmd` tillägget måste du undanta de `%` tecken som visas i SAS-token. Du kan göra det genom att lägga till ytterligare ett `%` tecken bredvid befintliga `%` tecken i SAS token-strängen.

#### <a name="run-scripts-by-using-jenkins"></a>Köra skript med Jenkins

Om du planerar att använda [Jenkins](https://jenkins.io/) för att köra skript, se till att placera följande kommando i början av skriptet.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Använd i Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) använder AzCopy för att utföra alla dess data överförings åtgärder. Du kan använda [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om du vill dra nytta av prestanda fördelarna med AZCopy, men du föredrar att använda ett grafiskt användar gränssnitt i stället för kommando raden för att interagera med dina filer.

Storage Explorer använder din konto nyckel för att utföra åtgärder, så när du har loggat in på Storage Explorer behöver du inte ange ytterligare autentiseringsuppgifter för auktorisering.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Konfigurera, optimera och åtgärda

Se [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Använd en tidigare version

Om du behöver använda den tidigare versionen av AzCopy, se någon av följande länkar:

- [AzCopy i Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy på Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Nästa steg

Om du har frågor, problem eller allmän feedback kan du skicka dem [till GitHub](https://github.com/Azure/azure-storage-azcopy) -sidan.
