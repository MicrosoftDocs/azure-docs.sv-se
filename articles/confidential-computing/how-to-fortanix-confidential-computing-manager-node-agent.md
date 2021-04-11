---
title: Köra ett program med Fortanix-hanteraren för konfidentiella data behandling
description: Lär dig hur du använder Fortanix-hanteraren för konfidentiella data behandling för att konvertera dina behållar avbildningar
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: JenCook
ms.openlocfilehash: 91a3f0a38d1182e445eba39bf31092be17f9a1fd
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111997"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>Så här gör du: kör ett program med Fortanix-hanteraren för konfidentiella data behandling

Börja köra ditt program i Azures konfidentiella data behandling med [Fortanix-hanteraren för konfidentiella data behandling](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) och [Fortanix Node agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) från [Fortanix](https://www.fortanix.com/).


Fortanix är en program varu leverantör från tredje part med produkter och tjänster som bygger på Azure-infrastrukturen. Det finns andra leverantörer från tredje part som erbjuder liknande tjänster för konfidentiell behandling på Azure.

> [!Note]
> De produkter som refereras i detta dokument är inte understyrda av Microsoft. Microsoft tillhandahåller den här informationen endast som en bekvämlighet, och referensen till dessa produkter från andra tillverkare än Microsoft kräver inte Microsoft.

Den här självstudien visar hur du konverterar program avbildningen till en konfidentiellt Compute-skyddad avbildning. I den här miljön används [Fortanix](https://www.fortanix.com/) -programvara som drivs av azures DCsv2-Series Intel SGX-aktiverade virtuella datorer. Den här lösningen dirigerar kritiska säkerhets principer, till exempel identitets verifiering och data åtkomst kontroll.

För Fortanix-/regionsspecifika support ansluter du till [Fortanix-slack-communityn](https://fortanix.com/community/) och använder kanalen `#enclavemanager` .

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har ett Fortanix-konto för konfidentiell dator hantering måste du [Registrera](https://ccm.fortanix.com/auth/sign-up) dig innan du börjar.
1. Ett privat [Docker](https://docs.docker.com/) -register för att skicka konverterade program avbildningar.
1. Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> De kostnads fria utvärderings kontona har inte åtkomst till de virtuella datorerna som används i den här självstudien. Uppgradera till en prenumeration där du betalar per användning.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Lägga till ett program i Fortanix-hanteraren för konfidentiella data behandling

1. Logga in på [Fortanix-hanteraren för konfidentiella data behandling (FORTANIX CCM)](https://ccm.fortanix.com).
1. Gå till sidan **konton** och välj **Lägg till konto** för att skapa ett nytt konto.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="Skärm bild som visar hur du skapar ett konto.":::

1. När ditt konto har skapats trycker du på **Välj** för att välja det nyligen skapade kontot. Nu kan vi börja registrera Compute-noderna och skapa program.
1. Klicka på knappen **+ program** för att lägga till ett program. I det här exemplet ska vi lägga till ett enklaven OS-program för en kolv.

1. Välj knappen **Lägg till** för enklaven OS-programmet.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="Skärm bild som visar hur du lägger till ett program.":::

   > [!NOTE]
   > I den här självstudien beskrivs bara att lägga till enklaven OS-program. [Läs mer](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager) om hur du ansluter EdP Rust-program till Fortanix-hanteraren för konfidentiella data behandling.

1. I den här självstudien använder vi Fortanix Docker-registret för exempel programmet. Fyll i informationen från följande information. Använd ditt privata Docker-register för att behålla avbildningen av utdata.

    - **Program namn**: python-program Server
    - **Beskrivning**: python-mätkolv-Server
    - **Namn på inspelnings avbildning**: fortanix/python-flaska
    - **Namn på utdata-avbildning**: fortanx-Private/python-mätkolv-SGX
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Minnes storlek**: 1 GB
    - **Antal trådar**: 128

    *Valfritt*: kör programmet.
    - **Docker-hubb**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **App**: fortanix/python-flaska

      Kör följande kommando:

      ```bash
         sudo docker run fortanix/python-flask
      ```

1. Lägg till ett certifikat. Fyll i informationen med hjälp av informationen nedan och välj sedan **Nästa**:
    - **Domän**: MyApp. domain. dom
    - **Typ**: certifikat utfärdat av konfidentiell data behandlings hanterare
    - **Nyckel Sök väg**:/appkey.pem
    - **Nyckel typ**: RSA
    - **Certifikat Sök väg**:/appcert.pem
    - **RSA-nyckel storlek**: 2048 bitar

## <a name="create-an-image"></a>Skapa en avbildning

En Fortanix CCM-avbildning är en program varu version eller version av ett program. Varje avbildning är associerad med en enklaven-hash (MRENCLAVE).

1. På sidan **Lägg till avbildning** anger du **autentiseringsuppgifterna** för **avbildnings namnet på utdata**. Dessa autentiseringsuppgifter används för att få åtkomst till det privata Docker-registret där avbildningen ska flyttas.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="Skärm bild som visar hur du skapar en bild.":::

1. Ange avbildnings tag gen och välj **skapa**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-tag.png" alt-text="Skärm bild som visar hur du lägger till en tagg.":::

## <a name="domain-and-image-allowlist"></a>Domän-och avbildnings tillåten

Ett program vars domän läggs till i tillåten kommer att få ett TLS-certifikat från Fortanix-hanteraren för konfidentiella data behandling. När ett program körs från den konverterade avbildningen kommer det att försöka kontakta Fortanix-hanteraren för konfidentiella data behandling. Programmet kommer sedan att fråga efter ett TLS-certifikat.

Växla till fliken **aktiviteter** till vänster och godkänn väntande begär Anden för att tillåta domänen och avbildningen.

## <a name="enroll-compute-node-agent-in-azure"></a>Registrera Compute Node-agenten i Azure

### <a name="generate-and-copy-join-token"></a>Generera och kopiera kopplings-token

I Fortanix-hanteraren för konfidentiella data behandling skapar du en token. Med den här token kan en Compute-nod i Azure autentisera sig själv. Du måste ge den här token till din virtuella Azure-dator.

1. I hanterings konsolen väljer du knappen **+ Registrera nod** .
1. Välj **generera token** för att generera kopplings-token. Kopiera token.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Registrera noder i Fortanix Node agent på Azure Marketplace

Om du skapar en Fortanix-Node-agent distribueras en virtuell dator, ett nätverks gränssnitt, ett virtuellt nätverk, en nätverks säkerhets grupp och en offentlig IP-adress till din Azure-resurs grupp. Din Azure-prenumeration kommer att faktureras per timme för den virtuella datorn. Innan du skapar en Fortanix Node-agent granskar du [pris sidan för virtuella](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Azure-datorer för DCsv2-serien. Ta bort Azure-resurser när de inte används.

1. Gå till [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) och logga in med dina Azure-autentiseringsuppgifter.
1. I Sök fältet skriver du **Fortanix konfidentiella data behandling Node agent**. Välj den app som visas i sökrutan som heter **Fortanix konfidentiell Computing Node agent** för att gå till start sidan för erbjudandet.

   ![Sök på Marketplace](media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png)
1. Välj **Hämta nu**, Fyll i din information om det behövs och välj **Fortsätt**. Du omdirigeras till Azure Portal.
1. Välj **skapa** för att ange distributions sidan för Fortanix-konfidentiell dator hantering.
1. På den här sidan anger du information för att distribuera en virtuell dator. Mer specifikt är den virtuella datorn en DCsv2-Series Intel SGX-aktiverad virtuell dator från Azure med Fortanix Node Agent-programvara installerad. Med Node-agenten kan den konverterade avbildningen köras säkert på Intel SGX-noder i Azure.  Välj den **prenumeration** och **resurs grupp** där du vill distribuera den virtuella datorn och de tillhör ande resurserna.

   > [!NOTE]
   > Det finns begränsningar när du distribuerar DCsv2-Series virtuella datorer i Azure. Du kan behöva begära kvot för ytterligare kärnor. Läs om [konfidentiella data behandlings lösningar på virtuella Azure-datorer](./virtual-machine-solutions.md) för mer information.

1. Välj en tillgänglig region.
1. Ange ett namn på den virtuella datorn i **nodnamn**.
1. Ange ett användar namn och lösen ord (eller SSH-nyckel) för autentisering i den virtuella datorn.
1. Lämna standard storleken för OS-disken som 200 och välj en storlek på virtuell dator (Standard_DC4s_v2 räcker för den här självstudien).
1. Klistra in token som genererades tidigare i **kopplings-token**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="Skärm bild som visar hur du distribuerar en resurs.":::

1. Välj **Granska + skapa**. Se till att verifierings passen är klar och välj sedan **skapa**. När alla resurser distribueras är Compute-noden nu registrerad i Fortanix-hanteraren för konfidentiella data behandling.

## <a name="run-the-application-image-on-the-compute-node"></a>Kör program avbildningen på Compute-noden

Kör programmet genom att köra följande kommando. Se till att ändra nodens IP-adress, port och konverterade avbildnings namn som indata för ditt specifika program.

I den här självstudien är kommandot som ska köras:

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

Plats:

- *52.152.206.164* är värd-IP för Node-agenten
- *9092* är porten som Node agent lyssnar på
- *fortanix-Private/python-flaskan-SGX* är den konverterade app som finns på fliken avbildningar under kolumnen **avbildnings namn** i tabellen **images** i Fortanix-webb portalen för konfidentiell data behandling.

## <a name="verify-and-monitor-the-running-application"></a>Verifiera och övervaka det program som körs

1. Återgå till [Fortanix-hanteraren för konfidentiella data behandling](https://ccm.fortanix.com/console).
1. Se till att du arbetar inuti det **konto** där du registrerade noden.
1. Gå till **hanterings konsolen** genom att välja den översta ikonen i det vänstra navigerings fönstret.
1. Välj fliken **program** .
1. Kontrol lera att det finns ett program som körs med en associerad Compute-nod.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurs gruppen, den virtuella datorn och de associerade resurserna. Om du tar bort resurs gruppen avregistreras de noder som är kopplade till den konverterade avbildningen.

Välj resurs gruppen för den virtuella datorn och välj sedan **ta bort**. Bekräfta resurs gruppens namn för att slutföra borttagningen av resurserna.

Om du vill ta bort Fortanix-kontot för konfidentiell data behandling som du har skapat går du till [sidan konton](https://ccm.fortanix.com/accounts) i Fortanix-hanteraren för konfidentiell bearbetning. Hovra över det konto som du vill ta bort. Välj de lodräta svarta punkterna i det övre högra hörnet och välj **ta bort konto**.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="Skärm bild som visar hur du tar bort kontot.":::

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Fortanix-verktyg för att konvertera program avbildningen så att den körs ovanpå en konfidentiell dator. Mer information om konfidentiell dator användning av virtuella datorer i Azure finns i [lösningar på Virtual Machines](virtual-machine-solutions.md).

Mer information om Azures tjänster för konfidentiell behandling finns i [Översikt över Azure konfidentiell beräkning](overview.md).

Lär dig hur du utför liknande uppgifter med andra tredjepartsprogram i Azure, t. ex. [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) och [Scone](https://sconedocs.github.io).