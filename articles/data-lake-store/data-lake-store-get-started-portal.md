---
title: Kom igång med Azure Data Lake Storage Gen1-portalen
description: Använd Azure Portal för att skapa ett Data Lake Storage Gen1 konto och utföra grundläggande åtgärder i kontot.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 428ed96c3223e644b0c78712723231a5fabbdc77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578588"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure Portal

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Lär dig hur du använder Azure Portal för att skapa ett Data Lake Storage Gen1 konto och utföra grundläggande åtgärder som att skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information finns i [Översikt över Azure Data Lake Storage gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudiekursen behöver du följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto

1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Klicka på **skapa en resurs > lagrings > data Lake Storage gen1**.
3. På bladet **ny data Lake Storage gen1** anger du de värden som visas på följande skärm bild:

    ![Skapa ett nytt Data Lake Storage Gen1 konto](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Skapa ett nytt Data Lake Storage Gen1 konto")

   * **Namn**. Ange ett unikt namn för Data Lake Storage Gen1 kontot.
   * **Prenumeration**. Välj den prenumeration som du vill skapa ett nytt Data Lake Storage Gen1s konto under.
   * **Resursgrupp**. Välj en befintlig resursgrupp eller klicka på **Skapa en resursgrupp** för att skapa en. En resursgrupp är en container som innehåller relaterade resurser för ett program. Mer information finns i [Resursgrupper i Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Plats**: Välj en plats där du vill skapa data Lake Storage Gen1s kontot.
   * **Krypterings inställningar**. Det finns tre alternativ:

     * **Aktivera inte kryptering**.
     * **Använd nycklar som hanteras av data Lake Storage gen1** om du vill data Lake Storage gen1 hantera dina krypterings nycklar.
     * **Välj nycklar från ditt eget nyckelvalv**. Du kan välja ett befintligt Azure Key Vault eller skapa ett nytt nyckelvalv. Om du vill använda nycklar från en Key Vault måste du tilldela behörighet för Data Lake Storage Gen1-kontot för att få åtkomst till Azure Key Vault. Anvisningar finns i [Tilldela behörigheter till Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Skärm bild av bladet nya Data Lake Storage gen 1-blad och krypterings inställningar.](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage Gen1 kryptering")

        Klicka på **OK** i bladet **krypteringsinställningar**.

        Mer information finns i [kryptering av data i Azure Data Lake Storage gen1](./data-lake-store-encryption.md).

4. Klicka på **Skapa**. Om du har valt att fästa kontot på instrument panelen kommer du tillbaka till instrument panelen och du kan se förloppet för din Data Lake Storage Gen1 konto etablering. När Data Lake Storage Gen1 kontot har tillhandahållits visas konto bladet.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Tilldela behörigheter till Azure Key Vault

Om du använde nycklar från en Azure Key Vault för att konfigurera kryptering på Data Lake Storage Gen1-kontot, måste du konfigurera åtkomst mellan Data Lake Storage Gen1-kontot och Azure Key Vault-kontot. Utför följande steg för att göra det.

1. Om du använde nycklar från Azure Key Vault visas en varning överst i bladet för Data Lake Storage Gen1-kontot. Klicka på varningen för att öppna **kryptering**.

    ![Skärm bild av bladet Data Lake Storage Gen1 konto som visar varningen "nyckel valvets behörighets konfiguration krävs. Klicka här för att konfigurera.](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage Gen1 kryptering")
2. Bladet visar två alternativ för att konfigurera åtkomst.

    ![Skärm bild av krypterings bladet.](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage Gen1 kryptering")

   * Klicka på **Ge behörighet** på första alternativet för att konfigurera åtkomst. Det första alternativet är bara aktiverat när användaren som skapade Data Lake Storage Gen1-kontot också är administratör för Azure Key Vault.
   * Ett annat alternativ är att köra PowerShell-cmdleten som visas på bladet. Du måste vara ägare till Azure Key Vault eller ha möjlighet att bevilja behörighet för Azure Key Vault. När du har kört cmdlet:en, gå tillbaka till bladet och klickar på **Aktivera** att konfigurera åtkomst.

> [!NOTE]
> Du kan också skapa ett Data Lake Storage Gen1-konto med Azure Resource Manager-mallar. Mallarna är tillgängliga från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Utan data kryptering: [distribuera Azure Data Lake Storage gen1 konto utan data kryptering](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Med data kryptering med Data Lake Storage Gen1: [distribuera data Lake Storage gen1 konto med kryptering (data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Med data kryptering med Azure Key Vault: [distribuera data Lake Storage gen1 konto med kryptering (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Skapa mappar

Du kan skapa mappar under ditt Data Lake Storage Gen1 konto för att hantera och lagra data.

1. Öppna det Data Lake Storage Gen1 konto som du har skapat. Klicka på **alla resurser** i den vänstra rutan, och klicka sedan på det konto namn under vilket du vill skapa mappar från bladet **alla resurser** . Om du fäst kontot på startsidan klickar du på kontoikonen.
2. I bladet Data Lake Storage Gen1 konto klickar du på **datautforskaren**.

    ![Skärm bild av bladet Data Lake Storage gen 1-konto med alternativet data Explorer inringat.](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Skapa mappar i ett Data Lake Storage Gen1 konto")
3. I bladet i Datautforskaren klickar du på **Ny mapp**, ange ett namn på den nya mappen och klicka sedan på **OK**.

    ![Skärm bild av bladet Datautforskaren med alternativet ny mapp och text rutan skapa ny mapp som kallas för.](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Skapa mappar i ett Data Lake Storage Gen1 konto")

    Den nya mappen visas i bladet **Datautforskaren**. Du kan skapa kapslade mappar upp till vilken nivå som helst.

    ![Skapa mappar i ett Data Lake konto](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Skapa mappar i ett Data Lake konto")

## <a name="upload-data"></a><a name="uploaddata"></a>Ladda upp data

Du kan ladda upp data till ett Data Lake Storage Gen1-konto direkt på rotnivå eller till en mapp som du har skapat i kontot.

1. I bladet i **Datautforskaren** klickar du på **Ladda upp**.
2. I bladet **Ladda upp filer** går du till de filer du vill ladda upp och klickar sedan på **Lägg till valda filer**. Du kan också välja mer än en fil att ladda upp.

    ![Ladda upp data](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Ladda upp data")

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Åtgärder som är tillgängliga för lagrade data

Klicka på ellipsikonen mot en fil och klicka i popup-menyn på den åtgärd du vill utföra på data.

![Egenskaper för data](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Egenskaper för data")

## <a name="secure-your-data"></a>Skydda dina data

Du kan skydda de data som lagras i ditt Data Lake Storage Gen1-konto med Azure Active Directory och åtkomst kontroll (ACL). Instruktioner för hur du gör finns i [skydda data i Azure Data Lake Storage gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Ta bort ditt konto

Om du vill ta bort ett Data Lake Storage Gen1 konto går du till bladet Data Lake Storage Gen1 och klickar på **ta bort**. För att bekräfta åtgärden uppmanas du att ange namnet på det konto som du vill ta bort. Ange namnet på kontot och klicka sedan på **Ta bort**.

![Ta bort Data Lake Storage Gen1 konto](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Ta bort Data Lake-konto")

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Data Lake Storage Gen1 för Big data-krav](data-lake-store-data-scenarios.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
