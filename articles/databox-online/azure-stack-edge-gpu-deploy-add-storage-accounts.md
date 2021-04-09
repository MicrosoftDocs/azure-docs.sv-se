---
title: Självstudie för att överföra data till lagrings konto med Azure Stack Edge Pro GPU | Microsoft Docs
description: Lär dig hur du lägger till och ansluter till lokala och gräns lagrings konton på Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 4d235136190845ba7531592fdeecd9cd05b0347c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200955"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Självstudie: överföra data via lagrings konton med Azure Stack Edge Pro GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här självstudien beskrivs hur du lägger till och ansluter till lagrings konton på din Azure Stack Edge Pro-enhet. När du har lagt till lagrings kontona kan Azure Stack Edge Pro överföra data till Azure.

Den här proceduren kan ta cirka 30 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägg till ett lagrings konto
> * Anslut till lagrings kontot

 
## <a name="prerequisites"></a>Förutsättningar

Innan du lägger till lagrings konton i Azure Stack Edge Pro, se till att:

- Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).

- Du har aktiverat den fysiska enheten enligt beskrivningen i [aktivera Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="add-an-edge-storage-account"></a>Lägg till ett gräns lagrings konto

Gör så här för att skapa ett gräns lagrings konto:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Anslut till gräns lagrings kontot

Nu kan du ansluta till Edge Storage REST-API: er via *http* eller *https*.

- *Https* är det säkraste och rekommenderade sättet.
- *Http* används vid anslutning över betrodda nätverk.

## <a name="connect-via-http"></a>Ansluta via http

Anslutning till Edge Storage REST-API: er via http kräver följande steg:

- Lägg till Azure-konsekvent tjänst-VIP-och blob service-slutpunkt till fjärrvärden
- Verifiera anslutningen 

Vart och ett av dessa steg beskrivs i följande avsnitt.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Lägg till enhetens IP-adress och blob service-slutpunkt till fjärrklienten

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Verifiera anslutning

För att verifiera anslutningen behöver du vanligt vis följande information (kan variera) som du samlade in i föregående steg:

- Lagrings konto namn.
- Åtkomst nyckel för lagrings konto.
- Blob Service slut punkt.

Du har redan lagrings kontots namn och blob service-slutpunkten. Du kan hämta åtkomst nyckeln för lagrings kontot genom att ansluta till enheten via Azure Resource Manager med hjälp av en Azure PowerShell-klient.

Följ stegen i [ansluta till enheten via Azure Resource Manager](azure-stack-edge-gpu-connect-resource-manager.md). När du har loggat in på de lokala enhets-API: erna via Azure Resource Manager hämtar du listan över lagrings konton på enheten. Kör följande cmdlet:

`Get-AzureRMStorageAccount`

I listan över lagrings kontona på enheten identifierar du det lagrings konto som du behöver åtkomst nyckeln för. Notera lagrings kontots namn och resurs grupp.

Ett exempel på utdata visas nedan:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Hämta åtkomst nyckeln genom att köra följande cmdlet:

`Get-AzureRmStorageAccountAccessKey`

Ett exempel på utdata visas nedan:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Kopiera och spara den här nyckeln. Du kommer att använda den här nyckeln för att verifiera anslutningen med hjälp av Azure Storage Explorer.

Verifiera att anslutningen har upprättats genom att ansluta till ett externt lagringskonto med Storage Explorer. Om du inte har Storage Explorer kan du [hämta Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Ansluta via https

För anslutning till REST-API:er för Azure Blob-lagring genom https krävs följande steg:

- Hämta ditt BLOB-slutpunkts certifikat
- Importera certifikatet på klienten eller fjärran sluten värd
- Lägg till enhetens IP-adress och blob service-slutpunkt till klienten eller fjärrvärden
- Konfigurera och verifiera anslutningen

Vart och ett av dessa steg beskrivs i följande avsnitt.

### <a name="get-certificate"></a>Hämta certifikat

Åtkomst till Blob Storage via HTTPS kräver ett SSL-certifikat för enheten. Du kommer också att överföra det här certifikatet till din Azure Stack Edge Pro-enhet som *. pfx* -fil med en privat nyckel som är kopplad till den. Mer information om hur du skapar (endast för testning och utveckling) och laddar upp certifikaten till din Azure Stack Edge Pro-enhet finns på:

- [Skapa BLOB-slutpunktens certifikat](azure-stack-edge-gpu-manage-certificates.md#create-certificates-optional).
- [Ladda upp BLOB-slutpunktens certifikat](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).
- [Importera certifikat på klienten som har åtkomst till enheten](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

### <a name="import-certificate"></a>Importera certifikatet

Om du använder Azure Storage Explorer för att ansluta till lagrings kontona på enheten, måste du också importera certifikat till Storage Explorer i PEM-format. I Windows-miljön är Base-64-kodad *. cer* samma som PEM-formatet.

Utför följande steg för att importera certifikaten på Azure Storage Explorer:

1. Kontrol lera att Azure Storage Explorer är mål för Azure Stack-API: er. Gå till **redigera > mål Azure Stack API: er**. När du uppmanas att göra det måste du starta om Storage Explorer för att ändringen ska börja gälla.

2. Om du vill importera SSL-certifikat går du till **redigera > SSL-certifikat > importera certifikat**.

  
   ![Importera certifikat till Storage Explorer](./media/azure-stack-edge-gpu-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Navigera till och ange signerings kedjan och blob-certifikat. Både signerings kedjan och blob-certifikatet bör vara i PEM-format som är samma som Base64-kodat format på Windows-system. Du får ett meddelande om att certifikaten har importer ATS.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Lägg till enhetens IP-adress och blob service-slutpunkt

Följ samma steg för att [lägga till enhetens IP-adress och blob service-slutpunkt vid anslutning via *http*](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>Konfigurera och verifiera anslutning

Följ stegen för att [Konfigurera och verifiera den anslutning som du använde vid anslutning via *http*](#verify-connection). Den enda skillnaden är att du låter alternativet *Använd http* vara avmarkerat.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om följande Azure Stack Edge Pro-ämnen:

> [!div class="checklist"]
> * Lägg till ett lagrings konto
> * Ansluta till ett lagringskonto

Om du vill lära dig hur du omvandlar dina data med hjälp av Azure Stack Edge Pro går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Transformera data med Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)
