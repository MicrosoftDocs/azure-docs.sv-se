---
title: Distribuera aver vFXT för Azure
description: Lär dig hur du använder distributions guiden som finns på Azure Marketplace för att distribuera ett kluster med AVERT vFXT för Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 31d12466186bb7f66197218fbb9675888a35fef3
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272781"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuera vFXT-klustret

Den här proceduren vägleder dig genom att använda distributions guiden som finns på Azure Marketplace. Guiden distribuerar automatiskt klustret med hjälp av en Azure Resource Manager mall. När du har angett parametrarna i formuläret och klickar på **skapa**slutförs automatiskt följande uppgifter i Azure:

* Skapar kluster styrenheten, som är en grundläggande virtuell dator som innehåller den program vara som krävs för att distribuera och hantera klustret.
* Konfigurerar resurs grupp och virtuell nätverks infrastruktur, inklusive att skapa nya element.
* Skapar de virtuella klusternoderna och konfigurerar dem som ett AVERT-kluster.
* Om det begärs skapas en ny Azure Blob-behållare och konfigureras som ett kluster kärnor.

När du har gått igenom anvisningarna i det här dokumentet har du ett virtuellt nätverk, ett undernät, en kluster styrenhet och ett vFXT-kluster som visas i följande diagram. Det här diagrammet visar de valfria Azure Blob core-filer som innehåller en ny Blob Storage-behållare (i ett nytt lagrings konto, inte visas) och en tjänst slut punkt för Microsoft-lagring i under nätet.

![diagram som visar tre koncentriska rektanglar med AVERT kluster komponenter. Den yttre rektangeln har etiketten resurs grupp och innehåller en sexhörning med etiketten Blob Storage (valfritt). Nästa rektangel i har etiketten "virtuellt nätverk: 10.0.0.0/16" och innehåller inga unika komponenter. Den innersta rektangeln har etiketten "undernät: 10.0.0.0/24" och innehåller en virtuell dator med namnet "Cluster Controller", en stack med tre virtuella datorer med namnet "vFXT Nodes (vFXT Cluster)" och en sexhörning med etiketten "tjänstens slut punkt". Det finns en pil som ansluter tjänst slut punkten (som finns i under nätet) och blob-lagringen (som ligger utanför under nätet och VNET i resurs gruppen). Pilen passerar genom under nätet och virtuella nätverks gränser.](media/avere-vfxt-deployment.png)

Innan du använder mallen för att skapa måste du kontrol lera att du har åtgärdat följande krav:  

* [Ny prenumeration](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Ägar behörigheter för prenumeration](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Kvot för vFXT-klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Storage Service-slutpunkt (vid behov)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – krävs för distributioner som använder ett befintligt virtuellt nätverk och skapar Blob Storage

Mer information om steg och planering för kluster distribution finns [i planera ditt AVERT vFXT-system](avere-vfxt-deploy-plan.md) och [distributions översikt](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Skapa ett AVERT-vFXT för Azure

Öppna mallen skapa i Azure Portal genom att söka efter aver och välja "aver vFXT for Azure ARM-mall".

![Webbläsarfönstret som visar Azure Portal med bröd Crumbs "ny > Marketplace > allt". På sidan allt innehåller Sök fältet termen "AVERT" och det andra resultatet, "aver vFXT for Azure ARM-mall" visas i rött för att markera det.](media/avere-vfxt-template-choose.png)

När du har läst informationen på sidan aver vFXT för Azure ARM-mall klickar du på knappen **skapa** för att starta.

![Azure Marketplace med den första sidan i distributions mal len som visar](media/avere-vfxt-deploy-first.png)

Mallen är uppdelad i fyra steg – två informations insamlings sidor, plus validerings-och bekräftelse steg.

* Sida en samlar in inställningar för den virtuella kluster styrenheten.
* Sida två samlar in parametrar för att skapa klustret och ytterligare resurser, t. ex. undernät och lagring.
* Sidan tre sammanfattar dina val och validerar konfigurationen.
* Sida fyra förklarar program villkor och gör att du kan starta processen för att skapa klustret.

## <a name="page-one-parameters---cluster-controller-information"></a>Sidan med en parameter – information om kluster styrenhet

Den första sidan i distributions mal len fokuserar på kluster styrenheten.

![Första sidan i distributions mal len](media/avere-vfxt-deploy-1.png)

Fyll i följande information:

* **Kluster styrenhets namn** – ange namnet på den virtuella kluster styrenheten.

* **Användar namn för kontrollant** – ange rot användar namnet för den virtuella kluster styrenheten.

* **Autentiseringstyp** – Välj antingen lösen ord eller autentisering med offentlig SSH-nyckel för att ansluta till kontrollanten. Metoden för offentlig SSH-nyckel rekommenderas. Läs om [hur du skapar och använder SSH-nycklar](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) om du behöver hjälp.

* **Lösen ord** eller **Offentlig SSH-nyckel** – beroende på vilken autentiseringstyp du valde måste du ange en offentlig RSA-nyckel eller ett lösen ord i nästa fält. Den här autentiseringsuppgiften används med användar namnet som angavs tidigare.

* **Prenumeration** – Välj prenumerationen för aver-vFXT.

* **Resurs grupp** – Välj en befintlig tom resurs grupp för det Avera vFXT-klustret eller klicka på Skapa ny och ange ett nytt resurs grupps namn.

* **Plats** – Välj Azure-platsen för ditt kluster och dina resurser.

Klicka på **OK** när du är färdig.

> [!NOTE]
> Om du vill att kluster styrenheten ska ha en offentlig IP-adress skapar du ett nytt virtuellt nätverk för klustret i stället för att välja ett befintligt nätverk. Den här inställningen finns på sidan två.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Page två parametrar-vFXT Cluster information

På den andra sidan i distributions mal len kan du ange kluster storlek, nodtyp, cachestorlek och lagrings parametrar bland andra inställningar.

![Andra sidan i distributions mal len](media/avere-vfxt-deploy-2.png)

* **AVERT vFXT antal klusternoder** – Välj antalet noder i klustret. Minimivärdet är tre noder och det högsta värdet är 12.

* **Kluster administrations lösen ord** – skapa lösen ordet för kluster administration. Det här lösen ordet används med användar namnet ```admin``` för att logga in på kontroll panelen kluster, där du kan övervaka klustret och konfigurera kluster inställningar.

* **Aver vFXT-kluster namn** – ge klustret ett unikt namn.

* **Storlek** – det här avsnittet visar den VM-typ som ska användas för klusternoderna. Även om det bara finns ett rekommenderat alternativ öppnar länken **ändra storlek** en tabell med information om den här instans typen och en länk till en pris kalkylator.

* **Cachestorlek per nod** – klustrets cacheminne sprids över klusternoderna, så den totala cachestorleken i ditt AVERT vFXT-kluster kommer att vara den här storleken multiplicerat med antalet noder.

  Rekommenderad konfiguration: Använd 4 TB per nod för Standard_E32s_v3 noder.

* **Virtuellt nätverk** – definiera ett nytt virtuellt nätverk för att House klustret, eller Välj ett befintligt nätverk som uppfyller de krav som beskrivs i [planera ditt AVERT vFXT-system](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Om du skapar ett nytt virtuellt nätverk får kluster styrenheten en offentlig IP-adress så att du kan komma åt det nya privata nätverket. Om du väljer ett befintligt virtuellt nätverk konfigureras kluster styrenheten utan en offentlig IP-adress.
  >
  > En offentligt synlig IP-adress på kluster styrenheten ger enklare åtkomst till vFXT-klustret, men skapar en liten säkerhets risk.
  >* Med en offentlig IP-adress på kluster styrenheten kan du använda den som en hopp värd för att ansluta till det vFXT-kluster som ligger utanför det privata under nätet.
  >* Om du inte har en offentlig IP-adress på styrenheten behöver du en annan hopp värd, en VPN-anslutning eller ExpressRoute för att få åtkomst till klustret. Använd till exempel ett befintligt virtuellt nätverk som redan har en konfigurerad VPN-anslutning.
  >* Om du skapar en kontrollant med en offentlig IP-adress bör du skydda den virtuella datorns kontrollant med en nätverks säkerhets grupp. Som standard skapar vFXT för Azure-distribution en nätverks säkerhets grupp som begränsar inkommande åtkomst till port 22 för kontrollanter med offentliga IP-adresser. Du kan skydda systemet ytterligare genom att låsa åtkomsten till ditt utbud av IP-adresser – det vill säga bara tillåta anslutningar från datorer som du vill använda för kluster åtkomst.

  Ett nytt virtuellt nätverk konfigureras också med en lagrings tjänst slut punkt för Azure Blob Storage och med nätverks åtkomst kontroll låst för att endast tillåta IP-adresser från klustrets undernät.

* **Undernät** – Välj ett undernät eller skapa ett nytt.

* **Skapa och Använd Blob Storage** – Välj **Sant** om du vill skapa en ny Azure Blob-behållare och konfigurera den som backend-lagring för det nya AVERT vFXT-klustret. Det här alternativet skapar också ett nytt lagrings konto i klustrets resurs grupp och skapar en Microsoft Storage Service-slutpunkt i kluster under nätet.
  
  Om du anger ett befintligt virtuellt nätverk måste det ha en lagrings tjänst slut punkt innan du skapar klustret. (Mer information finns i [planera ditt AVERT vFXT-system](avere-vfxt-deploy-plan.md).)

  Ange det här fältet till **falskt** om du inte vill skapa en ny behållare. I så fall måste du ansluta och konfigurera lagring när du har skapat klustret. Läs [Konfigurera lagring](avere-vfxt-add-storage.md) för instruktioner.

* **(Nytt) lagrings konto** – om du skapar en ny Azure Blob-behållare anger du ett namn för det nya lagrings kontot.

## <a name="validation-and-purchase"></a>Validering och Köp

Sidan tre sammanfattar konfigurationen och validerar parametrarna. När verifieringen har slutförts kontrollerar du sammanfattningen och klickar på **OK** .

> [!TIP]
> Du kan spara det här klustrets inställningar för att skapa genom att klicka på länken **Hämta mall och parametrar** bredvid knappen **OK** . Den här informationen kan vara till hjälp om du behöver skapa ett liknande kluster senare, till exempel för att skapa ett ersättnings kluster i ett haveri beredskaps scenario. (Läs mer om att läsa mer om [haveri beredskap](disaster-recovery.md) .)

![Tredje sidan i distributions mal len-verifiering](media/avere-vfxt-deploy-3.png)

Sidan fyra innehåller användnings villkoren och länkar till sekretess-och pris information.

Ange en kontakt information som saknas och klicka sedan på knappen **skapa** för att acceptera villkoren och skapa ett AVERT-VFXT för Azure-kluster.

![Fjärde sidan i distributions mal len – villkor, knappen Skapa](media/avere-vfxt-deploy-4.png)

Kluster distributionen tar 15-20 minuter.

## <a name="gather-template-output"></a>Samla in mallens utdata

När vFXT-mallen för att skapa klustret har slutförts, kommer viktig information om det nya klustret att matas ut.

> [!TIP]
> Se till att kopiera **hanterings-IP-adressen** från mallens utdata. Du behöver den här adressen för att administrera klustret.

Så här hittar du informationen:

1. Gå till resurs gruppen för kluster styrenheten.

1. På vänster sida klickar du på **distributioner**och sedan **Microsoft-AVERT. vfxt-Template**.

   ![Sidan resurs grupp Portal med distributioner markerat till vänster och Microsoft-AVERT. vfxt – mall som visas i en tabell under distributions namn](media/avere-vfxt-outputs-deployments.png)

1. På vänster sida klickar du på **utdata**. Kopiera värdena i vart och ett av fälten.

   ![Sidan utdata visar SSHSTRING, RESOURCE_GROUP, plats, NETWORK_RESOURCE_GROUP, nätverk, UNDERNÄT, SUBNET_ID, VSERVER_IPs och MGMT_IP värden i fält till höger om etiketterna](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Nästa steg

Nu när klustret körs och du vet dess hanterings-IP-adress [ansluter du till kluster konfigurations verktyget](avere-vfxt-cluster-gui.md).

Använd konfigurations gränssnittet för att anpassa klustret, inklusive dessa installations uppgifter:

* [Aktivera stöd](avere-vfxt-enable-support.md)
* [Lägg till lagring](avere-vfxt-add-storage.md) (vid behov)
