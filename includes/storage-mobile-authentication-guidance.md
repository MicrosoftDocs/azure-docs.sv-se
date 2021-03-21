---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: eeea7b1fed0c2a1f805e21b4dec9ec3cad7fc976
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996836"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurera ditt program till att få åtkomst till Azure Storage
Det finns två sätt att autentisera ditt program för att få åtkomst till lagrings tjänster:

* Delad nyckel: Använd bara delad nyckel i testnings syfte
* Signatur för delad åtkomst (SAS): Använd SAS för produktions program

### <a name="shared-key"></a>Delad nyckel
Autentisering med delad nyckel innebär att ditt program kommer att använda ditt konto namn och din konto nyckel för att komma åt lagrings tjänsterna. I syfte att snabbt visa hur du använder det här biblioteket använder vi autentisering med delad nyckel i det här kom igång.

> [!WARNING] 
> **Använd endast autentisering med delad nyckel i test syfte!** Ditt konto namn och din konto nyckel, som ger fullständig Läs-/skriv åtkomst till det tillhör ande lagrings kontot, distribueras till varje person som laddar ned din app. Detta är **inte** en bra metod när du riskerar att använda nyckeln som ej betrodda klienter har komprometterat.
> 
> 

När du använder autentisering med delad nyckel skapas en [anslutnings sträng](../articles/storage/common/storage-configure-connection-string.md). Anslutnings strängen består av:  

* **DefaultEndpointsProtocol** – du kan välja http eller https. Att använda HTTPS rekommenderas dock starkt.
* **Konto namn** – namnet på ditt lagrings konto
* **Konto nyckeln** – gå till ditt lagrings konto i [Azure Portal](https://portal.azure.com)och klicka på ikonen **nycklar** för att hitta den här informationen.
* Valfritt **EndpointSuffix** – används för lagrings tjänster i regioner med olika Endpoint-suffix, till exempel Azure Kina eller Azure-styrning.

Här är ett exempel på en anslutnings sträng med autentisering med delad nyckel:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Signaturer för delad åtkomst (SAS)
För ett mobil program är den rekommenderade metoden för att autentisera en begäran från en klient mot Azure Storage-tjänsten med hjälp av en signatur för delad åtkomst (SAS). SAS gör att du kan ge en klient åtkomst till en resurs under en angiven tids period med en angiven uppsättning behörigheter.
Som lagrings kontots ägare måste du skapa en SAS för dina mobila klienter att använda. För att generera SAS, vill du förmodligen skriva en separat tjänst som genererar SAS som ska distribueras till dina klienter. I test syfte kan du använda [Microsoft Azure Storage Explorer](https://storageexplorer.com) eller [Azure Portal](https://portal.azure.com) för att skapa en SAS. När du skapar SAS kan du ange det tidsintervall under vilket SAS är giltig och de behörigheter som SAS ger till klienten.

I följande exempel visas hur du använder Microsoft Azure Storage Explorer för att skapa en SAS.

1. Om du inte redan har gjort [det installerar du Microsoft Azure Storage Explorer](https://storageexplorer.com)
2. Ansluta till din prenumeration.
3. Klicka på ditt lagrings konto och klicka på fliken "åtgärder" längst ned till vänster. Klicka på "Hämta signatur för delad åtkomst" för att skapa en "anslutnings sträng" för dina SAS.
4. Här är ett exempel på en SAS-anslutningssträng som ger Läs-och skriv behörigheter på tjänst-, container-och objekt nivå för Blob-tjänsten för lagrings kontot.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Som du kan se visar du inte din konto nyckel i ditt program när du använder en SAS. Du kan lära dig mer om SAS och bästa praxis för att använda SAS genom att checka ut [signaturer för delad åtkomst: förstå SAS-modellen](../articles/storage/common/storage-sas-overview.md).