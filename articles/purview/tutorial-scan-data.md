---
title: 'Självstudie: Genomsök data med Azure avdelningens kontroll (för hands version)'
description: I den här självstudien kör du ett start paket för att skapa en datafastighet och Genomsök sedan data från data källor i din Azure avdelningens kontroll-katalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: e373556d189e9fffc0626d19250e79c09962a004
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062111"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Självstudie: Genomsök data med Azure avdelningens kontroll (för hands version)

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

I den här *själv studie serien i fem delar* får du lära dig grunderna i hur du hanterar data styrning över en datafastighet med Azure avdelningens kontroll (för hands version). Datafliken som du skapar i den här delen av självstudien används för resten av serien.

I del 1 av den här själv studie serien kommer du att:

> [!div class="checklist"]
>
> * Skapa en datafastighet med olika data resurser i Azure.
> * Sök igenom data i en katalog.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* Ett [Azure avdelningens kontroll-konto](create-catalog-portal.md).
* [Det start paket](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) som ska användas för att distribuera data fastigheten.

> [!NOTE]
> Start paketet är bara tillgängligt för Windows.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-data-estate"></a>Skapa en datafastighet

I det här avsnittet kör du Starter Kit-skripten för att skapa en simulerad datafastighet. En datafastighet är en portfölj med alla data ett företag äger. Skriptet för start paket utför följande åtgärder:

* Skapar ett Azure Blob Storage-konto och fyller i kontot med data.
* Skapar ett Azure Data Lake Storage Gen2-konto.
* Skapar en Azure Data Factory instans och kopplar instansen till Azure-avdelningens kontroll.
* Ställer in och utlöser en pipeline för kopierings aktivitet mellan Azure Blob Storage och Azure Data Lake Storage Gen2-konton.
* Push-överför den associerade härkomst från Azure Data Factory till Azure avdelningens kontroll.

### <a name="prepare-to-run-the-starter-kit"></a>Förbereda för att köra start paketet

Följ de här stegen för att konfigurera klient programmet för start paket på din Windows-dator:

1. [Hämta start paketet](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip)och extrahera innehållet till valfri plats.


1. På datorn anger du **PowerShell** i rutan Sök i aktivitets fältet i Windows. I Sök listan högerklickar du på **Windows PowerShell** och väljer sedan **Kör som administratör**.

1. I PowerShell-fönstret anger du följande kommando och ersätter `<path-to-starter-kit>` med mappsökvägen för de extraherade start paket filerna.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Ange följande kommando för att installera Azure-cmdletar.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Om du ser varnings meddelandet *krävs NuGet-providern för att fortsätta*, Skriv in **Y** och tryck på RETUR.

1. Om du ser varnings meddelandet, ett *ej betrott lager*, anger du **en** och trycker på RETUR.

Det kan ta upp till en minut för PowerShell att installera de moduler som krävs.

### <a name="collect-data-needed-to-run-the-scripts"></a>Samla in data som behövs för att köra skripten

Innan du kör PowerShell-skripten för att starta katalogen hämtar du värdena för följande argument som ska användas i skripten:

* TenantID
   1. Välj **Azure Active Directory** i [Azure-portalen](https://portal.azure.com).
   1. I avsnittet **Hantera** i det vänstra navigerings fönstret väljer du **Egenskaper**. Välj sedan kopierings ikonen för **klient-ID** för att spara värdet i Urklipp. Klistra in värdet i en text redigerare för senare användning.

* SubscriptionID
   1. I Azure Portal söker du efter och väljer namnet på den Azure avdelningens kontroll-instans som du skapade som ett krav.
   1. Välj avsnittet **Översikt** och spara GUID för **prenumerations-ID: t**.

   > [!NOTE]
   > Se till att du använder samma prenumeration som den där du skapade Azure avdelningens kontroll-kontot. Det här är samma prenumeration som placerades i listan över tillåtna.

* Katalog namn: namnet på det Azure avdelningens kontroll-konto som du skapade i [skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).

* CatalogResourceGroupName: namnet på den resurs grupp där du skapade ditt Azure avdelningens kontroll-konto.

### <a name="verify-permissions"></a>Kontrollera behörigheter

Följ dessa steg om du vill lägga till användaren som kör skriptet till det Azure avdelningens kontroll-konto som skapades som en förutsättning. Användare behöver både *avdelningens kontroll data curator* och *avdelningens kontroll Data Source Administrator* roles. 

Om du själv har skapat ett Azure avdelningens kontroll-konto får du automatiskt åtkomst och kan hoppa över det här avsnittet.

1. Gå till sidan med avdelningens kontroll-konton i Azure Portal och välj det Azure avdelningens kontroll-konto som du vill ändra.

1. På kontots sida väljer du fliken **åtkomst kontroll (IAM)** och **+ Lägg till**.

1. Välj **Lägg till rolltilldelning**.

1. Ange **avdelningens kontroll data curator roll** för *rollen*.
 
1. Använd standardvärdet för *tilldela åtkomst till*. Standardvärdet ska vara **användare, grupp eller tjänstens huvud namn**.

1. Ange namnet på användaren som kör skriptet i **Välj**.

1. Välj **Spara**.

1. Upprepa föregående steg med *rollen* som **avdelningens kontroll rollen som administratör för data källa**.

Mer information finns i [katalog behörigheter](catalog-permissions.md).

### <a name="run-the-client-side-setup-scripts"></a>Kör installations skripten på klient Sidan

När katalog konfigurationen har slutförts kör du följande skript i PowerShell-fönstret för att skapa till gångarna, och ersätter plats hållarna med de värden som du tidigare har samlat in.

1. Använd följande kommando för att navigera till katalogen för start paket. Ersätt `path-to-starter-kit` med sökvägen till den extraherade filens mappsökväg.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. Följande kommando anger körnings principen för den lokala datorn. Ange **ett** för *Ja till alla* när du uppmanas att ändra körnings principen.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Anslut till Azure med hjälp av följande kommando. Ersätt platshållarna `TenantID` och `SubscriptionID`.

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   När du kör kommandot visas ett popup-fönster där du kan logga in med dina Azure Active Directory autentiseringsuppgifter.


1. Använd följande kommando för att köra start paketet. Ersätt `CatalogName` `TenantID` `SubscriptionID` `NewResourceGroupName` `CatalogResourceGroupName` plats hållarna,,, och. För `NewResourceGroupName` använder du ett unikt namn (endast med alfanumeriska alfanumeriska tecken) för den resurs grupp som ska innehålla data fastigheten.

   > [!IMPORTANT]
   > **Newresourcegroupname** använder bara siffror och gemena bokstäver och måste vara kortare än 17 tecken. **Inga versaler och specialtecken tillåts.** Den här begränsningen kommer från lagrings kontots namngivnings regler.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

Det kan ta upp till 10 minuter innan miljön har kon figurer ATS. Under den här tiden kan du se olika popup-fönster, som du kan ignorera. Stäng inte **BlobDataCreator.exe** fönstret. den stängs automatiskt när den har slutförts.

När du ser meddelandet väntar du på `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` att en annan instans av **BlobDataCreator.exe** starta och avsluta körs.

> [!IMPORTANT]
> Om antalet aktiva aktiviteter slutar minska, kan du avsluta fönstret BLOB Creator och trycka på RETUR i PowerShell-fönstret

När processen har avslut ATS skapas en resurs grupp med det namn du angav. Azure Data Factory, Azure Blob Storage och Azure Data Lake Storage Gen2-konton finns i den här resurs gruppen. Resurs gruppen finns i den prenumeration du angav.

## <a name="scan-data-into-the-catalog"></a>Sök igenom data i katalogen

Genomsökning är en process med vilken katalogen ansluter direkt till en data källa enligt ett användardefinierat schema. Katalogen visar företagets datafastighet genom genomsökning, härkomst, portalen och API: et. Målen omfattar att undersöka vad som finns i, extrahera scheman och försöka förstå semantik. I det här avsnittet ska du skapa en genomsökning av de data källor som du har skapat med start paketet.

Start paket skriptet som du körde skapade två data källor, Azure Blob Storage och Azure Data Lake Storage Gen2. Du kan genomsöka dessa data källor till katalogen en i taget.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Autentisera till lagring med hanterad identitet

En hanterad identitet med samma namn som ditt Azure avdelningens kontroll-konto skapas automatiskt när ditt konto skapas. Innan du kan genomsöka dina data måste du ge Azure-avdelningens kontroll roll behörigheter för dina lagrings konton.

1. Navigera till resurs gruppen som skapades av start paketet och välj ditt Blob Storage-konto.

1. Välj **Access Control (IAM)** i den vänstra navigerings menyn. Välj sedan **+ Lägg till**.

1. Ange rollen som **Storage BLOB data Reader** och ange ditt Azure avdelningens kontroll-kontonamn för **Select**. Välj sedan **Spara** för att ge roll tilldelningen till ditt avdelningens kontroll-konto.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Lägg till rolltilldelning":::

1. Upprepa föregående steg för Azure Data Lake Storage Gen2.

### <a name="scan-your-data-sources"></a>Sök igenom dina data källor

1. Gå till Azure avdelningens kontroll-resursen i [Azure Portal](https://portal.azure.com) och välj *Öppna avdelningens kontroll Studio*. Du kommer automatiskt till avdelningens kontroll Studios start sida.

1. Välj **källor** på katalogens webb sida och välj **Registrera**. Välj sedan **Azure Blob Storage** och **Fortsätt**.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Registrera Blob Storage-resurs":::

1. Ange ett **namn** på sidan **Registrera källor** . Välj **lagrings konto namnet** för det Azure Blob Storage-konto som du skapade tidigare med start paketet. Kontot har namnet `<YourResourceGroupName>adcblob` . Välj **Slutför**.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Skärm bild som visar inställningarna för att registrera en Azure Blob Storage-datakälla." border="true":::

1. I vyn karta över **data källor** väljer du **Ny skanning** på Azure Blob Storage-panelen.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Skärm bild som visar hur du väljer en skannings konfiguration från en data källa." border="true":::

1. På sidan **Skanna** anger du ett namn på sökningen, väljer din hanterade identitet i list rutan för **autentiseringsuppgifter** och **fortsätter**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Skanna Blob Storage i Azure avdelningens kontroll":::

1. Du kan begränsa sökningen till enskilda blobbar. I den här självstudien behåller du alla till gångar markerade för att genomsöka allt och **fortsätta**.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Omfattning av lagrings genomsökning":::

1. Välj regel uppsättningen standard skanning och **Fortsätt**.

1. Du kan ställa in en genomsöknings utlösare för återkommande genomsökningar. I den här självstudien väljer du **en gång** och **fortsätter**.

1. Klicka på **Spara och kör** för att slutföra sökningen.

1. Upprepa föregående steg för att skanna Azure Data Lake Storage Gen2-kontot.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
>
> * Kör Start paket skriptet för att konfigurera en data fastighets miljö.
> * Sök igenom data i en Azure avdelningens kontroll-katalog.

Gå vidare till nästa självstudie och lär dig hur du navigerar på Start sidan och söker efter en till gång.

> [!div class="nextstepaction"]
> [Navigera till start sidan och Sök efter en till gång](tutorial-asset-search.md)
