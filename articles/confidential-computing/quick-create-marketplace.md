---
title: Snabbstart – Skapa en virtuell dator med konfidentiell databehandling i Azure med Marketplace
description: Kom igång med dina distributioner genom att lära dig hur du snabbt skapar en konfidentiell virtuell dator med Marketplace.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 6dbd6fb5c989e1e8b88bf752f5009bdbb62d8c39
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535458"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Snabbstart: Distribuera en virtuell Dator med konfidentiell databehandling i Azure på Marketplace

Kom igång med konfidentiell databehandling i Azure genom att använda Azure Marketplace för att skapa en virtuell dator (VM) som backas upp av Intel SGX. Sedan installerar du Open Enclave Software Development Kit (SDK) för att konfigurera utvecklingsmiljön. 

Den här självstudien rekommenderas om du snabbt vill börja distribuera en konfidentiell virtuell dator för databehandling. De virtuella datorerna körs på specialmaskinvara och kräver specifika konfigurationsinmatningar för att köras som avsett. Marketplace-erbjudandet som beskrivs i den här snabbstarten gör det enklare att distribuera genom att begränsa användarindata.

Om du är intresserad av att distribuera en konfidentiell virtuell dator med anpassad konfiguration följer du stegen Azure Portal Konfidentiell beräkning för [virtuell dator.](quick-create-portal.md)

## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> Konton för kostnadsfri utvärdering har inte åtkomst till de virtuella datorer som används i den här självstudien. Uppgradera till en prenumeration där du betalar per prenumeration.

## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Längst upp skriver du **konfidentiell databehandling i** sökfältet.

1. Välj **Konfidentiell databehandling i Azure (virtuell dator)** i **avsnittet Marketplace.** 

    ![Välj Marketplace](media/quick-create-marketplace/portal-search-marketplace.png)    

1. På landningssidan för konfidentiell beräkningsdistribution i Azure väljer du **Skapa.**
 

## <a name="configure-your-virtual-machine"></a>Konfigurera den virtuella datorn

1. Välj **Prenumeration** och **Resursgrupp** på fliken **Grunder**. Resursgruppen måste vara tom för att distribuera en virtuell dator från den här mallen till den.

1. Ange eller välj följande värden:

   * **Region:** Välj den Azure-region som är rätt för dig.

        > [!NOTE]
        > Virtuella datorer för konfidentiell beräkning körs bara på specialiserad maskinvara som är tillgänglig i specifika regioner. De senaste tillgängliga regionerna för virtuella DCsv2-Series finns i [tillgängliga regioner.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)
    
    * **Välj Bild:** Välj en avbildning. Om du vill slutföra den här specifika självstudien väljer du Ubuntu 18.04 (Gen 2). Annars omdirigeras du vid lämpliga steg nedan. 

    * **Namn på virtuell dator**, ange ett namn på den nya virtuella datorn.

    * **Autentiseringstyp:** Välj **Offentlig SSH-nyckel** om du skapar en virtuell Linux-dator. 

         > [!NOTE]
         > Du kan välja mellan offentlig SSH-nyckel eller lösenord för autentisering. SSH är säkrare. Instruktioner om hur du genererar en SSH-nyckel finns i [Skapa SSH-nycklar på Linux och Mac för virtuella Linux-datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Användarnamn:** Ange administratörsnamnet för den virtuella datorn.

    * **Offentlig SSH-nyckel:** Ange din offentliga RSA-nyckel om det är tillämpligt.
    
    * **Lösenord:** Ange ditt lösenord för autentisering om det är tillämpligt.
 
1. Välj knappen **Nästa: Inställningar för** virtuell dator längst ned på skärmen.

    > [!IMPORTANT]
    > Vänta tills sidan har uppdaterats. Du *bör inte* se meddelandet "Confidential Computing DCsv2-series VMs are available in a limited number of regions" (Virtuella datorer i konfidentiell databehandling i DCsv2-serien är tillgängliga i ett begränsat antal regioner)." Om det här meddelandet kvarstår går du tillbaka till föregående sida och väljer en DCsv2-Series region.

1. För **ändringsstorlek** väljer du en virtuell dator med konfidentiella beräkningsfunktioner i storleksväljaren. 

    > [!TIP]
    > Du bör se storlekar **DC1s_v2**, **DC2s_v2**, **DC4s_V2** och **DC8_v2**. Det här är de enda storlekar på virtuella datorer som för närvarande stöder konfidentiell databehandling. [Läs mer](virtual-machine-solutions.md).

1. För **OS-disktyp** väljer du en disktyp.

1. För **Virtual Network** skapar du en ny eller väljer från en befintlig resurs.

1. För **Undernät** skapar du ett nytt eller väljer från en befintlig resurs.

1. För **Välj offentliga inkommande portar** väljer du **SSH(Linux)/RDP(Windows).** I den här snabbstarten är det här steget nödvändigt för att ansluta till den virtuella datorn och slutföra Open Enclave SDK-konfigurationen. 

1. För **Startdiagnostik** lämnar du det inaktiverat för den här snabbstarten. 

1. Välj **Granska + skapa**.

1. Välj **Skapa** i fönstret **Granska + skapa**.

> [!NOTE]
> Gå vidare till nästa avsnitt och fortsätt med den här självstudien om du har distribuerat en virtuell Linux-dator. Om du har distribuerat en virtuell Windows-dator följer du dessa steg för att ansluta till den virtuella [Windows-datorn](../virtual-machines/windows/connect-logon.md) och [installerar sedan OE SDK på Windows.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)


## <a name="connect-to-the-linux-vm"></a> Ansluta till den virtuella Linux-datorn

Om du redan använder ett BASH-gränssnitt ansluter du till den virtuella Azure-datorn med **ssh**-kommandot. I följande kommando ersätter du VM-användarnamnet och IP-adressen för att ansluta till din virtuella Linux-dator.

```bash
ssh azureadmin@40.55.55.555
```

Du hittar den offentliga IP-adressen för den virtuella datorn i Azure Portal under avsnittet Översikt på den virtuella datorn.

![IP-adress i Azure Portal](media/quick-create-portal/public-ip-virtual-machine.png)

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

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.4/linux/distro/ubuntuServer18.04/sgx_linux_x64_driver_1.21.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Använd den senaste Intel SGX DCAP-drivrutinen [från Intels SGX-webbplats.](https://01.org/intel-software-guard-extensions/downloads)

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Installera Intel- och Open Enclave-paketen och beroendena

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Det här steget installerar även [paketet az-dcap-client](https://github.com/microsoft/azure-dcap-client) som krävs för att utföra fjärr attestation i Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Verifiera installationen av Open Enclave SDK**

Se [Använda Open Enclave SDK på](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) GitHub för att verifiera och använda den installerade SDK:n.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. 

Välj resursgruppen för den virtuella datorn och välj sedan Ta **bort.** Bekräfta namnet på resursgruppen för att slutföra borttagningen av resurserna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en konfidentiell virtuell dator och installerade Open Enclave SDK. Mer information om virtuella datorer för konfidentiell databehandling i Azure finns [i Lösningar på Virtual Machines](virtual-machine-solutions.md). 

Upptäck hur du kan skapa program för konfidentiell databehandling genom att fortsätta till Open Enclave SDK-exemplen på GitHub. 

> [!div class="nextstepaction"]
> [Skapa Open Enclave SDK-exempel](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
