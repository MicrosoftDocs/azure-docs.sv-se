---
title: 'Självstudie: skapa ett kluster för FXT Edge-kluster för att lagra kluster'
description: Lär dig hur du skapar ett cache-kluster med hybrid Storage med Azure FXT Edge-filer och hur du loggar in på kluster kontroll panelen för att konfigurera kluster inställningarna.
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: d019f5df4bba6d223076c8ce35151510afedf2e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220813"
---
# <a name="tutorial-create-the-azure-fxt-edge-filer-cluster"></a>Självstudie: Skapa Azure FXT Edge-kluster

När du har installerat och initierat Azure FXT Edge-filer för ditt cacheminne använder du kluster program varan FXT för att skapa cache-klustret.

Den här självstudien vägleder dig genom stegen för att konfigurera dina maskinvarukonfigurationer som ett kluster.

I den här kursen lär du dig:

> [!div class="checklist"]
>
> * Vilken information som krävs innan du börjar skapa klustret
> * Skillnaden mellan klustrets hanterings nätverk, kluster nätverket och det klientbaserade nätverket
> * Så här ansluter du till en klusternod
> * Så här skapar du ett första kluster med en Azure FXT Edge-nod
> * Så här loggar du in på kluster kontroll panelen för att konfigurera kluster inställningarna

Den här proceduren tar mellan 15 och 45 minuter, beroende på hur mycket forskning du behöver göra för att identifiera IP-adresser och nätverks resurser.

## <a name="prerequisites"></a>Förutsättningar

Slutför de här förutsättningarna innan du börjar den här självstudien:

* Installera dina Azure FXT Edge-postsystem i ditt data Center

  Du behöver bara en nod för att skapa klustret, men du måste [lägga till minst två noder](fxt-add-nodes.md) innan du kan konfigurera klustret och bli redo att använda.

* Anslut lämpliga strömförsörjnings-och nätverks kablar till systemet  
* Starta minst en Azure FXT Edge-nod och [Ange rot lösen ordet](fxt-node-password.md)

## <a name="gather-information-for-the-cluster"></a>Samla in information för klustret

Du behöver följande information för att skapa Azure FXT Edge-kluster:

* Klusternamn

* Administratörs lösen ord för att ange för klustret

* IP-adresser:

  * En enda IP-adress för kluster hantering och nät masken och routern som ska användas för hanterings nätverket
  * Den första och sista IP-adressen i ett sammanhängande intervall med IP-adresser för kluster (nod till nod)-kommunikation. Mer information finns i [IP-adress distribution](#ip-address-distribution)nedan.
  * (Klientbaserade IP-adresser anges när klustret har skapats.)

* Information om nätverks infrastruktur:

  * IP-adressen för en DNS-server för klustret
  * Namnet på DNS-domänen för klustret
  * Namnet eller IP-adressen för klustrets NTP-servrar (antingen en server eller tre eller flera)
  * Om du vill aktivera IEEE 802.1 AX-2008 länk agg regering på klustrets gränssnitt
  * Om du aktiverar länk agg regering, om du vill använda en dynamisk agg regering för IEEE 802.3 AD (LACP)

Du kan konfigurera dessa objekt för nätverks infrastruktur efter att du har skapat klustret, men det är bättre att göra det när det skapas.

### <a name="ip-address-distribution"></a>IP-adress distribution

Azure FXT Edge-filer med hybrid Storage cache-kluster använder IP-adresser i tre kategorier:

* Hanterings-IP: en enskild IP-adress för kluster hantering

  Den här adressen fungerar som start punkt för att komma åt kluster konfigurations verktygen (den webbaserade kontroll panelen eller XML-RPC-API). Den här adressen tilldelas automatiskt till den primära noden i klustret och den flyttas automatiskt om den primära noden ändras.

  Andra IP-adresser kan användas för att få åtkomst till kontroll panelen, men hanterings-IP-adressen är utformad för att ge åtkomst även om enskilda noder växlar över.

* Kluster nätverk: ett intervall med IP-adresser för kluster kommunikation

  Kluster nätverket används för kommunikation mellan klusternoder och för att hämta filer från Server dels lagringen (Core-filer).

  **Bästa praxis:** Allokera en IP-adress per fysisk port som används för kluster kommunikation på varje Azure FXT Edge-nod. Klustret tilldelar automatiskt adresserna i det angivna intervallet till enskilda noder.

* Klient nätverk: det intervall med IP-adresser som klienter använder för att begära och skriva filer

  Klientens nätverks adresser används av klienter för att komma åt kärn informationen i klustret. Till exempel kan en NFS-klient montera en av dessa adresser.

  **Bästa praxis:** Tilldela en IP-adress per fysisk port som används för klient kommunikation på varje FXT-serie nod.

  Klustret distribuerar IP-adresser som riktas mot klienter över dess ingående noder så jämnt som möjligt.

  För enkelhetens skull konfigurerar många administratörer ett enda DNS-namn med Round-Robin DNS-konfiguration (RRDNS) för att göra det enklare att distribuera klient begär anden över adress intervallet. Den här inställningen gör det också möjligt för alla klienter att använda samma Mount-kommando för att komma åt klustret. Läs [Konfigurera DNS](fxt-configure-network.md#configure-dns-for-load-balancing) om du vill ha mer information.

Hanterings-IP-adressen och ett intervall med kluster nätverks adresser måste anges för att skapa ett nytt kluster. Klienternas adresser anges efter att klustret har skapats.

## <a name="connect-to-the-first-node"></a>Anslut till den första noden

Du kan ansluta till någon av de installerade FXT-noderna och använda dess OS-programvara för att konfigurera klustret.

Om du inte redan har gjort det, kan du sätta på minst en av FXT-noderna för klustret och kontrol lera att den har en nätverks anslutning och en IP-adress. Du måste ange ett nytt rot lösen ord för att aktivera noden, så följ stegen i [Ange maskin varu lösen ord](fxt-node-password.md) om du inte redan har gjort det.

Kontrol lera nätverks anslutningen genom att se till att nodens indikatorer för nätverks länken är upplysta (och vid behov indikatorer på nätverks växeln som den är kopplad till). Indikator lampor beskrivs i [övervaka maskin varu status för Azure FXT Edge](fxt-monitor.md)-filer.

När noden startas, kommer den att begära en IP-adress. Om den är ansluten till en DHCP-server godkänner den IP-adressen som tillhandahålls av DHCP. (Den här IP-adressen är tillfällig. Det kommer att ändras när du skapar klustret.)

Om den inte är ansluten till en DHCP-server eller inte får något svar kommer noden att använda Bonjour-programvara för att ange en självtilldelad IP-adress i formatet 169,254. \* . \* . Du bör dock ange en tillfällig statisk IP-adress på en av nodens nätverkskort innan du använder den för att skapa ett kluster. Instruktioner finns i det äldre dokumentet. Kontakta Microsofts tjänst och support för uppdaterad information: [bilaga A: Ange en statisk IP-adress på en FXT-nod](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

### <a name="find-the-ip-address"></a>Hitta IP-adressen

Anslut till noden Azure FXT Edge-filer för att hitta dess IP-adress. Du kan använda en seriell kabel, direkt anslutning till USB-och VGA-portarna eller ansluta via en KVM-växel. (Information om port anslutning finns i [Ange initiala lösen ord](fxt-node-password.md).)

När du har anslutit loggar du in med det användar namn `root` och lösen ord som du angav när du startade noden för första gången.  

När du har loggat in måste du bestämma nodens IP-adress.

Använd kommandot `ifconfig` för att se de adresser som har tilldelats systemet.

Till exempel söker kommandot efter `ifconfig | grep -B5 inet` portar med Internet adresser och ger fem kontexter för att Visa port identifieraren.

Skriv ned alla IP-adresser som visas i ifconfig-rapporten. Adresser i listan med port namn som e0a eller e0b är lämpliga alternativ. Använd inte några IP-adresser i listan med E7 *-namn eftersom dessa namn bara används för iDRAC/IPMI-tjänstens portar.

## <a name="load-the-cluster-configuration-wizard"></a>Läs in kluster konfigurations guiden

Använd det webbläsarbaserade kluster konfigurations verktyget för att skapa klustret.

Ange IP-adressen för noden i en webbläsare. Om webbläsaren visar ett meddelande om att webbplatsen inte är betrodd fortsätter du till platsen. (Enskilda Azure FXT Edge-noder har inte CA-angivna säkerhetscertifikat.)

![Kontroll panelens inloggnings sida i webbläsarfönstret](media/fxt-cluster-create/unconfigured-node-gui.png)

Lämna fälten **användar namn** och **lösen ord** tomma. Klicka på **Logga** in för att läsa in sidan klustret skapas.

![Första installations skärmen för en ej konfigurerad nod i den webbläsarbaserade GUI-Kontrollpanelen. Det visar alternativ för att uppdatera program vara, konfigurera ett kluster manuellt eller konfigurera ett kluster från en installations fil.](media/fxt-cluster-create/setup-first-screen.png)

## <a name="create-the-cluster"></a>Skapa klustret

Kluster konfigurations verktyget vägleder dig genom en uppsättning skärmar för att skapa Azure FXT Edge-klustret. Kontrol lera att du har [nödvändig information](#gather-information-for-the-cluster) som är klar innan du startar.

### <a name="creation-options"></a>Alternativ för att skapa

Den första skärmen innehåller tre alternativ. Använd alternativet Manuell konfiguration om du inte har särskilda instruktioner från support personalen.

Klicka på **Jag konfigurerar klustret manuellt** för att läsa in den nya sidan kluster konfigurations alternativ.

De andra alternativen används sällan:

* "Uppdatera system avbildningen" kräver att du installerar nya OS-program innan du skapar klustret. (Den aktuella installerade program versionen visas överst på skärmen.) Du måste ange programpaket filen – antingen en URL och ett användar namn/lösen ord, eller genom att ladda upp en fil från datorn.

* Alternativet kluster installations fil används ibland av Microsofts kund tjänst och support.

## <a name="cluster-options"></a>Kluster alternativ

På nästa skärm uppmanas du att konfigurera alternativ för det nya klustret.

Sidan är indelad i två huvud avsnitt, **grundläggande konfiguration** och **nätverks konfiguration**. Avsnittet nätverks konfiguration innehåller också underavsnitt: ett för **hanterings** nätverket och ett för **kluster** nätverket.

### <a name="basic-configuration"></a>Grundläggande konfiguration

I det övre avsnittet fyller du i grundläggande information för det nya klustret.

![Information om avsnittet grundläggande konfiguration i webb läsar sidans GUI-sida. Det visar tre fält (kluster namn, administratörs lösen ord, bekräfta lösen ord)](media/fxt-cluster-create/basic-configuration.png)

* **Kluster namn** – ange ett unikt namn för klustret.

  Kluster namnet måste uppfylla följande kriterier:
  
  * Längden på 1 till 16 tecken
  * Kan innehålla bokstäver, siffror och bindestreck (-) och under streck (_)
  * Får inte innehålla andra skiljetecken eller specialtecken
  
  Du kan ändra namnet senare på sidan konfiguration av **kluster**  >  **allmän** konfiguration. (Mer information om kluster inställningarna finns i [guiden kluster konfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html), som inte ingår i den här dokumentationen.)

  > [!NOTE]
  > Kluster namnet används för att identifiera system information som har överförts till stöd för övervakning eller fel sökning, så det är användbart att inkludera ditt företags namn.

* **Administratörs lösen ord** – ange lösen ordet för den administrativa standard användaren `admin` .
  
  Du bör konfigurera enskilda användar konton för varje person som administrerar klustret, men du kan inte ta bort användaren `admin` . Logga in som `admin` om du behöver skapa ytterligare användare.

  Du kan ändra lösen ordet för `admin` på sidan Inställningar för **administrations**  >  **användare** i kluster kontroll panelen. Mer information finns i dokumentationen för **användare** i [guiden kluster konfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_users.html).

<!-- to do: update "legacy" URLs when docs are ported to Microsoft site -->

### <a name="network-configuration"></a>Konfiguration av nätverk

I avsnittet **nätverk** uppmanas du att ange vilken nätverks infrastruktur som ska användas i klustret.

Det finns två separata nätverk att konfigurera:

* *Hanterings nätverket* ger administratörs åtkomst till klustret för konfiguration och övervakning. Den IP-adress som anges här används vid anslutning till kontroll panelen eller för SSH-åtkomst.

  De flesta kluster använder bara en enda hanterings-IP-adress, men om du vill lägga till gränssnitt kan du göra det när du har skapat klustret.

* *Kluster nätverket* används för kommunikation mellan klusternoder och mellan klusternoder och backend-lagring (Core-filer).

Klient nätverks nätverket konfigureras senare efter att klustret har skapats.

Det här avsnittet innehåller också konfiguration för DNS-och NTP-servrar som används av båda nätverken.

### <a name="configure-the-management-network"></a>Konfigurera hanterings nätverket

Inställningarna i avsnittet **hantering** är för det nätverk som ger administratörs åtkomst till klustret.

![detaljerad information om avsnittet "hantering" med fält för 5 alternativ och en kryss ruta för 1 GB hanterings nätverk](media/fxt-cluster-create/management-network-filled.png)

* **Hanterings-IP** – ange den IP-adress som du ska använda för att få åtkomst till kluster kontroll panelen. Den här adressen kommer att begäras av klustrets primära nod, men den flyttas automatiskt till en felfri nod om den ursprungliga primära noden blir otillgänglig.

  De flesta kluster använder bara en IP-adress för hantering. Om du vill ha mer än en kan du lägga till dem när du har skapat klustret med hjälp av sidan **kluster**  >  Inställningar för **nätverks administration** . Läs mer i [guiden kluster konfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html).

* **Nätmask** – ange nät masken för hanterings nätverket.

* **Router** – ange den standardgateway-adress som används av hanterings nätverket.

* **VLAN-tagg (valfritt)** – om klustret använder VLAN-Taggar anger du taggen för hanterings nätverket.

  Ytterligare VLAN-inställningar konfigureras på sidan för **kluster**-  >  **VLAN** -inställningar. Läs mer om hur du [arbetar med VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) och [kluster > VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vlan.html) i kluster konfigurations guiden.

* **MTU** – om det behövs justerar du den högsta överförings enheten (MTU) för klustrets hanterings nätverk.

* **Använd 1 GB hanterings nätverk** – Markera den här kryss rutan om du vill tilldela de två 1GbE-nätverks portarna på dina FXT-noder enbart till hanterings nätverket. (Du måste ha 25GbE-eller 10 ports portar tillgängliga för all annan trafik.) Om du inte markerar den här kryss rutan använder hanterings nätverket den högsta tillgängliga hastighets porten.

### <a name="configure-the-cluster-network"></a>Konfigurera kluster nätverket

Kluster nätverks inställningarna gäller för trafik mellan klusternoderna och mellan klusternoder och Core-filer.

![information om "kluster"-avsnittet med fält för att ange sex värden](media/fxt-cluster-create/cluster-network-filled.png)

* **Första IP** -adress och **sista IP** -ange de IP-adresser som definierar intervallet som ska användas för intern kluster kommunikation. De IP-adresser som används här måste vara sammanhängande och inte tilldelas av DHCP.

  Du kan lägga till fler IP-adresser när du har skapat klustret. Använd inställnings sidan för **kluster**  >  **kluster nätverk** ([dokumentation om kluster konfigurations guiden](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_networks.html#gui-cluster-networks)).

  Värdet i **antal IP-adresser i intervallet** beräknas och visas automatiskt.

* **Icke-hantering av nätmask (valfritt)** – ange nät masken för kluster nätverket.

  Systemet föreslår automatiskt det netmask-värde som du angav för hanterings nätverket. ändra det om det behövs.

* **Cluster router (valfritt)** – ange den standardgateway-adress som används av kluster nätverket.

  Systemet föreslår automatiskt samma Gateway-adress som du angav för hanterings nätverket.

* **Kluster-VLAN-tagg (valfritt)** – om klustret använder VLAN-Taggar anger du taggen för kluster nätverket.

* **Icke-hanterings-MTU (valfritt)** – om det behövs justerar du den högsta överförings enheten (MTU) för kluster nätverket.

### <a name="configure-cluster-dns-and-ntp"></a>Konfigurera kluster-DNS och NTP

Under **kluster** avsnittet finns det fält för att ange DNS-och NTP-servrar och för att aktivera länk agg regering. De här inställningarna gäller för alla nätverk som används i klustret.

![Avsnitt för DNS-/NTP-konfiguration, med tre fält för DNS-servrar, fält för DNS-domän och DNS-sökning, tre fält för NTP-servrar och en nedrullningsbar meny för länk agg regerings alternativ](media/fxt-cluster-create/dns-ntp-filled.png)

* **DNS** -servrar – Ange IP-adressen till en eller flera DNS-servrar (Domain Name System).

  DNS rekommenderas för alla kluster och krävs om du vill använda SMB, AD eller Kerberos.
  
  För bästa prestanda konfigurerar du klustrets DNS-server för belastnings utjämning med resursallokering enligt beskrivningen i [Konfigurera DNS för Azure FXT Edge-kluster](fxt-configure-network.md#configure-dns-for-load-balancing).

* **DNS-domän** – ange det nätverks domän namn som klustret ska använda.

* **DNS-sökning** – alternativt kan du ange ytterligare domän namn som systemet ska söka efter för att lösa DNS-frågor. Du kan lägga till upp till sex domän namn, avgränsade med blank steg.

* **NTP** -servrar – Ange antingen en eller tre NTP-servrar (Network Time Protocol) i de angivna fälten. Du kan använda värdnamn eller IP-adresser.

* Med **länk agg regering** – länk agg regering kan du anpassa hur Ethernet-portarna på klustrets FXT-noder hanterar olika typer av trafik. Om du vill veta mer kan du läsa [länk agg regering](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cluster_general_setup.html#link-aggregation) i guiden kluster konfiguration.

### <a name="click-the-create-button"></a>Klicka på knappen Skapa

När du har angett alla nödvändiga inställningar i formuläret klickar du på knappen **skapa kluster** .

![nederst i slutfört formulär med markör över knappen Skapa längst ned till höger](media/fxt-cluster-create/create-cluster.png)

Systemet visar ett meddelande när klustret skapas.

![status meddelande för kluster konfiguration i webbläsaren: "FXT-noden skapar nu klustret. Detta tar flera minuter. När klustret har skapats kan du gå till den här länken för att slutföra konfigurationen. " med hyperlänk på "besök den här länken"](media/fxt-cluster-create/creating-message.png)

Efter en liten stund kan du klicka på länken i meddelandet för att gå till kluster kontroll panelen. (Den här länken tar dig till den IP-adress som du angav i **hanterings-IP**.) Det tar 15 sekunder till en minut innan länken aktive ras när du klickar på knappen Skapa. Om webb gränssnittet inte läses in väntar du flera sekunder och klickar sedan på länken igen.

Skapande av kluster tar en minut eller mer, men du kan logga in på kontroll panelen medan processen pågår. Det är normalt att kontroll panelens instrument panels sida visar varningar tills kluster skapande processen har slutförts.

## <a name="open-the-settings-pages"></a>Öppna inställnings sidorna

När du har skapat klustret måste du anpassa konfigurationen för ditt nätverk och arbets flöde.

Använd kontroll panelens webb gränssnitt för att konfigurera det nya klustret. Följ länken från skärmen för att skapa ett kluster eller bläddra till den hanterings-IP-adress som du har angett i klustret.

Logga in på webb gränssnittet med det användar namn `admin` och lösen ord som du angav när du skapade klustret.

![webbläsare som visar kontroll panelens inloggnings fält](media/fxt-cluster-create/admin-login.png)

Kontroll panelen öppnas och sidan **instrument panel** visas. När klustret har skapats klart bör eventuella varnings meddelanden tas bort från visningen.

Klicka på fliken **Inställningar** för att konfigurera klustret.

På fliken **Inställningar** visar den vänstra sid panelen en meny med konfigurations sidor. Sidorna är ordnade efter kategori. Klicka på + eller-kontrollen överst i kategori namnet för att expandera eller dölja enskilda sidor.

![Fliken Inställningar på kontroll panelen (i webbläsaren) med kluster > sidan allmän installations sida har lästs in](media/fxt-cluster-create/settings-tab-populated.png)

## <a name="cluster-setup-steps"></a>Steg för kluster konfiguration

I det här skedet finns klustret, men det har bara en nod, inga IP-adresser som riktas mot klienter och ingen backend-lagring. Ytterligare installations steg krävs för att gå från ett nytt kluster till ett cache-system som är redo att hantera ditt arbets flöde.

### <a name="required-configuration"></a>Obligatorisk konfiguration

De här stegen behövs för de flesta eller alla kluster.

* Lägg till noder i klustret

  Tre noder är standard, men många produktions kluster har mer upp till högst 24 noder.

  Läs [Lägg till klusternoder](fxt-add-nodes.md) för att lära dig hur du lägger till andra Azure FXT Edge-enheter i klustret och aktiverar hög tillgänglighet.

* Ange server dels lagring

  Lägg till definitioner för *kärn* filer för varje server dels lagrings system som klustret ska använda. Läs [Lägg till Server dels lagring och konfigurera virtuell namnrymd](fxt-add-storage.md#about-back-end-storage) för mer information.

* Konfigurera klient åtkomst och det virtuella namn området

  Skapa minst en virtuell server (vserver) och tilldela den ett IP-adressintervall för klient datorer som ska användas. Du måste också konfigurera kluster namn området (kallas ibland globalt namn område eller GNS), en funktion för virtuella fil system som gör att du kan mappa Server dels lagrings export till virtuella sökvägar. Kluster namn området ger klienterna en konsekvent och tillgänglig fil system struktur även om du växlar Server dels lagrings medier. Namn området kan också tillhandahålla en användarvänlig virtuell lagrings-hierarki för Azure Blob-behållare eller annan moln objekts lagring som stöds.

  Läs [Konfigurera namn området](fxt-add-storage.md#configure-the-namespace) för mer information. I det här steget ingår:
  * Skapar vservers
  * Konfigurera Knut punkter mellan klientens nätverks visning och Server dels lagring
  * Definiera vilka klient-IP-adresser som hanteras av varje vserver

  > [!Note]
  > En betydande planering rekommenderas innan du börjar konfigurera klustrets GNS. Läs avsnittet [använda ett globalt namn område](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) och [skapa och arbeta med VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) avsnitt i guiden kluster konfiguration för att få hjälp.

* [Justera nätverks inställningar](fxt-configure-network.md)

  Det finns flera nätverksrelaterade inställningar som bör verifieras eller anpassas för ett nytt kluster. Läs [Justera nätverks inställningar](fxt-configure-network.md) för information om dessa objekt:

  * Verifierar DNS-och NTP-konfiguration
  * Konfigurera katalog tjänster, om det behövs
  * Konfigurera VLAN
  * Konfigurera proxyservrar
  * Lägga till IP-adresser i kluster nätverket
  * Lagra krypterings certifikat

* [Konfigurera support övervakning](#enable-support)

  Du måste acceptera sekretess policyn för konfigurations verktyget och du bör konfigurera inställningarna för överförings överföring på samma tidpunkt.

  Klustret kan automatiskt ladda upp fel söknings data om klustret, inklusive statistik och fel sökning av filer. De här överföringarna gör att Microsofts kund service och support tillhandahåller bästa möjliga tjänst. Du kan anpassa vad som övervakas och eventuellt aktivera förebyggande fel söknings tjänsten.  

### <a name="optional-configuration"></a>Valfri konfiguration

De här stegen krävs inte för alla kluster. De behövs för vissa typer av arbets flöden eller för vissa kluster hanterings format.

* Anpassa Node-inställningar

  Du kan ange nodnamn och konfigurera nod-IPMI-portar på en nivå med hela klustret eller individuellt. Om du konfigurerar de här inställningarna innan du lägger till noder i klustret, kan de nya noderna hämta inställningarna automatiskt när de ansluter. Alternativen beskrivs i det äldre dokumentet för att skapa kluster [anpassar Node-inställningarna](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_node.html).

  > [!TIP]
  > Viss dokumentation för den här produkten är ännu inte tillgänglig på Microsoft Azure dokumentations webbplats. Länkar till [guiden kluster konfiguration](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) och den äldre versionen av [guiden skapa kluster](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/create_index.html) tar dig till en separat GitHub-värd webbplats.

* Konfigurera SMB

  Om du vill tillåta SMB-åtkomst till klustret samt NFS måste du konfigurera SMB och aktivera det. SMB (ibland kallat CIFS) används vanligt vis för att stödja Microsoft Windows-klienter.

  Planering och konfiguration av SMB omfattar mer än att klicka på några knappar på kontroll panelen. Beroende på system kraven kan SMB påverka hur du definierar Core-, hur många vservers du skapar, hur du konfigurerar dina Knut punkter och namn område, åtkomst behörigheter och andra inställningar.

  Mer information finns i konfigurations guiden för kluster [Konfigurera SMB-åtkomst](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/smb_overview.html) .

* Installera ytterligare licenser

  Om du vill använda en annan moln lagring än Azure Blob måste du installera en ytterligare funktions licens. Kontakta din Microsoft-representant om du vill ha mer information om att köpa en FlashCloud<sup>TM</sup> -licens. Information beskrivs i [Lägg till backend-lagring och konfigurera virtuell namnrymd](fxt-add-storage.md#about-back-end-storage).

### <a name="enable-support"></a>Aktivera stöd

Azure FXT Edge-klustret kan automatiskt överföra support data om klustret. De här överföringarna gör att personalen ger bästa möjliga kund tjänst.

Följ de här stegen för att konfigurera support överföringar.

1. Gå till sidan Inställningar för **kluster**  >  **support** . Godkänn sekretess policyn.

   ![Skärm bild som visar kontroll panelen och popup-fönstret med knappen Bekräfta för att godkänna sekretess policyn](media/fxt-cluster-create/fxt-privacy-policy.png)

1. Klicka på triangeln till vänster om **kund information** för att expandera avsnittet.
1. Klicka på knappen **Verifiera om upload information** .
1. Ange klustrets support namn i **unikt kluster namn** – se till att det unikt identifierar klustret så att det stöder personal.
1. Markera kryss rutorna för **statistik övervakning**, **allmän informations uppladdning** och **krasch informations uppladdning**.
1. Klicka på **Skicka**.  

   ![Skärm bild som innehåller avsnittet om slutförd kund information på sidan support inställningar](media/fxt-cluster-create/fxt-support-info.png)

1. Klicka på triangeln till vänster om **Secure proactive support (SPS)** för att expandera avsnittet.
1. Markera kryss rutan **Aktivera SPS-länk**.
1. Klicka på **Skicka**.

   ![Skärm bild som innehåller ett slutfört Secure proactive support-avsnitt på sidan stöd inställningar](media/fxt-cluster-create/fxt-support-sps.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat det grundläggande klustret och godkänt sekretess policyn lägger du till resten av klusternoderna.

> [!div class="nextstepaction"]
> [Lägga till klusternoder](fxt-add-nodes.md)
