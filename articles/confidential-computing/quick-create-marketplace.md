---
title: Snabb start – skapa en virtuell Azure-dator med konfidentiell dator med Marketplace
description: Kom igång med dina distributioner genom att lära dig hur du snabbt skapar en virtuell dator med konfidentiella data behandling.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: aba23b67574fb74b7cd571dc5d4642bb8b991b93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566642"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Snabb start: Distribuera en virtuell Azure-dator med konfidentiella data behandling på Marketplace

Kom igång med Azures konfidentiella data behandling genom att använda Azure Marketplace för att skapa en virtuell dator (VM) som backas upp av Intel SGX. Sedan installerar du den öppna enklaven Software Development Kit (SDK) för att konfigurera din utvecklings miljö. 

Den här självstudien rekommenderas om du snabbt vill börja distribuera en virtuell dator med konfidentiella data behandling. De virtuella datorerna körs på den särskilda maskin varan och kräver särskilda konfigurations inmatningar för att köras som avsett. Marketplace-erbjudandet som beskrivs i den här snabb starten gör det enklare att distribuera genom att begränsa indata från användaren.

Om du är intresse rad av att distribuera en konfidentiell virtuell dator med en mer anpassad konfiguration följer du [distributions stegen Azure Portal konfidentiell beräkning av virtuella datorer](quick-create-portal.md).

## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> De kostnads fria utvärderings kontona har inte åtkomst till de virtuella datorerna som används i den här självstudien. Uppgradera till en prenumeration där du betalar per användning.

## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Högst upp skriver du **Azures konfidentiella data behandling** i Sök fältet.

1. Välj **Azures konfidentiella data behandling (virtuell dator)** i **Marketplace** -avsnittet. 

    ![Välj Marketplace](media/quick-create-marketplace/portal-search-marketplace.png)    

1. På sidan för ingångs sidan för Azures konfidentiella beräknings distribution väljer du **skapa**.
 

## <a name="configure-your-virtual-machine"></a>Konfigurera den virtuella datorn

1. Välj **Prenumeration** och **Resursgrupp** på fliken **Grunder**. Resurs gruppen måste vara tom för att distribuera en virtuell dator från den här mallen till den.

1. Ange eller välj följande värden:

   * **Region**: Välj den Azure-region som passar dig.

        > [!NOTE]
        > Konfidentiella beräknings virtuella datorer kan bara köras på specialiserad maskin vara som är tillgänglig i vissa regioner. De senaste tillgängliga regionerna för DCsv2-Series virtuella datorer finns i [tillgängliga regioner](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).
    
    * **Välj bild**: Välj valfri bild. Om du vill slutföra den här självstudien väljer du Ubuntu 18,04 (Gen 2). Annars omdirigeras du enligt lämpliga steg nedan. 

    * **Namn på virtuell dator**, ange ett namn för den nya virtuella datorn.

    * **Autentiseringstyp**: Välj **Offentlig SSH-nyckel** om du skapar en virtuell Linux-dator. 

         > [!NOTE]
         > Du kan välja mellan offentlig SSH-nyckel eller lösenord för autentisering. SSH är säkrare. Instruktioner om hur du genererar en SSH-nyckel finns i [Skapa SSH-nycklar på Linux och Mac för virtuella Linux-datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Användar namn**: Ange administratörs namnet för den virtuella datorn.

    * **Offentlig SSH-nyckel**: Ange din offentliga RSA-nyckel om det är tillämpligt.
    
    * **Lösen ord**: om tillämpligt anger du ditt lösen ord för autentisering.
 
1. Välj **Nästa: inställningar för virtuell dator** längst ned på skärmen.

    > [!IMPORTANT]
    > Vänta tills sidan har uppdaterats. Du *bör inte* se ett meddelande om att virtuella datorer med konfidentiell dator användning i DCsv2-serien är tillgängliga i ett begränsat antal regioner. " Om det här meddelandet kvarstår går du tillbaka till föregående sida och väljer en tillgänglig DCsv2-Series region.

1. För **ändrings storlek** väljer du en virtuell dator med konfidentiella beräknings funktioner i storleks väljaren. 

    > [!TIP]
    > Du bör se storlekar **DC1s_v2**, **DC2s_v2**, **DC4s_V2** och **DC8_v2**. Detta är de enda storlekar för virtuella datorer som för närvarande stöder konfidentiell bearbetning. [Läs mer](virtual-machine-solutions.md).

1. Välj en disktyp för **typ av operativ system disk**.

1. Skapa ett nytt namn eller Välj från en befintlig resurs för **Virtual Network**.

1. Skapa ett nytt namn eller Välj från en befintlig resurs för **undernät**.

1. Välj **SSH (Linux)/RDP (Windows)** för **utvalda offentliga inkommande portar**. I den här snabb starten är det här steget nödvändigt för att ansluta till den virtuella datorn och slutföra den öppna enklaven SDK-konfigurationen. 

1. För **startdiagnostik** lämnar du den inaktive rad för den här snabb starten. 

1. Välj **Granska + skapa**.

1. Välj **Skapa** i fönstret **Granska + skapa**.

> [!NOTE]
> Fortsätt till nästa avsnitt och fortsätt med den här självstudien om du har distribuerat en virtuell Linux-dator. Om du har distribuerat en virtuell Windows-dator [följer du dessa steg för att ansluta till din virtuella Windows-dator](../virtual-machines/windows/connect-logon.md) och [installerar sedan OE SDK i Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a> Ansluta till den virtuella Linux-datorn

Om du redan använder ett BASH-gränssnitt ansluter du till den virtuella Azure-datorn med **ssh**-kommandot. I följande kommando ersätter du VM-användarnamnet och IP-adressen för att ansluta till din virtuella Linux-dator.

```bash
ssh azureadmin@40.55.55.555
```

Du hittar den offentliga IP-adressen för den virtuella datorn i Azure Portal i avsnittet Översikt på den virtuella datorn.

![IP-adress i Azure Portal](media/quick-create-portal/public-ip-virtual-machine.png)

Om du kör på Windows och inte har något BASH-gränssnitt kan du installera en SSH-klient som t.ex. PuTTY.

1. [Ladda ned och installera PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Kör PuTTY.

1. På PuTTY-konfigurationsskärmen anger du den virtuella datorns offentliga IP-adress.

1. Välj **Öppna** och ange ditt användarnamn och lösenord i prompterna.

Mer information om hur du ansluter till virtuella Linux-datorer finns i [Skapa en virtuell Linux-dator i Azure med hjälp av portalen](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Om du ser en PuTTY-säkerhetsvarning om att serverns värdnyckel inte cachelagras i registret kan du välja bland följande alternativ. Om du litar på den här värden väljer du **Ja** för att lägga till nyckeln i PuTTy-cacheminnet och fortsätta anslutningen. Om du vill fortsätta med att bara ansluta en gång utan att lägga till nyckeln i cacheminnet väljer du **Nej**. Om du inte litar på den här värden lämnar du anslutningen genom att välja **Avbryt**.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Installera Open enklaven SDK (OE SDK) <a id="Install"></a>

Följ de stegvisa anvisningarna för att installera [OE SDK](https://github.com/openenclave/openenclave) på din DCsv2-Series virtuella dator som kör en Ubuntu 18,04-LTS gen 2-avbildning. 

Om din virtuella dator körs på Ubuntu 16,04 LTS gen 2, måste du följa [installations anvisningarna för Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md). 

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Konfigurera Intel-och Microsoft APT-databaserna

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. installera Intel SGX DCAP-drivrutinen

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.4/linux/distro/ubuntuServer18.04/sgx_linux_x64_driver_1.21.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Använd den senaste Intel SGX DCAP-drivrutinen från [Intels SGX-plats](https://01.org/intel-software-guard-extensions/downloads).

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. installera Intel-och Open enklaven-paket och-beroenden

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Det här steget installerar även [AZ-dcap-client-](https://github.com/microsoft/azure-dcap-client) paketet som krävs för att utföra fjärrattestering i Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **kontrol lera den öppna ENKLAVEN SDK-installationen**

Se [använda Open ENKLAVEN SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) på GitHub för att verifiera och använda det installerade SDK: n.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. 

Välj resurs gruppen för den virtuella datorn och välj sedan **ta bort**. Bekräfta resurs gruppens namn för att slutföra borttagningen av resurserna.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en virtuell dator med konfidentiella data behandling och installerat Open enklaven SDK. Mer information om konfidentiell dator användning av virtuella datorer i Azure finns i [lösningar på Virtual Machines](virtual-machine-solutions.md). 

Upptäck hur du kan skapa konfidentiella data behandlings program genom att fortsätta till Open enklaven SDK-exempel på GitHub. 

> [!div class="nextstepaction"]
> [Skapa öppna enklaven SDK-exempel](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
