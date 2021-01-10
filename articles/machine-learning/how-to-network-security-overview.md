---
title: Översikt över virtuella nätverks isolering och säkerhet
titleSuffix: Azure Machine Learning
description: Använd en isolerad Azure-Virtual Network med Azure Machine Learning för att skydda arbets ytans resurser och beräknings miljöer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: 664264f2cd810f232b967f5af78ba3d522f0a41f
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060018"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Översikt över virtuella nätverks isolering och sekretess

I den här artikeln får du lära dig hur du använder virtuella nätverk (virtuella nätverk) för att skydda nätverkskommunikation i Azure Machine Learning. I den här artikeln används ett exempel scenario för att visa hur du konfigurerar ett fullständigt virtuellt nätverk.

Den här artikeln är en del av en serie i fem delar som vägleder dig genom att skydda ett Azure Machine Learning-arbetsflöde. Vi rekommenderar starkt att du läser igenom den här översikts artikeln för att förstå begreppen först. 

Här följer de andra artiklarna i den här serien:

**1. VNet-översikt**  >  [2. Skydda arbets ytan](how-to-secure-workspace-vnet.md)  >  [3. Skydda inlärnings miljö](how-to-secure-training-vnet.md)  >  [4. Skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)  >  [5. Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med följande avsnitt:
+ [Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md)
+ [IP-nätverk](../virtual-network/public-ip-addresses.md)
+ [Azure Private Link](how-to-configure-private-link.md)
+ [Nätverks säkerhets grupper (NSG)](../virtual-network/network-security-groups-overview.md)
+ [Nätverks brand väggar](../firewall/overview.md)

## <a name="example-scenario"></a>Exempelscenario

I det här avsnittet får du lära dig hur ett vanligt nätverks scenario är konfigurerat för att säkra Azure Machine Learning kommunikation med privata IP-adresser.

I tabellen nedan jämförs hur tjänster kommer åt olika delar av ett Azure Machine Learning nätverk både med ett VNet och utan ett VNet.

| Scenario | Arbetsyta | Associerade resurser | Beräknings miljö för utbildning | Inferencing beräknings miljö |
|-|-|-|-|-|-|
|**Inget virtuellt nätverk**| Offentlig IP-adress | Offentlig IP-adress | Offentlig IP-adress | Offentlig IP-adress |
|**Skydda resurser i ett virtuellt nätverk**| Privat IP (privat slut punkt) | Offentlig IP (tjänst slut punkt) <br> **eller** <br> Privat IP (privat slut punkt) | Privat IP | Privat IP  | 

* **Arbets yta** – skapa en privat slut punkt från ditt VNet för att ansluta till en privat länk på arbets ytan. Den privata slut punkten ansluter arbets ytan till VNet via flera privata IP-adresser.
* **Associerad resurs** – Använd tjänst slut punkter eller privata slut punkter för att ansluta till arbets ytans resurser som Azure storage, Azure Key Vault och Azure Container Services.
    * **Tjänst slut punkter** tillhandahåller identiteten för ditt virtuella nätverk till Azure-tjänsten. När du har aktiverat tjänstens slut punkter i ditt virtuella nätverk kan du lägga till en regel för virtuella nätverk för att skydda Azure-tjänstens resurser i det virtuella nätverket. Tjänst slut punkter använder offentliga IP-adresser.
    * **Privata slut punkter** är nätverks gränssnitt som på ett säkert sätt ansluter dig till en tjänst som drivs av en privat Azure-länk. Privat slut punkt använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt VNet.
* **Training Compute Access** -Access Training Compute mål som Azure Machine Learning beräknings instans och Azure Machine Learning beräknings kluster på ett säkert sätt med privata IP-adresser. 
* **Inferencing Compute Access** -Access Azure Kubernetes Services (AKS) Compute Clusters med privata IP-adresser.


Följande fem avsnitt visar hur du skyddar nätverks scenariot som beskrivs ovan. För att skydda ditt nätverk måste du:

1. Skydda [**arbets ytan och de associerade resurserna**](#secure-the-workspace-and-associated-resources).
1. Skydda [**inlärnings miljön**](#secure-the-training-environment).
1. Skydda [**inferencing-miljön**](#secure-the-inferencing-environment).
1. Du kan också: [**Aktivera Studio-funktioner**](#optional-enable-studio-functionality).
1. Konfigurera [ **brand Väggs inställningar**](#configure-firewall-settings)

## <a name="secure-the-workspace-and-associated-resources"></a>Skydda arbets ytan och tillhör ande resurser

Använd följande steg för att skydda arbets ytan och associerade resurser. De här stegen gör att tjänsterna kan kommunicera i det virtuella nätverket.

1. Skapa en [privat länk aktive rad arbets yta](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) för att aktivera kommunikation mellan ditt VNet och din arbets yta.
1. Lägg till Azure Key Vault i det virtuella nätverket med en [tjänst slut punkt](../key-vault/general/overview-vnet-service-endpoints.md) eller en [privat slut punkt](../key-vault/general/private-link-service.md). Ange Key Vault till ["Tillåt att betrodda Microsoft-tjänster kringgår den här brand väggen"](how-to-secure-workspace-vnet.md#secure-azure-key-vault).
1. Lägg till Azure Storage-kontot i det virtuella nätverket med en [tjänst slut punkt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) eller en [privat slut punkt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints).
1. [Konfigurera Azure Container Registry att använda en privat slut punkt](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr) och [Aktivera under näts delegering i Azure Container instances](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

![Arkitektur diagram som visar hur arbets ytan och de associerade resurserna kommunicerar med varandra över tjänst slut punkter eller privata slut punkter i ett virtuellt nätverk](./media/how-to-network-security-overview/secure-workspace-resources.png)

Detaljerade anvisningar om hur du utför de här stegen finns i [skydda en Azure Machine Learning-arbetsyta](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Begränsningar

Att skydda arbets ytan och associerade resurser i ett virtuellt nätverk har följande begränsningar:
- Användning av en Azure Machine Learning arbets yta med privat länk är inte tillgänglig i regionerna Azure Government eller Azure Kina 21Vianet.
- Alla resurser måste ligga bakom samma VNet. Undernät i samma VNet tillåts dock.

## <a name="secure-the-training-environment"></a>Skydda inlärnings miljön

I det här avsnittet får du lära dig hur du skyddar utbildnings miljön i Azure Machine Learning. Du lär dig också hur Azure Machine Learning slutför ett utbildnings jobb för att förstå hur nätverks konfigurationerna fungerar tillsammans.

Använd följande steg för att skydda inlärnings miljön:

1. Skapa ett Azure Machine Learning [beräknings instans och dator kluster i det virtuella nätverket](how-to-secure-training-vnet.md#compute-instance) för att köra utbildnings jobbet.
1. [Tillåt inkommande kommunikation från Azure Batch tjänsten](how-to-secure-training-vnet.md#mlcports) så att batch-tjänsten kan skicka jobb till dina beräknings resurser. 

![Arkitektur diagram som visar hur du skyddar hanterade beräknings kluster och instanser](./media/how-to-network-security-overview/secure-training-environment.png)

Detaljerade anvisningar om hur du utför de här stegen finns i [skydda en tränings miljö](how-to-secure-training-vnet.md). 

### <a name="example-training-job-submission"></a>Exempel på inlämning av utbildnings jobb 

I det här avsnittet får du lära dig Azure Machine Learning på ett säkert sätt kommunicera mellan tjänster för att skicka ett utbildnings jobb. Detta visar hur alla dina konfigurationer arbetar tillsammans för att säkra kommunikationen.

1. Klienten överför utbildnings skript och tränings data till lagrings konton som skyddas med en tjänst eller privat slut punkt.

1. Klienten skickar ett utbildnings jobb till Azure Machine Learning arbets ytan via den privata slut punkten.

1. Azure Batch Services tar emot jobbet från arbets ytan och skickar utbildnings jobbet till beräknings miljön via den offentliga belastningsutjämnaren som är etablerad med beräknings resursen. 

1. Beräknings resursen tar emot jobbet och påbörjar träningen. Med beräknings resurserna får du till gång till säkra lagrings konton för att ladda ned inlärnings filer och överföra utdata. 

![Arkitektur diagram som visar hur ett Azure Machine Learning utbildnings jobb skickas när ett VNet används](./media/how-to-network-security-overview/secure-training-job-submission.png)


### <a name="limitations"></a>Begränsningar

- Azure Compute instance och Azure Compute Clusters måste finnas i samma VNet, region och prenumeration som arbets ytan och dess associerade resurser. 

## <a name="secure-the-inferencing-environment"></a>Skydda inferencing-miljön

I det här avsnittet får du lära dig vilka alternativ som är tillgängliga för att skydda en inferencing-miljö. Vi rekommenderar att du använder Azure Kubernetes Services (AKS)-kluster för storskaliga produktions distributioner.

Du har två alternativ för AKS-kluster i ett virtuellt nätverk:

- Distribuera eller koppla ett standard kluster för AKS till ditt VNet.
- Koppla ett privat AKS-kluster till ditt VNet.

**Standard AKS-kluster** har ett kontroll plan med offentliga IP-adresser. Du kan lägga till ett standard kluster för AKS i ditt VNet under distributionen eller ansluta ett kluster när det har skapats.

**Privata AKS-kluster** har ett kontroll plan som bara kan nås via privata IP-adresser. Privata AKS-kluster måste anslutas efter att klustret har skapats.

Detaljerade anvisningar om hur du lägger till standard-och privata kluster finns i [skydda en inferencing-miljö](how-to-secure-inferencing-vnet.md). 

I följande nätverks diagram visas en skyddad Azure Machine Learning arbets yta med ett privat AKS-kluster som är kopplat till det virtuella nätverket.

![Arkitektur diagram som visar hur du ansluter ett privat AKS-kluster till det virtuella nätverket. Kontroll planet för AKS placeras utanför kundens VNet](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Begränsningar
- AKS-kluster måste tillhöra samma VNet som arbets ytan och dess associerade resurser. 

## <a name="optional-enable-studio-functionality"></a>Valfritt: Aktivera Studio-funktioner

[Skydda arbets ytan](#secure-the-workspace-and-associated-resources)  >  [Skydda inlärnings miljön](#secure-the-training-environment)  >  [Skydda inferencing-miljön](#secure-the-inferencing-environment)  >  **Aktivera Studio-funktioner**  >  [Konfigurera brand Väggs inställningar](#configure-firewall-settings)

Om ditt lagrings utrymme finns i ett VNet måste du först utföra ytterligare konfigurations steg för att aktivera fullständig funktionalitet i [Studio](overview-what-is-machine-learning-studio.md). Som standard är följande funktion inaktive rad:

* Förhandsgranska data i Studio.
* Visualisera data i designern.
* Distribuera en modell i designern.
* Skicka ett AutoML experiment.
* Starta ett etikettande projekt.

Om du vill aktivera fullständig Studio-funktionalitet inuti ett VNet, se [använd Azure Machine Learning Studio i ett virtuellt nätverk](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio). Studio stöder lagrings konton med hjälp av antingen tjänst slut punkter eller privata slut punkter.

### <a name="limitations"></a>Begränsningar

[Ml-stöd för data etiketter](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling) har inte stöd för standard lagrings konton som skyddas bakom ett virtuellt nätverk. Du måste använda ett lagrings konto som inte är standard för ML assisterad data etiketter. Observera att lagrings kontot som inte är standard kan skyddas bakom det virtuella nätverket. 

## <a name="configure-firewall-settings"></a>Konfigurera brandväggsinställningar

Konfigurera brand väggen för att kontrol lera åtkomsten till dina Azure Machine Learning arbets ytans resurser och det offentliga Internet. Även om vi rekommenderar Azure-brandvägg bör du kunna använda andra brand Väggs produkter för att skydda nätverket. Om du har frågor om hur du tillåter kommunikation via brand väggen kan du läsa dokumentationen för den brand vägg som du använder.

Mer information om brand Väggs inställningar finns i [använda arbets ytan bakom en brand vägg](how-to-access-azureml-behind-firewall.md).

## <a name="custom-dns"></a>Anpassad DNS

Om du behöver använda en anpassad DNS-lösning för ditt virtuella nätverk måste du lägga till värd poster för din arbets yta.

Mer information om de domän namn och IP-adresser som krävs finns i [så här använder du en arbets yta med en anpassad DNS-Server](how-to-custom-dns.md).

## <a name="next-steps"></a>Nästa steg

Den här artikeln är en del av en serie virtuella nätverks serier i fyra delar. Se resten av artiklarna för att lära dig hur du skyddar ett virtuellt nätverk:

* [Del 2: översikt över virtuella nätverk](how-to-secure-workspace-vnet.md)
* [Del 3: skydda tränings miljön](how-to-secure-training-vnet.md)
* [Del 4: skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)
* [Del 5: Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)