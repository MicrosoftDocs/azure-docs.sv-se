---
title: Skapa, Konfigurera Enterprise Security Package kluster – Azure
description: Lär dig hur du skapar och konfigurerar Enterprise Security Package kluster i Azure HDInsight
services: hdinsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: 3e6a5742d2dc8079c36f2d9f39a827f8db99cd67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867176"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Skapa och konfigurera Enterprise Security Package kluster i Azure HDInsight

Enterprise Security Package (ESP) för Azure HDInsight ger dig till gång till Active Directory-baserad autentisering, stöd för flera användare och rollbaserad åtkomst kontroll för dina Apache Hadoop-kluster i Azure. Med HDInsight ESP-kluster kan organisationer som följer strikta företags säkerhets principer bearbeta känsliga data på ett säkert sätt.

Den här guiden visar hur du skapar ett ESP-aktiverat Azure HDInsight-kluster. Det visar också hur du skapar en virtuell Windows IaaS-dator där Active Directory och Domain Name System (DNS) är aktiverade. Använd den här guiden för att konfigurera nödvändiga resurser så att lokala användare kan logga in på ett ESP-aktiverat HDInsight-kluster.

Den server som du skapar fungerar som en ersättning för din *faktiska* lokala miljö. Du använder den för konfigurations-och konfigurations stegen. Senare upprepar du stegen i din egen miljö.

Den här guiden hjälper dig också att skapa en hybrid identitets miljö med hjälp av lösen ords-hash-synkronisering med Azure Active Directory (Azure AD). Guiden kompletterar [användningen av ESP i HDInsight](apache-domain-joined-architecture.md).

Innan du använder den här processen i din egen miljö:

* Konfigurera Active Directory och DNS.
* Aktivera Azure AD.
* Synkronisera lokala användar konton till Azure AD.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png" alt-text="Arkitektur diagram för Azure AD" border="false":::

## <a name="create-an-on-premises-environment"></a>Skapa en lokal miljö

I det här avsnittet ska du använda en distributions mall för Azure snabb start för att skapa nya virtuella datorer, konfigurera DNS och lägga till en ny Active Directory skog.

1. Gå till distributions mal len för snabb start för att [skapa en virtuell Azure-dator med en ny Active Directory skog](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Välj **distribuera till Azure**.
1. Logga in på din Azure-prenumeration.
1. På sidan **skapa en virtuell Azure-dator med en ny AD-skog** anger du följande information:

    |Egenskap | Värde |
    |---|---|
    |Prenumeration|Välj den prenumeration där du vill distribuera resurserna.|
    |Resursgrupp|Välj **Skapa ny** och ange namnet `OnPremADVRG`|
    |Location|Välj en plats.|
    |Administratörens användar namn|`HDIFabrikamAdmin`|
    |Adminlösenord|Ange ett lösen ord.|
    |Domännamn|`HDIFabrikam.com`|
    |DNS-prefix|`hdifabrikam`|

    Lämna kvar standardvärdena.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png" alt-text="Mall för att skapa en virtuell Azure-dator med en ny Azure AD-skog" border="true":::

1. Granska de **allmänna** villkoren och välj sedan **Jag accepterar villkoren som anges ovan**.
1. Välj **köp** och övervaka distributionen och vänta tills den har slutförts. Distributionen tar cirka 30 minuter att slutföra.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurera användare och grupper för kluster åtkomst

I det här avsnittet ska du skapa de användare som ska ha åtkomst till HDInsight-klustret i slutet av den här hand boken.

1. Anslut till domänkontrollanten med hjälp av fjärr skrivbord.
    1. Från Azure Portal navigerar du till **resurs grupper**  >  **OnPremADVRG**  >  **adVM**  >  **Connect**.
    1. Välj den offentliga IP-adressen i list rutan **IP-adress** .
    1. Välj **Ladda ned RDP-fil** och öppna sedan filen.
    1. Använd `HDIFabrikam\HDIFabrikamAdmin` som användar namn.
    1. Ange det lösen ord som du har valt för administratörs kontot.
    1. Välj **OK**.

1. Från domänkontrollantens **Serverhanteraren** instrument panel navigerar du till **verktyg**  >  **Active Directory användare och datorer**.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png" alt-text="Öppna Active Directory hantering på Serverhanteraren instrument panelen" border="true":::

1. Skapa två nya användare: **HDIAdmin** och **HDIUser**. Dessa två användare kommer att logga in på HDInsight-kluster.

    1. På sidan **Active Directory användare och datorer** högerklickar `HDIFabrikam.com` du på och navigerar sedan till **ny**  >  **användare**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png" alt-text="Skapa en ny Active Directory användare" border="true":::

    1. På sidan **nytt objekt – användare** anger du `HDIUser` för **förnamn** och **användarens inloggnings namn**. De andra fälten fylls i automatiskt. Välj sedan **Nästa**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png" alt-text="Skapa det första administratörs användar objektet" border="true":::

    1. I popup-fönstret som visas anger du ett lösen ord för det nya kontot. Välj **lösen ord upphör aldrig att gälla** och sedan **OK** i popup-meddelandet.
    1. Välj **Nästa** och sedan **Slutför** för att skapa det nya kontot.
    1. Upprepa stegen ovan för att skapa användaren `HDIAdmin` .

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png" alt-text="Skapa ett andra administratörs användar objekt" border="true":::

1. Skapa en global säkerhets grupp.

    1. Från **Active Directory användare och datorer** högerklickar du på `HDIFabrikam.com` och navigerar sedan till **ny**  >  **grupp**.

    1. Ange `HDIUserGroup` i text rutan **grupp namn** .

    1. Välj **OK**.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png" alt-text="Skapa en ny Active Directory grupp" border="true":::

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png" alt-text="Skapa ett nytt objekt" border="true":::

1. Lägg till medlemmar i **HDIUserGroup**.

    1. Högerklicka på **HDIUser** och välj **Lägg till i en grupp.**...
    1. I text rutan **Ange de objekt namn som ska väljas** anger du `HDIUserGroup` . Välj **OK** och **OK** igen vid popup-fönstret.
    1. Upprepa föregående steg för **HDIAdmin** -kontot.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png" alt-text="Lägg till medlemmen HDIUser i gruppen HDIUserGroup" border="true":::

Nu har du skapat din Active Directorys miljö. Du har lagt till två användare och en användar grupp som har åtkomst till HDInsight-klustret.

Användarna kommer att synkroniseras med Azure AD.

### <a name="create-an-azure-ad-directory"></a>Skapa en Azure AD-katalog

1. Logga in på Azure-portalen.
1. Välj **skapa en resurs** och skriv `directory` . Välj **Azure Active Directory**  >  **skapa**.
1. Under **organisations namn** anger du `HDIFabrikam` .
1. Under **första domän namn** anger du `HDIFabrikamoutlook` .
1. Välj **Skapa**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png" alt-text="Skapa en Azure AD-katalog" border="true":::

### <a name="create-a-custom-domain"></a>Skapa en anpassad domän

1. Från din nya **Azure Active Directory**, under **Hantera**, väljer du **anpassade domän namn**.
1. Välj **+ Lägg till en anpassad domän**.
1. Under **anpassat domän namn** anger `HDIFabrikam.com` du och väljer sedan **Lägg till domän**.
1. Slutför sedan [Lägg till din DNS-information till domän registratorn](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png" alt-text="Skapa en anpassad domän" border="true":::

### <a name="create-a-group"></a>Skapa en grupp

1. Välj **grupper** under **hantera** på nytt **Azure Active Directory**.
1. Välj **+ ny grupp**.
1. I text rutan **grupp namn** anger du `AAD DC Administrators` .
1. Välj **Skapa**.

## <a name="configure-your-azure-ad-tenant"></a>Konfigurera din Azure AD-klient

Nu ska du konfigurera din Azure AD-klient så att du kan synkronisera användare och grupper från den lokala Active Directory-instansen till molnet.

Skapa en Active Directory klient organisations administratör.

1. Logga in på Azure Portal och välj din Azure AD-klient, **HDIFabrikam**.

1. Navigera för att **Hantera**  >  **användare**  >  **ny användare**.

1. Ange följande information för den nya användaren:

   **Identitet**

   |Egenskap |Beskrivning |
   |---|---|
   |Användarnamn|Ange `fabrikamazureadmin` i text rutan. I list rutan domän namn väljer du `hdifabrikam.com`|
   |Name| Ange `fabrikamazureadmin`.|

   **Lösenord**
   1. Välj **Låt mig skapa lösen ordet**.
   1. Ange ett valfritt säkert lösen ord.

   **Grupper och roller**
   1. Välj **0 grupper har valts**.
   1. Välj **AAD DC-administratörer** och **Välj** sedan.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png" alt-text="Dialog rutan Azure AD-grupper" border="true":::

   1. Välj **användare**.
   1. Välj **Global administratör** och **Välj** sedan.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png" alt-text="Dialog rutan Azure AD-roll" border="true":::

1. Välj **Skapa**.

1. Sedan kan den nya användaren logga in på den Azure Portal där den uppmanas att ändra lösen ordet. Du måste göra detta innan du konfigurerar Microsoft Azure Active Directory Connect.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synkronisera lokala användare med Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Konfigurera Microsoft Azure Active Directory Connect

1. Hämta [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)från domänkontrollanten.

1. Öppna den körbara filen som du laddade ned och godkänn licens villkoren. Välj **Fortsätt**.

1. Välj **Använd Express inställningar**.

1. På sidan **Anslut till Azure AD** anger du användar namn och lösen ord för den globala administratören för Azure AD. Använd det användar namn `fabrikamazureadmin@hdifabrikam.com` som du skapade när du konfigurerade Active Directory-klienten. Välj sedan **Nästa**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png" alt-text="Ansluta till Azure A D" border="true":::

1. På sidan **Anslut till Active Directory Domain Services** anger du användar namnet och lösen ordet för ett företags administratörs konto. Använd användar namnet `HDIFabrikam\HDIFabrikamAdmin` och lösen ordet som du skapade tidigare. Välj sedan **Nästa**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png" alt-text="Anslut till en D D S-sida." border="true":::

1. På sidan **konfiguration av Azure AD-inloggning** väljer du **Nästa**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png" alt-text="Konfigurations sidan för Azure AD-inloggning" border="true":::

1. Välj **Installera** på sidan **klar att konfigurera** .

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png" alt-text="Redo att konfigurera Sidan" border="true":::

1. På sidan **konfigurationen har slutförts** väljer du **Avsluta**.
   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png" alt-text="Sidan konfiguration slutförd" border="true":::

1. När synkroniseringen är klar bekräftar du att de användare som du skapade i IaaS-katalogen synkroniseras med Azure AD.
   1. Logga in på Azure-portalen.
   1. Välj **Azure Active Directory**  >  **HDIFabrikam**-  >  **användare**.

### <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användardefinierad hanterad identitet som du kan använda för att konfigurera Azure AD Domain Services (Azure AD DS). Mer information finns i [skapa, lista, ta bort eller tilldela en roll till en användardefinierad hanterad identitet med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Logga in på Azure-portalen.
1. Välj **skapa en resurs** och skriv `managed identity` . Välj **användare tilldelad hanterad identitet**  >  **skapa**.
1. För **resurs namnet** anger du `HDIFabrikamManagedIdentity` .
1. Välj din prenumeration.
1. Under **resurs grupp** väljer du **Skapa ny** och anger `HDIFabrikam-CentralUS` .
1. Under **plats** väljer du **centrala USA**.
1. Välj **Skapa**.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png" alt-text="Skapa en ny användare som tilldelats en hanterad identitet" border="true":::

### <a name="enable-azure-ad-ds"></a>Aktivera Azure AD DS

Följ dessa steg om du vill aktivera Azure AD DS. Mer information finns i [Aktivera Azure AD DS med hjälp av Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

1. Skapa ett virtuellt nätverk som värd för Azure AD DS. Kör följande PowerShell-kod.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Logga in på Azure-portalen.
1. Välj **skapa resurs**, ange `Domain services` och välj **Azure AD Domain Services**  >  **skapa**.
1. På sidan **grundläggande** :
   1. Under **katalog namn** väljer du den Azure AD-katalog som du skapade: **HDIFabrikam**.
   1. Ange *HDIFabrikam.com* som **namn på DNS-domän**.
   1. Välj din prenumeration.
   1. Ange resurs gruppen **HDIFabrikam-Central**. För **plats** väljer du **centrala USA**.

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png" alt-text="Grundläggande information om Azure AD DS" border="true":::

1. På sidan **nätverk** väljer du det nätverk (**HDIFabrikam-VNet**) och under nätet (**AADDS-Subnet**) som du skapade med hjälp av PowerShell-skriptet. Eller Välj **Skapa ny** för att skapa ett virtuellt nätverk nu.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png" alt-text="Steg för att skapa virtuellt nätverk" border="true":::

1. På sidan **Administratörs grupp** bör du se ett meddelande om att en grupp med namnet **AAD DC-administratörer** redan har skapats för att administrera den här gruppen. Du kan ändra medlemskapet för den här gruppen om du vill, men i det här fallet behöver du inte ändra den. Välj **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png" alt-text="Visa Azure AD-administratörskonsolen" border="true":::

1. På sidan **synkronisering** aktiverar du fullständig synkronisering genom att välja **alla**  >  **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png" alt-text="Aktivera Azure AD DS-synkronisering" border="true":::

1. På sidan **Sammanfattning** kontrollerar du informationen för Azure AD DS och väljer **OK**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png" alt-text="Aktivera Azure AD Domain Services" border="true":::

När du har aktiverat Azure AD DS körs en lokal DNS-server på de virtuella Azure AD-datorerna.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurera ditt virtuella Azure AD DS-nätverk

Använd följande steg för att konfigurera Azure AD DS Virtual Network (**HDIFabrikam-AADDSVNET**) för att använda dina anpassade DNS-servrar.

1. Leta upp IP-adresserna för dina anpassade DNS-servrar.
   1. Välj `HDIFabrikam.com` Azure AD DS-resurs.
   1. Välj **Egenskaper** under **Hantera**.
   1. Hitta IP-adresserna under **IP-adress i virtuellt nätverk**.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png" alt-text="Hitta anpassade DNS-IP-adresser för Azure AD DS" border="true":::

1. Konfigurera **HDIFabrikam-AADDSVNET** för att använda anpassade IP-adresser 10.0.0.4 och 10.0.0.5.

   1. Under **Inställningar** väljer du **DNS-servrar**.
   1. Välj **anpassad**.
   1. I text rutan anger du den första IP-adressen (*10.0.0.4*).
   1. Välj **Spara**.
   1. Upprepa stegen för att lägga till den andra IP-adressen (*10.0.0.5*).

I vårt scenario konfigurerade vi Azure AD DS att använda IP-adresser 10.0.0.4 och 10.0.0.5 och ange samma IP-adress på det virtuella Azure AD DS-nätverket:

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png" alt-text="Sidan anpassade DNS-servrar" border="true":::

## <a name="securing-ldap-traffic"></a>Skydda LDAP-trafik

LDAP (Lightweight Directory Access Protocol) används för att läsa från och skriva till Azure Active Directory. Du kan göra LDAP-trafik konfidentiell och säker genom att använda Secure Sockets Layer (SSL) eller Transport Layer Security (TLS). Du kan aktivera LDAP över SSL (LDAPs) genom att installera ett korrekt formaterat certifikat.

Mer information om säker LDAP finns i [Konfigurera LDAPS för en Azure AD DS-hanterad domän](../../active-directory-domain-services/tutorial-configure-ldaps.md).

I det här avsnittet skapar du ett självsignerat certifikat, laddar ned certifikatet och konfigurerar LDAPs för den **HDIFabrikam** Azure AD DS-hanterade domänen.

Följande skript skapar ett certifikat för **HDIFabrikam**. Certifikatet sparas i *LocalMachine* -sökvägen.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Alla verktyg eller program som skapar en giltig PKCS 10-begäran (Public Key Cryptography Standards) \# kan användas för att forma begäran om TLS/SSL-certifikat.

Kontrol lera att certifikatet är installerat i datorns **personliga** Arkiv:

1. Starta Microsoft Management Console (MMC).
1. Lägg till snapin-modulen **certifikat** som hanterar certifikat på den lokala datorn.
1. Expandera **certifikat (lokal dator)**  > **personliga** > **certifikat**. Ett nytt certifikat bör finnas i det **personliga** arkivet. Certifikatet utfärdas till det fullständigt kvalificerade värd namnet.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png" alt-text="Bekräfta skapande av lokalt certifikat" border="true":::

1. I fönstret till höger högerklickar du på det certifikat som du har skapat. Peka på **alla aktiviteter** och välj sedan **Exportera**.

1. På sidan **Exportera privat nyckel** väljer du **Ja, exportera den privata nyckeln**. Datorn där nyckeln kommer att importeras behöver den privata nyckeln för att läsa de krypterade meddelandena.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png" alt-text="Sidan Exportera privat nyckel i guiden Exportera certifikat" border="true":::

1. På sidan **fil format för export** lämnar du standardinställningarna och väljer sedan **Nästa**.
1. På sidan **lösen ord** anger du ett lösen ord för den privata nyckeln. För **kryptering** väljer du **TripleDES-SHA1**. Välj sedan **Nästa**.
1. På sidan **fil som ska exporteras** anger du sökvägen till och namnet på den exporterade certifikat filen och väljer sedan **Nästa**. Fil namnet måste ha fil namns tillägget. pfx. Den här filen konfigureras i Azure Portal för att upprätta en säker anslutning.
1. Aktivera LDAPs för en Azure AD DS-hanterad domän.
   1. Välj domänen från Azure Portal `HDIFabrikam.com` .
   1. Under **Hantera** väljer du **säkert LDAP**.
   1. På sidan **säkert LDAP** väljer du **Aktivera** under **säkert LDAP**.
   1. Bläddra efter. PFX-certifikat filen som du exporterade på datorn.
   1. Ange certifikatets lösen ord.

   :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png" alt-text="Aktivera säker LDAP" border="true":::

1. Nu när du har aktiverat LDAPs kontrollerar du att det går att komma åt dem genom att aktivera port 636.
   1. I resurs gruppen **HDIFabrikam-centrala** väljer du nätverks säkerhets gruppen **AADDS-HDIFabrikam.com-NSG**.
   1. Under **Inställningar** väljer du **inkommande säkerhets regler**  >  **Lägg till**.
   1. På sidan **Lägg till inkommande säkerhets regel** anger du följande egenskaper och väljer **Lägg till**:

      | Egenskap | Värde |
      |---|---|
      | Källa | Valfri |
      | Källportintervall | * |
      | Mål | Valfri |
      | Målportintervall | 636 |
      | Protokoll | Valfri |
      | Åtgärd | Tillåt |
      | Prioritet | \<Desired number> |
      | Namn | Port_LDAP_636 |

      :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png" alt-text="Dialog rutan Lägg till inkommande säkerhets regel" border="true":::

**HDIFabrikamManagedIdentity** är den användare som tilldelats den hanterade identiteten. Rollen HDInsight Domain Services Contributor är aktive rad för den hanterade identitet som tillåter den här identiteten att läsa, skapa, ändra och ta bort domän tjänster.

:::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png" alt-text="Skapa en användartilldelad hanterad identitet" border="true":::

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Skapa ett ESP-aktiverat HDInsight-kluster

Det här steget kräver följande krav:

1. Skapa en ny resurs grupp *HDIFabrikam* på platsen **västra USA**.
1. Skapa ett virtuellt nätverk som ska vara värd för det ESP-aktiverade HDInsight-klustret.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Skapa en peer-relation mellan det virtuella nätverk som är värd för Azure AD DS ( `HDIFabrikam-AADDSVNET` ) och det virtuella nätverk som ska vara värd för det ESP-aktiverade HDInsight-klustret ( `HDIFabrikam-HDIVNet` ). Använd följande PowerShell-kod för att peer-koppla de två virtuella nätverken.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Skapa ett nytt Azure Data Lake Storage Gen2-konto med namnet **Hdigen2store**. Konfigurera kontot med den användar hanterade identiteten **HDIFabrikamManagedIdentity**. Mer information finns i [använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Konfigurera anpassad DNS på det virtuella **HDIFabrikam-AADDSVNET-** nätverket.
    1. Gå till Azure Portal > **resurs grupper**  >  **OnPremADVRG**  >  **HDIFabrikam-AADDSVNET**  >  **DNS-servrar**.
    1. Välj **anpassad** och ange *10.0.0.4* och *10.0.0.5*.
    1. Välj **Spara**.

        :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png" alt-text="Spara anpassade DNS-inställningar för ett virtuellt nätverk" border="true":::

1. Skapa ett nytt ESP-aktiverat HDInsight Spark-kluster.
    1. Välj **Anpassad (storlek, inställningar, appar)**.
    1. Ange information om **grunderna** (avsnitt 1). Se till att **kluster typen** är **Spark 2,3 (HDI 3,6)**. Se till att **resurs gruppen** är **HDIFabrikam-Central**.

    1. För **säkerhet + nätverk** (avsnitt 2) fyller du i följande information:
        * Under **Enterprise Security Package** väljer du **aktive rad**.
        * Välj **kluster administratörs användare** och välj det **HDIAdmin** -konto som du skapade som den lokala administratörs användaren. Klicka på **Välj**.
        * Välj **kluster åtkomst grupp**  >  **HDIUserGroup**. Alla användare som du lägger till i den här gruppen i framtiden kommer att kunna komma åt HDInsight-kluster.

            :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg" alt-text="Välj gruppen HDIUserGroup för kluster åtkomst" border="true":::

    1. Slutför de andra stegen i kluster konfigurationen och kontrol lera informationen i **kluster sammanfattningen**. Välj **Skapa**.

1. Logga in på Ambari-ANVÄNDARGRÄNSSNITTET för det nyligen skapade klustret på `https://CLUSTERNAME.azurehdinsight.net` . Använd administratörens användar namn `hdiadmin@hdifabrikam.com` och lösen ord.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg" alt-text="Fönstret Apache Ambari UI-inloggning" border="true":::

1. Välj **roller** på instrument panelen för klustret.
1. På sidan **roller** , under **tilldela roller till dessa**, anger du gruppen *hdiusergroup* bredvid rollen **kluster administratör** .

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg" alt-text="Tilldela rollen kluster administratör till hdiusergroup" border="true":::

1. Öppna din SSH-klient (Secure Shell) och logga in på klustret. Använd **hdiuser** som du skapade i den lokala Active Directory-instansen.

    :::image type="content" source="./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg" alt-text="Logga in på klustret med SSH-klienten" border="true":::

Om du kan logga in med det här kontot har du konfigurerat ditt ESP-kluster på ett korrekt sätt för att synkronisera med din lokala Active Directory-instans.

## <a name="next-steps"></a>Nästa steg

Läs [en introduktion till Apache Hadoop säkerhet med ESP](hdinsight-security-overview.md).
