---
title: Tjänster för anpassade kommandon tjänst kryptering av vilande data
titleSuffix: Azure Cognitive Services
description: Anpassade kommandon kryptering av data i vila.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 89d7a6f8beb004f57a00dfe75e4cc387c8591b1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716592"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Anpassade kommandon för kryptering av vilande data

Anpassade kommandon krypterar automatiskt dina data när de sparas i molnet. Den anpassade kommando tjänst Krypteringen skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

> [!NOTE]
> Custom commands-tjänsten aktiverar inte automatiskt kryptering för de LUIS-resurser som är kopplade till ditt program. Om det behövs måste du aktivera kryptering för LUIS-resursen härifrån [.](../luis/encrypt-data-at-rest.md)

## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services kryptering
Data krypteras och dekrypteras med hjälp av [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-kompatibla 256-bitars AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kryptering. Kryptering och dekryptering är transparent, vilket innebär att kryptering och åtkomst hanteras åt dig. Dina data skyddas som standard och du behöver inte ändra din kod eller dina program för att utnyttja krypteringen.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

När du använder anpassade kommandon kommer tal tjänsten att lagra följande data i molnet:
* Konfigurations-JSON bakom programmet för anpassade kommandon
* LUIS-redigering och förutsägelse nyckel

Som standard använder din prenumeration krypteringsnycklar som hanteras av Microsoft. Men du kan också hantera din prenumeration med egna krypteringsnycklar. Kundhanterade nycklar (CMK) kallas även för BYOK och ger större flexibilitet att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.


> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga resurser som skapats efter den 27 juni 2020. Om du vill använda CMK med tal tjänster måste du skapa en ny tal resurs. När resursen har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

För att begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar Customer-Managed formuläret för förfrågnings formulär. Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med tal tjänster måste du skapa en ny tal resurs från Azure Portal.
   > [!NOTE]
   > **Kundhanterade nycklar (CMK) stöds endast för anpassade kommandon.**
   >
   >  **Custom Speech och anpassad röst stöds fortfarande bara för att ta med din egen lagring (BYOS).**  [Läs mer](speech-encryption-of-data-at-rest.md)
   >
   > Om du använder den aktuella tal resursen för att komma åt dessa tjänster måste du uppfylla kraven genom att uttryckligen konfigurera BYOS.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Du måste använda Azure Key Vault för att lagra Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Tal resursen och nyckel valvet måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](../../key-vault/general/overview.md).

När en ny tal resurs skapas och används för att etablera anpassade kommandon, krypteras alltid data med hjälp av Microsoft-hanterade nycklar. Det går inte att aktivera Kundhanterade nycklar vid den tidpunkt då resursen skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckel valvet måste tillhandahållas med åtkomst principer som ger nyckel behörigheter till den hanterade identitet som är kopplad till den Cognitive Services resursen. Den hanterade identiteten är bara tillgänglig när resursen har skapats med den pris nivå som krävs för CMK.

Om du aktiverar Kundhanterade nycklar aktive ras även en systemtilldelad [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md), en funktion i Azure AD. När systemtilldelad hanterad identitet har Aktiver ATS registreras den här resursen med Azure Active Directory. När den hanterade identiteten har registrerats får den åtkomst till den Key Vault som valts under kund hanterade nyckel inställningar. 

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter tas åtkomst till nyckel valvet bort och alla data som krypteras med kund nycklarna kommer inte längre att vara tillgängliga. Alla funktioner som är beroende av dessa data upphör att fungera.

> [!IMPORTANT]
> Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger. När du konfigurerar Kundhanterade nycklar i Azure Portal tilldelas en hanterad identitet automatiskt under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller resursen från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till resursen till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **överföra en prenumeration mellan Azure AD-kataloger** i [vanliga frågor och svar och kända problem med hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Konfigurera Azure Key Vault

Om du använder Kundhanterade nycklar måste du ange två egenskaper i nyckel valvet, **mjuk borttagning** och **Rensa inte**. Dessa egenskaper är inte aktiverade som standard, men kan aktive ras med antingen PowerShell eller Azure CLI på ett nytt eller befintligt nyckel valv.

> [!IMPORTANT]
> Om du inte har den **mjuka borttagningen** och inte **rensar** de egenskaper som är aktiverade och du tar bort din nyckel, kan du inte återställa data i din kognitiva tjänst resurs.

Information om hur du aktiverar de här egenskaperna för ett befintligt nyckel valv finns i avsnitten med rubriken **Aktivera mjuk borttagning** och **Aktivera rensnings skydd** i någon av följande artiklar:

- [Använda mjuk borttagning med PowerShell](../../key-vault/general/key-vault-recovery.md).
- [Använda mjuk borttagning med CLI](../../key-vault/general/key-vault-recovery.md).

Endast RSA-nycklar med storleken 2048 stöds med Azure Storage kryptering. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](../../key-vault/general/about-keys-secrets-certificates.md).

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Aktivera Kundhanterade nycklar för din tal resurs

Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Navigera till din tal resurs.
1. På bladet **Inställningar** för din tal resurs klickar du på **kryptering**. Välj alternativet **Kundhanterade nycklar** , som du ser i följande bild.

 ![Skärm bild som visar hur du väljer Kundhanterade nycklar](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Ange en nyckel

När du har aktiverat Kundhanterade nycklar har du möjlighet att ange en nyckel som ska associeras med den Cognitive Services resursen.

### <a name="specify-a-key-as-a-uri"></a>Ange en nyckel som en URI

Följ dessa steg om du vill ange en nyckel som en URI:

1. Om du vill hitta nyckel-URI: n i Azure Portal navigerar du till nyckel valvet och väljer inställningen **nycklar** . Välj önskad nyckel och klicka sedan på nyckeln för att visa dess versioner. Välj en nyckel version för att visa inställningarna för den versionen.
1. Kopiera värdet för fältet **nyckel identifierare** , som innehåller URI.

    ![Skärm bild som visar Key Vault Key-URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. I **krypterings** inställningarna för din röst tjänst väljer du alternativet för att **Ange nyckel-URI** .
1. Klistra in den URI som du kopierade i fältet **nyckel-URI** .

1. Ange den prenumeration som innehåller nyckel valvet.
1. Spara ändringarna.

### <a name="specify-a-key-from-a-key-vault"></a>Ange en nyckel från ett nyckel valv

Om du vill ange en nyckel från ett nyckel valv måste du först kontrol lera att du har ett nyckel valv som innehåller en nyckel. Följ dessa steg om du vill ange en nyckel från ett nyckel valv:

1. Välj alternativet **Välj från Key Vault** .
1. Välj det nyckel valv som innehåller den nyckel som du vill använda.
1. Välj nyckeln från nyckel valvet.

   ![Skärm bild som visar kundhanterad nyckel alternativ](media/custom-commands/configure-cmk-fromKV.png)

1. Spara ändringarna.

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

När du skapar en ny version av en nyckel uppdaterar du tal resursen för att använda den nya versionen. Följ de här stegen:

1. Navigera till din tal resurs och visa **krypterings** inställningarna.
1. Ange URI för den nya nyckel versionen. Alternativt kan du välja nyckel valvet och nyckeln igen för att uppdatera versionen.
1. Spara ändringarna.

## <a name="use-a-different-key"></a>Använd en annan nyckel

Följ dessa steg om du vill ändra nyckeln som används för kryptering:

1. Navigera till din tal resurs och visa **krypterings** inställningarna.
1. Ange URI för den nya nyckeln. Alternativt kan du välja nyckel valvet och välja en ny nyckel.
1. Spara ändringarna.

## <a name="rotate-customer-managed-keys"></a>Rotera Kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt efterlevnadsprinciper. När nyckeln roteras måste du uppdatera tal resursen för att använda den nya nyckel-URI: n. Information om hur du uppdaterar resursen för att använda en ny version av nyckeln i Azure Portal finns i [Uppdatera nyckel versionen](#update-the-key-version).

Rotation av nyckeln utlöser inte Omkryptering av data i resursen. Det krävs ingen ytterligare åtgärd från användaren.

## <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till Kundhanterade nycklar

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) eller [Azure Key Vault CLI](/cli/azure/keyvault). Att återkalla åtkomsten på ett effektivt sätt blockerar åtkomsten till alla data i Cognitive Services resursen, eftersom krypterings nyckeln inte är tillgänglig via Cognitive Services.

## <a name="disable-customer-managed-keys"></a>Inaktivera Kundhanterade nycklar

När du inaktiverar Kundhanterade nycklar krypteras din tal resurs sedan med Microsoft-hanterade nycklar. Följ dessa steg om du vill inaktivera Kundhanterade nycklar:

1. Navigera till din tal resurs och visa **krypterings** inställningarna.
1. Avmarkera kryss rutan bredvid inställningen **Använd din egen nyckel** .

## <a name="next-steps"></a>Nästa steg

* [Formulär för begäran om tal Customer-Managed nyckel](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](../../key-vault/general/overview.md)
* [Vad är hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md)