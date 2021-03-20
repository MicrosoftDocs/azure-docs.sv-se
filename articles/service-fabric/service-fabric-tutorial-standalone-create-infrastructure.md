---
title: Skapa en infrastruktur för ett kluster på AWS
description: I den här självstudien lär du dig att konfigurera AWS-infrastrukturen till att köra ett Service Fabric-kluster.
ms.topic: tutorial
ms.date: 05/11/2018
ms.custom: mvc
ms.openlocfilehash: c7a18b0dcdc04bdf66ac4b36ce7376ee018eb238
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842911"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Självstudie: Skapa en AWS-infrastruktur som är värd för ett Service Fabric-kluster

Med fristående Service Fabric-kluster kan du välja en egen miljö och skapa ett kluster som en del av metoden ”valfritt operativsystem, valfritt moln” som används i Service Fabric. I den här självstudieserien skapar du ett fristående kluster som hanteras av AWS och installerar ett program i det.

Den här självstudien ingår i en serie. I den här artikeln genererar du de AWS-resurser som krävs för att vara värd för ditt fristående kluster i Service Fabric. I kommande artiklar måste du installera det fristående Service Fabric-paketet, installera ett exempelprogram i klustret och slutligen rensa klustret.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa en uppsättning med EC2-instanser
> * Ändra säkerhetsgruppen
> * Logga in på en av instanserna
> * Förbereda instansen för Service Fabric

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett AWS-konto för att kunna slutföra den här självstudien.  Om du inte redan har ett konto kan du gå till [AWS-konsolen](https://aws.amazon.com/) för att skapa ett.

## <a name="create-ec2-instances"></a>Skapa EC2-instanser

Logga in på AWS-konsolen > ange **EC2** i rutan Sök > **EC2 virtuella servrar i molnet**

![AWS-konsolsökning][aws-console]

Välj **Starta instans**. På nästa skärm väljer du **Välj** bredvid Microsoft Windows Server 2016 Base.

![Val av EC2-instans][aws-ec2instance]

Välj **t2.medium** och välj sedan **Nästa: Konfigurera instansinformation**. På nästa skärm ändrar du antalet instanser till `3` och väljer **Avancerad information** för att expandera avsnittet.

För att du ska kunna ansluta dina virtuella datorer tillsammans i Service Fabric, måste de virtuella datorer som är värd för din infrastruktur ha samma autentiseringsuppgifter.  Det finns två vanliga metoder för att få likadana autentiseringsuppgifter: Anslut dem till samma domän, eller ange samma administratörslösenord på varje virtuell dator.  I den här självstudien använder du ett användardataskript till att ange att EC2-instanserna ska ha samma lösenord.  I en produktionsmiljö är det säkrare att ansluta värdarna till en Windows-domän.

Ange följande skript i fältet för användardata i konsolen:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

När du har angett PowerShell-skriptet väljer du **Granska och starta**

![Granska och starta EC2][aws-ec2configure2]

På granskningsskärmen väljer du **Starta**.  Ändra sedan listrutan till **Fortsätt utan nyckelpar** och markera kryssrutan som visar att du känner till lösenordet.

![Välja AWS-nyckelpar][aws-keypair]

Välj slutligen **Starta instanser** och sedan **Visa instanser**.  Du har nu skapat grunden för ditt Service Fabric-kluster. Nu måste du lägga till några slutliga konfigurationer i själva instanserna för att förbereda dem för Service Fabric-konfigurationen.

## <a name="modify-the-security-group"></a>Ändra säkerhetsgruppen

Service Fabric kräver att ett antal portar är öppna mellan värdarna i klustret. Välj en av de instanser som du skapade för att öppna dessa portar i AWS-infrastrukturen. Välj sedan namnet på säkerhetsgruppen, till exempel **launch-wizard-1**. Nu väljer du fliken **Inkommande**.

För att undvika att portarna öppnas för alla, kan du i stället öppna dem enbart för värdarna i samma säkerhetsgrupp. Anteckna säkerhetsgruppens ID. I detta exempel är det **sg-c4fb1eba**.  Välj sedan **Redigera**.

Lägg sedan till fyra regler i säkerhetsgruppen för tjänstens beroenden, och därefter tre i för själva Service Fabric. Den första regeln är att tillåta ICMP-trafik för grundläggande anslutningskontroller. De andra reglerna öppnar de portar som krävs för att aktivera SMB och Remote Registry.

För den första regeln väljer du **Lägg till regel** och sedan **Alla ICMP – IPv4** i listrutan. Välj inmatningsrutan bredvid den anpassade och ange ditt säkerhetsgrupps-ID enligt ovan.

För de tre sista beroendena följer du en liknande process.  Välj **Lägg till regel**. I listrutan väljer du **Anpassad TCP-regel** och i portintervallet anger du antingen `135`, `137-139` eller `445` för varje regel. I källrutan anger du slutligen ditt säkerhetsgrupp-ID.

![Portar för säkerhetsgruppen][aws-ec2securityports]

Nu när beroendeportarna är öppna, måste du göra samma sak för de portar som Service Fabric själv använder för kommunikation. Välj **Lägg till regel**. I listrutan väljer du **Anpassad TCP-regel** och i portintervallet anger du `20001-20031` för säkerhetsgruppen i källrutan.

Lägg sedan till en regel för det tillfälliga portintervallet.  Välj **Lägg till regel**. I listrutan väljer du **Anpassad TCP-regel** och i portintervallet anger du `20606-20861`. I källrutan anger du slutligen ditt säkerhetsgrupp-ID.

I de sista två reglerna för Service Fabric öppnar du det för alla, så att du kan hantera ditt Service Fabric-kluster från din egna dator. Välj **Lägg till regel**. I listrutan väljer du **Anpassad TCP-regel** och i portintervallet anger du `19000-19003` eller `19080-19081` och ändrar sedan listrutan Källa till Överallt.

Slutligen öppnar du port 8080 så att du kan se programmet när det distribueras. Välj **Lägg till regel**. I listrutan väljer du **Anpassad TCP-regel** och i portintervallet anger du `8080` och ändrar sedan listrutan Källa till Överallt.

Alla regler har nu angetts. Välj **Spara**.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Anslut till en instans och kontrollera anslutningen

På fliken Säkerhetsgrupp väljer du **Instanser** i den vänstra menyn.  Välj de instanser som du har skapat och anteckna deras privata IP-adresser, eftersom exemplen nedan använder `172.31.21.141` och `172.31.20.163`.

När du har alla IP-adresser väljer du en av instanserna som du vill ansluta till. Högerklicka på instansen och välj **Anslut**.  Härifrån kan du ladda ned RDP-filen för den här specifika instansen.  Välj alternativet för att **ladda ned en fjärrskrivbordsfil** och öppna sedan filen som laddades ned för att upprätta en anslutning till fjärrskrivbord (RDP) för den här instansen.  När du uppmanas till det anger du ditt lösenord `serv1ceF@bricP@ssword`.

![Ladda ned en fjärrskrivbordsfil][aws-rdp]

När du har anslutit till din instans kontrollerar du att du har en anslutning mellan dem och att du kan dela filer.  Du har samlat in IP-adresser för alla instanser. Välj en som du för närvarande inte är ansluten till. Gå till **Start**, ange `cmd` och välj **Kommandotolk**.

I dessa exempel har RDP-anslutningen upprättats till följande IP-adress: 172.31.21.141. Alla anslutningstest utförs till den andra IP-adressen: 172.31.20.163.

Om du vill kontrollera att den grundläggande anslutningen fungerar, använder du ping-kommandot.

```
ping 172.31.20.163
```

Om dina utdata ser ut så här `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` upprepade fyra gånger, fungerar anslutningen mellan instanserna.  Nu ska du kontrollera att din SMB-delning fungerar med följande kommando:

```
net use * \\172.31.20.163\c$
```

`Drive Z: is now connected to \\172.31.20.163\c$.` ska returneras som utdata.

## <a name="prep-instances-for-service-fabric"></a>Förbereda instanserna för Service Fabric

Om du har skapat detta från grunden, behöver du utföra några fler steg.  Du måste kontrollera att fjärregistret kördes, aktivera SMB och öppna portarna som krävs för SMB och fjärregistret.

För att göra det enklare bäddade du in allt detta när du startade instanserna med ditt användardataskript.

Detta är det PowerShell-kommando som du använde för att aktivera SMB:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Om du vill öppna portarna i brandväggen använder du följande PowerShell-kommando:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Nästa steg

I del ett i serien lärde du dig att starta tre EC2-instanser och du konfigurerade dem för Service Fabric-installationen:

> [!div class="checklist"]
> * Skapa en uppsättning med EC2-instanser
> * Ändra säkerhetsgruppen
> * Logga in på en av instanserna
> * Förbereda instansen för Service Fabric

Gå vidare till avsnitt två i serien för att konfigurera Service Fabric på klustret.

> [!div class="nextstepaction"]
> [Installera Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png