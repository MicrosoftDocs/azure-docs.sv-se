---
title: Azure närhets placerings grupper för SAP-program | Microsoft Docs
description: Beskriver distributions scenarier i SAP med placerings grupper för Azure närhet
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 924fdef475c43023c69c3006db19cd9a5aa15349
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669661"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program
SAP-program som baseras på SAP NetWeaver-eller SAP S/4HANA-arkitekturen är känsliga för nätverks fördröjning mellan SAP-programnivån och SAP-databasnivå. Den här känsligheten är resultatet av det mesta av affärs logiken som körs i program skiktet. Eftersom SAP-programlagret kör affärs logiken, utfärdar den frågor till databas nivån med hög frekvens, med en hastighet av tusentals eller flera tusen tusen per sekund. I de flesta fall är arten av frågorna enkel. De kan ofta köras på databas nivå i 500 mikrosekunder eller mindre.

Den tid som ägnats åt nätverket att skicka en sådan fråga från program nivån till databas nivån och ta emot resultat uppsättningen tillbaka har en stor inverkan på den tid det tar att köra affärs processer. Den här känsligheten för nätverks fördröjning är varför du kanske vill uppnå en viss högsta nätverks fördröjning i SAP-distributions projekt. Se [SAP obs #1100926 – vanliga frågor och svar: nätverks prestanda](https://launchpad.support.sap.com/#/notes/1100926/E) för rikt linjer om hur du klassificerar nätverks fördröjningen.

I många Azure-regioner har antalet Data Center växt. Samtidigt använder kunderna, särskilt för avancerade SAP-system, flera särskilda VM-SKU: er för M-eller Mv2-serien eller HANA-stora instanser. Dessa typer av virtuella Azure-datorer är inte alltid tillgängliga i alla data Center som kompletterar en Azure-region. Dessa fakta kan skapa möjligheter för att optimera nätverks fördröjningen mellan SAP-program skiktet och SAP-DBMS-skiktet.

För att ge dig möjlighet att optimera nätverks fördröjningen erbjuder Azure [närhets placerings grupper](../../co-location.md). Närhets placerings grupper kan användas för att tvinga gruppering av olika VM-typer till ett enda Azure-datacenter för att optimera nätverks fördröjningen mellan de olika typerna av virtuella datorer på bästa möjliga sätt. I processen för att distribuera den första virtuella datorn till en sådan närhets grupp, blir den virtuella datorn kopplad till ett särskilt data Center. I takt med att det här låter ljud visas, introducerar användningen av konstruktionen vissa begränsningar också:

- Du kan inte anta att alla typer av virtuella Azure-datorer är tillgängliga i alla och alla Azure-datacenter. Det innebär att kombinationen av olika typer av virtuella datorer inom en närhets placerings grupp kan begränsas. De här begränsningarna beror på att värd maskin varan som behövs för att köra en viss VM-typ kanske inte finns i data centret som placerings gruppen har distribuerats till
- När du ändrar storlek på delar av de virtuella datorerna som finns inom en närhets placerings grupp, kan du inte automatiskt anta att den nya VM-typen är tillgänglig i samma data Center som de andra virtuella datorerna som ingår i närhets placerings gruppen
- När Azure inaktiverar maskin varan kan den tvinga vissa virtuella datorer av en närhets placerings grupp till ett annat Azure-datacenter. Mer information om det här fallet finns i dokumentet [samplacera resurser för förbättrad svars tid](../../co-location.md#planned-maintenance-and-proximity-placement-groups)  

> [!IMPORTANT]
> Som ett resultat av de potentiella begränsningarna bör närhets placerings grupper användas:
>
> - Endast när det behövs
> - Endast på granularitet av ett enda SAP-system och inte för ett helt system liggande eller ett fullständigt SAP-landskap
> - På ett sätt att behålla de olika VM-typerna och antalet virtuella datorer inom en närhets placerings grupp till ett minimum

Anta att om du distribuerar virtuella datorer genom att ange Tillgänglighetszoner och väljer samma Tillgänglighetszoner måste nätverks fördröjningen mellan de virtuella datorerna vara tillräcklig för att kunna använda SAP-NetWeaver och S/4HANA-system med prestanda och data flöde. Detta antagande är oberoende av faktumet om en viss zon har skapats av ett Data Center eller flera data Center. Den enda anledningen till att använda närhets placerings grupper i zonindelade-distributioner är det fall där du vill allokera distribuerade virtuella datorer i Azures tillgänglighets uppsättningar tillsammans med zonindelade-distribuerade virtuella datorer.


## <a name="what-are-proximity-placement-groups"></a>Vad är placerings grupper för närhet? 
En Azure närhets placerings grupp är en logisk konstruktion. När en närhets placering definieras är den kopplad till en Azure-region och en Azure-resurs grupp. När virtuella datorer distribueras refereras en närhets placerings grupp av:

- Den första virtuella Azure-datorn som distribuerats i data centret. Du kan tänka på den första virtuella datorn som "omfattning VM" som distribueras i ett Data Center baserat på Azure-allokeringsinställningar som slutligen kombineras med användar definitioner för en speciell tillgänglighets zon.
- Alla efterföljande virtuella datorer som har distribuerats som referens till närhets placerings gruppen för att placera alla sedan distribuerade virtuella Azure-datorer i samma data Center som den första virtuella datorn.

> [!NOTE]
> Om det inte finns någon värd maskin vara som är distribuerad och som kan köra en speciell VM-typ i data centret där den första virtuella datorn placerades, kommer distributionen av den begärda VM-typen inte att lyckas. Du får ett fel meddelande.

En enda [Azure-resurs grupp](../../../azure-resource-manager/management/manage-resources-portal.md) kan ha flera olika närhets placerings grupper. Men en närhets placerings grupp kan bara tilldelas till en Azure-resurs grupp.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Närhets placerings grupper med SAP-system som bara använder virtuella Azure-datorer
De flesta SAP NetWeaver-och S/4HANA-system distributioner på Azure använder inte [Hana-stora instanser](./hana-overview-architecture.md). För distributioner som inte använder HANA-stora instanser är det viktigt att ge optimala prestanda mellan SAP-program skiktet och DBMS-nivån. Det gör du genom att definiera en placerings grupp för Azure närhet för systemet.

I de flesta kund distributioner skapar kunder en enda [Azure-resurs grupp](../../../azure-resource-manager/management/manage-resources-portal.md) för SAP-system. I så fall finns det en en-till-en-relation mellan exempelvis resurs gruppen för produktions ERP-systemet och dess närhets placerings grupp. I andra fall ordnar kunderna sina resurs grupper horisontellt och samlar in alla produktions system i en enda resurs grupp. I det här fallet har du en en-till-många-relation mellan din resurs grupp för produktion av SAP-system och flera närhets placerings grupper för din produktion SAP ERP, SAP BW och så vidare.

Undvik att hantera flera SAP-produktions-eller icke-produktionssystem i en enda närhets placerings grupp. Om ett litet antal SAP-system eller SAP-system och vissa omgivande program behöver ha nätverks kommunikation med låg latens, kan du överväga att flytta dessa system till en närhets placerings grupp. Undvik system samlingar eftersom ju fler system som du grupperar i en närhets placerings grupp, desto högre sannolikhet:

- Att du behöver en VM-typ som inte kan köras i det angivna data centret som närhets placerings gruppen har tilldelats till.
- Resurser som inte är vanliga virtuella datorer, t. ex. virtuella datorer i M-serien, kan slutligen bli ouppfyllda när du behöver mer eftersom du lägger till program vara i en närhets placering över tid.

Det här är den perfekta konfigurationen, som du ser ovan:

![Närhets placerings grupper med enbart virtuella Azure-datorer](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

I det här fallet grupperas enkla SAP-system i en enda resurs grupp, med en närhets placerings grupp. Det finns inget beroende av om du använder långsamma skalnings-eller DBMS-konfigurationer.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Närhets placerings grupper och HANA-stora instanser
Om några av dina SAP-system förlitar sig på [Hana-stora instanser](./hana-overview-architecture.md) för program skiktet, kan du uppleva avsevärda förbättringar i nätverks fördröjningen mellan de stora instanser av Hana och virtuella Azure-datorer när du använder de stora instanser av Hana-enheter som distribueras i [revision 4-rader eller-stämplar](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). En förbättring är att de HANA-stora instans enheterna, när de distribueras, distribueras med en närhets placerings grupp. Du kan använda den här närhets gruppen för att distribuera dina virtuella program lager datorer. Detta innebär att de virtuella datorerna distribueras i samma data Center som är värd för dina HANA stora instanser-enheter.

För att avgöra om din enhet av HANA-stora instanser har distribuerats i en revision 4-stämpel eller-rad, kontrollerar du artikeln [Azure Hana stor instanss-kontroll via Azure Portal](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). I översikten över attributen för en enhet med stora instanser av HANA kan du också bestämma namnet på närhets placerings gruppen eftersom den skapades när den stora volymen HANA stor instanserna distribuerades. Namnet som visas i översikten över attribut är namnet på den närhets placerings grupp som du bör distribuera dina virtuella program skikt till.

Jämfört med SAP-system som bara använder virtuella Azure-datorer, har du mindre flexibilitet när du ska bestämma hur många [Azure-resurs grupper](../../../azure-resource-manager/management/manage-resources-portal.md) som ska användas när du använder Hana-stora instanser. Alla instanser av HANA stora instanser av en [Hana-stor instans-klient](./hana-know-terms.md) grupperas i en enda resurs grupp, enligt beskrivningen i [den här artikeln](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal). Om du inte distribuerar till olika klienter för att separera, t. ex. produktions-och icke-produktions system eller andra system, kommer alla dina HANA stora instanser-enheter att distribueras i en klient för HANA-stora instanser. Den här klienten har en 1-till-1-relation med en resurs grupp. Men en annan närhets placerings grupp definieras för var och en av de enskilda enheterna.

Det innebär att relationerna mellan Azures resurs grupper och närhets placerings grupper för en enskild klient organisation visas här:

![Närhets placerings grupper och HANA-stora instanser](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Exempel på distribution med närhets placerings grupper
Här följer några PowerShell-kommandon som du kan använda för att distribuera dina virtuella datorer med placerings grupper för Azure närhet.

Det första steget, när du har loggat in på [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), är att kontrol lera om du är i den Azure-prenumeration som du vill använda för distributionen:

<pre><code>
Get-AzureRmContext
</code></pre>

Om du behöver byta till en annan prenumeration kan du göra det genom att köra det här kommandot:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Skapa en ny Azure-resurs grupp genom att köra det här kommandot:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Skapa den nya närhets placerings gruppen genom att köra det här kommandot:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Distribuera din första virtuella dator till närhets placerings gruppen med hjälp av ett kommando som detta:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Föregående kommando distribuerar en Windows-baserad virtuell dator. När den här virtuella dator distributionen lyckas definieras Data Center omfånget för närhets placerings gruppen i Azure-regionen. Alla efterföljande VM-distributioner som refererar till närhets placerings gruppen, som du ser i föregående kommando, distribueras i samma Azure-datacenter, så länge den virtuella dator typen kan finnas på maskin vara som placerats i data centret, och kapaciteten för den VM-typen är tillgänglig.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Kombinera tillgänglighets uppsättningar och Tillgänglighetszoner med närhets placerings grupper
En av nack delarna med att använda Tillgänglighetszoner för distribution av SAP-system är att du inte kan distribuera SAP-programlagret med hjälp av tillgänglighets uppsättningar i den specifika zonen. Du vill att SAP-programlagret ska distribueras i samma zoner som DBMS-skiktet. Det finns inte stöd för att referera till en tillgänglighets zon och en tillgänglighets uppsättning när du distribuerar en enskild virtuell dator. Så tidigare var du tvungen att distribuera ditt program lager genom att referera till en zon. Du har förlorat möjligheten att se till att de virtuella program skikten var spridda över olika uppdaterings-och feldomäner.

Genom att använda närhets placerings grupper kan du kringgå den här begränsningen. Här är distributions ordningen:

- Skapa en närhets placerings grupp.
- Distribuera din virtuella dator för virtuella datorer, vanligt vis DBMS-servern, genom att referera till en tillgänglighets zon.
- Skapa en tillgänglighets uppsättning som refererar till Azure närhets gruppen. (Mer information finns i kommandot längre fram i den här artikeln.)
- Distribuera program skiktets virtuella datorer genom att referera till tillgänglighets uppsättningen och närhets placerings gruppen.

I stället för att distribuera den första virtuella datorn som visas i föregående avsnitt refererar du till en tillgänglighets zon och närhets placerings gruppen när du distribuerar den virtuella datorn:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

En lyckad distribution av den virtuella datorn skulle vara värd för SAP-systemets databas instans i en tillgänglighets zon. Omfånget för närhets placeringen är åtgärdat till ett av de data Center som representerar den tillgänglighets zon som du har definierat.

Anta att du distribuerar virtuella datorer för centrala tjänster på samma sätt som de virtuella DBMS-datorerna, som refererar till samma zon eller zoner och samma närhets placerings grupper. I nästa steg måste du skapa de tillgänglighets uppsättningar som du vill använda för program lagret i SAP-systemet.

Definiera och skapa placerings gruppen närhet. Kommandot för att skapa tillgänglighets uppsättningen kräver en ytterligare referens till närhets placerings gruppens ID (inte namnet). Du kan hämta ID för närhets placerings gruppen genom att använda det här kommandot:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

När du skapar tillgänglighets uppsättningen måste du överväga ytterligare parametrar när du använder Managed disks (standard om inget annat anges) och placerings grupper för närhet:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Vi rekommenderar att du använder tre fel domäner. Men antalet fel domäner kan variera från region till region. I det här fallet är det maximala antalet fel domäner som är möjligt för de olika regionerna två. Om du vill distribuera virtuella datorer för program lager måste du lägga till en referens till namn på tillgänglighets uppsättningen och närhets placerings gruppens namn, som du ser här:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Resultatet av distributionen är:
- Ett DBMS-skikt och centrala tjänster för ditt SAP-system som finns i en bestämd tillgänglighets zon eller Tillgänglighetszoner.
- Ett SAP-program skikt som finns via tillgänglighets uppsättningar i samma Azure-datacenter som den virtuella DBMS-datorn eller de virtuella datorerna.

> [!NOTE]
> Eftersom du distribuerar en virtuell DBMS-dator till en zon och den andra DBMS-datorn i en annan zon för att skapa en hög tillgänglighets konfiguration måste du ha en annan närhets placerings grupp för var och en av zonerna. Samma sak gäller för alla tillgänglighets uppsättningar som du använder.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Flytta ett befintligt system till närhets placerings grupper
Om du redan har distribuerat SAP-system kanske du vill optimera nätverks fördröjningen för några av dina kritiska system och leta upp program lagret och DBMS-skiktet i samma data Center. Om du vill flytta de virtuella datorerna för en fullständig Azure-tillgänglighets uppsättning till en befintlig närhets grupp som redan är begränsad, måste du stänga av alla virtuella datorer i tillgänglighets uppsättningen och tilldela tillgänglighets uppsättningen till den befintliga närhets placerings gruppen via Azure Portal, PowerShell eller CLI. Om du vill flytta en virtuell dator som inte ingår i en tillgänglighets uppsättning till en befintlig närhets placerings grupp, behöver du bara stänga av den virtuella datorn och tilldela den till en befintlig närhets placerings grupp. 


## <a name="next-steps"></a>Nästa steg
Kolla in dokumentationen:

- [SAP-arbetsbelastningar på Azure: planering och distribution check lista](./sap-deployment-checklist.md)
- [För hands version: distribuera virtuella datorer till närhets placerings grupper med Azure CLI](../../linux/proximity-placement-groups.md)
- [För hands version: distribuera virtuella datorer till närhets placerings grupper med hjälp av PowerShell](../../windows/proximity-placement-groups.md)
- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](./dbms_guide_general.md)