---
title: 'REST API: fil Systems åtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: 'Använd WebHDFS REST API: er för att utföra fil Systems åtgärder på Azure Data Lake Storage Gen1'
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 51e0fb2ffa7b573ecfeda163d9ad99597ff735a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109212"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Fil Systems åtgärder på Azure Data Lake Storage Gen1 som använder REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

I den här artikeln får du lära dig hur du använder WebHDFS REST API: er och Data Lake Storage Gen1 REST-API: er för att utföra fil Systems åtgärder på Azure Data Lake Storage Gen1. Instruktioner för hur du utför konto hanterings åtgärder på Data Lake Storage Gen1 med REST API finns i [konto hanterings åtgärder på data Lake Storage gen1 med hjälp av REST API](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage gen1 konto**. Följ anvisningarna i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md).

* **[sväng](https://curl.haxx.se/)**. I den här artikeln används en sväng för att demonstrera hur du gör REST API anrop mot ett Data Lake Storage Gen1-konto.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hur autentiserar jag med Azure Active Directory?
Du kan använda två sätt för att autentisera med Azure Active Directory.

* För slutanvändarens autentisering för ditt program (interaktiv), se [slutanvändarens autentisering med data Lake Storage gen1 med .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Information om tjänst-till-tjänst-autentisering för ditt program (icke-interaktiv) finns i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Skapa mappar
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Använd följande cURL-kommando. Ersätt **\<yourstorename>** med namnet på data Lake Storage Gen1s kontot.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'
```

I det föregående kommandot ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtades tidigare. Det här kommandot skapar en katalog med namnet **MyTempDir** under rotmappen för ditt data Lake Storage gen1-konto.

Om åtgärden slutförs bör du se ett svar som följande fragment:

```output
{"boolean":true}
```

## <a name="list-folders"></a>Lista mappar
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Använd följande cURL-kommando. Ersätt **\<yourstorename>** med namnet på data Lake Storage Gen1s kontot.

```console
curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'
```

I det föregående kommandot ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtades tidigare.

Om åtgärden slutförs bör du se ett svar som följande fragment:

```output
{
"FileStatuses": {
    "FileStatus": [{
        "length": 0,
        "pathSuffix": "mytempdir",
        "type": "DIRECTORY",
        "blockSize": 268435456,
        "accessTime": 1458324719512,
        "modificationTime": 1458324719512,
        "replication": 0,
        "permission": "777",
        "owner": "<GUID>",
        "group": "<GUID>"
    }]
}
}
```

## <a name="upload-data"></a>Ladda upp data
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Använd följande cURL-kommando. Ersätt **\<yourstorename>** med namnet på data Lake Storage Gen1s kontot.

```console
curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'
```

I ovanstående syntax är parametern **-T** platsen för den fil som du överför.

De utdata som genereras liknar följande fragment:
   
```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
...
Content-Length: 0

HTTP/1.1 100 Continue

HTTP/1.1 201 Created
...
```

## <a name="read-data"></a>Läsa data
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Att läsa data från ett Data Lake Storage Gen1 konto är en två stegs process.

* Du skickar först en GET-begäran mot slutpunkten `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Det här anropet returnerar en plats dit du skickar nästa GET-begäran.
* Du skickar sedan GET-begäran mot slutpunkten `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Det här anropet visar innehållet i filen.

Eftersom det inte finns någon skillnad i indataparametrarna mellan det första och andra steget, kan du använda parameter `-L` för att skicka den första begäran. `-L`-alternativet kombinerar i stort sett två begärande till ett och gör att cURL gör om begäran på den nya platsen. Till sist visas utdata från alla begärananrop, som det visas i följande fragment. Ersätt **\<yourstorename>** med namnet på data Lake Storage Gen1s kontot.

```console
curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'
```

Du bör se utdata som liknar följande fragment:

```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
...

HTTP/1.1 200 OK
...

Hello, Data Lake Store user!
```

## <a name="rename-a-file"></a>Byt namn på en fil
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Använd kommandot cURL för att byta namn på en fil. Ersätt **\<yourstorename>** med namnet på data Lake Storage Gen1s kontot.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'
```

Du bör se utdata som liknar följande fragment:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="delete-a-file"></a>Ta bort en fil
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Använd kommandot cURL för att ta bort en fil. Ersätt **\<yourstorename>** med namnet på data Lake Storage Gen1s kontot.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'
```

Du bör se utdata som liknar följande:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="next-steps"></a>Nästa steg
* [Konto hanterings åtgärder på data Lake Storage gen1 med REST API](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Se även
* [Azure Data Lake Storage Gen1 REST API referens](/rest/api/datalakestore/)
* [Stor data program med öppen källkod som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)