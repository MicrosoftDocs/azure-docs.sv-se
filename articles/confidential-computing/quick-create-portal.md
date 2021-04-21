---
title: Snabbstart – Skapa en virtuell dator för konfidentiell databehandling i Azure i Azure Portal
description: Kom igång med dina distributioner genom att lära dig hur du snabbt skapar en konfidentiell virtuell dator för databehandling i Azure Portal.
author: JBCook
ms.author: JenCook
ms.date: 04/23/2020
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: 1ae6631c3f6ee71d7a09832956c7e687ceca22b6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819060"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Snabbstart: Distribuera en virtuell Azure-dator för konfidentiell databehandling i Azure Portal

Kom igång med konfidentiell databehandling i Azure genom att använda Azure Portal för att skapa en virtuell dator (VM) som backas upp av Intel SGX. Sedan installerar du Open Enclave Software Development Kit (SDK) för att konfigurera utvecklingsmiljön. 

Den här självstudien rekommenderas om du är intresserad av att distribuera en konfidentiell virtuell dator för beräkning med anpassad konfiguration. Annars rekommenderar vi att du följer de [konfidentiella distributionsstegen för virtuella datorer för databehandling på Microsofts kommersiella marknadsplats.](quick-create-marketplace.md)


## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> Konton för kostnadsfri utvärdering har inte åtkomst till de virtuella datorer som används i den här självstudien. Uppgradera till en Betala per prenumeration.


## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Längst upp väljer du **Skapa en resurs**.

1. I **fönstret Marketplace** väljer du **Compute** till vänster.

1. Leta upp och **välj Virtuell dator**.

    ![Distribuera en virtuell dator](media/quick-create-portal/compute-virtual-machine.png)

1. På landningssidan för virtuell dator väljer du **Skapa**.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Konfigurera en virtuell dator för konfidentiell databehandling

1. Välj **Prenumeration** och **Resursgrupp** på fliken **Grunder**.

1. I **Namn på virtuell dator** anger du ett namn för den nya virtuella datorn.

1. Ange eller välj följande värden:

   * **Region:** Välj den Azure-region som är rätt för dig.

        > [!NOTE]
        > Konfidentiella virtuella datorer för beräkning körs bara på specialiserad maskinvara som är tillgänglig i specifika regioner. De senaste tillgängliga regionerna för virtuella DCsv2-Series finns i [tillgängliga regioner.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

1. Konfigurera operativsystemavbildningen som du vill använda för den virtuella datorn.

    * **Välj Bild:** För den här självstudien väljer du Ubuntu 18.04 LTS. Du kan också välja Windows Server 2019, Windows Server 2016 eller och Ubuntu 20.04 LTS. Om du väljer att göra det omdirigeras du i den här självstudien.
    
    * **Växla avbildningen för Gen 2:** Confidential compute virtual machines (Virtuella datorer för konfidentiell beräkning) körs bara på [generation 2-avbildningar.](../virtual-machines/generation-2.md) Se till att den avbildning som du väljer är en Gen 2-avbildning. Klicka på **fliken** Avancerat ovan där du konfigurerar den virtuella datorn. Rulla nedåt tills du hittar avsnittet "VM Generation" (VM-generation). Välj Gen 2 och gå sedan tillbaka till **fliken Grundläggande** inställningar.
    

        ![Fliken Avancerat](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![VM-generation](media/quick-create-portal/gen2-virtual-machine.png)

    * **Gå tillbaka till grundläggande** konfiguration: Gå tillbaka **till fliken Grundläggande** inställningar med hjälp av navigeringen längst upp.

1. Välj en virtuell dator med konfidentiella beräkningsfunktioner i storleksväljaren genom att välja **ändra storlek.** I storleksväljaren för den virtuella datorn klickar du **på Rensa alla filter.** Välj **Lägg till filter,** **välj** Familj som filtertyp och välj sedan endast **Konfidentiell beräkning.**

    ![DCsv2-Series virtuella datorer](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Du bör se storlekar **DC1s_v2**, **DC2s_v2**, **DC4s_V2** och **DC8_v2**. Det här är de enda storlekar på virtuella datorer som för närvarande stöder konfidentiell databehandling med Intel SGX. [Läs mer](virtual-machine-solutions.md).

1. Fyll i följande information:

   * **Autentiseringstyp:** Välj **Offentlig SSH-nyckel** om du skapar en virtuell Linux-dator. 

        > [!NOTE]
        > Du kan välja mellan offentlig SSH-nyckel eller lösenord för autentisering. SSH är säkrare. Instruktioner om hur du genererar en SSH-nyckel finns i [Skapa SSH-nycklar på Linux och Mac för virtuella Linux-datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Användarnamn:** Ange administratörsnamnet för den virtuella datorn.

    * **Offentlig SSH-nyckel:** Ange din offentliga RSA-nyckel om det är tillämpligt.
    
    * **Lösenord:** Ange ditt lösenord för autentisering om det är tillämpligt.

    * **Offentliga inkommande portar:** Välj **Tillåt valda** portar och **välj SSH (22)** och **HTTP (80)** i listan Välj offentliga **inkommande** portar. Om du distribuerar en virtuell Windows-dator väljer du **HTTP (80)** och **RDP (3389).** I den här snabbstarten är det här steget nödvändigt för att ansluta till den virtuella datorn och slutföra Open Enclave SDK-konfigurationen. 

     ![Ingående portar](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Gör ändringar på **fliken** Diskar.

   * Om du väljer **en DC1s_v2**, **DC2s_v2**, **DC4s_V2** virtuell dator väljer du en disktyp som antingen **är Standard SSD** eller **Premium SSD**. 
   * Om du väljer en **DC8_v2** virtuell dator väljer **du Standard SSD** som disktyp.

1. Gör eventuella ändringar som du vill i inställningarna på följande flikar eller behåll standardinställningarna.

    * **Nätverk**
    * **Hantering**
    * **Gästkonfiguration**
    * **Taggar**

1. Välj **Granska + skapa**.

1. Välj **Skapa** i fönstret **Granska + skapa**.

> [!NOTE]
> Fortsätt till nästa avsnitt och fortsätt med den här självstudien om du har distribuerat en virtuell Linux-dator. Om du har distribuerat en virtuell Windows-dator följer du dessa steg för att ansluta till din virtuella [Windows-dator](../virtual-machines/windows/connect-logon.md) och [installerar sedan OE SDK på Windows.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)


## <a name="connect-to-the-linux-vm"></a> Ansluta till den virtuella Linux-datorn

Om du redan använder ett BASH-gränssnitt ansluter du till den virtuella Azure-datorn med **ssh**-kommandot. I följande kommando ersätter du VM-användarnamnet och IP-adressen för att ansluta till din virtuella Linux-dator.

```bash
ssh azureadmin@40.55.55.555
```

Du hittar den offentliga IP-adressen för den virtuella datorn i Azure Portal under avsnittet Översikt för den virtuella datorn.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="IP-adress i Azure Portal":::

Om du kör på Windows och inte har något BASH-gränssnitt kan du installera en SSH-klient som t.ex. PuTTY.

1. [Ladda ned och installera PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Kör PuTTY.

1. På PuTTY-konfigurationsskärmen anger du den virtuella datorns offentliga IP-adress.

1. Välj **Öppna** och ange ditt användarnamn och lösenord i prompterna.

Mer information om hur du ansluter till virtuella Linux-datorer finns i [Skapa en virtuell Linux-dator i Azure med hjälp av portalen](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Om du ser en PuTTY-säkerhetsvarning om att serverns värdnyckel inte cachelagras i registret kan du välja bland följande alternativ. Om du litar på den här värden väljer du **Ja** för att lägga till nyckeln i PuTTy-cacheminnet och fortsätta anslutningen. Om du vill fortsätta med att bara ansluta en gång utan att lägga till nyckeln i cacheminnet väljer du **Nej**. Om du inte litar på den här värden lämnar du anslutningen genom att välja **Avbryt**.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Installera OPEN Enclave SDK (OE SDK) <a id="Install"></a>

Följ de stegvisa anvisningarna för att installera [OE SDK](https://github.com/openenclave/openenclave) på din virtuella DCsv2-Series dator som kör en Ubuntu 18.04 LTS Gen 2-avbildning. 

Om den virtuella datorn körs på Ubuntu 18.04 LTS Gen 2 måste du följa installationsanvisningarna för [Ubuntu 18.04.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Konfigurera Intel- och Microsoft APT-lagringsplatsen

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. Installera Intel SGX DCAP-drivrutinen
Vissa versioner av Ubuntu kan redan ha Intel SGX-drivrutinen installerad. Kontrollera med följande kommando: 

```bash
dmesg | grep -i sgx
[  106.775199] sgx: intel_sgx: Intel SGX DCAP Driver {version}
``` 
Om utdata är tomma installerar du drivrutinen: 

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.7/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.35.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Använd den senaste Intel SGX DCAP-drivrutinen [från Intels SGX-webbplats.](https://01.org/intel-software-guard-extensions/downloads)

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Installera Intel- och Open Enclave-paketen och beroendena


```bash
sudo apt -y install clang-8 libssl-dev gdb libsgx-enclave-common libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Det här steget installerar även [paketet az-dcap-client](https://github.com/microsoft/azure-dcap-client) som krävs för att utföra fjärr attestation i Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Verifiera installationen av Open Enclave SDK**

Se [Använda Open Enclave SDK på](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) GitHub för att verifiera och använda den installerade SDK:n.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. 

Välj resursgruppen för den virtuella datorn och välj sedan Ta **bort.** Bekräfta namnet på resursgruppen för att slutföra borttagningen av resurserna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en virtuell dator för konfidentiell databehandling och installerade Open Enclave SDK. Mer information om virtuella datorer för konfidentiell databehandling i Azure finns [i Lösningar på Virtual Machines](virtual-machine-solutions.md). 

Upptäck hur du kan skapa program för konfidentiell databehandling genom att fortsätta till Open Enclave SDK-exemplen på GitHub. 

> [!div class="nextstepaction"]
> [Skapa Open Enclave SDK-exempel](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
