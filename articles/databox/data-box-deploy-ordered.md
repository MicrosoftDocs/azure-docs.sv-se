---
title: Självstudie för att beställa Azure Data Box | Microsoft Docs
description: I den här självstudien får du lära dig Azure Data Box, en hybridlösning som gör att du kan importera lokala data till Azure och hur du beställer Azure Data Box.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: eae8cca0302993c16ea29adddf6e4ee9b5b24be8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770906"
---
# <a name="tutorial-order-azure-data-box"></a>Självstudie: Beställa Azure Data Box

Azure Data Box är en hybridmolnlösning som gör att du kan importera lokala data till Azure på ett snabbt, enkelt och tillförlitligt sätt. Du överför dina data till en lagringsenhet på 80 TB (användbar kapacitet) från Microsoft och skickar sedan tillbaka enheten. Dessa data överförs sedan till Azure.

I den här självstudien beskriver vi hur du kan beställa en Azure Data Box. I den här självstudien lär du dig:  

> [!div class="checklist"]
>
> * Förutsättningar för att distribuera Data Box
> * Beställa en Data Box
> * Spåra beställningen
> * Avbryta beställningen

## <a name="prerequisites"></a>Krav

# <a name="portal"></a>[Portal](#tab/portal)

Slutför följande konfigurationsförutsättningar för Data Box-enhet tjänst och enhet innan du distribuerar enheten:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

Du kan logga in på Azure och köra Azure CLI-kommandon på något av två sätt:

* Du kan installera CLI och köra CLI-kommandon lokalt.
* Du kan köra CLI-kommandon från Azure Portal i Azure Cloud Shell.

Vi använder Azure CLI via Windows PowerShell för självstudien, men du kan välja något av alternativen.

### <a name="for-azure-cli"></a>För Azure CLI

Innan du börjar ska du kontrollera att:

#### <a name="install-the-cli-locally"></a>Installera CLI lokalt

* Installera [Azure CLI](/cli/azure/install-azure-cli) version 2.0.67 eller senare. Du kan också installera med [hjälp av MSI](https://aka.ms/installazurecliwindows).

**Logga in på Azure**

Öppna ett Windows PowerShell och logga in på Azure med [kommandot az login:](/cli/azure/reference-index#az_login)

```azurecli
PS C:\Windows> az login
```

Här är utdata från en lyckad inloggning:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Installera cli Azure Data Box tillägget**

Innan du kan använda Azure Data Box CLI-kommandon måste du installera tillägget. Med Azure CLI-tillägg får du tillgång till experimentella kommandon som inte finns med i standardversionen av CLI:t ännu. Mer information om tillägg finns i [Använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

Om du vill installera tillägget Azure Data Box kör du följande `az extension add --name databox` kommando:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Om tillägget har installerats visas följande utdata:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Du kan använda [Azure Cloud Shell](https://shell.azure.com/), en interaktiv gränssnittsmiljö i Azure, via webbläsaren för att köra CLI-kommandon. Azure Cloud Shell stöder Bash eller Windows PowerShell med Azure-tjänster. Azure CLI är förinstallerat och konfigurerat för användning med ditt konto. Välj knappen Cloud Shell på menyn i det övre högra avsnittet av Azure Portal:

![Cloud Shell menyval](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Knappen startar ett interaktivt gränssnitt som du kan använda för att köra stegen som beskrivs i den här artikeln.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>För Azure PowerShell

Innan du börjar bör du kontrollera att du:

* Installera Windows PowerShell 6.2.4 eller senare.
* Installera Azure PowerShell (AZ)-modul.
* Installera Azure Data Box (Az.DataBox).
* Logga in i Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Installera Azure PowerShell moduler lokalt

**Installera eller uppgradera Windows PowerShell**

Du måste ha installerat Windows PowerShell version 6.2.4 eller senare. Om du vill ta reda på vilken version av PowerShell som du har installerat kör du: `$PSVersionTable` .

Du ser följande utdata:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Om din version är lägre än 6.2.4 måste du uppgradera din version av Windows PowerShell. Information om hur du installerar den senaste Windows PowerShell finns [i Installera Azure PowerShell](/powershell/scripting/install/installing-powershell).

**Installera Azure PowerShell och Data Box-enhet moduler**

Du måste installera de moduler Azure PowerShell att använda Azure PowerShell för att beställa Azure Data Box. Så här installerar du Azure PowerShell moduler:

1. Installera [Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).
2. Installera sedan Az.DataBox med kommandot `Install-Module -Name Az.DataBox` .

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna ett Windows PowerShell och logga in på Azure med kommandot [Connect-AzAccount:](/powershell/module/az.accounts/Connect-AzAccount)

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Här är utdata från en lyckad inloggning:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Detaljerad information om hur du loggar in på Azure med Windows PowerShell finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Beställa Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Gör följande i Azure Portal att beställa en enhet.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på denna URL: [https://portal.azure.com](https://portal.azure.com).
2. Välj **+ Skapa en resurs** och sök efter *Azure Data Box*. Välj **Azure Data Box**.

   ![Skärmbild av nytt avsnitt med Azure Data Box i sökfältet](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Välj **Skapa**.

   ![Skärmbild av Azure Data Box avsnitt med alternativet Skapa förvalt](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Kontrollera om Data Box-tjänsten är tillgänglig i din region. Ange eller välj följande information och välj **Tillämpa**.

    |Inställning  |Värde  |
    |---------|---------|
    |Överföringstyp     | Välj **Importera till Azure**.        |
    |Prenumeration     | Välj en prenumeration för EA, CSP eller Azure-sponsring för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Resursgrupp | Välj en befintlig resursgrupp. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. |
    |Källans land/region    |    Välj landet/regionen där dina data finns.         |
    |Azure-målregion     |     Välj den Azure-region dit du vill överföra data. <br> Mer information finns i [regional tillgänglighet](data-box-overview.md#region-availability).            |

    [![Starta en Azure Data Box en importorder ](media/data-box-deploy-ordered/select-data-box-import-04-b.png)](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Välj **Data Box**. Den maximala användbara kapaciteten för en enskild order är 80 TB. Du kan skapa flera beställningar för större datamängder.

    ![Tillgängliga datastorlekar: Data Box Disk, 40 terabyte; Data Box-enhet, 100 terabyte; Data Box Heavy, 1 000 terabyte; Skicka dina egna diskar, 1 terabyte](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. I **Ordning** går du till **fliken** Grundläggande inställningar. Ange eller välj följande information och välj **Nästa: Datamål>**.

    |Inställning  |Värde  |
    |---------|---------|
    |Prenumeration      | Prenumerationen fylls i automatiskt baserat på ditt tidigare val.|
    |Resursgrupp    | Den resursgrupp som du valde tidigare. |
    |Namn på importorder | Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.    |

    ![Data Box-enhet guiden Importera beställning, skärmen Grundläggande, med rätt information ifylld](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. På skärmen **Datamål** väljer du **Datamål –** antingen lagringskonton eller hanterade diskar.

    Om du **använder lagringskonto(er)** som lagringsmål visas följande skärm:

    ![Data Box-enhet guiden Importera beställning, skärmen Datamål där lagringskonton har valts](media/data-box-deploy-ordered/select-data-box-import-07.png)

    Baserat på den angivna Azure-regionen väljer du ett eller flera lagringskonton i den filtrerade listan över befintliga lagringskonton. Data Box kan länkas med upp till 10 lagringskonton. Du kan också skapa ett nytt konto för **Generell användning v1**, **Generell användning v2** eller **bloblagring**.

   > [!NOTE]
   > - Om du väljer Azure Premium-filLagringskonton ökar den etablerade kvoten på lagringskontoresursen till storleken på data som kopieras till filresurser. När kvoten har ökat justeras den inte igen, till exempel om det av någon anledning Data Box-enhet inte kan kopiera dina data.
   > - Den här kvoten används för fakturering. När dina data har laddats upp till datacentret bör du justera kvoten efter dina behov. Mer information finns i Förstå [fakturering.](../../articles/storage/files/understanding-billing.md)

    Lagringskonton med virtuella nätverk stöds. För att Data Box-tjänsten ska fungera med skyddade lagringskonton aktiverar du de betrodda tjänsterna i inställningarna för nätverksbrandväggen för lagringskontot. Mer information finns i Lägga till [Azure Data Box en betrodd tjänst.](../storage/common/storage-network-security.md#exceptions)

    Om du Data Box-enhet för  att skapa hanterade diskar från lokala virtuella hårddiskar (VHD) måste du också ange följande information:

    |Inställning  |Värde  |
    |---------|---------|
    |Resursgrupper     | Skapa nya resursgrupper om du planerar att skapa hanterade diskar från lokala virtuella hårddiskar. Du kan bara använda en befintlig resursgrupp om resursgruppen skapades tidigare när du skapade en Data Box-enhet-beställning för hanterade diskar av Data Box-enhet tjänsten. <br> Ange flera resursgrupper avgränsade med semikolon. Högst 10 resursgrupper stöds.|

    ![Data Box-enhet guiden Importera beställning, skärmen Datamål med Managed Disks valt](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    Det angivna lagringskontot för hanterade diskar används som ett mellanlagringskonto. Data Box-tjänsten laddar upp de virtuella hårddiskarna som sidblobar till mellanlagringskontot innan de konverteras till hanterade diskar och flyttas till resursgrupperna. Mer information finns i [Verifiera dataöverföring till Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

   > [!NOTE]
   > Om en sidblob inte konverteras till en hanterad disk finns den kvar på lagringskontot och du debiteras för lagring.

8. Välj **Nästa: Säkerhet för** att fortsätta.

    På **skärmen** Säkerhet kan du använda din egen krypteringsnyckel och din egen enhet och dela lösenord och välja att använda dubbel kryptering.

    Alla inställningar på skärmen **Säkerhet** är valfria. Om du inte ändrar några inställningar tillämpas standardinställningarna.

    ![Säkerhetsskärmen i guiden Data Box-enhet importera beställning](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Om du vill använda din egen kund hanterade nyckel för att skydda upplåsningsnyckeln för den nya resursen expanderar du **Krypteringstyp**.

    Det är valfritt att konfigurera en kund hanterad nyckel Azure Data Box din dator. Som standard använder Data Box-enhet Microsoft-hanterad nyckel för att skydda upplåsningsnyckeln.

    En kund hanterad nyckel påverkar inte hur data på enheten krypteras. Nyckeln används endast för att kryptera nyckeln för enhetsupplåsning.

    Om du inte vill använda en kund hanterad nyckel går du vidare till steg 15.

   ![Säkerhetsskärm som visar inställningar för krypteringstyp](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. Välj **Kund hanterad** nyckel som nyckeltyp. Välj sedan **Välj ett nyckelvalv och nyckel**.
   
    ![Säkerhetsskärm, inställningar för en kund hanterad nyckel](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. Prenumerationen **fylls i Azure Key Vault** på bladet Välj nyckel från Azure Key Vault.

    - För **Nyckelvalv** kan du välja ett befintligt nyckelvalv i listrutan.

      ![Välj nyckel Azure Key Vault skärmen](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - Du kan också välja **Skapa nytt för** att skapa ett nytt nyckelvalv. På skärmen **Skapa nyckelvalv** anger du resursgruppen och namnet på nyckelvalvet. Se till **att skydd för** mjuk borttagning och **rensning** är aktiverat. Acceptera alla andra standardvärden och välj **Granska + skapa.**

      ![Skapa en ny Azure Key Vault inställningar](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Granska informationen för nyckelvalvet och välj **Skapa.** Vänta några minuter tills skapandet av nyckelvalvet har slutförts.

      ![Ny Azure Key Vault granskningsskärm](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. I **Välj nyckel från Azure Key Vault** kan du välja en befintlig nyckel i nyckelvalvet.

    ![Välj befintlig nyckel från Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Om du vill skapa en ny nyckel väljer du **Skapa ny.** Du måste använda en RSA-nyckel. Storleken kan vara 2 048 eller större. Ange ett namn för den nya nyckeln, acceptera de andra standardvärdena och välj **Skapa.**

      ![Alternativet Skapa en ny nyckel](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      Du meddelas när nyckeln har skapats i nyckelvalvet.

13. Välj den **version** av nyckeln som ska användas och välj sedan **Välj**.

      ![Ny nyckel som skapats i nyckelvalvet](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Om du vill skapa en ny nyckelversion väljer du **Skapa ny.**

    ![Öppna en dialogruta för att skapa en ny nyckelversion](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Välj inställningar för den nya nyckelversionen och välj **Skapa.**

    ![Skapa en ny nyckelversion](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    **Krypteringstypsinställningarna** på skärmen **Säkerhet** visar ditt nyckelvalv och din nyckel.

    ![Nyckel- och nyckelvalv för en kund hanterad nyckel](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Välj en användaridentitet som du ska använda för att hantera åtkomsten till den här resursen. Välj **Välj en användaridentitet.** I panelen till höger väljer du den prenumeration och den hanterade identitet som ska användas. Välj sedan **Välj**.

    En användar tilldelad hanterad identitet är en fristående Azure-resurs som kan användas för att hantera flera resurser. Mer information finns i [Hanterade identitetstyper.](../active-directory/managed-identities-azure-resources/overview.md)  

    Om du behöver skapa en ny hanterad identitet följer du riktlinjerna i [Skapa, lista,](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)ta bort eller tilldela en roll till en användar tilldelad hanterad identitet med hjälp av Azure Portal .
    
    ![Välj en användaridentitet](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    Användaridentiteten visas i Inställningar **för krypteringstyp.**

    ![En vald användaridentitet som visas i Inställningar för krypteringstyp](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Om du inte vill använda de systemgenererade lösenord som Azure Data Box använder som standard expanderar du Bring **your own password** (Ta med ditt eget lösenord) på **skärmen** Säkerhet.

    De systemgenererade lösenorden är säkra och rekommenderas om inte din organisation kräver något annat.

    ![Utökade alternativ för ta med ditt eget lösenord för en Data Box-enhet importbeställning](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Om du vill använda ditt eget lösenord för den nya enheten genom att ange inställningar för enhetslösenordet **väljer** du Använd ditt eget lösenord och anger ett lösenord som uppfyller säkerhetskraven.
     
     Lösenordet måste vara alfanumeriskt och innehålla mellan 12 och 15 tecken, med minst en versal, en gemen, ett specialtecken och en siffra. 

     - Tillåtna specialtecken: @ # - $ % ^ ! + = ; : _ ( )
     - Tecken tillåts inte: I I L o O 0
   
     ![Alternativ för att använda ditt eget enhetslösenord på skärmen Säkerhet för en Data Box-enhet importbeställning](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - Så här använder du dina egna lösenord för resurser:

   1. Efter **Ange inställning för resurslösenord** väljer du Använd dina egna **lösenord** och sedan Välj lösenord **för resursen**.
     
       ![Alternativ för att använda egna resurslösenord på skärmen Säkerhet för en Data Box-enhet importbeställning](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    1. Ange ett lösenord för varje lagringskonto i ordningen. Lösenordet används på alla resurser för lagringskontot.
    
       Lösenordet måste vara alfanumeriskt och innehålla mellan 12 och 64 tecken, med minst en versal, en gemen, ett specialtecken och en siffra.

       - Tillåtna specialtecken: @ # - $ % ^ ! + = ; : _ ( )
       - Tecken tillåts inte: I I L o O 0
     
    1. Om du vill använda samma lösenord för alla lagringskonton väljer du **Kopiera till alla.** 

    1. När du är klar väljer du **Spara**.
     
       ![Skärm för att ange resurslösenord för en Data Box-enhet importbeställning](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

    På **skärmen Säkerhet** kan du använda **Visa eller ändra lösenord** för att ändra lösenorden.

16. Om **du** vill aktivera programvarubaserad dubbel kryptering i Säkerhet expanderar du Dubbelkryptering **(för** miljöer med hög säkerhet) och väljer Aktivera dubbel kryptering **för ordern**.

    ![Säkerhetsskärm för Data Box-enhet import, aktivera programvarubaserad kryptering för en Data Box-enhet ordning](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    Den programvarubaserade krypteringen utförs utöver AES-256-bitarskryptering av data på Data Box-enhet.

    > [!NOTE]
    > Om du aktiverar det här alternativet kan det ta längre tid att bearbeta order och kopiera data. Du kan inte ändra det här alternativet när du har skapat din beställning.

    Välj **Nästa: Kontaktinformation för** att fortsätta.

17. I **Kontaktinformation** väljer du **+ Lägg till leveransadress.**

    ![På skärmen Kontaktinformation lägger du till leveransadresser i din Azure Data Box importbeställning](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. I **Leveransadress** uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Välj **Verifiera adress**. Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det.

    ![Skärmbild av dialogrutan Lägg till leveransadress med alternativen Förser med och alternativet Lägg till leveransadress.](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Om du har valt egen hanterad leverans får du ett e-postmeddelande när beställningen har gjorts. Mer information om egen hanterad frakt finns i [Använda egen hanterad frakt.](data-box-portal-customer-managed-shipping.md)

19. Välj **Lägg till leveransadress** när leveransinformationen har verifierats. Du kommer tillbaka till **fliken Kontaktinformation.**

20. När du har återgåt **till Kontaktinformation** lägger du till en eller flera e-postadresser. Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

    ![E-postavsnittet i Kontaktinformation i orderguiden](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. Granska informationen i **Granska + order som** rör beställnings-, kontakt-, meddelande- och sekretessvillkor. Markera rutan för avtalet till sekretesspolicyn.

22. Välj **Beställ**. Det tar några minuter att skapa beställningen.

    ![Skärmen Granska och Ordna i guiden Order](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gör följande med Azure CLI för att beställa en enhet:

1. Skriv ned inställningarna för din Data Box-enhet ordning. De här inställningarna omfattar din personliga/affärsinformation, prenumerationsnamn, enhetsinformation och leveransinformation. Du måste använda de här inställningarna som parametrar när du kör CLI-kommandot för att skapa Data Box-enhet ordning. I följande tabell visas de parameterinställningar som används för `az databox job create` :

   | Inställning (parameter) | Description |  Exempelvärde |
   |---|---|---|
   |resource-group| Använd ett befintligt eller skapa ett nytt. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
   |name| Namnet på den order som du skapar. | "mydataboxorder"|
   |kontaktnamn| Namnet som är associerat med leveransadressen. | "Så här ser det ut"|
   |phone| Telefonnumret till den person eller det företag som får ordern.| "14255551234"
   |location| Den azure-region som är närmast dig och som ska skicka din enhet.| "USA, västra"|
   |sku| Den specifika Data Box-enhet som du beställer. Giltiga värden är: "DataBox", "DataBoxDisk" och "DataBoxHeavy"| "DataBox" |
   |e-postlista| De e-postadresser som är associerade med ordern.| "gusp@contoso.com" |
   |street-address1| Gatuadressen till vilken ordern kommer att levereras. | "15700 NE 39th St" |
   |street-address2| Den sekundära adressinformationen, till exempel nummer eller byggnummer. | "Byggnad 123" |
   |city| Den stad som enheten ska levereras till. | "Redmond" |
   |state-or-province| Det tillstånd där enheten ska levereras.| "WA" |
   |land| Det land som enheten ska levereras till. | "USA" |
   |postnummer| Postnummer eller postnummer som är associerat med leveransadressen.| "98052"|
   |företagsnamn| Namnet på ditt företag som du arbetar för.| "Contoso, LTD" |
   |storage account| Det Azure Storage konto som du vill importera data från.| "mystorageaccount"|
   |felsökning| Inkludera felsökningsinformation för utförlig loggning  | --debug |
   |Hjälp| Visa hjälpinformation för det här kommandot. | --help -h |
   |only-show-errors| Visa endast fel och ignorera varningar. | --only-show-errors |
   |output -o| Anger utdataformatet.  Tillåtna värden: json, jsonc, none, table, tsv, yaml, yamlc. Standardvärdet är json. | --output "json" |
   |DocumentDB| JMESPath-frågesträngen. Mer information finns i [JMESPath.](http://jmespath.org/) | --query <string>|
   |utförlig| Inkludera utförlig loggning. | --verbose |

2. I valfri kommandotolk eller terminal kör du [az data box job create för](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-create) att skapa Azure Data Box ordning.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Här är ett exempel på kommandoanvändning:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Här är utdata från körningen av kommandot:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Alla Azure CLI-kommandon använder json som utdataformat som standard om du inte ändrar det. Du kan ändra utdataformatet med hjälp av den globala parametern `--output <output-format>` . Om du ändrar formatet till "table" blir utdata läsbarheten bättre.

   Här är samma kommando som vi precis körde med en liten anpassning för att ändra formateringen:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Här är utdata från att köra kommandot:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Gör följande med hjälp Azure PowerShell att beställa en enhet:

1. Innan du skapar importordern måste du hämta ditt lagringskonto och spara lagringskontoobjektet i en variabel.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Skriv ned inställningarna för din Data Box-enhet ordning. De här inställningarna omfattar din personliga/affärsinformation, prenumerationsnamn, enhetsinformation och leveransinformation. Du måste använda de här inställningarna som parametrar när du kör PowerShell-kommandot för att skapa Data Box-enhet ordning. I följande tabell visas parameterinställningarna som används för [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob).

    | Inställning (parameter) | Description |  Exempelvärde |
    |---|---|---|
    |ResourceGroupName [krävs]| Använd en befintlig resursgrupp. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
    |Namn [krävs]| Namnet på den order som du skapar. | "mydataboxorder"|
    |ContactName [obligatoriskt]| Namnet som är associerat med leveransadressen. | "Så här ser det ut"|
    |PhoneNumber [krävs]| Telefonnumret till den person eller det företag som får ordern.| "14255551234"
    |Plats [krävs]| Den azure-region som är närmast dig och som ska skicka din enhet.| "WestUS"|
    |DataBoxType [krävs]| Den specifika Data Box-enhet som du beställer. Giltiga värden är: "DataBox", "DataBoxDisk" och "DataBoxHeavy"| "DataBox" |
    |EmailId [krävs]| De e-postadresser som är associerade med ordern.| "gusp@contoso.com" |
    |StreetAddress1 [Krävs]| Gatuadressen till vilken ordern kommer att levereras. | "15700 NE 39th St" |
    |StreetAddress2| Sekundär adressinformation, till exempel nummer eller byggnummer. | "Byggnad 123" |
    |StreetAddress3| Tertiär adressinformation. | |
    |Stad [krävs]| Den stad som enheten ska levereras till. | "Redmond" |
    |StateOrProvinceCode [krävs]| Det tillstånd där enheten ska levereras.| "WA" |
    |Landskod [krävs]| Det land som enheten ska levereras till. | "USA" |
    |Postnummer [krävs]| Postnummer eller postnummer som är associerat med leveransadressen.| "98052"|
    |CompanyName| Namnet på ditt företag som du arbetar för.| "Contoso, LTD" |
    |StorageAccountResourceId [krävs]| Det Azure Storage konto-ID som du vill importera data från.| <AzStorageAccount>.id |

3. I valfri kommandotolk eller terminal använder du [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) för att skapa Azure Data Box ordning.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Här är utdata från att köra kommandot:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>Spåra beställningen

# <a name="portal"></a>[Portal](#tab/portal)

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Öppna Data Box-beställningen och navigera till **Overview** (Översikt) för att visa status. Portalen visar beställningen i tillståndet **Ordered** (beställd).

Om enheten inte är tillgänglig får du ett meddelande. Om enheten är tillgänglig identifierar Microsoft enheten för leverans och förbereder försändelsen. Vid enhetsförberedelsen utförs följande åtgärder:

* SMB-resurser skapas för varje lagringskonto som är associerat med enheten.
* För varje resurs genereras autentiseringsuppgifter för åtkomst såsom användarnamn och lösenord.
* Det enhetslösenord som hjälper till att låsa upp enheten skapas också.
* Data Box låses för att förhindra obehörig åtkomst till enheten.

När enhetsförberedelserna är klara visar portalen beställningen i tillståndet **Processed** (Behandlad).

![En Data Box-enhet som har bearbetats](media/data-box-overview/data-box-order-status-processed.png)

Microsoft förbereder sedan enheten och skickar den via en regional transportör. Du får ett spårningsnummer när enheten har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).

![En Data Box-enhet som har skickats](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Spåra en enskild beställning

Om du vill hämta spårningsinformation om en enskild befintlig Azure Data Box order kör du [`az databox job show`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-show) . Kommandot visar information om ordern, till exempel, men inte begränsat till: namn, resursgrupp, spårningsinformation, prenumerations-ID, kontaktinformation, leveranstyp och enhets-SKU.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   I följande tabell visas parameterinformationen för `az databox job show` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resursgrupp [krävs]| Namnet på resursgruppen som är associerad med ordern. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
   |namn [Krävs]| Namnet på ordern som ska visas. | "mydataboxorder"|
   |felsökning| Inkludera felsökningsinformation för utförlig loggning | --debug |
   |Hjälp| Visa hjälpinformation för det här kommandot. | --help -h |
   |only-show-errors| Visa endast fel och ignorera varningar. | --only-show-errors |
   |output -o| Anger utdataformatet.  Tillåtna värden: json, jsonc, none, table, tsv, yaml, yamlc. Standardvärdet är json. | --output "json" |
   |DocumentDB| JMESPath-frågesträngen. Mer information finns i [JMESPath.](http://jmespath.org/) | --query <string>|
   |utförlig| Inkludera utförlig loggning. | --verbose |

   Här är ett exempel på kommandot med utdataformat inställt på "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Här är utdata från att köra kommandot:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Listordning kan stödjas på prenumerationsnivå och det gör resursgruppen till en valfri parameter (i stället för en obligatorisk parameter).

### <a name="list-all-orders"></a>Visa en lista över alla beställningar

Om du har beställt flera enheter kan du köra för [`az databox job list`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-list) att visa alla Azure Data Box beställningar. Kommandot visar en lista över alla beställningar som tillhör en specifik resursgrupp. Visas också i utdata: ordernamn, leveransstatus, Azure-region, leveranstyp, orderstatus. Annullerade beställningar ingår också i listan.
Kommandot visar också tidsstämplar för varje order.

```azurecli
az databox job list --resource-group <resource-group>
```

I följande tabell visas parameterinformationen för `az databox job list` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resursgrupp [krävs]| Namnet på den resursgrupp som innehåller beställningarna. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
   |felsökning| Inkludera felsökningsinformation för utförlig loggning | --debug |
   |Hjälp| Visa hjälpinformation för det här kommandot. | --help -h |
   |only-show-errors| Visa endast fel och ignorera varningar. | --only-show-errors |
   |output -o| Anger utdataformatet.  Tillåtna värden: json, jsonc, none, table, tsv, yaml, yamlc. Standardvärdet är json. | --output "json" |
   |DocumentDB| JMESPath-frågesträngen. Mer information finns i [JMESPath.](http://jmespath.org/) | --query <string>|
   |utförlig| Inkludera utförlig loggning. | --verbose |

   Här är ett exempel på kommandot med utdataformat inställt på "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Här är utdata från att köra kommandot:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Spåra en enskild beställning

Om du vill hämta spårningsinformation om en enskild befintlig Azure Data Box kör du [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob). Kommandot visar information om ordern, till exempel, men inte begränsat till: namn, resursgrupp, spårningsinformation, prenumerations-ID, kontaktinformation, leveranstyp och enhets-SKU.

> [!NOTE]
> `Get-AzDataBoxJob` används för att visa både enkla och flera ordrar. Skillnaden är att du anger ordernamnet för enskilda beställningar.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   I följande tabell visas parameterinformationen för `Get-AzDataBoxJob` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |ResourceGroup [krävs]| Namnet på resursgruppen som är associerad med ordern. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
   |Namn [krävs]| Namnet på ordern att hämta information för. | "mydataboxorder"|
   |ResourceId| ID:t för resursen som är associerad med ordern. |  |

   Här är ett exempel på kommandot med utdata:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Här är utdata från att köra kommandot:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Visa en lista över alla beställningar

Om du har beställt flera enheter kan du köra för [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) att visa alla dina Azure Data Box beställningar. Kommandot visar en lista över alla beställningar som tillhör en specifik resursgrupp. Visas också i utdata: ordernamn, leveransstatus, Azure-region, leveranstyp, orderstatus. Annullerade beställningar ingår också i listan.
Kommandot visar också tidsstämplar för varje beställning.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Här är ett exempel på kommandot:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Här är utdata från körningen av kommandot:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>Avbryta beställningen

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill avbryta beställningen går Azure Portal till **Översikt** och väljer **Avbryt** i kommandofältet.

När du har gjort en beställning kan du avbryta den när som helst innan orderstatusen markeras som Processed (Behandlad).

Om du vill ta bort en avbruten beställning går **du till Översikt** och väljer **Ta** bort i kommandofältet.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Annullera en beställning

Om du vill avbryta Azure Data Box en beställning kör du [`az databox job cancel`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-cancel) . Du måste ange varför du vill annullera beställningen.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   I följande tabell visas parameterinformationen för `az databox job cancel` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resursgrupp [krävs]| Namnet på resursgruppen som är associerad med den order som ska tas bort. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
   |namn [Krävs]| Namnet på den order som ska tas bort. | "mydataboxorder"|
   |orsak [Krävs]| Orsaken till att beställningen avbröts. | "Jag angav felaktig information och behövde avbryta beställningen." |
   |ja| Fråga inte om bekräftelse. | --yes (-y)| 
   |felsökning| Inkludera felsökningsinformation för utförlig loggning | --debug |
   |Hjälp| Visa hjälpinformation för det här kommandot. | --help -h |
   |only-show-errors| Visa endast fel och ignorera varningar. | --only-show-errors |
   |output -o| Anger utdataformatet.  Tillåtna värden: json, jsonc, none, table, tsv, yaml, yamlc. Standardvärdet är json. | --output "json" |
   |DocumentDB| JMESPath-frågesträngen. Mer information finns i [JMESPath.](http://jmespath.org/) | --query <string>|
   |utförlig| Inkludera utförlig loggning. | --verbose |

   Här är ett exempel på kommandot med utdata:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Här är utdata från att köra kommandot:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Ta bort en order

Om du har avbrutit en Azure Data Box beställning kan du köra för [`az databox job delete`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-delete) att ta bort ordern.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   I följande tabell visas parameterinformationen för `az databox job delete` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resursgrupp [krävs]| Namnet på resursgruppen som är associerad med den order som ska tas bort. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
   |namn [Krävs]| Namnet på den order som ska tas bort. | "mydataboxorder"|
   |prenumeration| Namn eller ID (GUID) för din Azure-prenumeration. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |ja| Fråga inte om bekräftelse. | --yes (-y)|
   |felsökning| Inkludera felsökningsinformation för utförlig loggning | --debug |
   |Hjälp| Visa hjälpinformation för det här kommandot. | --help -h |
   |only-show-errors| Visa endast fel och ignorera varningar. | --only-show-errors |
   |output -o| Anger utdataformatet.  Tillåtna värden: json, jsonc, none, table, tsv, yaml, yamlc. Standardvärdet är json. | --output "json" |
   |DocumentDB| JMESPath-frågesträngen. Mer information finns i [JMESPath.](http://jmespath.org/) | --query <string>|
   |utförlig| Inkludera utförlig loggning. | --verbose |

Här är ett exempel på kommandot med utdata:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Här är utdata från att köra kommandot:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Annullera en beställning

Om du vill Azure Data Box en beställning kör [du Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob). Du måste ange orsaken till att du avbryter beställningen.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

I följande tabell visas parameterinformationen för `Stop-AzDataBoxJob` :

| Parameter | Beskrivning |  Exempelvärde |
|---|---|---|
|ResourceGroup [krävs]| Namnet på resursgruppen som är associerad med ordern som ska avbrytas. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
|Namn [krävs]| Namnet på den order som ska tas bort. | "mydataboxorder"|
|Orsak [krävs]| Orsaken till att beställningen annulleras. | "Jag angav felaktig information och behövde avbryta beställningen." |
|Force | Tvingar cmdleten att köras utan användarbekräftelse. | -Force |

Här är ett exempel på kommandot med utdata:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Här är utdata från körningen av kommandot:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Ta bort en order

Om du har avbrutit en Azure Data Box beställning kan du köra för [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) att ta bort ordern.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

I följande tabell visas parameterinformationen för `Remove-AzDataBoxJob` :

| Parameter | Beskrivning |  Exempelvärde |
|---|---|---|
|ResourceGroup [krävs]| Namnet på resursgruppen som är associerad med den order som ska tas bort. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | "myresourcegroup"|
|Namn [krävs]| Namnet på den order som ska tas bort. | "mydataboxorder"|
|Force | Tvingar cmdleten att köras utan användarbekräftelse. | -Force |

Här är ett exempel på kommandot med utdata:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Här är utdata från körningen av kommandot:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt Azure Data Box artiklar som:

> [!div class="checklist"]
>
> * Förutsättningar för att distribuera Data Box
> * Beställa Data Box
> * Spåra beställningen
> * Avbryta beställningen

Gå vidare till nästa självstudie och lär dig hur du ställer in din Data Box.

> [!div class="nextstepaction"]
> [Konfigurera Azure Data Box](./data-box-deploy-set-up.md)
