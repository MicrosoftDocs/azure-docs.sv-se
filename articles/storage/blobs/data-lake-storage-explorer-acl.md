---
title: 'Storage Explorer: ange ACL: er i Azure Data Lake Storage Gen2'
description: 'Använd Azure Storage Explorer för att hantera åtkomst kontrol listor (ACL: er) i lagrings konton som har hierarkiskt namn område (HNS) aktiverat.'
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654547"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Använd Azure Storage Explorer för att hantera ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att hantera åtkomst kontrol listor (ACL: er) i lagrings konton som har hierarkiskt namn område (HNS) aktiverat.

Du kan använda Storage Explorer för att visa och sedan uppdatera ACL: er för kataloger och filer. ACL-arv är redan tillgängligt för nya underordnade objekt som skapas under en överordnad katalog. Men du kan också använda ACL-inställningar rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt. 

Den här artikeln visar hur du ändrar ACL för fil eller katalog och hur du tillämpar ACL-inställningar rekursivt i underordnade kataloger.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](../common/storage-account-create.md) anvisningarna för att skapa en.

- Azure Storage Explorer installerat på den lokala datorn. Information om hur du installerar Azure Storage Explorer för Windows, Macintosh och Linux finns i [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Storage Explorer använder sig av båda [slut punkterna](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) BLOB (blob) & data Lake Storage Gen2 (DFS) när du arbetar med Azure Data Lake Storage Gen2. Om åtkomst till Azure Data Lake Storage Gen2 har kon figurer ATS med hjälp av privata slut punkter, se till att två privata slut punkter skapas för lagrings kontot: en med den underordnade underresursen `blob` och den andra med mål under resursen `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Logga in på Storage Explorer

När du först startar Storage Explorer visas fönstret **Microsoft Azure Storage Explorer – anslut**. Storage Explorer erbjuder flera sätt att ansluta till lagringskonton, men för närvarande stöds bara ett sätt för att hantera ACL: er.

|Uppgift|Syfte|
|---|---|
|Lägga till ett Azure-konto | Omdirigerar dig till din organisations inloggnings sida för att autentisera dig för Azure. Det här är för närvarande den enda autentiseringsmetod som stöds om du vill hantera och ange ACL: er.|
|Använda en anslutningssträng eller en signatur-URI för delad åtkomst | Kan användas för direkt åtkomst till en container eller ett lagringskonto med en SAS-token eller en delad anslutningssträng. |
|Använda lagringskontots namn och nyckel| Använd lagringskontonamnet och nyckeln för ditt lagringskonto för att ansluta till Azure Storage.|

Välj **Lägg till ett Azure-konto** och klicka på **Logga in..**. Logga in på ditt Azure-konto genom att följa anvisningarna på skärmen.

![Skärm bild som visar Microsoft Azure Storage Explorer och markerar alternativet Lägg till ett Azure-konto och knappen Logga in.](media/storage-quickstart-blobs-storage-explorer/connect.png)

När anslutningen är klar läses Azure Storage Explorer in med fliken **Utforskaren** öppen. Den här vyn ger dig insikt om alla dina Azure Storage-konton samt lokal lagring som kon figurer ATS via [Azurite Storage-emulator](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) -konton eller [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) miljöer.

![Fönstret Microsoft Azure Storage Explorer – anslut](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="manage-an-acl"></a>Hantera en ACL

Högerklicka på behållaren, en katalog eller en fil och klicka sedan på **hantera Access Control listor**.  Följande skärm bild visar menyn som den visas när du högerklickar på en katalog.

> [!div class="mx-imgBorder"]
> ![Högerklicka på en katalog i Azure Storage Explorer](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

I dialog rutan **Hantera åtkomst** kan du hantera behörigheter för ägare och gruppen ägare. Du kan även lägga till nya användare och grupper i åtkomstkontrollistan, som du sedan kan lägga till behörigheter för.

> [!div class="mx-imgBorder"]
> ![Dialog rutan Hantera åtkomst](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Om du vill lägga till en ny användare eller grupp i åtkomst kontrol listan väljer du knappen **Lägg till** . Ange sedan motsvarande Azure Active Directory (Azure AD)-post som du vill lägga till i listan och välj sedan **Lägg till**.  Användaren eller gruppen visas nu i fältet **Användare och grupper:**, där du kan börja hantera deras behörigheter.

> [!NOTE]
> Vi rekommenderar att du skapar en säkerhets grupp i Azure AD och upprätthåller behörigheter för gruppen i stället för enskilda användare. Mer information om den här rekommendationen och andra rekommenderade metoder finns i [åtkomst kontroll modell i Azure Data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

Använd kryss rutorna för att ange åtkomst-och standard-ACL: er. Mer information om skillnaden mellan dessa typer av ACL: er finns i [typer av ACL: er](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>Använda ACL: er rekursivt

Du kan använda ACL-poster rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt.

Om du vill tillämpa ACL-poster rekursivt, högerklickar du på behållaren eller en katalog och klickar sedan på **sprida Access Control listor**.  Följande skärm bild visar menyn som den visas när du högerklickar på en katalog.

> [!div class="mx-imgBorder"]
> ![Högerklicka på en katalog och välja inställningen för att sprida åtkomst kontroll](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den Data Lake Storage Gen2 behörighets modellen.

> [!div class="nextstepaction"]
> [Åtkomst kontroll modell i Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)
