---
title: Azure NetApp Files med Azure VMware-lösning
description: Använd Azure NetApp Files med virtuella Azure VMware-lösningar för att migrera och synkronisera data mellan lokala servrar, virtuella Azure VMware-lösningar och moln infrastrukturer.
ms.topic: how-to
ms.date: 01/20/2021
ms.openlocfilehash: 97a9172dfbed2722d4af0abbd6231a0367dc4e3c
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798495"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files med Azure VMware-lösning

I den här artikeln går vi igenom stegen för att integrera Azure NetApp Files med Azure VMware Solution-baserade arbets belastningar. Gäst operativ systemet körs på virtuella datorer (VM) som använder Azure NetApp Files volymer. 

## <a name="azure-netapp-files-overview"></a>Översikt över Azure NetApp Files

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) är en Azure First service-tjänst för migrering och körning av de mest krävande företags fil arbets belastningarna i molnet, inklusive databaser, SAP och data behandlings program med höga prestanda, utan kod ändringar.

### <a name="features"></a>Funktioner
(Tjänster där Azure NetApp Files används.)

- **Active Directory anslutningar**: Azure NetApp Files stöder [Active Directory Domain Services och Azure Active Directory Domain Services](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use).

- **Dela protokoll**: Azure NETAPP Files stöder SMB-(Server Message Block) och NFS-protokoll (Network File System). Det här stödet innebär att volymerna kan monteras på Linux-klienten och kan mappas på Windows-klienten.

- **Azure VMware-lösning**: Azure NetApp Files-resurser kan monteras från virtuella datorer som skapas i Azure VMware-lösnings miljön.

Azure NetApp Files finns i många Azure-regioner och stöder replikering över flera regioner. Information om hur du Azure NetApp Files konfigurations metoder finns i [Storage-hierarki för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Referensarkitektur

Följande diagram illustrerar en anslutning via Azure-ExpressRoute till ett privat moln i Azure VMware-lösningen. Det visar användningen av en Azure NetApp Files-resurs, som monteras på virtuella datorer i Azure VMware-lösningen, som används av Azure VMware-lösnings miljön.

![Diagram som visar NetApp-filer för Azure VMware-lösningens arkitektur.](media/net-app-files/net-app-files-topology.png)

Den här artikeln beskriver hur du ställer in, testar och verifierar Azure NetApp Files volym som en fil resurs för virtuella Azure VMware-lösningar. I det här scenariot har vi använt NFS-protokollet. Azure NetApp Files och Azure VMware-lösningen skapas i samma Azure-region.

## <a name="prerequisites"></a>Förutsättningar 

> [!div class="checklist"]
> * Azure-prenumeration med Azure NetApp Files aktive rad
> * Undernät för Azure NetApp Files
> * Virtuell Linux-dator i Azure VMware-lösning
> * Virtuella Windows-datorer i Azure VMware-lösning

## <a name="regions-supported"></a>Regioner som stöds

Lista över regioner som stöds finns i [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all).

## <a name="verify-pre-configured-azure-netapp-files"></a>Verifiera förkonfigurerade Azure NetApp Files 

Följ de stegvisa anvisningarna i följande artiklar för att skapa och montera Azure NetApp Files-volymer på virtuella datorer i Azure VMware-lösningen.

- [Skapa ett NetApp-konto](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Konfigurera en kapacitetspool](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Skapa en SMB-volym för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Skapa en NFS-volym för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegera ett undernät till Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

Följande steg innehåller verifiering av den förkonfigurerade Azure NetApp Files som skapats i Azure på Azure NetApp Files Premium servicenivå.

1. I Azure Portal under **lagring** väljer du **Azure NetApp Files**. En lista över dina konfigurerade Azure NetApp Files visas. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Skärm bild som visar en lista över förkonfigurerade Azure NetApp Files."::: 

2. Välj ett konfigurerat konto för NetApp-filer för att visa dess inställningar. Välj till exempel **contoso-anf2**. 

3. Välj **kapacitets grupper** för att verifiera den konfigurerade poolen. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Skärm bild som visar alternativ för att Visa kapacitets grupper och volymer för ett konfigurerat NetApp-fil konto.":::

    Sidan kapacitets pooler öppnas och visar kapacitet och service nivå. I det här exemplet konfigureras lagringspoolen som 4 TiB med en Premium service-nivå.

4. Välj **volymer** för att Visa volymer som skapats under kapacitets gruppen. (Se föregående skärm bild.)

5. Välj en volym för att visa dess konfiguration.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Skärm bild som visar volymer som skapats under kapacitets gruppen.":::

    Ett fönster öppnas med information om konfigurations informationen för volymen.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Skärm bild som visar konfigurations information för en volym.":::

    Du kan se att volymen anfvolume, med en storlek på 200 GiB, skapades i pool-anfpool1 och exporterats som en NFS-filresurs via 10.22.3.4:/ANFVOLUME. En privat IP-adress från Azure-Virtual Network (VNet) skapades för Azure NetApp Files och NFS-sökvägen för att montera på den virtuella datorn. Information om Azure NetApp Files volym prestanda i förhållande till storlek ("kvot") finns i [prestanda överväganden för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Verifiera förkonfigurerad resurs mappning för virtuell Azure VMware-lösning

Innan du demonstrerar tillgängligheten för Azure NetApp Files dela till en virtuell Azure VMware-lösning är det viktigt att förstå mappning mellan SMB och NFS-resurser. När du har konfigurerat SMB-eller NFS-volymer kan de monteras enligt dokumentationen här.

- SMB-resurs: skapa en Active Directory anslutning innan du distribuerar en SMB-volym. De angivna domän kontrol Lanterna måste vara tillgängliga för det delegerade under nätet för Azure NetApp Files för lyckad anslutning. När Active Directory har kon figurer ATS i Azure NetApp Files kontot visas det som ett valbart objekt när du skapar SMB-volymer.

- NFS-resurs: Azure NetApp Files bidrar till att skapa volymer med NFS eller Dual Protocol (NFS och SMB). En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. NFS kan monteras på Linux-servern genom att använda kommando rader eller/etc/fstab-poster.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Användnings fall av Azure NetApp Files med Azure VMware-lösning

Följande är bara några övertygande Azure NetApp Files användnings fall. 
- Hantering av Horisont profil
- Hantering av Citrix-profiler
- Fjärrskrivbordstjänster profil hantering
- Fil resurser på Azure VMware-lösning

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [resurs gränser för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits).
- Se [rikt linjer för Azure NetApp Files nätverks planering](../azure-netapp-files/azure-netapp-files-network-topologies.md).
- Lär dig mer om [replikering mellan regioner av Azure NetApp Files volymer](../azure-netapp-files/cross-region-replication-introduction.md). 
- Se [vanliga frågor och svar om Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md).
