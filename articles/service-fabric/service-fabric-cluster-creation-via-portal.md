---
title: Skapa ett Service Fabric-kluster i Azure-portalen
description: Lär dig hur du konfigurerar ett säkert Service Fabric kluster i Azure med hjälp av Azure Portal och Azure Key Vault.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: c679a804db09b1034f31e9d8da1f7d2ad206f684
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563734"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Skapa ett Service Fabric kluster i Azure med hjälp av Azure Portal
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure-portalen](service-fabric-cluster-creation-via-portal.md)
> 
> 

Det här är en steg-för-steg-guide som vägleder dig genom stegen för att konfigurera ett Service Fabric kluster (Linux eller Windows) i Azure med hjälp av Azure Portal. Den här guiden vägleder dig genom följande steg:

* Skapa ett kluster i Azure via Azure Portal.
* Autentisera administratörer med certifikat.

> [!NOTE]
> För mer avancerade säkerhets alternativ, till exempel användarautentisering med Azure Active Directory och konfiguration av certifikat för program säkerhet, [skapar du klustret med Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Klustersäkerhet 
Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information om hur du använder certifikat i Service Fabric finns i [Service Fabric cluster security scenarios][service-fabric-cluster-security] (Säkerhet för Service Fabric-kluster).

Om det här är första gången du skapar ett Service Fabric-kluster eller distribuerar ett kluster för test arbets belastningar kan du gå vidare till nästa avsnitt (**skapa kluster i Azure Portal**) och låta systemet generera certifikat som krävs för kluster som kör test arbets belastningar. Om du konfigurerar ett kluster för produktions arbets belastningar fortsätter du att läsa.

#### <a name="cluster-and-server-certificate-required"></a>Kluster-och Server certifikat (obligatoriskt)
Detta certifikat krävs för att skydda ett kluster och förhindra obehörig åtkomst till det. Den ger kluster säkerhet på ett par olika sätt:

* **Autentisering av kluster:** Autentiserar nod-till-nod-kommunikation för kluster Federation. Endast noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret.
* Serverautentisering **:** Autentiserar kluster hanterings slut punkter till en hanterings klient, så att hanterings klienten vet att den kommunicerar med det riktiga klustret. Det här certifikatet tillhandahåller även TLS för HTTPS Management API och för Service Fabric Explorer över HTTPS.

För att kunna hantera dessa måste certifikatet uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckel utbyte, exporteras till en personal information Exchange-fil (. pfx).
* Certifikatets **ämnes namn måste matcha den domän** som används för att komma åt Service Fabric klustret. Detta krävs för att tillhandahålla TLS för klustrets slut punkter för HTTPS-hantering och Service Fabric Explorer. Det går inte att hämta ett TLS/SSL-certifikat från en certifikat utfärdare (CA) för `.cloudapp.azure.com` domänen. Hämta ett anpassat domän namn för klustret. När du begär ett certifikat från en certifikat utfärdare måste certifikatets ämnes namn matcha det anpassade domän namn som används för klustret.

#### <a name="client-authentication-certificates"></a>Certifikat för klientautentisering
Ytterligare klient certifikat autentisera administratörer för kluster hanterings aktiviteter. Service Fabric har två åtkomst nivåer: **admin** och **skrivskyddad användare**. Ett enskilt certifikat för administrativ åtkomst bör vara minst. För ytterligare åtkomst på användar nivå måste ett separat certifikat tillhandahållas. Mer information om åtkomst roller finns i [rollbaserad åtkomst kontroll för Service Fabric klienter][service-fabric-cluster-security-roles].

Du behöver inte ladda upp certifikat för klientautentisering till Key Vault för att arbeta med Service Fabric. Dessa certifikat behöver endast tillhandahållas till användare som har behörighet för kluster hantering. 

> [!NOTE]
> Azure Active Directory är det rekommenderade sättet att autentisera klienter för kluster hanterings åtgärder. Om du vill använda Azure Active Directory måste du [skapa ett kluster med hjälp av Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Program certifikat (valfritt)
Valfritt antal ytterligare certifikat kan installeras i ett kluster för program säkerhets syfte. Innan du skapar klustret bör du tänka på vilka program säkerhets scenarier som kräver att ett certifikat installeras på noderna, till exempel:

* Kryptering och dekryptering av program konfigurations värden
* Kryptering av data mellan noder under replikering 

Det går inte att konfigurera program certifikat när [du skapar ett kluster via Azure Portal](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Om du vill konfigurera program certifikat när klustret konfigureras måste du [skapa ett kluster med hjälp av Azure Resource Manager][create-cluster-arm]. Du kan också lägga till program certifikat i klustret när det har skapats.

## <a name="create-cluster-in-the-azure-portal"></a>Skapa kluster i Azure Portal

Att skapa ett produktions kluster som passar dina program behov innefattar viss planering, som hjälper dig med det, rekommenderar vi starkt att du läser och förstår det [Service Fabric kluster planerings][service-fabric-cluster-capacity] dokumentet. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Sök efter Service Fabric kluster resursen

Logga in på [Azure-portalen][azure-portal].
Klicka på **skapa en resurs** för att lägga till en ny resurs mal len. Sök efter Service Fabric Cluster-mallen i **Marketplace** under **allt**.
Välj **Service Fabric kluster** i listan.

![Sök efter Service Fabric Cluster-mall på Azure Portal.][SearchforServiceFabricClusterTemplate]

Gå till bladet **Service Fabric kluster** och klicka på **skapa**.

Bladet **skapa Service Fabric kluster** har följande fyra steg:

### <a name="1-basics"></a>1. grundläggande information
![Skärm bild som visar hur du skapar en ny resurs grupp.][CreateRG]

På bladet grundläggande måste du ange grundläggande information om klustret.

1. Ange namnet på klustret.
2. Ange ett **användar namn** och **lösen ord** för fjärr skrivbord för de virtuella datorerna.
3. Se till att välja den **prenumeration** som du vill att klustret ska distribueras till, särskilt om du har flera prenumerationer.
4. Skapa en ny **resurs grupp**. Det är bäst att ge det samma namn som klustret, eftersom det hjälper till att hitta dem senare, särskilt när du försöker göra ändringar i distributionen eller ta bort klustret.
   
   > [!NOTE]
   > Även om du kan välja att använda en befintlig resurs grupp, är det en bra idé att skapa en ny resurs grupp. Detta gör det enkelt att ta bort kluster och alla resurser som används.
   > 
   > 
5. Välj den **plats** där du vill skapa klustret. Om du planerar att använda ett befintligt certifikat som du redan har laddat upp till ett nyckel valv måste du använda samma region som nyckel valvet. 

### <a name="2-cluster-configuration"></a>2. kluster konfiguration
![Skapa en nodtyp][CreateNodeType]

Konfigurera klusternoderna. Nodtyper definierar VM-storlekar, antalet virtuella datorer och deras egenskaper. Klustret kan ha fler än en nodtyp, men den primära nodtypen (den första som du definierar på portalen) måste ha minst fem virtuella datorer, eftersom det här är nodtypen där Service Fabric system tjänster placeras. Konfigurera inte **placerings egenskaper** eftersom en standard placerings egenskap för "NodeTypeName" läggs till automatiskt.

> [!NOTE]
> Ett vanligt scenario för flera nodtyper är ett program som innehåller en front-end-tjänst och en backend-tjänst. Du vill placera frontend-tjänsten på mindre virtuella datorer (VM-storlekar som D2_V2) med portar öppna på Internet och placera backend-tjänsten på större virtuella datorer (med VM-storlekar som D3_V2, D6_V2, D15_V2 och så vidare) utan att några portar öppnas på Internet.
> 

1. Välj ett namn för nodtypen (1 till 12 tecken som bara innehåller bokstäver och siffror).
2. Minimi **storleken** på virtuella datorer för den primära nodtypen styrs av den **hållbarhets nivå** som du väljer för klustret. Standardvärdet för hållbarhets nivån är brons. Mer information om hållbarhet finns i [så här väljer du Service Fabric klustrets hållbarhet][service-fabric-cluster-durability].
3. Välj **storlek på virtuell dator**. Virtuella datorer i D-serien har SSD-enheter och rekommenderas för tillstånds känsliga program. Använd inte några VM-SKU: er som har delar av kärnor eller färre än 10 GB ledigt disk utrymme. Mer information om hur du väljer VM-storlek hittar du i [planerings dokumentet för Service Fabric-kluster][service-fabric-cluster-capacity] .
4.  **Kluster med en nod och tre nod kluster** är avsedda för endast test användning. De stöds inte för produktions arbets belastningar som körs.
5. Välj den **ursprungliga skalnings uppsättnings kapaciteten för virtuella datorer** för nodtypen. Du kan skala in eller ut antalet virtuella datorer i en nodtyp senare, men på den primära nodtypen är minimivärdet fem för produktions arbets belastningar. Andra nodtyper kan ha minst en virtuell dator. Det minsta **antalet** virtuella datorer för den primära nodtypen styr klustrets **tillförlitlighet** .  
6. Konfigurera **anpassade slut punkter**. Med det här fältet kan du ange en kommaavgränsad lista med portar som du vill exponera genom Azure Load Balancer till det offentliga Internet för dina program. Om du till exempel planerar att distribuera ett webb program till klustret anger du "80" här för att tillåta trafik på port 80 till klustret. Mer information om slut punkter finns i [kommunicera med program][service-fabric-connect-and-communicate-with-services]
7. **Aktivera omvänd proxy**.  [Service Fabric omvänd proxy](service-fabric-reverseproxy.md) hjälper mikrotjänster som körs i ett Service Fabric-kluster att identifiera och kommunicera med andra tjänster som har http-slutpunkter.
8. Gå tillbaka till bladet **kluster konfiguration** under **+ Visa valfria inställningar**, konfigurera kluster **diagnos**. Som standard är diagnostik aktiverat på klustret för att hjälpa till med fel söknings problem. Om du vill inaktivera diagnostik ändrar du **statusen** växla till **av**. Du rekommenderas **inte** att stänga av diagnostik. Om du redan har skapat Application Insights projekt kan du ge dess nyckel, så att program spårningarna dirigeras till den.
9. **Ta med DNS-tjänst**.  [DNS-tjänsten](service-fabric-dnsservice.md) en valfri tjänst som gör att du kan hitta andra tjänster med hjälp av DNS-protokollet.
10. Välj det **uppgraderings läge för Fabric** som du vill konfigurera klustret till. Välj **Automatisk** om du vill att systemet automatiskt ska hämta den senaste tillgängliga versionen och försöka uppgradera klustret till det. Ange läget **manuellt** om du vill välja en version som stöds. Mer information om uppgraderings läget för Fabric finns i [Service Fabric Cluster Upgrade Document.][service-fabric-cluster-upgrade]

> [!NOTE]
> Vi stöder bara kluster som kör Service Fabric-versioner som stöds. Genom att välja det **manuella** läget tar du ansvar för att uppgradera klustret till en version som stöds.
> 

### <a name="3-security"></a>3. säkerhet
![Skärm bild av säkerhetskonfigurationer på Azure Portal.][BasicSecurityConfigs]

Vi har angett det **grundläggande** alternativet för att göra det enkelt för dig att konfigurera ett säkert test kluster. Om du redan har ett certifikat och har laddat upp det till ditt [nyckel valv](../key-vault/index.yml) (och aktiverat nyckel valvet för distribution), använder du det **anpassade** alternativet

#### <a name="basic-option"></a>Grundläggande alternativ
Följ skärmarna för att lägga till eller återanvända ett befintligt nyckel valv och lägga till ett certifikat. Tillägget av certifikatet är en synkron process, så du måste vänta tills certifikatet har skapats.

Flytta bort frestelsenen från skärmen tills föregående process har slutförts.

![Skärm bild som visar sidan säkerhet med Basic valt med nyckel valv fönstret och fönstret Skapa nyckel valv.][CreateKeyVault]

Nu när nyckel valvet har skapats redigerar du åtkomst principerna för nyckel valvet. 

![Skärm bild som visar fönstret Skapa Service Fabric kluster med alternativ 3 säkerhet markerat och en förklaring till att nyckel valvet inte har Aktiver ATS.][CreateKeyVault2]

Klicka på **Redigera åtkomst principer** och Visa sedan **avancerade åtkomst principer** och aktivera åtkomst till Azure-Virtual Machines för distribution. Vi rekommenderar att du aktiverar mall distributionen också. När du har gjort dina val ska du inte glömma att klicka på knappen **Spara** och stänga av fönstret **åtkomst principer** .

![Skärm bild som visar fönstret Skapa Service Fabric kluster med fönstret säkerhet öppet och fönstret åtkomst principer öppen.][CreateKeyVault3]

Ange certifikatets namn och klicka på **OK**.

![Skärm bild som visar fönstret Skapa Service Fabric kluster med säkerhet valt som tidigare men utan förklaringen att nyckel valvet inte är aktiverat.][CreateKeyVault4]

#### <a name="custom-option"></a>Anpassat alternativ
Hoppa över det här avsnittet om du redan har utfört stegen i **Basic** -alternativet.

![Skärm bild som visar dialog rutan säkerhet Konfigurera kluster säkerhets inställningar.][SecurityCustomOption]

Du behöver käll nyckel valvet, certifikatets URL och certifikatets tumavtryck för att slutföra säkerhets sidan. Om du inte har det kan du öppna ett nytt webbläsarfönster och i Azure Portal göra följande

1. Navigera till Key Vault-tjänsten.
2. Välj fliken "egenskaper" och kopiera "resurs-ID" till "käll nyckel valvet" i det andra webbläsarfönstret 

    ![Skärm bild som visar Fönstret Egenskaper för nyckel valvet.][CertInfo0]

3. Välj nu fliken "certifikat".
4. Klicka på tumavtryck för certifikatet, som tar dig till sidan versioner.
5. Klicka på de GUID som du ser under den aktuella versionen.

    ![Skärm bild som visar certifikat fönstret för nyckel valvet][CertInfo1]

6. Nu bör du vara på skärmen som nedan. Kopiera det hexadecimala SHA-1-tumavtrycket till "certifikat-tumavtryck" i det andra webbläsarfönstret
7. Kopiera "hemlig identifierare" till "certifikatets URL" i ett annat webbläsarfönster.

    ![Skärm bild som visar dialog rutan certifikat version med ett alternativ för att kopiera certifikat-ID.][CertInfo2]

Markera kryss rutan **Konfigurera avancerade inställningar** om du vill ange klient certifikat för **Administratörs klienten** och den **skrivskyddade klienten**. I de här fälten anger du tumavtrycket för ditt administratörs klient certifikat och tumavtrycket för det skrivskyddade användar klient certifikatet, om tillämpligt. När administratörer försöker ansluta till klustret beviljas de åtkomst endast om de har ett certifikat med ett tumavtryck som matchar de tumavtryck som anges här.  

### <a name="4-summary"></a>4. Sammanfattning

Nu är du redo att distribuera klustret. Innan du gör det kan du hämta certifikatet och titta i den stora informations rutan för länken. Se till att hålla certifikatet på en säker plats. du behöver den för att ansluta till klustret. Eftersom det certifikat du hämtade inte har ett lösen ord, bör du lägga till ett.

Klicka på **skapa** om du vill slutföra skapandet av klustret. Du kan också hämta mallen.

![Skärm bild som visar sidan Skapa Service Fabric kluster Sammanfattning med en länk för att visa och hämta ett certifikat.][Summary]

Du kan se förloppet bland aviseringarna. (Klicka på ikonen "Bell" nära statusfältet överst till höger på skärmen.) Om du klickade på **Fäst på Start sidan** när du skapade klustret, ser du **distribuera Service Fabric kluster** fäst till **Start** kortet. Den här processen kan ta lite tid. 

För att kunna utföra hanterings åtgärder på klustret med hjälp av PowerShell eller CLI måste du ansluta till klustret och läsa mer om hur du [ansluter till klustret](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Visa kluster status
![Skärm bild av kluster information på instrument panelen.][ClusterDashboard]

När klustret har skapats kan du inspektera klustret i portalen:

1. Gå till **Bläddra** och klicka på **Service Fabric kluster**.
2. Leta upp klustret och klicka på det.
3. Där kan du se information om klustret på instrumentpanelen, bland annat klustrets offentliga slutpunkter och en länk till Service Fabric Explorer.

Avsnittet **Node Monitor** på bladet för klustrets instrument panel anger antalet virtuella datorer som är felfria och inte felfria. Du hittar mer information om klustrets hälsa på [Service Fabric hälso modell introduktion][service-fabric-health-introduction].

> [!NOTE]
> Service Fabric kluster kräver att ett visst antal noder alltid är igång för att upprätthålla tillgänglighet och bevara tillstånd – kallas "Kvarhåll kvorum". Därför är det vanligt vis inte säkert att stänga av alla datorer i klustret om du inte har gjort en [fullständig säkerhets kopia av ditt tillstånd][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Fjärrans luta till en instans av en skalnings uppsättning för virtuell dator eller en klusternod
Var och en av de NodeTypes som du anger i klustret resulterar i en konfiguration av en skalnings uppsättning för virtuella datorer. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster som använder certifikat för hanterings autentisering. Anslut sedan [till klustret](service-fabric-connect-to-secure-cluster.md) och lär dig hur du [hanterar program hemligheter](service-fabric-application-secret-management.md).  Läs även om [Service Fabric support alternativ](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: /powershell/azure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
