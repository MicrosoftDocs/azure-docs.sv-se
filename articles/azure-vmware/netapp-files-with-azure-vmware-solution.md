---
title: Azure NetApp Files med Azure VMware Solution
description: Använd Azure NetApp Files med Azure VMware Solution virtuella datorer för att migrera och synkronisera data mellan lokala servrar, Azure VMware Solution virtuella datorer och molninfrastrukturer.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 09b9ba2ff6b95e12558b1ac49e401ce6dede4942
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870993"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files med Azure VMware Solution

I den här artikeln går vi igenom stegen för att integrera Azure NetApp Files med Azure VMware Solution-baserade arbetsbelastningar. Gästoperativsystemet körs på virtuella datorer (VM) som har åtkomst Azure NetApp Files volymer. 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files översikt

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) är en Azure-tjänst för migrering och körning av de mest krävande företagsfilarbetsbelastningarna i molnet: databaser, SAP och högpresterande databehandlingsprogram, utan kodändringar.

### <a name="features"></a>Funktioner
(Tjänster Azure NetApp Files används.)

- **Active Directory-anslutningar:** Azure NetApp Files stöder [Active Directory Domain Services och Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use).

- **Share Protocol:** Azure NetApp Files stöder Server Message Block(SMB) och NFS-protokoll (Network File System). Det här stödet innebär att volymerna kan monteras på Linux-klienten och kan mappas på Windows-klienten.

- **Azure VMware Solution:** Azure NetApp Files-resurser kan monteras från virtuella datorer som skapas i den Azure VMware Solution miljön.

Azure NetApp Files finns i många Azure-regioner och stöder replikering mellan regioner. Mer information om Azure NetApp Files finns i [Lagringshierarki för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Referensarkitektur

Följande diagram illustrerar en anslutning via en Azure ExpressRoute till ett Azure VMware Solution privat moln. Den Azure VMware Solution miljön har åtkomst till Azure NetApp Files resurs som monterats på Azure VMware Solution virtuella datorer.

![Diagram som visar NetApp Files för Azure VMware Solution arkitektur.](media/net-app-files/net-app-files-topology.png)

Den här artikeln innehåller instruktioner för att konfigurera, testa och verifiera Azure NetApp Files som en filresurs för Azure VMware Solution virtuella datorer. I det här scenariot har vi använt NFS-protokollet. Azure NetApp Files och Azure VMware Solution skapas i samma Azure-region.

## <a name="prerequisites"></a>Förutsättningar 

> [!div class="checklist"]
> * Azure-prenumeration med Azure NetApp Files aktiverat
> * Undernät för Azure NetApp Files
> * Virtuell Linux-dator på Azure VMware Solution
> * Virtuella Windows-datorer på Azure VMware Solution

## <a name="regions-supported"></a>Regioner som stöds

En lista över regioner som stöds finns i [Azure-produkter efter region.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)

## <a name="verify-pre-configured-azure-netapp-files"></a>Verifiera förkonfigurerade Azure NetApp Files 

Följ de stegvisa anvisningarna i följande artiklar för att skapa och montera Azure NetApp Files volymer på Azure VMware Solution virtuella datorer.

- [Skapa ett NetApp-konto](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Konfigurera en kapacitetspool](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Skapa en SMB-volym för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Skapa en NFS-volym för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegera ett undernät till Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

Följande steg omfattar verifiering av de förkonfigurerade Azure NetApp Files som skapats i Azure på Azure NetApp Files Premium-tjänstnivå.

1. I Azure Portal under **LAGRING** väljer du **Azure NetApp Files**. En lista över dina konfigurerade Azure NetApp Files visas. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Skärmbild som visar en lista över förkonfigurerade Azure NetApp Files."::: 

2. Välj ett konfigurerat NetApp Files-konto för att visa dess inställningar. Välj till exempel **Contoso-anf2**. 

3. Välj **Kapacitetspooler** för att verifiera den konfigurerade poolen. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Skärmbild som visar alternativ för att visa kapacitetspooler och volymer för ett konfigurerat NetApp Files-konto.":::

    Sidan Kapacitetspooler öppnas med kapacitet och servicenivå. I det här exemplet konfigureras lagringspoolen som 4 TiB med en Premium-servicenivå.

4. Välj **Volymer för** att visa volymer som skapats under kapacitetspoolen. (Se föregående skärmbild.)

5. Välj en volym för att visa dess konfiguration.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Skärmbild som visar volymer som skapats under kapacitetspoolen.":::

    Ett fönster öppnas som visar konfigurationsinformationen för volymen.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Skärmbild som visar konfigurationsinformation för en volym.":::

    Du kan se att anfvolume har en storlek på 200 GiB och är i kapacitetspoolen anfpool1. Den exporteras som en NFS-filresurs via 10.22.3.4:/ANFVOLUME. En privat IP-adress från Azure Virtual Network (VNet) skapades för Azure NetApp Files och NFS-sökvägen för montering på den virtuella datorn.

    Mer information om Azure NetApp Files volymprestanda efter storlek eller "kvot" finns i [Prestandaöverväganden för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Kontrollera förkonfigurerad Azure VMware Solution VM-resursmappning

Om du vill Azure NetApp Files resursresursen tillgänglig Azure VMware Solution den virtuella datorn måste du förstå SMB- och NFS-resursmappning. Det är först när du har konfigurerat SMB- eller NFS-volymerna som du kan montera dem enligt dokumenteringen här.

- SMB-resurs: Skapa en Active Directory-anslutning innan du distribuerar en SMB-volym. De angivna domänkontrollanterna måste vara tillgängliga för det delegerade undernätet Azure NetApp Files för att anslutningen ska lyckas. När Active Directory har konfigurerats i Azure NetApp Files visas den som ett valbart objekt när du skapar SMB-volymer.

- NFS-resurs: Azure NetApp Files bidrar till att skapa volymerna med NFS eller dubbla protokoll (NFS och SMB). En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet. NFS kan monteras på Linux-servern med hjälp av kommandoraderna eller posterna /etc/fstab.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Användningsfall för Azure NetApp Files med Azure VMware Solution

Följande är bara några övertygande Azure NetApp Files användningsfall. 
- Horizon-profilhantering
- Citrix-profilhantering
- Fjärrskrivbordstjänster profilhantering
- Filresurser på Azure VMware Solution

## <a name="next-steps"></a>Nästa steg

Nu när du har gått Azure NetApp Files integrering med Azure VMware Solution arbetsbelastningar kanske du vill veta mer om:

- [Resursbegränsningar för Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Riktlinjer för Azure NetApp Files-nätverksplanering](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Replikering mellan regioner av Azure NetApp Files volymer](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Vanliga frågor och svar om Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md)
