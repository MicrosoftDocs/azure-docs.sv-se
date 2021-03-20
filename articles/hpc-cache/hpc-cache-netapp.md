---
title: Använd Azure HPC-cache och Azure NetApp Files
description: Använda Azure HPC cache för att förbättra åtkomsten till data som lagras i Azure NetApp Files
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86497020"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Använd Azure HPC cache med Azure NetApp Files

Du kan använda [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) som ett lagrings mål för din Azure HPC-cache. Den här artikeln förklarar hur de två tjänsterna kan fungera tillsammans och ger tips om hur du konfigurerar dem.

Azure NetApp Files kombinerar sitt operativ system för ONTAP med skalbarhet och hastighet för Microsoft Azure. Med den här kombinationen kan användarna flytta etablerade arbets flöden till molnet utan att skriva om koden.

Att lägga till en Azure HPC cache-komponent kan förbättra fil åtkomsten genom att presentera flera Azure NetApp Files volymer i ett sammanlagt namn område. Den kan tillhandahålla Edge caching för volymer som finns i en annan tjänst region. Det kan också förbättra prestanda på begäran för volymer som har skapats på lägre nivå service nivåer för att spara kostnader.

## <a name="overview"></a>Översikt

Följ den här processen om du vill använda ett Azure NetApp Files system som server dels lagring med Azure HPC-cache.

1. Skapa Azure NetApp Files system och volymer enligt rikt linjerna i [planera systemet, nedan](#plan-your-azure-netapp-files-system).
1. Skapa Azure HPC-cache i den region där du behöver fil åtkomst. (Följ instruktionerna i [skapa en Azure HPC-cache](hpc-cache-create.md).)
1. [Definiera lagrings mål](#create-storage-targets-in-the-cache) i cachen som pekar på Azure NetApp Files volymer. Skapa ett lagrings mål för varje unik IP-adress som används för att få åtkomst till volymerna.
1. Låta klienterna [montera Azure HPC-cachen](#mount-storage-targets) i stället för att montera Azure NetApp Files-volymer direkt.

## <a name="plan-your-azure-netapp-files-system"></a>Planera Azure NetApp Files systemet

När du planerar Azure NetApp Files systemet bör du tänka på artiklarna i det här avsnittet för att se till att du kan integrera det smidigt med Azure HPC-cache.

Läs även [Azure NetApp Files-dokumentationen](../azure-netapp-files/index.yml) innan du skapar volymer för användning med Azure HPC cache.

### <a name="nfs-client-access-only"></a>Endast NFS-klient åtkomst

Azure HPC-cache stöder för närvarande endast NFS-åtkomst. Det kan inte användas med bit-volymer för SMB ACL eller POSIX-läge.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Exklusivt undernät för Azure NetApp Files

Azure NetApp Files använder ett enda delegerat undernät för volymerna. Inga andra resurser kan använda det under nätet. Dessutom kan endast ett undernät i ett virtuellt nätverk användas för Azure NetApp Files. Läs mer i [rikt linjer för Azure NetApp Files nätverks planering](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Delegerad under näts storlek

Använd minimi storleken för det delegerade under nätet när du skapar ett Azure NetApp Files system för användning med Azure HPC cache.

Den minsta storleken, som anges med nät masken/28, ger 16 IP-adresser. I praktiken använder Azure NetApp Files bara tre av de tillgängliga IP-adresserna för att få åtkomst till volymen. Det innebär att du bara behöver skapa tre lagrings mål i din Azure HPC-cache för att kunna använda alla volymer.

Om det delegerade under nätet är för stort, är det möjligt för Azure NetApp Files volymer att använda fler IP-adresser än en enda Azure HPC cache-instans kan hantera. En enda cache får ha högst 10 lagrings mål.

I exemplet för snabb start i Azure NetApp Files dokumentationen används 10.7.0.0/16 för det delegerade under nätet, vilket ger ett för stort undernät som är för stort.

### <a name="capacity-pool-service-level"></a>Service nivå för kapacitets pool

När du väljer service nivå för din kapacitetsutnyttjande bör du tänka på arbets flödet. Om du ofta skriver data tillbaka till Azure NetApp Files volym kan cachens prestanda begränsas om tillbakaskrivning är långsam. Välj en hög tjänste nivå för volymer som ska ha frekventa skrivningar.

Volymer med låg tjänste nivå kan också visa en fördröjning i början av en aktivitet medan innehållet i cachen fylls. När cachen är igång och körs med en fungerande fil uppsättning bör fördröjningen bli obrukbar.

Det är viktigt att planera kapacitets uppsättningens service nivå i förväg, eftersom den inte kan ändras efter att den har skapats. En ny volym måste skapas i en annan kapacitets pool och de data som kopieras över.

Observera att du kan ändra lagrings kvoten för en volym och storleken på kapacitetsutnyttjandet utan att störa åtkomsten.

## <a name="create-storage-targets-in-the-cache"></a>Skapa lagrings mål i cachen

När Azure NetApp Files systemet har kon figurer ATS och Azure HPC-cachen har skapats definierar du lagrings målen i cachen som pekar på fil system volymerna.

Skapa ett lagrings mål för varje IP-adress som används av Azure NetApp Files volymer. IP-adressen visas på sidan monterings instruktioner för volymen.

Om flera volymer delar samma IP-adress, kan du använda ett lagrings mål för alla.  

Följ [anvisningarna i Azure NetApp Files-dokumentationen](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) för att hitta de IP-adresser som ska användas.

Du kan också hitta IP-adresser med Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Export namn på Azure NetApp Files systemet har en enda Sök vägs komponent. Försök inte att skapa ett lagrings mål för rot exporten ``/`` i Azure NetApp Files eftersom exporten inte ger åtkomst till filen.

Det finns inga särskilda begränsningar för virtuella namn rymds Sök vägar för dessa lagrings mål.

## <a name="mount-storage-targets"></a>Montera lagrings mål

Klient datorerna bör montera cacheminnet i stället för att montera Azure NetApp Files-volymer direkt. Följ instruktionerna i [montera Azure HPC cache](hpc-cache-mount.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du konfigurerar och använder [Azure NetApp Files](../azure-netapp-files/index.yml)
* [Kontakta supporten](hpc-cache-support-ticket.md)om du vill ha hjälp med att planera och konfigurera Azure HPC cache-systemet för att använda Azure NetApp Files.
