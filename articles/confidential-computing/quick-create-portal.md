---
title: Snabb start – skapa en virtuell Azure-dator med konfidentiella data behandling i Azure Portal
description: Kom igång med dina distributioner genom att lära dig hur du snabbt skapar en virtuell dator med konfidentiella data behandling i Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: JenCook
ms.openlocfilehash: 3f0984acd66bd5d6c148be8451938d3152fb9ca7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566676"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Snabb start: Distribuera en virtuell Azure-dator med konfidentiella data behandling i Azure Portal

Kom igång med Azures konfidentiella data behandling genom att använda Azure Portal för att skapa en virtuell dator (VM) som backas upp av Intel SGX. Sedan installerar du den öppna enklaven Software Development Kit (SDK) för att konfigurera din utvecklings miljö. 

Den här själv studie kursen rekommenderas för dig om du är intresse rad av att distribuera en konfidentiell virtuell dator med anpassad konfiguration. I annat fall rekommenderar vi att du följer [distributions stegen för den virtuella datorn för den konfidentiella beräkningen för Microsofts kommersiella marknads](quick-create-marketplace.md)plats.


## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> De kostnads fria utvärderings kontona har inte åtkomst till de virtuella datorerna som används i den här självstudien. Uppgradera till en prenumeration där du betalar per användning.


## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **skapa en resurs** längst upp.

1. I fönstret **Marketplace** väljer du **Beräkna** till vänster.

1. Sök efter och välj **virtuell dator**.

    ![Distribuera en virtuell dator](media/quick-create-portal/compute-virtual-machine.png)

1. På sidan landning av virtuell dator väljer du **skapa**.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Konfigurera en virtuell dator med konfidentiell dator användning

1. Välj **Prenumeration** och **Resursgrupp** på fliken **Grunder**.

1. För **namn på virtuell dator** anger du ett namn för den nya virtuella datorn.

1. Ange eller välj följande värden:

   * **Region**: Välj den Azure-region som passar dig.

        > [!NOTE]
        > Konfidentiella beräknings virtuella datorer kan bara köras på specialiserad maskin vara som är tillgänglig i vissa regioner. De senaste tillgängliga regionerna för DCsv2-Series virtuella datorer finns i [tillgängliga regioner](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Konfigurera den operativ Systems avbildning som du vill använda för den virtuella datorn.

    * **Välj bild**: i den här självstudien väljer du Ubuntu 18,04 LTS. Du kan också välja Windows Server 2019, Windows Server 2016 eller och Ubuntu 16,04 LTS. Om du väljer att göra det omdirigeras du i den här självstudien enligt detta.
    
    * **Växlar avbildningen för gen 2**: konfidentiella beräkning av virtuella datorer körs bara på [generation 2](../virtual-machines/generation-2.md) -avbildningar. Se till att den avbildning du väljer är en gen 2-avbildning. Klicka på fliken **Avancerat** ovan där du konfigurerar den virtuella datorn. Rulla nedåt tills du hittar avsnittet "VM-generering". Välj gen 2 och gå sedan tillbaka till fliken **grundläggande** .
    

        ![Fliken Avancerat](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![VM-generation](media/quick-create-portal/gen2-virtual-machine.png)

    * **Återgå till grundläggande konfiguration**: gå tillbaka till fliken **grundläggande** med hjälp av navigeringen längst upp.

1. Välj en virtuell dator med konfidentiella beräknings funktioner i storleks väljaren genom att välja **ändra storlek**. I storleks väljaren för virtuell dator klickar du på **Rensa alla filter**. Välj **Lägg till filter**, Välj en **serie** för filter typen och välj sedan endast **konfidentiell beräkning**.

    ![DCsv2-Series virtuella datorer](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Du bör se storlekar **DC1s_v2**, **DC2s_v2**, **DC4s_V2** och **DC8_v2**. Detta är de enda storlekar för virtuella datorer som för närvarande stöder konfidentiell bearbetning. [Läs mer](virtual-machine-solutions.md).

1. Fyll i följande information:

   * **Autentiseringstyp**: Välj **Offentlig SSH-nyckel** om du skapar en virtuell Linux-dator. 

        > [!NOTE]
        > Du kan välja mellan offentlig SSH-nyckel eller lösenord för autentisering. SSH är säkrare. Instruktioner om hur du genererar en SSH-nyckel finns i [Skapa SSH-nycklar på Linux och Mac för virtuella Linux-datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Användar namn**: Ange administratörs namnet för den virtuella datorn.

    * **Offentlig SSH-nyckel**: Ange din offentliga RSA-nyckel om det är tillämpligt.
    
    * **Lösen ord**: om tillämpligt anger du ditt lösen ord för autentisering.

    * **Offentliga inkommande portar**: Välj **Tillåt valda portar** och välj **SSH (22)** och **http (80)** i listan **Välj offentliga inkommande portar** . Om du distribuerar en virtuell Windows-dator väljer du **http (80)** och **RDP (3389)**. I den här snabb starten är det här steget nödvändigt för att ansluta till den virtuella datorn och slutföra den öppna enklaven SDK-konfigurationen. 

     ![Ingående portar](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Gör ändringar på fliken **diskar** .

   * Om du väljer en **DC1s_v2** **DC2s_v2** **DC4s_V2** virtuell dator väljer du en disktyp som antingen är **standard SSD** eller **Premium SSD**. 
   * Om du väljer en **DC8_v2** virtuell dator väljer du **standard SSD** som typ av disk.

1. Gör de ändringar du vill i inställningarna på följande flikar eller Behåll standardinställningarna.

    * **Nätverk**
    * **Hantering**
    * **Gästkonfiguration**
    * **Taggar**

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

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="IP-adress i Azure Portal":::

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
wget https://download.01.org/intel-sgx/sgx-dcap/1.9/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.36.2.bin -O sgx_linux_x64_driver.bin
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