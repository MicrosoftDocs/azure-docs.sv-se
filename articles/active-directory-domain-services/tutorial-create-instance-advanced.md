---
title: Självstudie – Skapa en anpassad Azure Active Directory Domain Services hanterad domän | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar och konfigurerar en anpassad Azure Active Directory Domain Services hanterad domän och anger avancerade konfigurations alternativ med hjälp av Azure Portal.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4d03edac98329d7c47b8bfafbf4d30cfca18863c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618237"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain-with-advanced-configuration-options"></a>Självstudie: skapa och konfigurera en Azure Active Directory Domain Services hanterad domän med avancerade konfigurations alternativ

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibelt med Windows Server Active Directory. Du använder dessa domän tjänster utan att distribuera, hantera och korrigera domänkontrollanter själv. Azure AD DS integreras med din befintliga Azure AD-klient. Med den här integreringen kan användarna logga in med sina företags uppgifter, och du kan använda befintliga grupper och användar konton för att skydda åtkomsten till resurser.

Du kan [skapa en hanterad domän med hjälp av standard konfigurations alternativ][tutorial-create-instance] för nätverk och synkronisering, eller definiera inställningarna manuellt. Den här självstudien visar hur du definierar de avancerade konfigurations alternativen för att skapa och konfigurera en Azure AD DS-hanterad domän med hjälp av Azure Portal.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera inställningar för DNS och virtuellt nätverk för en hanterad domän
> * Skapa en hanterad domän
> * Lägga till administrativa användare till domän hantering
> * Aktivera hashsynkronisering för lösenord

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att aktivera Azure AD DS.
* Du behöver *deltagar* behörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.

Även om det inte krävs för Azure AD DS rekommenderar vi att du [konfigurerar återställning av lösen ord för självbetjäning (SSPR)][configure-sspr] för Azure AD-klienten. Användare kan ändra sina lösen ord utan SSPR, men SSPR hjälper om de glömmer bort sitt lösen ord och behöver återställa det.

> [!IMPORTANT]
> När du har skapat en hanterad domän kan du inte flytta den hanterade domänen till en annan resurs grupp, ett virtuellt nätverk, en prenumeration osv. Var noga med att välja den lämpligaste prenumerationen, resurs gruppen, regionen och det virtuella nätverket när du distribuerar den hanterade domänen.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du och konfigurerar den hanterade domänen med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="create-a-managed-domain-and-configure-basic-settings"></a>Skapa en hanterad domän och konfigurera grundläggande inställningar

Utför följande steg för att starta guiden för att **aktivera Azure AD Domain Services** :

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
1. Ange *domän tjänster* i Sök fältet och välj sedan *Azure AD Domain Services* från Sök förslagen.
1. På sidan Azure AD Domain Services väljer du **skapa**. Guiden **aktivera Azure AD Domain Services** startas.
1. Välj den Azure- **prenumeration** där du vill skapa den hanterade domänen.
1. Välj den **resurs grupp** som den hanterade domänen ska tillhöra. Välj att **skapa en ny** eller Välj en befintlig resurs grupp.

När du skapar en hanterad domän anger du ett DNS-namn. Det finns några saker att tänka på när du väljer det här DNS-namnet:

* **Inbyggt domän namn:** Som standard används det inbyggda domän namnet för katalogen (a *. onmicrosoft.com* suffix). Om du vill aktivera säker LDAP-åtkomst till den hanterade domänen via Internet kan du inte skapa ett digitalt certifikat för att skydda anslutningen till den här standard domänen. Microsoft äger *onmicrosoft.com* -domänen så att en certifikat utfärdare (ca) inte utfärdar ett certifikat.
* **Anpassade domän namn:** Det vanligaste tillvägagångs sättet är att ange ett anpassat domän namn, vanligt vis ett som du redan äger och som är dirigerbart. När du använder en dirigerbart, anpassad domän kan trafik flöda korrekt efter behov för att stödja dina program.
* **Icke-dirigerbart domänsuffix:** Vi rekommenderar vanligt vis att du undviker ett icke-dirigerbart domänsuffix, t. ex. *contoso. local*. *Lokalt* suffix kan inte dirigeras och kan orsaka problem med DNS-matchning.

> [!TIP]
> Om du skapar ett eget domän namn bör du ta hand om befintliga DNS-namnområden. Vi rekommenderar att du använder ett domän namn separat från ett befintligt Azure eller lokalt DNS-adressutrymme.
>
> Om du till exempel har ett befintligt DNS-namnområdet *contoso.com* skapar du en hanterad domän med det anpassade domän namnet för *aaddscontoso.com*. Om du behöver använda säker LDAP måste du registrera och äga det här anpassade domän namnet för att generera nödvändiga certifikat.
>
> Du kan behöva skapa ytterligare DNS-poster för andra tjänster i din miljö, eller villkorliga DNS-vidarebefordrare mellan befintliga DNS-namn utrymmen i din miljö. Om du till exempel kör en webb server som är värd för en plats som använder rot-DNS-namnet, kan det finnas namn konflikter som kräver ytterligare DNS-poster.
>
> I dessa självstudier och instruktions artiklar används den anpassade domänen för *aaddscontoso.com* som ett kort exempel. I alla kommandon anger du ditt eget domän namn.

Följande DNS-namn begränsningar gäller också:

* **Begränsningar för domänautentiseringsuppgifter:** Du kan inte skapa en hanterad domän med ett prefix som är längre än 15 tecken. Prefixet för det angivna domän namnet (t. ex. *aaddscontoso* i domän namnet *aaddscontoso.com* ) måste innehålla högst 15 tecken.
* **Nätverks namns konflikter:** DNS-domännamnet för den hanterade domänen bör inte redan finnas i det virtuella nätverket. Mer specifikt kan du söka efter följande scenarier som leder till en namn konflikt:
    * Om du redan har en Active Directory domän med samma DNS-domännamn i det virtuella Azure-nätverket.
    * Om det virtuella nätverket där du planerar att aktivera den hanterade domänen har en VPN-anslutning till ditt lokala nätverk. I det här scenariot ser du till att du inte har en domän med samma DNS-domännamn i det lokala nätverket.
    * Om du har en befintlig Azure-moln tjänst med det namnet i det virtuella Azure-nätverket.

Fyll i fälten i fönstret *grundläggande* för Azure Portal om du vill skapa en hanterad domän:

1. Ange ett **DNS-domännamn** för din hanterade domän, och ta hänsyn till föregående punkter.
1. Välj den Azure- **plats** där den hanterade domänen ska skapas. Om du väljer en region som stöder Tillgänglighetszoner fördelas Azure AD DS-resurserna mellan zoner för ytterligare redundans.

    > [!TIP]
    > Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner.
    >
    > Det finns inget som du kan konfigurera för att Azure AD DS ska distribueras mellan zoner. Azure-plattformen hanterar automatiskt zon distributionen av resurser. Mer information och mer information om regions tillgänglighet finns i [Vad är Tillgänglighetszoner i Azure?][availability-zones]

1. **SKU: n** bestämmer prestanda, säkerhets kopierings frekvens och maximalt antal skogs förtroenden som du kan skapa. Du kan ändra SKU efter att den hanterade domänen har skapats om din verksamhet kräver eller att kraven förändras. Mer information finns i [Azure AD DS SKU-koncept][concepts-sku].

    I den här självstudien väljer du *standard* -SKU.
1. En *skog* är en logisk konstruktion som används av Active Directory Domain Services för att gruppera en eller flera domäner. Som standard skapas en hanterad domän som en *användar* skog. Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användar konton som skapats i en lokal AD DS-miljö.

    En *resurs* skog synkroniserar bara användare och grupper som skapats direkt i Azure AD. Lösen ords-hashar för lokala användare synkroniseras aldrig till en hanterad domän när du skapar en resurs skog. Mer information om *resurs* skogar, inklusive varför du kan använda en och hur du skapar skogs förtroenden med lokala AD DS-domäner finns i [Översikt över Azure AD DS resurs skogar][resource-forests].

    I den här självstudien väljer du att skapa en *användar* skog.

    ![Konfigurera grundläggande inställningar för en Azure AD Domain Services hanterad domän](./media/tutorial-create-instance-advanced/basics-window.png)

1. Välj **Nästa nätverk** om du vill konfigurera ytterligare alternativ manuellt. Annars väljer du **Granska + skapa** för att acceptera standard konfigurations alternativen och fortsätter sedan till avsnittet för att [distribuera den hanterade domänen](#deploy-the-managed-domain). Följande standardinställningar konfigureras när du väljer det här alternativet för att skapa:

    * Skapar ett virtuellt nätverk med namnet *aadds-VNet* som använder IP-adressintervallet *10.0.1.0/24*.
    * Skapar ett undernät med namnet *aadds-Subnet* med IP-adressintervallet *10.0.1.0/24*.
    * Synkroniserar *alla* användare från Azure AD till den hanterade domänen.

## <a name="create-and-configure-the-virtual-network"></a>Skapa och konfigurera det virtuella nätverket

För att tillhandahålla anslutning behövs ett virtuellt Azure-nätverk och ett dedikerat undernät. Azure AD DS är aktiverat i det här virtuella nätverkets undernät. I den här självstudien skapar du ett virtuellt nätverk, men du kan istället välja att använda ett befintligt virtuellt nätverk. I båda metoderna måste du skapa ett dedikerat undernät som ska användas av Azure AD DS.

Några saker att tänka på för det här dedikerade virtuella nätverks under nätet är följande områden:

* Under nätet måste ha minst 3-5 tillgängliga IP-adresser i adress intervallet för att stödja Azure AD DS-resurserna.
* Välj inte *Gateway* -undernätet för distribution av Azure AD DS. Det finns inte stöd för att distribuera Azure AD DS till ett *Gateway* -undernät.
* Distribuera inte några andra virtuella datorer till under nätet. Program och virtuella datorer använder ofta nätverks säkerhets grupper för att skydda anslutningen. Genom att köra dessa arbets belastningar i ett separat undernät kan du tillämpa dessa nätverks säkerhets grupper utan att avbryta anslutningen till din hanterade domän.
* Du kan inte flytta din hanterade domän till ett annat virtuellt nätverk när du har aktiverat Azure AD DS.

Mer information om hur du planerar och konfigurerar det virtuella nätverket finns i [nätverks överväganden för Azure Active Directory Domain Services][network-considerations].

Fyll i fälten i fönstret *nätverk* enligt följande:

1. På sidan **nätverk** väljer du ett virtuellt nätverk för att distribuera Azure AD DS till i den nedrullningsbara menyn eller väljer **Skapa nytt**.
    1. Om du väljer att skapa ett virtuellt nätverk anger du ett namn för det virtuella nätverket, till exempel *myVnet*, och anger sedan ett adress intervall, t. ex. *10.0.1.0/24*.
    1. Skapa ett dedikerat undernät med ett tydligt namn, till exempel *DomainServices*. Ange ett adress intervall, t. ex. *10.0.1.0/24*.

    [![Skapa ett virtuellt nätverk och undernät som ska användas med Azure AD Domain Services](./media/tutorial-create-instance-advanced/create-vnet.png)](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Se till att välja ett adress intervall som ligger inom ditt privata IP-adressintervall. IP-adressintervall som du inte äger som finns i det offentliga adress utrymmet orsakar fel i Azure AD DS.

1. Välj ett undernät för virtuellt nätverk, till exempel *DomainServices*.
1. När du är klar väljer du **Nästa administration**.

## <a name="configure-an-administrative-group"></a>Konfigurera en administrativ grupp

En särskild administrativ grupp med namnet *AAD DC-administratörer* används för hantering av Azure AD DS-domänen. Medlemmar i den här gruppen beviljas administrativa behörigheter på virtuella datorer som är domänanslutna till den hanterade domänen. På domänanslutna virtuella datorer läggs gruppen till i den lokala gruppen Administratörer. Medlemmar i den här gruppen kan också använda fjärr skrivbord för att fjärrans luta till domänanslutna virtuella datorer.

> [!IMPORTANT]
> Du har inte *domän administratörs* -eller *företags administratörs* behörighet för en hanterad domän med hjälp av Azure AD DS. Dessa behörigheter är reserverade för tjänsten och görs inte tillgängliga för användare i klienten.
>
> I stället kan du använda gruppen *AAD DC-administratörer* för att utföra vissa privilegierade åtgärder. Dessa åtgärder omfattar att tillhöra administrations gruppen på domänanslutna virtuella datorer och konfigurera grupprincip.

Guiden skapar automatiskt gruppen *AAD DC-administratörer* i Azure AD-katalogen. Om du har en befintlig grupp med det här namnet i din Azure AD-katalog väljer guiden den här gruppen. Du kan välja att lägga till ytterligare användare till den här *Administratörs* gruppen för AAD-domänkontrollanter under distributions processen. De här stegen kan utföras senare.

1. Om du vill lägga till ytterligare användare i denna *Administratörs* grupp för AAD-domänkontrollanter väljer du **Hantera grupp medlemskap**.

    ![Konfigurera grupp medlemskap för administratörs gruppen för AAD-domänkontrollanter](./media/tutorial-create-instance-advanced/admin-group.png)

1. Välj knappen **Lägg till medlemmar** och Sök sedan efter och välj användare från Azure AD-katalogen. Sök till exempel efter ditt eget konto och Lägg till det i *Administratörs gruppen för AAD-domänkontrollanten* .
1. Om du vill kan du ändra eller lägga till ytterligare mottagare för aviseringar när det finns aviseringar i den hanterade domänen som kräver uppmärksamhet.
1. När du är klar väljer du **nästa synkronisering**.

## <a name="configure-synchronization"></a>Konfigurera synkronisering

Med Azure AD DS kan du synkronisera *alla* användare och grupper som är tillgängliga i Azure AD, eller en *begränsad* synkronisering av enbart vissa grupper. Du kan ändra omfånget för synkronisering nu eller när den hanterade domänen har distribuerats. Mer information finns i [Azure AD Domain Services omfångs synkronisering][scoped-sync].

1. I den här självstudien väljer du att synkronisera **alla** användare och grupper. Det här valet av synkronisering är standard alternativet.

    ![Utföra en fullständig synkronisering av användare och grupper från Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Välj **Granska + skapa**.

## <a name="deploy-the-managed-domain"></a>Distribuera den hanterade domänen

Granska konfigurations inställningarna för din hanterade domän på sidan **Sammanfattning** i guiden. Du kan gå tillbaka till valfritt steg i guiden för att göra ändringar. Om du vill distribuera om en hanterad domän till en annan Azure AD-klient på ett konsekvent sätt med hjälp av dessa konfigurations alternativ kan du också **Ladda ned en mall för automatisering**.

1. Välj **skapa** för att skapa den hanterade domänen. En anteckning visas att vissa konfigurations alternativ som DNS-namn eller virtuellt nätverk inte kan ändras när Azure AD DS Managed har skapats. Fortsätt genom att välja **OK**.
1. Processen för etablering av din hanterade domän kan ta upp till en timme. Ett meddelande visas i portalen som visar förloppet för din Azure AD DS-distribution. Välj aviseringen om du vill visa detaljerad förloppet för distributionen.

    ![Meddelande i pågående distributions Azure Portal](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Välj din resurs grupp, till exempel *myResourceGroup*, och välj sedan din hanterade domän i listan över Azure-resurser, till exempel *aaddscontoso.com*. Fliken **Översikt** visar att den hanterade domänen för närvarande *distribuerar*. Du kan inte konfigurera den hanterade domänen förrän den är helt etablerad.

    ![Status för domän tjänster under etablerings tillståndet](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. När den hanterade domänen är helt etablerad visar fliken **Översikt** den domän status som *körs*.

    ![Status för domän tjänster när den har kon figurer ATS](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

> [!IMPORTANT]
> Den hanterade domänen är kopplad till din Azure AD-klient. Under etablerings processen skapar Azure AD DS två företags program med namnet *domänkontrollant tjänster* och *AZUREACTIVEDIRECTORYDOMAINCONTROLLERSERVICES* i Azure AD-klienten. De här företags programmen behövs för att underhålla din hanterade domän. Ta inte bort de här programmen.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Uppdatera DNS-inställningarna för det virtuella Azure-nätverket

När Azure AD DS har distribuerats kan du nu konfigurera det virtuella nätverket så att andra anslutna virtuella datorer och program kan använda den hanterade domänen. För att tillhandahålla den här anslutningen ska du uppdatera DNS-serverinställningarna för ditt virtuella nätverk så att de pekar på de två IP-adresserna där den hanterade domänen är distribuerad.

1. På fliken **Översikt** för din hanterade domän visas några **nödvändiga konfigurations steg**. Det första konfigurations steget är att uppdatera DNS-serverinställningarna för ditt virtuella nätverk. När DNS-inställningarna har kon figurer ATS korrekt visas inte längre det här steget.

    Adresserna i listan är domän kontrol Lanterna som används i det virtuella nätverket. I det här exemplet är dessa adresser *10.0.1.4* och *10.0.1.5*. Du kan senare hitta de här IP-adresserna på fliken **Egenskaper** .

    ![Konfigurera DNS-inställningar för ditt virtuella nätverk med Azure AD Domain Services IP-adresser](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Om du vill uppdatera DNS-serverinställningar för det virtuella nätverket väljer du knappen **Konfigurera** . DNS-inställningarna konfigureras automatiskt för det virtuella nätverket.

> [!TIP]
> Om du har valt ett befintligt virtuellt nätverk i föregående steg får alla virtuella datorer som är anslutna till nätverket bara de nya DNS-inställningarna efter en omstart. Du kan starta om virtuella datorer med Azure Portal, Azure PowerShell eller Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Aktivera användar konton för Azure AD DS

För att autentisera användare på den hanterade domänen behöver Azure AD DS lösen ords-hashar i ett format som är lämpligt för NT LAN Manager (NTLM) och Kerberos-autentisering. Azure AD genererar eller lagrar inte lösen ordets hash-värden i det format som krävs för NTLM-eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhets skäl lagrar Azure AD inte heller lösen ords referenser i klartext-format. Därför kan inte Azure AD automatiskt generera dessa NTLM-eller Kerberos-hashvärden utifrån användarnas befintliga autentiseringsuppgifter.

> [!NOTE]
> När det är korrekt konfigurerat lagras de användbara lösen ords hasharna i den hanterade domänen. Om du tar bort den hanterade domänen, raderas även alla lösen ords-hashar som lagras i den punkten.
>
> Det går inte att använda synkroniserad autentiseringsinformation i Azure AD igen om du senare skapar en hanterad domän – du måste konfigurera om lösen ordets hash-synkronisering för att lagra lösen ordets hashs igen. Tidigare domänanslutna virtuella datorer eller användare kan inte omedelbart autentisera – Azure AD behöver generera och lagra lösen ordets hash-värden i den nya hanterade domänen.
>
> Mer information finns i [synkronisering av lösen ords-hash för Azure AD DS och Azure AD Connect][password-hash-sync-process].

Stegen för att generera och lagra dessa lösen ords-hashar skiljer sig åt för molnbaserade användar konton som skapats i Azure AD kontra användar konton som synkroniseras från din lokala katalog med hjälp av Azure AD Connect.

Ett endast molnbaserat användarkonto är ett konto som skapats i Azure AD-katalogen med antingen Azure Portal eller Azure AD PowerShell-cmdletar. Dessa användar konton synkroniseras inte från en lokal katalog.

I den här självstudien får vi arbeta med ett grundläggande användar konto i molnet. Mer information om de ytterligare steg som krävs för att använda Azure AD Connect finns i [Synkronisera lösen ords-hashar för användar konton som synkroniserats från din lokala AD till din hanterade domän][on-prem-sync].

> [!TIP]
> Om din Azure AD-klient har en kombination av endast molnbaserade användare och användare från din lokala AD måste du slutföra båda uppsättningarna.

För endast molnbaserade användar konton måste användare ändra sina lösen ord innan de kan använda Azure AD DS. Den här processen för lösen ords ändring gör att lösen ords hashar för Kerberos-och NTLM-autentisering skapas och lagras i Azure AD. Kontot är inte synkroniserat från Azure AD till Azure AD DS förrän lösen ordet har ändrats. Du kan antingen ange lösen orden för alla moln användare i klienten som behöver använda Azure AD DS, vilket tvingar lösen ords ändring vid nästa inloggning eller instruera moln användare att manuellt ändra sina lösen ord. I den här självstudien ändrar vi manuellt ett användar lösen ord.

Innan en användare kan återställa sina lösen ord måste Azure AD-klienten [konfigureras för lösen ords återställning via självbetjäning][configure-sspr].

Användaren måste utföra följande steg för att ändra lösen ordet för en endast molnbaserad användare:

1. Gå till Azure AD Access panel-sidan på [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. Välj ditt namn i det övre högra hörnet och välj sedan **profil** på den nedrullningsbara menyn.

    ![Välj profil](./media/tutorial-create-instance-advanced/select-profile.png)

1. På sidan **profil** väljer du **ändra lösen ord**.
1. På sidan **ändra lösen ord** anger du det befintliga (gamla) lösen ordet och anger och bekräftar sedan ett nytt lösen ord.
1. Välj **Skicka**.

Det tar några minuter efter att du har ändrat ditt lösen ord för att det nya lösen ordet ska kunna användas i Azure AD DS och för att logga in på datorer som är anslutna till den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera inställningar för DNS och virtuellt nätverk för en hanterad domän
> * Skapa en hanterad domän
> * Lägga till administrativa användare till domän hantering
> * Aktivera användar konton för Azure AD DS och generera lösen ords-hashar

Om du vill se den här hanterade domänen i praktiken skapar du och ansluter en virtuell dator till domänen.

> [!div class="nextstepaction"]
> [Anslut en virtuell Windows Server-dator till din hanterade domän](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
