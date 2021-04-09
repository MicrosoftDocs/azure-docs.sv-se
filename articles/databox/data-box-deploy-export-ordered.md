---
title: Självstudie för att exportera data från Azure Data Box | Microsoft Docs
description: Lär dig mer om distributions kraven och hur du exporterar data från en Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 42476e2689cc503edc19e8e299a01ce922f1bf42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789203"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Självstudie: skapa en export ordning för Azure Data Box

Azure Data Box är en hybrid lösning som gör att du kan flytta data från Azure till din plats. I den här självstudien beskrivs hur du skapar en export ordning för Azure Data Box. Huvud anledningen till att skapa en export ordning är för haveri beredskap, om lagringen på plats blir komprometterad och en säkerhets kopiering måste återställas.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Krav för export
> * Beställ ett Data Box-enhet för export
> * Spåra export ordningen
> * Avbryt export ordningen

## <a name="prerequisites"></a>Förutsättningar

Slutför följande konfigurations krav för Data Box-enhet tjänst och enhet innan du beställer enheten.

### <a name="for-service"></a>För tjänsten

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Se till att du har en befintlig resurs grupp som du kan använda med Azure Data Box.

* Se till att ditt Azure Storage-konto som du vill exportera data från är en av de lagrings konto typer som stöds enligt beskrivningen som [stöds för data Box-enhet](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>För enheten

Innan du börjar ska du kontrollera att:

* Du bör ha en värddator som är ansluten till datacenternätverket. Du kommer att kopiera data från Azure Data Box till den här datorn. Värddatorn måste köra ett operativsystem som stöds enligt beskrivningen i [Systemkrav för Azure Data Box](data-box-system-requirements.md).

* Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10-GbE-anslutning inte är tillgänglig kan en 1 – GbE-datalänk användas, men kopierings hastigheten påverkas.

## <a name="order-data-box-for-export"></a>Beställ Data Box-enhet för export

Utför följande steg på Azure-portalen för att beställa en enhet.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på denna URL: [https://portal.azure.com](https://portal.azure.com).

2. Välj **+ Skapa en resurs** och sök efter *Azure Data Box*. Välj **Azure Data Box**.

   ![Skapa en resurs](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Välj **Skapa**.

   ![Skapa en Azure Data Box resurs](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Kontrol lera om Azure Data Box tjänsten är tillgänglig i din region. Ange eller välj följande information och välj **Tillämpa**.

    |Inställning  |Värde  |
    |---------|---------|
    |Överföringstyp     | Välj **Exportera till Azure**.        |
    |Prenumeration     | Välj en prenumeration för EA, CSP eller Azure-sponsring för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Resursgrupp     |    Välj en befintlig resurs grupp. <br> En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans.         |
    |Käll Azure-region    |    Välj den Azure-region där dina data finns.         |
    |Mål land     |     Välj det land där du vill skicka enheten.        |

   ![Välj Data Box-enhet inställningar](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Välj **Data Box**. Den högsta användbara kapaciteten för en enskild order är 80 TB. Du kan skapa flera beställningar för större datamängder.

   ![Välj Data Box-enhet kapacitet](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. Ange **grundläggande** beställnings information i **ordning**. Ange eller välj följande information.

    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration     | Prenumerationen fylls i automatiskt baserat på din tidigare val.|
    |Resursgrupp | Den resurs grupp som du valde tidigare. |
    |Exportera beställnings namn     |  Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.      |

    ![Grundläggande om export order](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Välj **Nästa: data urvalet** för att gå vidare.

7. I **data urval** väljer du **Lägg till lagrings konto och export typ**.

    ![Lägg till lagrings konto och export typ](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. I **Välj export alternativ** anger du alternativ informationen för export. Ange eller Välj följande information och välj sedan **Lägg till**.

    |Inställning  |Värde  |
    |---------|---------|
    |Lagringskonto     | Det Azure Storage konto som du vill exportera data från. |
    |Export typ     | Anger vilken typ av data som ska exporteras från **alla objekt** och **använda XML-filen**.<ul><li> **Alla objekt** – anger att jobbet ska exportera alla data beroende på ditt val av **överförings alternativ**.</li><li> **Använd XML-fil** – anger en XML-fil som innehåller en uppsättning sökvägar och prefix för blobbar och/eller filer som ska exporteras från lagrings kontot. XML-filen måste finnas i det valda lagrings kontots behållare och det finns för närvarande inte stöd för att välja från fil resurser. Filen måste vara en XML-fil som inte är tom.</li></ul>        |
    |Överförings alternativ     |  Anger alternativ för data överföring från **Välj alla**, **alla blobbar** och **alla filer**. <ul><li> **Markera alla** -anger att alla blobbar och Azure Files exporteras. Om du använder ett lagrings konto som bara stöder blobbar (Blob Storage konto) går det inte att välja alternativet **alla filer** .</li><li> **Alla blobbar** – anger att endast block-och sid-blobar ska exporteras.</li><li> **Alla filer** -anger att alla filer exporteras exklusive blobbar. Vilken typ av lagrings konto du har (GPv1 och GPv2, Premium Storage eller Blob Storage) bestämmer vilka typer av data som du kan exportera. Mer information finns i [lagrings konton som stöds för export](../import-export/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Inkludera utförlig logg     | Anger om du vill ha en utförlig logg fil som innehåller en lista över alla filer som har exporter ATS.        |

    > [!NOTE]
    >
    > Om du väljer **Använd XML-fil** för inställningen **export typ** måste du kontrol lera att XML-koden innehåller giltiga sökvägar och/eller prefix. Du måste skapa och tillhandahålla XML-filen.  Om filen är ogiltig eller inga data matchar de angivna Sök vägarna, avslutas ordningen med ofullständiga data eller så har inga data exporter ATS.

    Information om hur du lägger till en XML-fil i en behållare finns i [Exportera order med XML-fil](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Välj export alternativ](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Ett exempel på XML-indata finns i [exempel på XML-indata](data-box-deploy-export-ordered.md#sample-xml-file)

9. Granska inställningarna i **data urval** och välj **nästa: säkerhets>** för att fortsätta.

   ![Exportera order, data urval](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    På sidan **säkerhet** kan du använda en egen krypterings nyckel och välja att använda Double Encryption.

    Alla inställningar på **säkerhets** skärmen är valfria. Om du inte ändrar några inställningar används standardinställningarna.

    ![Säkerhets skärmen i guiden Data Box-enhet importera order](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Om du vill använda din egen Kundhanterade nyckel för att skydda upplåsnings nyckeln för den nya resursen expanderar du **krypterings typ**.

    Det är valfritt att konfigurera en kundhanterad nyckel för din Azure Data Box. Som standard använder Data Box-enhet en Microsoft-hanterad nyckel för att skydda upplåsnings nyckeln.

    En kundhanterad nyckel påverkar inte hur data på enheten krypteras. Nyckeln används bara för kryptering av enhetens upplåsnings nyckel.

    Om du inte vill använda en kundhanterad nyckel går du vidare till steg 16.

    ![Säkerhets skärm som visar inställningar för krypterings typ](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. Välj **kund hanterad nyckel** som nyckel typ. Välj sedan **Välj ett nyckel valv och nyckel**.
   
    ![Säkerhets skärm, inställningar för en kundhanterad nyckel](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. På skärmen **Välj nyckel från Azure Key Vault** fylls prenumerationen i automatiskt.

    - För **Key Vault** kan du välja ett befintligt nyckel valv i list rutan.

      ![Välj nyckel från Azure Key Vault skärmen](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - Du kan också välja **Skapa nytt** för att skapa ett nytt nyckel valv. På skärmen **skapa nyckel valv** anger du resurs gruppen och ett nyckel valvs namn. Se till att det **mjuka borttagnings** -och **rensnings skyddet** har Aktiver ATS. Acceptera alla andra standardvärden och välj **Granska + skapa**.

      ![Skapa en ny Azure Key Vault inställningar](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Granska informationen för nyckel valvet och välj **skapa**. Vänta några minuter tills nyckel valvet har skapats.

      ![Ny Azure Key Vault gransknings skärmen](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. På skärmen **Välj nyckel från Azure Key Vault** kan du välja en befintlig nyckel i nyckel valvet.

    ![Välj befintlig nyckel från Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Om du vill skapa en ny nyckel väljer du **Skapa ny**. Du måste använda en RSA-nyckel. Storleken kan vara 2048 eller högre. Ange ett namn för den nya nyckeln, godkänn de andra standardinställningarna och välj **skapa**.

      ![Skapa ett nytt nyckel alternativ](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Du får ett meddelande när nyckeln har skapats i nyckel valvet.

14. Välj den **version** av nyckeln som ska användas och välj sedan **Välj**.

      ![Ny nyckel har skapats i Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Om du vill skapa en ny nyckel version väljer du **Skapa ny**.

    ![Öppna en dialog ruta för att skapa en ny nyckel version](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    På skärmen **Skapa ny nyckel** väljer du inställningar för den nya nyckel versionen och väljer **skapa**.

    ![Skapa en ny nyckel version](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    Inställningarna för **krypterings typ** på **säkerhets** skärmen visar ditt nyckel valv och nyckel.

    ![Nyckel-och nyckel valv för en kundhanterad nyckel](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Välj en användar identitet som du ska använda för att hantera åtkomst till den här resursen. Välj **Välj en användar identitet**. I panelen till höger väljer du prenumerationen och den hanterade identiteten som ska användas. Välj sedan **Välj**.

    En användare som tilldelats en hanterad identitet är en fristående Azure-resurs som kan användas för att hantera flera resurser. Mer information finns i [hanterade identitets typer](../active-directory/managed-identities-azure-resources/overview.md).  

    Om du behöver skapa en ny hanterad identitet följer du rikt linjerna i [skapa, lista, ta bort eller tilldela en roll till en användardefinierad hanterad identitet med hjälp av Azure Portal](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Välj en användar identitet](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    Användar identiteten visas i inställningar för **krypterings typ** .

    Du kan minimera **krypterings typs** inställningarna nu.

    ![En vald användar identitet visas i inställningar för krypterings typ](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Om du vill aktivera programvarubaserad dubbel kryptering expanderar du **Double Encryption (för miljöer med hög säkerhet)** och väljer **Aktivera dubbel kryptering för ordern**. 

    Den programvarubaserade krypteringen utförs förutom AES-256-bitars kryptering av data på Data Box-enhet.

    > [!NOTE]
    > Att aktivera det här alternativet kan göra order bearbetning och data kopieringen ta längre tid. Du kan inte ändra det här alternativet när du har skapat din beställning.

    ![Säkerhets skärm för import av data Box, dubbel kryptering](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Välj **Nästa: kontakt uppgifter** för att fortsätta.

11. I **kontakt uppgifterna** väljer du **+ Lägg till leverans adress** för att ange leverans information.

    ![Lägg till leverans adress](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. I **Lägg till leverans adress** anger du ditt för-och efter namn, namn och post adress för företaget och ett giltigt telefonnummer. Välj **Verifiera**. Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det.

    ![Verifiera leverans adress](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Om du beställer i en region där själv hanterad leverans är tillgängligt kan du välja det här alternativet. Mer information om självhanterad leverans finns i [Använd självhanterad leverans](data-box-portal-customer-managed-shipping.md).

13. Välj **Lägg till leverans adress** när leverans informationen har verifierats.

14. Granska leverans adressen och e-postadressen i **kontakt information**. Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

    ![Kontaktinformation](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Välj **Nästa: granska och beställ>**. Du måste godkänna de allmänna villkoren om du vill fortsätta att skapa ordern.

16. Välj **Beställ**. Det tar några minuter att skapa beställningen.

    ![Inchecknings ordning](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Exportera order med XML-fil

Om du väljer **Använd XML-fil** kan du ange vissa behållare och blobbar (sida och block) som du vill exportera. Du måste följa specifikationerna i [XML-filtabellen](#sample-xml-file) för att formatera XML-filen. Stegen nedan visar hur du använder en XML-fil för att exportera dina data:

1. För **export typ** väljer du **Använd XML-fil**. Det här är XML-filen som anger vilka blobbar och Azure-filer som du vill exportera. Om du vill lägga till XML-filen väljer du **Klicka här för att välja en XML-fil**.

     ![Välj export alternativ, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Välj **+ container** för att skapa en behållare.

    ![Välj export alternativ, behållare](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. I fliken **ny behållare** som visas från höger sida av Azure Portal lägger du till ett namn för behållaren. Namnet måste innehålla gemener och du kan inkludera siffror och bindestreck "-". Välj sedan den **offentliga åtkomst nivån** i list rutan. Vi rekommenderar att du väljer **privat (icke-anonym åtkomst)** för att hindra andra från att komma åt dina data. Mer information om åtkomst nivåer för behållare finns i [åtkomst behörigheter för behållare](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Välj export alternativ, nya behållar inställningar](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Välj **Skapa**.

   ![Välj export alternativ, skapa ny behållare.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Om din behållare har skapats visas följande meddelande:

   ![Containern har skapats](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Välj den behållare som du skapade och dubbelklicka på den.

   ![Visa information om behållare](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Om du dubbelklickar på behållaren visas behållar egenskaper-vyn. Nu vill du bifoga (eller bläddra till) XML-filen som innehåller listan över blobbar och/eller Azure Files som du vill exportera. Välj **Överför**.

   ![Ladda upp blob till container](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Du har lagt till XML-filen i behållaren. Endast blobbar och Azure Files som du har angett i denna XML-fil kommer att exporteras.

   ![XML-fil Tillagd i behållare](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="track-the-order"></a>Spåra beställningen

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Öppna Data Box-beställningen och navigera till **Overview** (Översikt) för att visa status. Portalen visar beställningen i tillståndet **Ordered** (beställd).

När enhets förberedelsen är klar kommer data kopieringen att börja från de valda lagrings kontona. Portalen visar ordningen i status för **data kopiering pågår** .

![Data Box-enhet export ordning, data kopiering pågår](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box-enhet kopierar data från käll lagrings kontona. När data kopieringen är klar är Data Box-enhet låst och portalen visar ordningen i **Kopiera slutfört** tillstånd.

![Data Box-enhet export order, data kopieringen har slutförts](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Om enheten inte är tillgänglig visas ett meddelande. Om enheten är tillgänglig identifierar Microsoft enheten för leverans och förbereder försändelsen. Vid förberedelse av enheten utförs följande åtgärder:

* SMB-resurser skapas för varje lagringskonto som är associerat med enheten.
* För varje resurs genereras autentiseringsuppgifter för åtkomst såsom användarnamn och lösenord.
* Enheten är låst och kan bara användas med enhets upplåsnings lösen ordet. För att hämta lösen ordet måste du logga in på ditt Azure Portal-konto och välja **enhets information**.

Microsoft förbereder och skickar sedan din enhet via en regional operatör. Du får ett spårningsnummer när enheten har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).

![Data Box-enhet export order har skickats](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Om du väljer själv hanterad leverans får du ett e-postmeddelande med nästa steg när enheten är redo att hämtas från data centret. Mer information om självhanterad leverans finns i [självhanterad leverans](data-box-portal-customer-managed-shipping.md).

![Självhanterad leverans klar för upphämtning](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Avbryta beställningen

Om du vill avbryta den här ordningen går du till **Översikt** i Azure Portal och väljer **Avbryt** från kommando fältet.

När du har placerat en order kan du avbryta den när som helst innan bearbetningen börjar beställningen.

Om du vill ta bort en annullerad order går du till **Översikt** och väljer **ta bort** från kommando fältet.

## <a name="sample-xml-file"></a>Exempel-XML-fil

Följande XML visar ett exempel på BLOB-namn, BLOB-prefix och Azure Files som finns i det XML-format som export ordningen använder när du väljer alternativet **Använd XML-fil** :

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Några viktiga punkter i avseende XML-filer:

* XML-taggar är Skift läges känsliga och måste matcha exakt enligt vad som anges i exemplet ovan.
* Inledande och avslutande taggar måste vara identiska.
* Felaktig XML-kod eller formatering kan leda till att data exporten Miss lyckas.
* Inga data exporteras om blobben och/eller Filprefixet är ogiltiga.

### <a name="examples-of-valid-blob-paths"></a>Exempel på giltiga BLOB-sökvägar

I följande tabell visas exempel på giltiga BLOB-sökvägar:

   | Väljare | BLOB-sökväg | Beskrivning |
   | --- | --- | --- |
   | Börjar med |/ |Exporterar alla blobar i lagrings kontot |
   | Börjar med |/$root/ |Exporterar alla blobbar i rot behållaren |
   | Börjar med |/containers |Exporterar alla blobbar i behållare som börjar med prefix **behållare** |
   | Börjar med |container |Exporterar alla blobbar i container **container-Name** |
   | Börjar med |/container-name/prefix |Exporterar alla blobbar i container **container-Name** som börjar **med prefixet prefix** |
   | Lika med |$root/logo.bmp |Exporterar BLOB- **logo.bmp** i rot behållaren |
   | Lika med |8tbpageblob/mydata.txt |Exporterar BLOB- **mydata.txt** i container **8tbpageblob** |

## <a name="sample-log-files"></a>Exempel på loggfiler

Det här avsnittet innehåller exempel på loggfiler som genereras under exporten. Fel loggarna genereras automatiskt. Om du vill generera utförlig logg filen måste du välja **Inkludera utförlig logg** i Azure Portal när du konfigurerar export ordningen.
Mer information om kopierings-och utförliga loggar finns i [kopiera loggar](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
>
> * Krav för export
> * Beställ ett Data Box-enhet för export
> * Spåra export ordningen
> * Avbryt export ordningen

Gå vidare till nästa självstudie och lär dig hur du ställer in din Data Box.

> [!div class="nextstepaction"]
> [Konfigurera Azure Data Box](./data-box-deploy-set-up.md)