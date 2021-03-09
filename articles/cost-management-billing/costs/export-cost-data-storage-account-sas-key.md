---
title: Exportera kostnads data med en Azure Storage-konto SAS-nyckel
description: Den här artikeln hjälper partners att skapa en SAS-nyckel och konfigurera Cost Management exporter.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509686"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Exportera kostnads data med en Azure Storage-konto SAS-nyckel

Följande information gäller endast för Microsoft-partner.

Partner har ofta inte sina egna Azure-prenumerationer i den klient som är associerad med sitt eget avtal för Microsoft-partner. Partner med en Microsoft partner Agreement-plan som är globala administratörer av deras fakturerings konto kan exportera och kopiera kostnads data till ett lagrings konto i en annan klient med hjälp av en SAS-nyckel (Shared Access Service). Med andra ord kan ett lagrings konto med en SAS-nyckel tillåta partner att använda ett lagrings konto som ligger utanför partner avtalet för att ta emot exporterad information. Den här artikeln hjälper partners att skapa en SAS-nyckel och konfigurera Cost Management exporter.

## <a name="requirements"></a>Krav

- Du måste vara partner med ett Microsoft partner avtal och ha kunder i Azure-planen.
- Du måste vara global administratör för din partner organisations fakturerings konto.
- Du måste ha åtkomst till att konfigurera ett lagrings konto som tillhör en annan klient organisation. Du ansvarar för att upprätthålla behörigheter och data åtkomst när du exporterar data till ditt lagrings konto.

## <a name="configure-azure-storage-with-a-sas-key"></a>Konfigurera Azure Storage med en SAS-nyckel

Hämta en SAS-token för lagrings konto eller skapa en med hjälp av Azure Portal. Använd följande steg för att skapa på Azure Portal. Mer information om SAS-nycklar finns i [bevilja begränsad åtkomst till data med signaturer för delad åtkomst (SAS).](../../storage/common/storage-sas-overview.md)

1. Navigera till lagrings kontot i Azure Portal.
    - Om ditt konto har åtkomst till flera klienter byter du katalog för att komma åt lagrings kontot. Välj ditt konto i det övre högra hörnet av Azure Portal och välj sedan **Växla kataloger**.
    - Du kan behöva logga in på Azure Portal med motsvarande klient konto för att komma åt lagrings kontot.
1. På den vänstra menyn väljer du **signatur för delad åtkomst**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Skärm bild som visar en konfigurerad signatur för delad åtkomst för Azure Storage." lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. Konfigurera token med samma inställningar som identifierades i föregående bild.
    1. Välj **BLOB** för _tillåtna tjänster_.
    1. Välj **tjänst**, **behållare** och **objekt** för _tillåtna resurs typer_.
    1. Välj **läsa**, **skriva**, **ta bort**, **lista**, **Lägg till** och **skapa** för _tillåtna behörigheter_.
    1. Välj förfallo datum och-datum. Se till att uppdatera SAS-token för export innan den upphör att gälla. Längre än den tids period som du konfigurerar före förfallo datum, körs den längre tills exporten behöver en ny SAS-token.
1. Välj **endast https** för _tillåtna protokoll_.
1. Välj **Basic** för _önskad routnings nivå_.
1. Välj **KEY1** för _signerings nyckel_. Om du roterar eller uppdaterar nyckeln som används för att signera SAS-token måste du återskapa en ny SAS-token för exporten.
1. Välj **Skapa SAS och anslutningssträng**.
    Värdet för **SAS-token** som visas är den token som du behöver när du konfigurerar export.

## <a name="create-a-new-export-with-a-sas-token"></a>Skapa en ny export med en SAS-token

Navigera till **exporter** i fakturerings konto omfånget och skapa en ny export med hjälp av följande steg.

1. Välj **Skapa**.
1. Konfigurera export informationen på samma sätt som för en normal export. Du kan konfigurera exporten så att den använder en befintlig katalog eller behållare, eller så kan du ange en ny katalog eller behållare så skapas de automatiskt.
1. När du konfigurerar lagring väljer du **Använd en SAS-token**.  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="Skärm bild som visar den nya exporten där du väljer SAS-token." lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. Ange namnet på lagrings kontot och klistra in det i din SAS-token.
1. Ange en befintlig behållare eller katalog eller identifiera de nya som ska skapas.
1. Välj **Skapa**.

SAS-baserad export fungerar bara när token är giltig. Återställ token innan den nuvarande tiden upphör att gälla, eller så slutar exporten att fungera. Eftersom token ger åtkomst till ditt lagrings konto skyddar du token på ett noggrant sätt som annan känslig information. Du är ansvarig för att upprätthålla behörigheter och data åtkomst när du exporterar data till ditt lagrings konto.

## <a name="troubleshoot-exports-using-sas-tokens"></a>Felsöka exporter med SAS-token

Följande är vanliga problem som kan uppstå när du konfigurerar eller använder SAS-baserad export.

- Du ser inte alternativet SAS-nyckel i Azure Portal.
  - Kontrol lera att du är en partner som har ett Microsoft-partner avtal och att du har global administratörs behörighet till fakturerings kontot. De är de enda personer som kan exportera med en SAS-nyckel.

- Du får följande fel meddelande när du försöker konfigurera exporten:

    **Kontrol lera att SAS-token är giltig för BLOB service, är giltig för resurs typer för behållare och objekt och har behörigheter: Lägg till skapa läsa skriva ta bort. (Lagrings tjänst fel kod: AuthorizationResourceTypeMismatch)**

    - Kontrol lera att du konfigurerar och genererar SAS-nyckeln korrekt i Azure Storage.

- Du kan inte se den fullständiga SAS-nyckeln när du har skapat en export.
  - Inte att Visa nyckeln är förväntat beteende. När SAS-exporten har kon figurer ATS är nyckeln dold av säkerhets skäl.

- Du kan inte komma åt lagrings kontot från klienten där exporten har kon figurer ATS.
  - Det är ett förväntat beteende. Om lagrings kontot finns i en annan klient måste du gå till den klient organisationen först i Azure Portal för att hitta lagrings kontot.

- Exporten Miss lyckas på grund av ett SAS-token-relaterat fel.
  - Exporten fungerar bara när SAS-token är giltiga. Skapa en ny nyckel och kör exporten.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du exporterar Cost Management data finns i [skapa och exportera data](tutorial-export-acm-data.md).
- Information om hur du exporterar stora mängder användnings data finns i [Hämta stora data mängder med export](ingest-azure-usage-at-scale.md).
