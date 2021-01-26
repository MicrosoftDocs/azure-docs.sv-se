---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80e34c117c02c2126b46297c4219effa85e9caa6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792446"
---
- Kopiera inte filer direkt till någon av de förskapade resurserna. Du måste skapa en mapp under resursen och sedan kopiera filer till den mappen.
- En mapp under *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* är en behållare. Till exempel skapas behållare som *StorageAccount_BlockBlob/container* och *StorageAccount_PageBlob/container*.
- Varje mapp som skapas direkt under *StorageAccount_AzureFiles* översätts till en Azure-filresurs.
- Om ett objekt som kopieras har samma namn som ett Azure-objekt, till exempel en BLOB eller en fil som redan finns i molnet, skrivs filen i molnet av Data Box-enhet.
- Varje fil som skrivs till *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* resurser laddas upp som en Block-Blob och Page blob.
- Azure Blob Storage stöder inte kataloger. Om du skapar en mapp under mappen *StorageAccount_BlockBlob* skapas virtuella mappar i BLOB-namnet. För Azure Files underhålls den faktiska katalog strukturen.
- En tom katalogpartition (utan några filer) som skapats under *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* mappar laddas inte upp.
- Om det uppstår några fel när data överförs till Azure skapas en fellogg på mål lagrings kontot. Sökvägen till den här fel loggen är tillgänglig när uppladdningen är klar och du kan granska loggen för att vidta lämpliga åtgärder. Ta inte bort data från källan utan att verifiera överförda data.
- Fil-och NTFS-behörigheter kan bevaras när data laddas upp till Azure Files genom att använda vägledning för att [bevara fil-ACL: er, attribut och tidsstämplar med Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).
