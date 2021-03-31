---
title: Skapa en infrastruktur för ett kluster på virtuella Azure-datorer
description: I den här självstudien får du lära dig hur du konfigurerar Azure VM-infrastrukturen för att köra ett Service Fabric kluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: e9f62f944fff331bcf2dad1b380161e563614219
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90561848"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Självstudie: skapa en Azure VM-infrastruktur som värd för ett Service Fabric kluster

Med fristående Service Fabric-kluster kan du välja en egen miljö och skapa ett kluster som en del av metoden ”valfritt operativsystem, valfritt moln” som används i Service Fabric. I den här själv studie serien skapar du ett fristående kluster som finns på virtuella Azure-datorer och installerar ett program på den.

Den här självstudien ingår i en serie. I den här artikeln skapar du de Azure VM-resurser som krävs för att vara värd för ditt fristående kluster med Service Fabric. I kommande artiklar måste du installera det fristående Service Fabric-paketet, installera ett exempelprogram i klustret och slutligen rensa klustret.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa en uppsättning AzureVM-instanser
> * Ändra säkerhetsgruppen
> * Logga in på någon av instanserna
> * Förbereda instansen för Service Fabric

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.  Om du inte redan har ett konto går du till [Azure Portal](https://portal.azure.com) för att skapa ett.

## <a name="create-azure-virtual-machine-instances"></a>Skapa instanser av virtuella Azure-datorer

1. Logga in på Azure Portal och välj **virtuella datorer** (inte Virtual Machines (klassisk)).

   ![Azure Portal VM][az-console]

2. Välj knappen **Lägg till** som öppnar formuläret **skapa en virtuell dator** .

3. På fliken **grundläggande** , se till att välja den prenumeration och resurs grupp du vill använda (med en ny resurs grupp rekommenderas).

4. Ändra **avbildnings** typen till **Windows Server 2016 Data Center**. 
 
5. Ändra instans **storleken** till **standard DS2 v2**. Ange ett administratörs **användar namn** och **lösen ord** och notera vad de är.

6. Lämna **reglerna för inkommande port** blockerade för tillfället. de kommer att konfigureras i nästa avsnitt.

7. På fliken **nätverk** skapar du en ny **Virtual Network** och noterar dess namn.

8. Ställ sedan in nätverkskortets **nätverks säkerhets grupp** på **Avancerat**. Skapa en ny säkerhets grupp med namnet och skapa följande regler för att tillåta TCP-trafik från vilken källa som helst:

   ![Skärm bild som visar hur du skapar regler för att tillåta inkommande TCP-trafik.][sf-inbound]

   * Port `3389` , för RDP och ICMP (grundläggande anslutning).
   * Portar `19000-19003` , för Service Fabric.
   * Portar `19080-19081` , för Service Fabric.
   * Port `8080` för webb läsar begär Anden.

   > [!TIP]
   > För att du ska kunna ansluta dina virtuella datorer tillsammans i Service Fabric, måste de virtuella datorer som är värd för din infrastruktur ha samma autentiseringsuppgifter.  Det finns två vanliga metoder för att få likadana autentiseringsuppgifter: Anslut dem till samma domän, eller ange samma administratörslösenord på varje virtuell dator. Lyckligt vis tillåter Azure att alla virtuella datorer i samma **virtuella nätverk** är lätta att ansluta, så vi ska se till att ha alla våra instanser i samma nätverk.

9. Lägg till en annan regel. Ange att källan ska vara **service tag** och ange käll tjänst tag gen till **VirtualNetwork**. Service Fabric kräver att följande portar är öppna för kommunikation i klustret: 135137-139, 445, 20001-20031, 20606-20861.

   ![Skärm bild som visar hur du skapar regler för att tillåta TCP-trafik för ett kluster.][vnet-inbound]

10. Resten av alternativen är acceptabla i sitt standard tillstånd. Granska dem om du vill och starta sedan den virtuella datorn.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Skapa fler instanser för ditt Service Fabric-kluster

Starta ytterligare två **Virtual Machines**, se till att behålla samma inställningar som beskrivs i föregående avsnitt. I synnerhet behåller du samma administratörs användar namn och lösen ord. **Nätverks säkerhets gruppen** **Virtual Network** och NIC bör inte återskapas. Välj de som du redan har skapat från List menyn. Det kan ta några minuter innan varje instans kan distribueras.

## <a name="connect-to-your-instances"></a>Anslut till dina instanser

1. Välj en av instanserna från avsnittet **virtuell dator** .

2. På fliken **Översikt** noterar du den *privata* IP-adressen. Klicka sedan på **Anslut**.

3. På fliken **RDP** noterar du att vi använder den offentliga IP-adressen och port 3389, som vi har öppnat tidigare. Ladda ned RDP-filen.
 
4. Öppna RDP-filen och ange användar namn och lösen ord som du angav i VM-installationen när du uppmanas att göra det.

5. När du är ansluten till en instans måste du verifiera att fjärrregistret kördes, Aktivera SMB och öppna de nödvändiga portarna för SMB och fjär registret.

   Detta är PowerShell-kommandot för att aktivera SMB:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Om du vill öppna portarna i brandväggen använder du följande PowerShell-kommando:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Upprepa den här processen för andra instanser och notera de privata IP-adresserna.

## <a name="verify-your-settings"></a>Kontrollera inställningarna

1. Om du vill verifiera den grundläggande anslutningen ansluter du till en av de virtuella datorerna med hjälp av RDP.

2. Öppna **kommando tolken** inifrån den virtuella datorn och Använd Ping-kommandot för att ansluta från en virtuell dator till en annan, och ersätt IP-adressen nedan med en av de privata IP-adresser som du antecknade tidigare (inte IP-adressen för den virtuella datorn som du är ansluten till redan).

   ```
   ping 172.31.20.163
   ```

   Om dina utdata ser ut så här `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` upprepade fyra gånger, fungerar anslutningen mellan instanserna.

3. Nu ska du kontrollera att din SMB-delning fungerar med följande kommando:

   ```
   net use * \\172.31.20.163\c$
   ```

   `Drive Z: is now connected to \\172.31.20.163\c$.` ska returneras som utdata.


   Nu är dina instanser korrekt för beredda för Service Fabric.

## <a name="next-steps"></a>Nästa steg

I del ett av serien har du lärt dig hur du startar tre Azure VM-instanser och får dem konfigurerade för den Service Fabric installationen:

> [!div class="checklist"]
> * Skapa en uppsättning virtuella Azure-instanser
> * Ändra säkerhetsgruppen
> * Logga in på någon av instanserna
> * Förbereda instansen för Service Fabric

Gå vidare till avsnitt två i serien för att konfigurera Service Fabric på klustret.

> [!div class="nextstepaction"]
> [Installera Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
