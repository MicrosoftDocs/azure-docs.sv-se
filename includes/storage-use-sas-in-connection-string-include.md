---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187561"
---
Om du har en URL för signatur för delad åtkomst (SAS) som ger dig åtkomst till resurser i ett lagrings konto kan du använda SAS i en anslutnings sträng. Eftersom SAS innehåller den information som krävs för att autentisera begäran, tillhandahåller en anslutnings sträng med en SAS protokollet, tjänst slut punkten och de autentiseringsuppgifter som krävs för att få åtkomst till resursen.

Om du vill skapa en anslutnings sträng som innehåller en signatur för delad åtkomst anger du strängen i följande format:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Varje tjänst slut punkt är valfri, men anslutnings strängen måste innehålla minst en.

> [!NOTE]
> Att använda HTTPS med en SAS rekommenderas som bästa praxis.
>
> Om du anger en SAS i en anslutnings sträng i en konfigurations fil kan du behöva koda specialtecken i URL: en.
>
>

### <a name="service-sas-example"></a>Exempel på tjänstens SAS
Här är ett exempel på en anslutnings sträng som innehåller en tjänst-SAS för Blob Storage:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

Här är ett exempel på samma anslutnings sträng med kodning av specialtecken:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>SAS-exempel för konto
Här är ett exempel på en anslutnings sträng som innehåller en konto-SAS för blob-och fil lagring. Observera att slut punkter för båda tjänsterna har angetts:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

Här är ett exempel på samma anslutnings sträng med URL-kodning:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

