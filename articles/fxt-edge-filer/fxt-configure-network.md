---
title: 'Självstudie: Konfigurera nätverk i ett Azure FXT Edge-kluster'
description: Anpassa nätverks inställningar när du har skapat Azure FXT Edge-kluster
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4ce7ffc66e0b6164b2e4ca9725b3f26403292a4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220779"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Självstudie: Konfigurera klustrets nätverks inställningar

Innan du använder ett nyligen skapat Azure FXT Edge-kluster bör du kontrol lera och anpassa flera nätverks inställningar för arbets flödet.

I den här självstudien förklaras de nätverks inställningar som du kan behöva justera för ett nytt kluster.

Du kommer att lära dig:

> [!div class="checklist"]
>
> * Vilka nätverks inställningar som kan behöva uppdateras när du har skapat ett kluster
> * Det krävs en AD-server eller en DNS-server för att använda fall av Azure FXT Edge
> * Konfigurera resursallokering (Round-Robin DNS) för att automatiskt belastningsutjämna klient begär anden till FXT-klustret

Hur lång tid det tar att slutföra de här stegen beror på hur många konfigurations ändringar som krävs i systemet:

* Om du bara behöver läsa igenom självstudien och kontrol lera några inställningar bör det ta 10 till 15 minuter.
* Om du behöver konfigurera resursallokering med DNS kan den aktiviteten ta en timme eller mer.

## <a name="adjust-network-settings"></a>Justera nätverks inställningar

Flera nätverksrelaterade uppgifter är en del av att konfigurera ett nytt Azure FXT Edge-kluster. Markera listan och bestäm vilka som ska gälla för systemet.

Om du vill veta mer om nätverks inställningar för klustret läser du [Konfigurera nätverks tjänster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) i guiden kluster konfiguration.

* Konfigurera resursallokering (Round-Robin) för klient nätverk (valfritt)

  Belastnings Utjämnings kluster trafik genom att konfigurera DNS-systemet enligt beskrivningen i [Konfigurera DNS för FXT Edge-kluster](#configure-dns-for-load-balancing).

* Verifiera NTP-inställningar

* Konfigurera Active Directory-och användar namn/grupp namn nedladdningar (om det behövs)

  Om dina nätverks värdar använder Active Directory eller någon annan typ av extern katalog tjänst måste du ändra klustrets konfiguration för katalog tjänster för att konfigurera hur klustret laddar ned användar namn och grupp information. Mer information finns i **kluster**  >  **katalog tjänster** i guiden för kluster konfiguration.

  En AD-server krävs om du vill ha SMB-stöd. Konfigurera AD innan du börjar konfigurera SMB.

* Definiera VLAN (valfritt)
  
  Konfigurera ytterligare VLAN behövs innan du definierar klustrets vservers och globala namn område. Mer information finns i [arbeta med VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) i kluster konfigurations guiden.

* Konfigurera proxyservrar (vid behov)

  Om klustret använder en proxyserver för att komma åt externa adresser följer du dessa steg för att konfigurera den:

  1. Definiera proxyservern på sidan **konfigurations** inställningar för proxy
  1. Använd konfigurations sidan för proxyservern på sidan **kluster**  >  **konfiguration** eller sidan **Core-information** .
  
  Mer information finns i [använda webb-proxyservrar](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) i guiden kluster konfiguration.

* Ladda upp [krypterings certifikat](#encryption-certificates) för klustret som ska användas (valfritt)

### <a name="encryption-certificates"></a>Krypteringscertifikat

FXT Edge-kluster använder X. 509-certifikat för dessa funktioner:

* Kryptera trafik för kluster administration

* För att autentisera på uppdrag av en klient till KMIP-servrar från tredje part

* För att verifiera Cloud providers Server certifikat

Om du behöver ladda upp certifikat till klustret använder du sidan Inställningar för **kluster**  >  **certifikat** . Information finns på sidan [kluster > certifikat](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) i guiden kluster konfiguration.

Om du vill kryptera kluster hanterings kommunikationen använder du sidan **kluster**  >  **allmänna inställningar** för att välja vilket certifikat som ska användas för administrativt TLS.

> [!Note]
> Åtkomst nycklar för moln tjänster lagras med hjälp av konfigurations sidan **Cloud credentials** . I avsnittet [Lägg till en Core](fxt-add-storage.md#add-a-core-filer) -filer ovan visas ett exempel. Mer information finns i avsnittet [Cloud credentials](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) i guiden för kluster konfiguration.

## <a name="configure-dns-for-load-balancing"></a>Konfigurera DNS för belastnings utjämning

I det här avsnittet beskrivs grunderna i hur du konfigurerar ett RRDNS-system (Round-Robin) för att distribuera klient belastningen bland alla klienternas IP-adresser i ditt FXT Edge-kluster.

### <a name="decide-whether-or-not-to-use-dns"></a>Bestämma om DNS ska användas eller inte

Belastnings utjämning rekommenderas alltid, men du behöver inte alltid använda DNS. Med vissa typer av klient arbets flöden kan det till exempel vara mer meningsfullt att använda ett skript för att tilldela kluster-IP-adresser jämnt mellan klienter när de monterar klustret. Vissa metoder beskrivs i [montera klustret](fxt-mount-clients.md).

Tänk på följande när du bestämmer om du vill använda en DNS-Server:

* Om systemet endast används av NFS-klienter krävs inte DNS. Det går att ange alla nätverks adresser genom att använda numeriska IP-adresser.

* Om systemet har stöd för SMB-åtkomst, krävs DNS, eftersom du måste ange en DNS-domän för Active Directory-servern.

* DNS krävs om du vill använda Kerberos-autentisering.

### <a name="round-robin-dns-configuration-details"></a>Information om DNS-konfiguration för resursallokering

När klienter får åtkomst till klustret, balanserar RRDNS automatiskt sina förfrågningar mellan alla tillgängliga gränssnitt.

För optimala prestanda konfigurerar du DNS-servern så att den hanterar klientbaserade kluster adresser som visas i följande diagram.

Ett kluster vserver visas till vänster och IP-adresser visas i mitten och till höger. Konfigurera varje klient åtkomst punkt med en post och pekare som illustreras.

![Kluster Round-Robin DNS-diagram-detaljerad alternativ text länk följer bild ](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
 [detaljerad text Beskrivning](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

### <a name="enable-dns-in-the-cluster"></a>Aktivera DNS i klustret

Ange den DNS-server som klustret använder på sidan Inställningar för **kluster** för  >  **administrativa nätverk** . Inställningarna på sidan är:

* DNS-serveradress
* DNS-domännamn
* DNS-sökdomäner

Mer information finns i [DNS-inställningar](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) i guiden kluster konfiguration.

## <a name="next-steps"></a>Nästa steg

Detta är det sista grundläggande konfigurations steget för Azure FXT Edge-klustret.

* Lär dig mer om systemets indikatorer och andra indikatorer i [övervaka maskin varu status](fxt-monitor.md).
* Läs mer om hur klienter ska montera FXT Edge-kluster i [montera klustret](fxt-mount-clients.md).
* Mer information om hur du hanterar ett FXT Edge-kluster finns i [kluster konfigurations guiden](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html).
