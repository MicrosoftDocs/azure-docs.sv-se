---
title: Aver vFXT DNS-Azure
description: Konfigurera en DNS-server för Round-Robin-belastnings utjämning med AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009032"
---
# <a name="avere-cluster-dns-configuration"></a>Konfiguration av DNS för Avere-kluster

I det här avsnittet beskrivs grunderna i hur du konfigurerar ett DNS-system för belastnings utjämning av ditt AVERT vFXT-kluster.

Det här dokumentet *innehåller inte* instruktioner för att konfigurera och hantera en DNS-server i Azure-miljön.

I stället för att använda Round-Robin-DNS för att belastningsutjämna ett vFXT-kluster i Azure bör du överväga att använda manuella metoder för att tilldela IP-adresser jämnt mellan klienter när de monteras. Det finns flera metoder som beskrivs i [montera ett AVERT-kluster](avere-vfxt-mount-clients.md).

Tänk på följande när du bestämmer om du vill använda en DNS-Server:

* Om systemet endast används av NFS-klienter krävs det inte att du använder DNS. det är möjligt att ange alla nätverks adresser genom att använda numeriska IP-adresser.

* Om systemet har stöd för SMB-åtkomst, krävs DNS, eftersom du måste ange en DNS-domän för Active Directory-servern.

* DNS krävs om du vill använda Kerberos-autentisering.

## <a name="load-balancing"></a>Belastningsutjämning

Om du vill distribuera den övergripande belastningen konfigurerar du din DNS-domän så att den använder resursallokering med resursallokering (Round-Robin) för IP-adresser.

## <a name="configuration-details"></a>Konfigurations information

När klienter får åtkomst till klustret, balanserar RRDNS automatiskt sina förfrågningar mellan alla tillgängliga gränssnitt.

För optimala prestanda konfigurerar du DNS-servern så att den hanterar klientbaserade kluster adresser som visas i följande diagram.

Ett kluster vserver visas till vänster och IP-adresser visas i mitten och till höger. Konfigurera varje klient åtkomst punkt med en post och pekare som illustreras.

![AVERT kluster Round-Robin DNS-diagram](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Varje klient riktad IP-adress måste ha ett unikt namn för intern användning av klustret. (I det här diagrammet heter klientens IP-adresser VS1-client-IP-* för tydlighetens skull, men i produktion bör du förmodligen använda något mer koncis, som klient *.)

Klienter monterar klustret med namnet vserver som server argument.

Ändra DNS-serverns ``named.conf`` fil för att ställa in cyklisk ordning för frågor till din vserver. Det här alternativet säkerställer att alla tillgängliga värden går igenom. Lägg till en instruktion som följande:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Följande ``nsupdate`` kommandon innehåller ett exempel på hur du konfigurerar DNS korrekt:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Kluster-DNS-inställningar

Ange den DNS-server som vFXT-klustret använder på sidan **kluster**  >  **administrativa nätverks** inställningar. Inställningarna på sidan är:

* DNS-serveradress
* DNS-domännamn
* DNS-sökdomäner

Läs [DNS-inställningarna](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) i kluster konfigurations guiden för AVERT om du vill ha mer information om hur du använder den här sidan.
