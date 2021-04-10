---
title: Konvertera certifikat innehåll till bas-64 – Azure HDInsight
description: Konvertera innehållet i tjänstens huvud namn till det Base64 64-kodade sträng formatet i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944418"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Konverterar innehåll för tjänstens huvud namn till formatet Base-64-kodat sträng format i HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du får ett fel meddelande om att indata inte är en giltig bas-64-sträng eftersom det innehåller ett tecken som inte är av basen 64, fler än två utfyllnad i utfyllnad eller ett tecken som inte är blank steg mellan utfyllnads tecknen.

## <a name="cause"></a>Orsak

När du använder en PowerShell-eller Azure-mall för att skapa kluster med Data Lake som primär eller ytterligare lagrings utrymme, är tjänstens huvud certifikat innehåll som har åtkomst till Data Lake lagrings kontot i formatet Base-64. Felaktig konvertering av PFX-certifikatets innehåll till den Base-64-kodade strängen kan leda till det här felet.

## <a name="resolution"></a>Lösning

När du har ett tjänst huvud certifikat i PFX-format (se [här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) för exempel åtgärder för att skapa tjänstens huvud namn), använder du följande PowerShell-kommando eller C#-kodfragment för att konvertera certifikat innehållet till formatet base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]