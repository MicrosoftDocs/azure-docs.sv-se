---
title: Översikt över virtuella Linux-datorer i Azure
description: Översikt över virtuella Linux-datorer i Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe1db967dac8a76119733f930e54a38160395932
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549846"
---
# <a name="linux-virtual-machines-in-azure"></a>Virtuella Linux-datorer i Azure

Virtuella Azure-datorer (Virtual Machines, VM) är en av flera typer av [behovsbaserade och skalbara datorresurser](/azure/architecture/guide/technology-choices/compute-decision-tree) som Azure erbjuder. Normalt använder du en virtuell dator om du behöver mer kontroll över datormiljön än vad de andra alternativen erbjuder. Den här artikeln innehåller information om vad du bör tänka på innan du skapar en virtuell dator, hur du skapar den och hur du hanterar den.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du behöver dock fortfarande underhålla den virtuella datorn genom att utföra uppgifter, som att konfigurera, korrigera och underhålla programvaran som körs på den virtuella datorn.

Virtuella datorer i Azure kan användas på olika sätt. Några exempel är:

* **Utveckling och tester** – Virtuella datorer i Azure erbjuder ett snabbt och enkelt sätt att skapa en dator med specifika konfigurationer som krävs för att koda och testa ett program.
* **Program i molnet** – Eftersom behovet av ditt program kan variera kan det vara bra ur ekonomisk synpunkt att köra den på en virtuell dator i Azure. Du betalar extra för virtuella datorer när du behöver dem och stänger av dem när du inte behöver dem.
* **Utökat datacenter** – Virtuella datorer i ett virtuellt nätverk i Azure kan enkelt anslutas till organisationens nätverk.

Antalet virtuella datorer som programmet använder kan skalas upp och ned beroende på vilka behov du har.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Vad behöver jag tänka på innan jag skapar en virtuell dator?
Det finns alltid en rad [överväganden vid utformning](/azure/architecture/reference-architectures/n-tier/linux-vm) när du utökar en programinfrastruktur i Azure. Följande aspekter av en virtuell dator är viktiga att tänka på innan du börjar:

* Programresursernas namn
* Lagringsplatsen för resurserna
* Den virtuella datorns storlek
* Det högsta antalet virtuella datorer som kan skapas
* Operativsystemet som körs på den virtuella datorn
* Konfigurationen av den virtuella datorn när den startats
* Relaterade resurser som krävs för den virtuella datorn

### <a name="locations"></a>Platser
Alla resurser som skapats i Azure fördelas på flera [geografiska områden](https://azure.microsoft.com/regions/) runtom i världen. Vanligtvis kallas regionen **plats** när du skapar en virtuell dator. För en virtuell dator anger platsen var de virtuella hårddiskarna lagras.

I den här tabellen finns några exempel på hur du kan hämta en lista över tillgängliga platser.

| Metod | Beskrivning |
| --- | --- |
| Azure Portal |Välj en plats i listan när du skapar en virtuell dator. |
| Azure PowerShell |Använd kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| REST-API |Använd åtgärden [List locations](/rest/api/resources/subscriptions) (Listplatser). |
| Azure CLI |Använd åtgärden [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Tillgänglighet
Azure har tillkännagivit ett branschledande serviceavtal på 99,9 % för virtuella datorer med en instans, förutsatt att du distribuerar den virtuella datorn med premiumlagring för alla diskar.  För att distributionen ska kunna omfattas av standardserviceavtalet på 99,95 % för virtuella datorer behöver du fortfarande distribuera två eller flera virtuella datorer som kör arbetsbelastningen i en tillgänglighetsuppsättning. En tillgänglighetsuppsättning säkerställer att dina virtuella datorer distribueras via flera feldomäner i Azure-datacentren och på värdar med olika underhållsfönster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

## <a name="vm-size"></a>Storlek på virtuell dator
[Storleken](../sizes.md) på den virtuella datorn som du använder bestäms av den arbetsbelastning som du vill köra. Storleken som du väljer avgör sedan faktorer som processorkraft, minne och lagringskapacitet. Azure erbjuder en rad olika storlekar för att passa en mängd olika användningar.

Azure debiterar ett [Tim pris](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) baserat på den virtuella datorns storlek och operativ system. För delar av timmar tar Azure bara betalt för användningen per minut. Lagringsutrymme prissätts och debiteras separat.

## <a name="vm-limits"></a>Begränsningar för den virtuella datorn
Prenumerationen har [standardkvotgränser](../../azure-resource-manager/management/azure-subscription-service-limits.md) som kan påverka ditt projekt om många virtuella datorer distribueras. Den aktuella gränsen på basis av per prenumeration är 20 virtuella datorer per region. Begränsningen kan ökas om du [anmäler ett supportärende och begär en ökning](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Managed Disks

Managed Disks hanterar skapande och hantering av Azure-lagringskontot i bakgrunden och säkerställer att du inte behöver bekymra dig om lagringskontots skalbarhetsgränser. Du anger diskens storlek och prestandanivå (Standard eller Premium) och sedan skapar och hanterar Azure disken. Om du lägger till diskar eller skalar upp eller ned den virtuella datorn behöver du inte oroa dig om lagringsutrymmet som används. Om du skapar nya virtuella datorer ska du [använda Azure CLI](quick-create-cli.md) eller Azure-portalen för att skapa virtuella datorer med hanterade OS- och datadiskar. Om du har virtuella datorer med ohanterade diskar kan du [konvertera de virtuella datorerna så att de stöds av Managed Disks](convert-unmanaged-to-managed-disks.md).

Du kan även hantera dina anpassade avbildningar i ett lagringskonto per Azure-region och använda dem för att skapa hundratals virtuella datorer i samma prenumeration. Mer information om Managed Disks finns i [översikten över Managed Disks](../managed-disks-overview.md).

## <a name="distributions"></a>Distributioner 
Microsoft Azure har stöd för körning av ett antal populära Linux-distributioner som tillhandahålls och underhålls av ett antal partners.  Du kan hitta tillgängliga distributioner på Azure Marketplace. Microsoft arbetar aktivt med flera Linux-communities för att lägga till ännu fler alternativ i listan över [Azure-godkända Linux-distributioner](endorsed-distros.md).

Om den önskade Linux-distributionen inte finns i galleriet för närvarande kan du ”Bring your own Linux”-VM genom att [skapa och ladda upp en virtuell Linux-hårddisk i Azure](create-upload-generic.md).

Microsoft har ett nära samarbete med partner för att se till att de tillgängliga avbildningarna är uppdaterade och optimerade för Azure-körning.  Mer information om erbjudanden för Azure-partner finns i följande länkar:

* Linux på Azure – [godkända distributioner](endorsed-distros.md)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red Hat – [Azure Marketplace – Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Kanoniskt läge – [Azure Marketplace – Ubuntu-Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian – [Azure Marketplace – Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD – [Azure Marketplace – FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar – [Azure Marketplace – Flatcar-behållare Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS – [Azure Marketplace – RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami – [Bitnami Library för Azure](https://azure.bitnami.com/)
* Mesosphere – [Azure Marketplace – Mesosphere DC/OS på Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker – [Azure Marketplace – Docker-avbildningar](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins – [Azure Marketplace – CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

All infrastruktur måste vara kod för att uppnå en korrekt DevOps-kultur.  När all infrastruktur finns i kod kan den enkelt återskapas.  Azure fungerar med alla större automatiseringsverktyg som Ansible, Chef, SaltStack och Puppet.  Azure har även en egen verktygsuppsättning för automatisering:

* [Azure-mallar](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

Azure har stöd för [Cloud-Init](https://cloud-init.io/) i de flesta Linux-distributioner som har stöd för det.  Vi arbetar aktivt med våra godkända Linux distribution-partner för att få moln-init-aktiverade avbildningar tillgängliga på Azure Marketplace. De här avbildningarna gör att dina Cloud-Init-distributioner och-konfigurationer fungerar sömlöst med virtuella datorer och skalnings uppsättningar för virtuella datorer.

* [Använda cloud-init på virtuella Azure Linux-datorer](using-cloud-init.md)

## <a name="storage"></a>Storage
* [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Lägga till en disk i en virtuell Linux-dator med azure-cli](add-disk.md)
* [Lägga till en datadisk i en virtuell Linux-dator i Azure-portalen](attach-disk-portal.md)

## <a name="networking"></a>Nätverk
* [Översikt över Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [IP-adresser i Azure](../../virtual-network/public-ip-addresses.md)
* [Öppna portar till en virtuell Linux-dator i Azure](nsg-quickstart.md)
* [Skapa ett fullständigt domännamn i Azure-portalen](../create-fqdn.md)


## <a name="data-residency"></a>Dataplacering

I Azure är funktionen för att aktivera lagring av kunddata i en enda region för närvarande endast tillgänglig i Sydostasien region (Singapore) i regionen Asien och stillahavsområdet Geo-och Brasilien syd (Sao Paulo State) i Brasilien. För alla andra regioner lagras kund information på Geo. Mer information finns i [säkerhets Center](https://azure.microsoft.com/global-infrastructure/data-residency/).


## <a name="next-steps"></a>Nästa steg

Skapa din första virtuella dator!

- [Portal](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
