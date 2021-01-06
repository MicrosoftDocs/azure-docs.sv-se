---
title: Generera en SAS-URI för en VM-avbildning – Azure Marketplace
description: Generera en URL för signatur för delad åtkomst (SAS) för en virtuell hård disk (VHD) på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 1/5/2021
ms.openlocfilehash: 560699296b8cae83413c36820106eedf7fef7414
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914169"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Så här skapar du en SAS-URI för en VM-avbildning

Under publicerings processen måste du ange en SAS-URI (Shared Access Signature) för varje virtuell hård disk som är kopplad till dina planer (tidigare kallade SKU: er). Microsoft behöver åtkomst till dessa VHD: er under certifierings processen. Du anger denna URI på fliken **planer** i Partner Center.

Att skapa SAS-URI: er för dina virtuella hård diskar uppfyller följande krav:

- De stöder bara ohanterade virtuella hård diskar.
- Endast list-och Läs behörigheter krävs. Ange inte Skriv-eller borttagnings behörighet.
- Tiden för åtkomst (utgångs datum) bör vara minst tre veckor från det att SAS-URI: n skapas.
- För att skydda mot UTC-tidsändringar anger du Start datumet till en dag före det aktuella datumet. Om det aktuella datumet är till exempel 16 juni 2020 väljer du 6/15/2020.

## <a name="generate-the-sas-address"></a>Generera SAS-adressen

Det finns två vanliga verktyg som används för att skapa en SAS-adress (URL):

1. **Azure Storage Explorer** – finns på Azure Portal.
2. **Azure CLI** – rekommenderas för operativ system som inte kommer från Windows och automatiserade eller kontinuerliga integrerings miljöer.

### <a name="using-tool-1-azure-storage-explorer"></a>Använda verktyg 1: Azure Storage Explorer

1. Gå till ditt **lagrings konto**.
1. Öppna **Storage Explorer**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Fönstret lagrings konto.":::

3. I **behållaren** högerklickar du på VHD-filen och väljer **Hämta signatur för resurs åtkomst**.
4. I dialog rutan **signatur för delad åtkomst** fyller du i följande fält:

    1. Start tid – behörighetens start datum för VHD-åtkomst. Ange ett datum som infaller en dag före det aktuella datumet.
    2. Förfallo tid – behörighetens förfallo datum för VHD-åtkomst. Ange ett datum minst tre veckor bortom det aktuella datumet.
    3. Behörigheter – Välj behörigheterna läsa och lista.
    4. Container-Level – Markera kryss rutan skapa signatur-URI för delad åtkomst på behållare nivå.

    ![Dialog rutan signatur för delad åtkomst.](media/vm/create-sas-uri-storage-explorer.png)

5. Om du vill skapa en tillhör ande SAS-URI för den här virtuella hård disken väljer du **skapa**.
6. Kopiera URI: n och spara den i en textfil på en säker plats. Den här genererade SAS-URI: n är för åtkomst på container nivå. Om du vill göra det speciellt kan du redigera text filen och lägga till VHD-namnet.
7. Infoga ditt VHD-namn efter VHD-strängen i SAS-URI: n (inklusive ett snedstreck). Den sista SAS-URI: n bör se ut så här:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Upprepa de här stegen för varje virtuell hård disk i de planer som du ska publicera.

### <a name="using-tool-2-azure-cli"></a>Använda verktyg 2: Azure CLI

1. Hämta och installera [Microsoft Azure kl](/cli/azure/install-azure-cli). Versioner är tillgängliga för Windows, macOS och olika distributioner i Linux.
2. Skapa en PowerShell-fil (fil namns tillägget. ps1), kopiera i följande kod och spara den lokalt.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Redigera filen om du vill använda följande parameter värden. Ange datum i UTC-format, t. ex. 2020-04-01T00:00:00Z.

    - konto namn – namnet på ditt Azure Storage-konto.
    - konto nyckel – din Azure Storage-kontoinformation.
    - start datum – behörighetens start datum för VHD-åtkomst. Ange ett datum en dag före det aktuella datumet.
    - förfallo Datum-datum – behörighetens förfallo datum för VHD-åtkomst. Ange ett datum minst tre veckor efter det aktuella datumet.

    Här är ett exempel på korrekt parameter värden (när detta skrivs):

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Spara ändringarna.
2. Använd någon av följande metoder för att köra det här skriptet med administratörs behörighet för att skapa en SAS-anslutningssträng för åtkomst på behållare nivå:

    - Kör skriptet från-konsolen. I Windows högerklickar du på skriptet och väljer **Kör som administratör**.
    - Kör skriptet från en PowerShell-skriptfil som [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Den här skärmen visar hur en SAS-anslutningssträng skapas i den här redigeraren:

    [![skapa en SAS-anslutningssträng i PowerShell-redigeraren](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Kopiera SAS-anslutningssträngen och spara den i en textfil på en säker plats. Redigera den här strängen för att lägga till information om VHD-platsen för att skapa den sista SAS-URI: n.
7. I Azure Portal går du till blob-lagringen som innehåller den virtuella hård disk som är associerad med den nya URI: n.
8. Kopiera URL: en för BLOB service-slutpunkten:

    ![Kopierar URL: en för BLOB service-slutpunkten.](media/vm/create-sas-uri-blob-endpoint.png)

9. Redigera text filen med SAS-anslutningssträngen från steg 6. Skapa hela SAS-URI: n med följande format:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Verifiera SAS-URI: n

Kontrol lera SAS-URI: n innan du publicerar den på Partner Center för att undvika eventuella problem som rör SAS URI post sändning av begäran. Den här processen är valfri men rekommenderas.

- URI: n innehåller fil namnet för VHD-avbildningen, inklusive fil namns tillägget `.vhd` .
- `Sp=rl` visas nära mitten av din URI. Den här strängen visar Läs-och list åtkomst har angetts.
- När `sr=c` Detta visas innebär det att åtkomst till container nivå har angetts.
- Kopiera och klistra in URI: n i en webbläsare för att testa bloben (du kan avbryta åtgärden innan nedladdningen är klar).

## <a name="next-steps"></a>Nästa steg

- Om du stöter på problem, se [fel meddelanden för virtuella datorer](azure-vm-sas-failure-messages.md).
- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Skapa ett erbjudande för virtuell dator på Azure Marketplace](azure-vm-create.md)
