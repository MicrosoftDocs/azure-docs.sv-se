---
title: Skapa och hantera Active Directory anslutningar för Azure NetApp Files | Microsoft Docs
description: Den här artikeln visar hur du skapar och hanterar Active Directory-anslutningar för Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 44959b2f60f9aafd7d9430c9c19baea72344293f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183887"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Skapa och hantera Active Directory anslutningar för Azure NetApp Files

Flera funktioner i Azure NetApp Files kräver att du har en Active Directory anslutning.  Du måste till exempel ha en Active Directory anslutning innan du kan skapa en SMB- [volym](azure-netapp-files-create-volumes-smb.md) eller en [volym med dubbla protokoll](create-volumes-dual-protocol.md).  Den här artikeln visar hur du skapar och hanterar Active Directory-anslutningar för Azure NetApp Files.

## <a name="before-you-begin"></a>Innan du börjar  

Du måste redan ha konfigurerat en kapacitetspool.   
[Konfigurera en pool för kapacitet](azure-netapp-files-set-up-capacity-pool.md)   
Ett undernät måste delegeras till Azure NetApp Files.  
[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Krav för Active Directory-anslutningar

 Kraven för Active Directory anslutningar är följande: 

* Det administratörs konto som du använder måste ha möjlighet att skapa dator konton i den organisationsenhet (OU) som du ska ange.  

* Rätt portar måste vara öppna på den aktuella Windows Active Directory-servern (AD).  
    De portar som krävs är följande: 

    |     Tjänst           |     Port     |     Protokoll     |
    |-----------------------|--------------|------------------|
    |    AD-webbtjänster    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    Ej tillämpligt       |    Eko svar    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-namn       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32Time            |    123       |    UDP           |

* Platstopologi för mål Active Directory Domain Services måste följa rikt linjerna, i synnerhet det virtuella Azure-VNet där Azure NetApp Files distribueras.  

    Adress utrymmet för det virtuella nätverk där Azure NetApp Files distribueras måste läggas till på en ny eller befintlig Active Directory plats (där en domänkontrollant som kan kontaktas av Azure NetApp Files är). 

* De angivna DNS-servrarna måste gå att komma åt från det [delegerade under nätet](./azure-netapp-files-delegate-subnet.md) för Azure NetApp Files.  

    Se [rikt linjer för Azure NetApp Files nätverks planering](./azure-netapp-files-network-topologies.md) för nätverkstopologier som stöds.

    Nätverks säkerhets grupper (NSG: er) och brand väggar måste ha rätt konfigurerade regler för att tillåta Active Directory-och DNS-trafik begär Anden. 

* Det Azure NetApp Files delegerade under nätet måste kunna komma åt alla Active Directory Domain Services (lägger till) domänkontrollanter i domänen, inklusive alla lokala domänkontrollanter och fjärranslutna domänkontrollanter. I annat fall kan tjänst avbrott uppstå.  

    Om du har domänkontrollanter som inte kan kontaktas av Azure NetApp Files delegerade under nätet kan du ange en Active Directory plats när du skapar Active Directory anslutningen.  Azure NetApp Files behöver endast kommunicera med domänkontrollanter på den plats där Azure NetApp Files-adress utrymmet för under nätet är.

    Se [utforma platstopologi](/windows-server/identity/ad-ds/plan/designing-the-site-topology) om AD-platser och-tjänster. 
    
* Du kan aktivera AES-kryptering för AD-autentisering genom att markera rutan **AES-kryptering** i fönstret [Anslut Active Directory](#create-an-active-directory-connection) . Azure NetApp Files stöder krypterings typerna DES, Kerberos AES 128 och Kerberos AES 256 (från den minst säkra som är säkrast). Om du aktiverar AES-kryptering måste de användarautentiseringsuppgifter som används för att ansluta till Active Directory ha det högsta motsvarande konto alternativet som matchar de funktioner som har Aktiver ATS för din Active Directory.    

    Om din Active Directory till exempel endast har AES-128-funktionen, måste du aktivera alternativet AES-128 konto för autentiseringsuppgifterna för användaren. Om din Active Directory har AES-256-funktionen måste du aktivera alternativet AES-256 konto (som också stöder AES-128). Om din Active Directory inte har någon Kerberos-krypterings kapacitet använder Azure NetApp Files DES som standard.  

    Du kan aktivera konto alternativen i egenskaperna för Active Directory användare och datorer Microsoft Management Console (MMC):   

    ![Active Directory användare och datorer MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files stöder [LDAP-signering](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), vilket möjliggör säker överföring av LDAP-trafik mellan Azure NetApp Files-tjänsten och de [Active Directory domän kontrol Lanterna](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Om du följer anvisningarna i Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) för LDAP-signering bör du aktivera funktionen LDAP-signering i Azure NetApp Files genom att markera rutan **LDAP-signering** i fönstret [Anslut Active Directory](#create-an-active-directory-connection) . 

    Enbart [LDAP-kanals bindnings](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) konfiguration har ingen påverkan på den Azure NetApp Files tjänsten. Men om du använder både LDAP-kanalens bindning och säker LDAP (till exempel LDAPs eller `start_tls` ), kommer SMB-volymen att Miss sen att skapas.

## <a name="decide-which-domain-services-to-use"></a>Bestäm vilka domän tjänster som ska användas 

Azure NetApp Files stöder både [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (lägger till) och Azure Active Directory Domain Services (AADDS) för AD-anslutningar.  Innan du skapar en AD-anslutning måste du bestämma om du vill använda tillägg eller AADDS.  

Mer information finns i [jämföra självhanterade Active Directory Domain Services, Azure Active Directory och hanterade Azure Active Directory Domain Services](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Du kan använda önskade [Active Directory platser och tjänste](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) omfång för Azure NetApp Files. Med det här alternativet kan läsningar och skrivningar Active Directory Domain Services (lägger till) domänkontrollanter som är [tillgängliga via Azure NetApp Files](azure-netapp-files-network-topologies.md). Det förhindrar också att tjänsten kommunicerar med domänkontrollanter som inte finns på den angivna Active Directory platser och tjänster-platsen. 

Om du vill hitta plats namnet när du använder tillägg kan du kontakta den administrativa gruppen i organisationen som ansvarar för Active Directory Domain Services. Exemplet nedan visar plugin-programmet för Active Directory platser och tjänster där plats namnet visas: 

![Active Directory-platser och -tjänster](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

När du konfigurerar en AD-anslutning för Azure NetApp Files anger du plats namnet i området för fältet **AD-plats namn** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services konfiguration och vägledning för (AADDS) finns i [Azure AD Domain Services dokumentation](../active-directory-domain-services/index.yml).

Ytterligare AADDS-överväganden gäller för Azure NetApp Files: 

* Se till att VNet eller undernät där AADDS har distribuerats finns i samma Azure-region som Azure NetApp Files-distributionen.
* Om du använder ett annat VNet i den region där Azure NetApp Files distribueras, bör du skapa en peering mellan de två virtuella nätverk.
* Azure NetApp Files stöd `user` och `resource forest` typer.
* För synkronisering av typ kan du välja `All` eller `Scoped` .   
    Om du väljer `Scoped` , se till att rätt Azure AD-grupp har valts för åtkomst till SMB-resurser.  Om du är osäker kan du använda den här `All` typen av synkronisering.
* Du måste använda Enterprise-eller Premium-SKU: n. Standard-SKU: n stöds inte.

När du skapar en Active Directory anslutning noterar du följande information för AADDS:

* Du hittar information om **primärt** DNS-, **sekundärt DNS**-och **AD DNS-domännamn** på AADDS-menyn.  
För DNS-servrar används två IP-adresser för att konfigurera Active Directory-anslutningen. 
* **Sökvägen till organisationsenheten** är `OU=AADDC Computers` .  
Den här inställningen konfigureras i **Active Directory anslutningar** under **NetApp-konto**:

  ![Sökväg till organisationsenhet](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Autentiseringsuppgifter för **användar namn** kan vara alla användare som är medlemmar i Azure AD-gruppen **Azure AD DC-administratörer**.


## <a name="create-an-active-directory-connection"></a>Skapa en Active Directory anslutning

1. Klicka på **Active Directory anslutningar** på ditt NetApp-konto och klicka sedan på **Anslut**.  

    ![Active Directory anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. I fönstret Anslut Active Directory anger du följande information baserat på de domän tjänster som du vill använda:  

    Information som är speciell för de domän tjänster som du använder finns i [Bestäm vilka domän tjänster som ska användas](#decide-which-domain-services-to-use). 

    * **Primär DNS**  
        Detta är den DNS som krävs för åtgärderna för att Active Directory domän anslutning och SMB-autentisering. 
    * **Sekundär DNS**   
        Det här är den sekundära DNS-servern för att säkerställa redundanta namn tjänster. 
    * **AD DNS-domännamn**  
        Detta är domän namnet för din Active Directory Domain Services som du vill ansluta till.
    * **AD-platsens namn**  
        Detta är webbplats namnet som identifieringen av domänkontrollanten ska begränsas till. Detta ska matcha plats namnet i Active Directory-platser och-tjänster.
    * **Prefix för SMB-server (dator konto)**  
        Detta är namngivnings prefixet för dator kontot i Active Directory som Azure NetApp Files används för att skapa nya konton.

        Exempel: om namngivnings standarden som din organisation använder för fil servrar är NAS-01, NAS-02..., NAS-045, anger du "NAS" för prefixet. 

        Tjänsten kommer att skapa ytterligare dator konton i Active Directory efter behov.

        > [!IMPORTANT] 
        > Att byta namn på SMB-serverns prefix när du har skapat Active Directory anslutningen är störande. Du måste montera om befintliga SMB-resurser när du har bytt namn på SMB-serverns prefix.

    * **Sökväg till organisationsenhet**  
        Det här är LDAP-sökvägen för organisationsenheten (OU) där SMB-serverns dator konton kommer att skapas. Det vill säga OU = den andra nivån, OU = första nivån. 

        Om du använder Azure NetApp Files med Azure Active Directory Domain Services är organisationsenhetens sökväg `OU=AADDC Computers` när du konfigurerar Active Directory för ditt NetApp-konto.

        ![Anslut Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES-kryptering**   
        Markera den här kryss rutan om du vill aktivera AES-kryptering för en SMB-volym. Se [krav för Active Directory anslutningar](#requirements-for-active-directory-connections) för krav. 

        ![Active Directory AES-kryptering](../media/azure-netapp-files/active-directory-aes-encryption.png)

        **AES-kryptering** är för närvarande en för hands version. Om det här är första gången du använder den här funktionen ska du registrera funktionen innan du använder den: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Kontrol lera status för funktions registreringen: 

        > [!NOTE]
        > **RegistrationState** kan vara i ett `Registering` tillstånd i upp till 60 minuter innan den ändras till `Registered` . Vänta tills statusen är `Registered` innan du fortsätter.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Du kan också använda [Azure CLI-kommandon](/cli/azure/feature) `az feature register` och `az feature show` Registrera funktionen och Visa registrerings status. 

    * **LDAP-signering**   
        Markera den här kryss rutan om du vill aktivera LDAP-signering. Den här funktionen möjliggör säker LDAP-sökning mellan Azure NetApp Files tjänsten och de användardefinierade [Active Directory Domain Services domän kontrol Lanterna](/windows/win32/ad/active-directory-domain-services). Mer information finns i [ADV190023 | Microsofts vägledning för att aktivera bindning av LDAP-kanaler och LDAP-signering](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Active Directory LDAP-signering](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        Funktionen **LDAP-signering** är för närvarande en för hands version. Om det här är första gången du använder den här funktionen ska du registrera funktionen innan du använder den: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Kontrol lera status för funktions registreringen: 

        > [!NOTE]
        > **RegistrationState** kan vara i ett `Registering` tillstånd i upp till 60 minuter innan den ändras till `Registered` . Vänta tills statusen är `Registered` innan du fortsätter.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Du kan också använda [Azure CLI-kommandon](/cli/azure/feature) `az feature register` och `az feature show` Registrera funktionen och Visa registrerings status. 

     * **Användare av säkerhets kopierings princip**  
        Du kan inkludera ytterligare konton som kräver förhöjd behörighet för det dator konto som skapas för användning med Azure NetApp Files. De angivna kontona kommer att kunna ändra NTFS-behörigheter på fil-eller mappnivå. Du kan till exempel ange ett icke-privilegierat tjänst konto som används för att migrera data till en SMB-filresurs i Azure NetApp Files.  

        ![Active Directory säkerhets kopierings princip användare](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        Funktionen **säkerhets kopierings princip användare** är för närvarande en för hands version. Om det här är första gången du använder den här funktionen ska du registrera funktionen innan du använder den: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Kontrol lera status för funktions registreringen: 

        > [!NOTE]
        > **RegistrationState** kan vara i ett `Registering` tillstånd i upp till 60 minuter innan den ändras till `Registered` . Vänta tills statusen är `Registered` innan du fortsätter.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Du kan också använda [Azure CLI-kommandon](/cli/azure/feature) `az feature register` och `az feature show` Registrera funktionen och Visa registrerings status. 

    * Autentiseringsuppgifter, inklusive ditt **användar namn** och **lösen ord**

        ![Active Directory autentiseringsuppgifter](../media/azure-netapp-files/active-directory-credentials.png)

3. Klicka på **Anslut**.  

    Active Directory anslutningen som du skapade visas.

    ![Skapade Active Directory-anslutningar](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="next-steps"></a>Nästa steg  

* [Skapa en SMB-volym](azure-netapp-files-create-volumes-smb.md)
* [Skapa en volym med dubbla protokoll](create-volumes-dual-protocol.md)
* [Konfigurera NFSv4.1 Kerberos-kryptering](configure-kerberos-encryption.md)
* [Installera en ny Active Directory skog med hjälp av Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
