---
title: Kryptera Azure Data Factory med kundhanterad nyckel
description: Förbättra Data Factory säkerhet med Bring Your Own Key (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: 2a943b82689184353b9d350b931a069df9c35ff2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392705"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Kryptera Azure Data Factory med Kundhanterade nycklar

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory krypterar data i vila, inklusive enhets definitioner och alla data som cachelagras medan körning pågår. Som standard krypteras data med en slumpmässigt genererad Microsoft-hanterad nyckel som är unikt kopplad till din data fabrik. För ytterligare säkerhets garantier kan du nu aktivera Bring Your Own Key (BYOK) med kund hanterade nycklar i Azure Data Factory. När du anger en kundhanterad nyckel använder Data Factory __både__ fabriks system nyckeln och CMK för att kryptera kunddata. Antingen skulle det leda till nekad åtkomst till data och fabrik.

Azure Key Vault krävs för att lagra Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Key Vault och Data Factory måste finnas i samma Azure Active Directory-klient (Azure AD) och i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)

> [!NOTE]
> En kundhanterad nyckel kan bara konfigureras på en tom data fabrik. Data fabriken kan inte innehålla några resurser, till exempel länkade tjänster, pipeliner och data flöden. Vi rekommenderar att du aktiverar kundhanterad nyckel direkt efter att du har skapat fabriken.

## <a name="about-customer-managed-keys"></a>Om Kundhanterade nycklar

Följande diagram visar hur Data Factory använder Azure Active Directory och Azure Key Vault för att göra förfrågningar med hjälp av kundhanterad nyckel:

  ![Diagram över hur Kundhanterade nycklar fungerar i Azure Data Factory](media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png)

I följande lista beskrivs de numrerade stegen i diagrammet:

1. En Azure Key Vault administratör beviljar behörigheter till krypterings nycklar till den hanterade identitet som är associerad med Data Factory
1. En Data Factory admin aktiverar kund hanterad nyckel funktion i fabriken
1. Data Factory använder den hanterade identitet som är kopplad till fabriken för att autentisera åtkomsten till Azure Key Vault via Azure Active Directory
1. Data Factory omsluter fabriks krypterings nyckeln med kund nyckeln i Azure Key Vault
1. Vid Läs-och skriv åtgärder skickar Data Factory begär anden till Azure Key Vault för att packa upp konto krypterings nyckeln för att utföra kryptering och dekryptering

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Krav – konfigurera Azure Key Vault och generera nycklar

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Aktivera mjuk borttagning och rensa inte på Azure Key Vault

Om du använder Kundhanterade nycklar med Data Factory måste två egenskaper anges på Key Vault, __mjuk borttagning__ och __Rensa inte__. De här egenskaperna kan aktive ras med antingen PowerShell eller Azure CLI på ett nytt eller befintligt nyckel valv. Information om hur du aktiverar de här egenskaperna för ett befintligt nyckel valv finns i avsnitten med rubriken _Aktivera mjuk borttagning_ och _Aktivera rensnings skydd_ i någon av följande artiklar:

- [Använda mjuk borttagning med PowerShell](../key-vault/general/key-vault-recovery.md)
- [Använda mjuk borttagning med CLI](../key-vault/general/key-vault-recovery.md)

Om du skapar en ny Azure Key Vault via Azure Portal, kan __mjuk borttagning__ och __Rensa inte__ vara aktive rad på följande sätt:

  ![Skärm bild som aktiverar mjuk borttagning och rensning när Key Vault skapas](media/enable-customer-managed-key/01-enable-purge-protection.png)

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Bevilja Data Factory åtkomst till Azure Key Vault

Se till att Azure Key Vault och Azure Data Factory finns i samma Azure Active Directory-klient (Azure AD) och i _samma region_. Från Azure Key Vault åtkomst kontroll beviljar du Data Factory-Hanterad tjänstidentitet (MSI) följande behörigheter: _Hämta_, _packa_ upp och _packa upp nyckel_. Dessa behörigheter krävs för att aktivera Kundhanterade nycklar i Data Factory.

  ![Skärm bild som aktiverar Data Factory åtkomst till Key Vault](media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png)

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Generera eller överför kundhanterad nyckel till Azure Key Vault

Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Endast 2048-bitars RSA-nycklar stöds med Data Factory kryptering. Mer information finns i [om nycklar, hemligheter och certifikat](../key-vault/general/about-keys-secrets-certificates.md).

  ![Skapa kund hanterad nyckel på skärm bilden](media/enable-customer-managed-key/03-create-key.png)

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

1. Se till att Data Factory är tom. Data fabriken kan inte innehålla några resurser, till exempel länkade tjänster, pipeliner och data flöden. För tillfället leder distributionen av kundhanterad nyckel till en icke-tom fabrik ett fel.

1. Om du vill hitta nyckel-URI: n i Azure Portal navigerar du till Azure Key Vault och väljer inställningen nycklar. Välj önskad nyckel och klicka sedan på nyckeln för att visa dess versioner. Välj en nyckel version som du vill visa inställningarna för

1. Kopiera värdet för fältet nyckel identifierare, som tillhandahåller URI: n

    ![Skärm bild Hämta nyckel-URI från Key Vault](media/enable-customer-managed-key/04-get-key-identifier.png)

1. Starta Azure Data Factory Portal och Använd navigerings fältet till vänster och gå till Data Factory Hanteringsportal

1. Klicka på __hanterade nyckel__ ikon för kund

    ![Skärm bild som aktiverar kundhanterad nyckel i Data Factory](media/enable-customer-managed-key/05-customer-managed-key-configuration.png)

1. Ange URI: n för kundhanterad nyckel som du kopierade tidigare

1. Klicka på __Spara__ och hanterade nyckel kryptering har Aktiver ats för Data Factory

## <a name="update-key-version"></a>Uppdatera nyckel version

När du skapar en ny version av en nyckel uppdaterar du data fabriken för att använda den nya versionen. Följ liknande steg som beskrivs i avsnittet _aktivera Customer-Managed nycklar_, inklusive:

1. Hitta URI: n för den nya nyckel versionen via Azure Key Vault Portal

1. Navigera till __kund hanterad nyckel__ inställning

1. Ersätt och klistra in i URI: n för den nya nyckeln

1. Klicka på __Spara__ och Data Factory kommer nu att krypteras med den nya nyckel versionen

## <a name="use-a-different-key"></a>Använd en annan nyckel

Om du vill ändra nyckeln som används för Data Factory kryptering måste du manuellt uppdatera inställningarna i Data Factory. Följ liknande steg som beskrivs i avsnittet _aktivera Customer-Managed nycklar_, inklusive:

1. Hitta URI: n för den nya nyckeln via Azure Key Vault Portal

1. Navigera till inställningen __kund hanterade-nyckel__

1. Ersätt och klistra in i URI: n för den nya nyckeln

1. Klicka på __Spara__ och Data Factory kommer nu att krypteras med den nya nyckeln

## <a name="disable-customer-managed-keys"></a>Inaktivera Customer-Managed nycklar

Efter designen kan du inte ta bort det extra säkerhets steget när funktionen kundhanterad nyckel är aktive rad. Vi förväntar alltid sig en kund som har fått nyckeln för att kryptera fabrik och data.

## <a name="next-steps"></a>Nästa steg

Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier.