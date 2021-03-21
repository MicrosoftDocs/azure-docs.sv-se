---
title: Kom igång med Storage Explorer | Microsoft Docs
description: Börja hantera Azure Storage-resurser med Storage Explorer. Hämta och installera Azure Storage Explorer, Anslut till ett lagrings konto eller en tjänst med mera.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441596"
---
# <a name="get-started-with-storage-explorer"></a>Kom igång med Storage Explorer

## <a name="overview"></a>Översikt

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux.

I den här artikeln lär du dig flera olika sätt att ansluta till och hantera dina Azure Storage-konton.

:::image type="content" alt-text="Microsoft Azure Storage Explorer" source="./vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>Förutsättningar

# <a name="windows"></a>[Windows](#tab/windows)

Följande versioner av Windows stöder Storage Explorer:

* Windows 10 (rekommenderas)
* Windows 8
* Windows 7

För alla versioner av Windows kräver Storage Explorer .NET Framework 4.7.2.

# <a name="macos"></a>[macOS](#tab/macos)

Följande versioner av macOS stöder Storage Explorer:

* macOS 10,12 Sierra och senare versioner

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer finns i [Snap Store](https://snapcraft.io/storage-explorer) för de vanligaste distributionerna av Linux. Vi rekommenderar Snap Store för den här installationen. Snapin-modulen Storage Explorer installerar alla dess beroenden och uppdateringar när nya versioner publiceras i Snap-arkivet.

För distributioner som stöds, se [ `snapd` installations sidan](https://snapcraft.io/docs/installing-snapd).

Storage Explorer kräver att du använder en lösen ords hanterare. Du kan behöva ansluta till en lösen ords hanterare manuellt. Du kan ansluta Storage Explorer till systemets lösen ords hanterare genom att köra följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer är också tillgängligt som en *. tar. gz* -hämtning. Om du använder *. tar. gz* måste du installera beroenden manuellt. Följande distributioner av Linux-support *. tar. gz* -installation:

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

Installationen av *. tar. gz* kan fungera på andra distributioner, men endast dessa listade stöds officiellt.

Mer hjälp med att installera Storage Explorer på Linux finns i [Linux-beroenden](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) i Azure Storage Explorer fel söknings guide.

---

## <a name="download-and-install"></a>Hämta och installera

Information om hur du hämtar och installerar Storage Explorer finns i [Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Ansluta till ett lagringskonto eller en tjänst

Storage Explorer tillhandahåller flera olika sätt att ansluta till Azure-resurser:

* [Logga in på Azure för att få åtkomst till dina prenumerationer och deras resurser](#sign-in-to-azure)
* [Koppla till en enskild Azure Storage resurs](#attach-to-an-individual-resource)
* [Anslut till en CosmosDB-resurs](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Logga in på Azure

> [!NOTE]
> För att få fullständig åtkomst till resurser när du har loggat in, kräver Storage Explorer både hantering (Azure Resource Manager) och data lager behörigheter. Det innebär att du behöver Azure Active Directory (Azure AD)-behörigheter för att komma åt ditt lagrings konto, behållarna i kontot och data i behållarna. Om du har behörighet för data lagret kan du överväga att välja alternativet **Logga in med Azure Active Directory (Azure AD)** när du ansluter till en resurs. Mer information om de speciella behörigheter som Storage Explorer kräver finns i [fel söknings guiden för Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. I Storage Explorer väljer du **Visa**  >  **konto hantering** eller väljer knappen **Hantera konton** .

    :::image type="content" alt-text="Hantera konton" source ="./vs-storage-explorer-manage-accounts.png":::

1. **Konto hantering** visar nu alla Azure-konton som du är inloggad på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto..**..

1. Dialog rutan **Anslut till Azure Storage** öppnas. I panelen **Välj resurs** väljer du **prenumeration**.

    :::image type="content" alt-text="Dialog rutan Anslut" source="./vs-storage-explorer-connect-dialog.png":::

1. I panelen **Välj Azure-miljö** väljer du en Azure-miljö för att logga in på. Du kan logga in på Global Azure, ett nationellt moln eller en Azure Stack instans. Välj sedan **Nästa**.

    :::image type="content" alt-text="Alternativ för att logga in" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Mer information om Azure Stack finns i [ansluta Storage Explorer till en Azure Stack-prenumeration eller ett lagrings konto](/azure-stack/user/azure-stack-storage-connect-se).

1. Storage Explorer öppnar en webb sida där du kan logga in.

1. När du har loggat in med ett Azure-konto visas kontot och de Azure-prenumerationer som är kopplade till kontot under **konto hantering**. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**.

    :::image type="content" alt-text="Välja Azure-prenumerationer" source="./vs-storage-explorer-account-panel.png":::

1. **Explorer** visar de lagrings konton som är kopplade till de valda Azure-prenumerationerna.

    :::image type="content" alt-text="Valda Azure-prenumerationer" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Koppla till en enskild resurs

Med Storage Explorer kan du ansluta till enskilda resurser, till exempel en Azure Data Lake Storage Gen2 behållare, med olika autentiseringsmetoder. Vissa autentiseringsmetoder stöds bara för vissa resurs typer.

| Resurstyp    | Azure AD | Konto namn och nyckel | Signatur för delad åtkomst (SAS)  | Offentlig (anonym) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Lagringskonton | Ja      | Ja                  | Ja (anslutnings sträng eller URL) | Inga                 |
| Blobcontainrar  | Ja      | Inga                   | Ja (URL)                      | Ja                |
| Gen2 behållare  | Ja      | Inga                   | Ja (URL)                      | Ja                |
| Gen2-kataloger | Ja      | Inga                   | Ja (URL)                      | Ja                |
| Filresurser      | Inga       | Inga                   | Ja (URL)                      | Inga                 |
| Köer           | Ja      | Inga                   | Ja (URL)                      | Inga                 |
| Tables           | Inga       | Inga                   | Ja (URL)                      | Inga                 |
 
Storage Explorer kan också ansluta till en [lokal lagrings-emulator](#local-storage-emulator) med emulatorns konfigurerade portar.

Om du vill ansluta till en enskild resurs väljer du knappen **Anslut** i det vänstra verktygsfältet. Följ sedan anvisningarna för den resurs typ som du vill ansluta till.

:::image type="content" alt-text="Alternativet Anslut till Azure Storage" source="./vs-storage-explorer-connect-button.png":::

När en anslutning till ett lagrings konto har lagts till visas en ny trädnod under **lokala & anslutna**  >  **lagrings konton**.

För andra resurs typer läggs en ny nod till i **lokala & anslutna**  >  **lagrings konton**  >  **(anslutna behållare)**. Noden kommer att visas under en gruppnod som matchar dess typ. Till exempel visas en ny anslutning till en Azure Data Lake Storage Gen2 behållare under BLOB- **behållare**.

Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa mer i [fel söknings guiden för Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

I följande avsnitt beskrivs olika autentiseringsmetoder som du kan använda för att ansluta till enskilda resurser.

#### <a name="azure-ad"></a>Azure AD

Storage Explorer kan använda ditt Azure-konto för att ansluta till följande resurs typer:
* Blobcontainrar
* Azure Data Lake Storage Gen2 behållare
* Azure Data Lake Storage Gen2 kataloger
* Köer
 
Azure AD är det bästa alternativet om du har åtkomst till data lager till din resurs men ingen åtkomst till hanterings lager.

1. Logga in på minst ett Azure-konto med hjälp av [stegen som beskrivs ovan](#sign-in-to-azure).
1. I dialog rutan **Välj resurs** i dialog rutan **Anslut till Azure Storage** väljer du **BLOB-behållare**, **ADLS Gen2 behållare** eller **kö**.
1. Välj **Logga in med Azure Active Directory (Azure AD)** och välj **Nästa**.
1. Välj ett Azure-konto och klient organisation. Kontot och klienten måste ha åtkomst till den lagrings resurs som du vill ansluta till. Välj **Nästa**.
1. Ange ett visnings namn för anslutningen och URL: en för resursen. Välj **Nästa**.
1. Granska anslutnings informationen på panelen **Sammanfattning** . Om anslutnings informationen är korrekt väljer du **Anslut**.

#### <a name="account-name-and-key"></a>Konto namn och nyckel

Storage Explorer kan ansluta till ett lagrings konto med hjälp av lagrings kontots namn och nyckel.

Du kan hitta dina konto nycklar i [Azure Portal](https://portal.azure.com). Öppna sidan lagrings konto och välj **Inställningar**  >  **åtkomst nycklar**.

1. I panelen **Välj resurs** i dialog rutan **Anslut till Azure Storage** väljer du **lagrings konto**.
1. Välj **konto namn och nyckel** och välj **Nästa**.
1. Ange ett visnings namn för anslutningen, namnet på kontot och en av konto nycklarna. Välj lämplig Azure-miljö. Välj **Nästa**.
1. Granska anslutnings informationen på panelen **Sammanfattning** . Om anslutnings informationen är korrekt väljer du **Anslut**.

#### <a name="shared-access-signature-sas-connection-string"></a>Anslutnings sträng för signatur för delad åtkomst (SAS)

Storage Explorer kan ansluta till ett lagrings konto med hjälp av en anslutnings sträng med en signatur för delad åtkomst (SAS). En SAS-Anslutningssträng ser ut så här:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. I panelen **Välj resurs** i dialog rutan **Anslut till Azure Storage** väljer du **lagrings konto**.
1. Välj **signatur för delad åtkomst (SAS)** och välj **Nästa**.
1. Ange ett visnings namn för anslutningen och SAS-anslutningssträngen för lagrings kontot. Välj **Nästa**.
1. Granska anslutnings informationen på panelen **Sammanfattning** . Om anslutnings informationen är korrekt väljer du **Anslut**.

#### <a name="shared-access-signature-sas-url"></a>URL till signatur för delad åtkomst (SAS)

Storage Explorer kan ansluta till följande resurs typer med hjälp av en SAS-URI:
* Blobcontainer
* Azure Data Lake Storage Gen2 behållare eller katalog
* Filresurs
* Kö
* Tabell

En SAS-URI ser ut så här:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. I panelen **Välj resurs** i dialog rutan **Anslut till Azure Storage** väljer du den resurs som du vill ansluta till.
1. Välj **signatur för delad åtkomst (SAS)** och välj **Nästa**.
1. Ange ett visnings namn för anslutningen och SAS-URI: n för resursen. Välj **Nästa**.
1. Granska anslutnings informationen på panelen **Sammanfattning** . Om anslutnings informationen är korrekt väljer du **Anslut**.

#### <a name="local-storage-emulator"></a>Lokal Storage-emulator

Storage Explorer kan ansluta till en Azure Storage-emulator. Det finns för närvarande två emulatorer som stöds:

* [Azure Storage emulator](storage/common/storage-use-emulator.md) (endast Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS eller Linux)

Om emulatorn lyssnar på standard portarna kan du använda den **lokala & anslutna**  >  **Storage Accounts**  >  **-standardportar-** noden för att få åtkomst till emulatorn.

Om du vill använda ett annat namn för anslutningen, eller om emulatorn inte körs på standard portarna:

1. Starta din emulator.

   > [!IMPORTANT]
   > Storage Explorer startar inte emulatorn automatiskt. Du måste starta den manuellt.

1. I panelen **Välj resurs** i dialog rutan **Anslut till Azure Storage** väljer du **lokal Storage-emulator**.
1. Ange ett visnings namn för anslutningen och port numret för varje emulerad tjänst som du vill använda. Lämna motsvarande port tom om du inte vill använda för en tjänst. Välj **Nästa**.
1. Granska anslutnings informationen på panelen **Sammanfattning** . Om anslutnings informationen är korrekt väljer du **Anslut**.

### <a name="connect-to-azure-cosmos-db"></a>Ansluta till Azure Cosmos DB

Storage Explorer också stöd för anslutning till Azure Cosmos DB resurser.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutnings sträng

I stället för att hantera Azure Cosmos DB-konton via en Azure-prenumeration kan du ansluta till Azure Cosmos DB med hjälp av en anslutnings sträng. Gör så här för att ansluta:

1. Under **Utforskaren**, expandera **lokala & anslutna**, högerklicka på **Cosmos DB konton** och välj **Anslut till Azure Cosmos DB**.

    ![Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng][21]

1. Välj Azure Cosmos DB API, ange dina **anslutnings sträng** data och välj sedan **OK** för att ansluta Azure Cosmos DB-kontot. Information om hur du hämtar anslutnings strängen finns i [hantera ett Azure Cosmos-konto](./cosmos-db/how-to-manage-database-account.md).

    ![Anslutningssträng][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Anslut till Azure Data Lake Store per URI

Du kan komma åt en resurs som inte finns i din prenumeration. Du behöver någon som har åtkomst till den resursen för att ge resurs-URI: n. När du har loggat in ansluter du till Data Lake Store med hjälp av URI: n. Gör så här för att ansluta:

1. Under **Utforskaren** expanderar du **lokala & anslutna**.

1. Högerklicka på **data Lake Storage gen1** och välj **Anslut till data Lake Storage gen1**.

    ![Ansluta till Data Lake Store snabb meny](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Ange URI och välj sedan **OK**. Data Lake Store visas under **data Lake Storage**.

    ![Anslut till Data Lake Store resultat](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

I det här exemplet används Data Lake Storage Gen1. Azure Data Lake Storage Gen2 är nu tillgängligt. Mer information finns i [Vad är Azure Data Lake Storage gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generera en signatur för delad åtkomst i Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Signatur för delad åtkomst på konto nivå

1. Högerklicka på det lagrings konto som du vill dela och välj sedan **Hämta signatur för delad åtkomst**.

    ![Snabb meny alternativet Hämta signatur för delad åtkomst][14]

1. I **signaturen för delad åtkomst** anger du den tidsram och de behörigheter som du vill använda för kontot och väljer sedan **skapa**.

    ![Hämta en signatur för delad åtkomst][15]

1. Kopiera antingen **anslutnings strängen** eller den råa **frågesträngen** till Urklipp.

### <a name="service-level-shared-access-signature"></a>Signatur för delad åtkomst på service nivå

Du kan få en signatur för delad åtkomst på tjänst nivå. Mer information finns i [Hämta SAS för en BLOB-behållare](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton

Om du vill hitta en lagrings resurs kan du söka i fönstret **Utforskaren** .

När du anger text i sökrutan visar Storage Explorer alla resurser som matchar Sök värdet som du har angett till den punkten. I det här exemplet visas en sökning efter **slut punkter**:

![Lagringskontosökning][23]

> [!NOTE]
> Om du vill påskynda din sökning använder du **konto hantering** för att avmarkera alla prenumerationer som inte innehåller det objekt som du söker efter. Du kan också högerklicka på en nod och välja **Sök härifrån** för att börja söka från en speciell nod.

## <a name="next-steps"></a>Nästa steg

* [Hantera Azure Blob Storage-resurser med Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Arbeta med data med hjälp av Azure Storage Explorer](./cosmos-db/storage-explorer.md)
* [Hantera Azure Data Lake Store resurser med Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
