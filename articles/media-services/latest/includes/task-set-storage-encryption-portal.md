---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013283"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Ange kryptering för ett lagrings konto

1. I Azure Portal anger du namnet på det lagrings konto som du vill kryptera i **Sök** fältet högst upp på skärmen.  Matchningar visas under Sök fältet.
1. Välj det lagrings konto som du letar efter. Skärmen lagrings konto visas.
1. Välj **kryptering**.
1. Välj antingen Microsoft-hanterade nycklar eller kundens hanterade nycklar.

### <a name="use-microsoft-managed-keys"></a>Använd Microsoft-hanterade nycklar

Som standard krypteras data i lagrings kontot med hjälp av Microsoft-hanterade nycklar.

### <a name="use-customer-managed-keys"></a>Använda kundhanterade nycklar

1. Välj **Kundhanterade nycklar**.
1. Välj antingen **Ange nyckel-URI** eller **Välj från Key Vault**.
    1. Om du väljer **Ange nyckel-URI**, anger du nyckel-URI i fältet nyckel-URI och väljer prenumerationen. (Den kan redan väljas åt dig.)
    1. Om du väljer **Välj från Key Vault** väljer du sedan **Välj ett nyckel valv och nyckel**. Skärmen Välj nyckel från Azure Key Vault visas.
1. Välj den **Key Vault** som du vill använda och välj antingen en nyckel som du redan har i ditt nyckel valv eller **skapa en ny nyckel**.
    1. Om du väljer att skapa en ny nyckel väljer du **generera** eller **Importera** från List rutan **alternativ** . Du kan bara importera RSA-nycklar.
    1. Om du vill generera en ny nyckel ger du nyckeln ett namn i fältet **namn** och väljer sedan nyckel typen:
        1. RSA-nyckel storlekar: 2048, 3072 eller 4096. Detta är vad de flesta kunder väljer.
        1. EG-Elliptic kurv namn: P-256, P-384, P-521 eller P-256 k
        1. Du kan också ange aktiverings-och förfallo datum för nyckeln.
        1. Välj **Ja** om du vill aktivera automatisk nyckel rotation.
        1. Välj **Skapa**.
    1. Om du vill importera en nyckel väljer du den fil som ska överföras genom att klicka var som helst i fältet **Välj en fil**.
        1. Ge nyckeln ett namn i fältet **namn** .
        1. Du kan också ange aktiverings-och förfallo datum för nyckeln.
        1. Välj **Ja** om du vill aktivera automatisk nyckel rotation.
        1. Välj **Skapa**.
    1. Välj **Välj** för att välja den här nyckeln för att kryptera ditt lagrings konto. Du kommer tillbaka till krypterings skärmen.
1. **OVIKTIG!** Välj **Spara** för att spara dina krypterings inställningar eller allt du precis gjorde kommer att gå förlorat.
