---
title: Azure VMware-lösning av CloudSimple – Använd Azure AD som identitets källa i privat moln
description: Beskriver hur du lägger till Azure AD som en identitetsprovider i ditt CloudSimple privata moln för att autentisera användare som har åtkomst till CloudSimple från Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f55a0f52f5e028f9cbf7a9fabbb3c24ad43c3800
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898614"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Använd Azure AD som identitets leverantör för vCenter på CloudSimple privata moln

Du kan ställa in din CloudSimple privata moln vCenter för att autentisera med Azure Active Directory (Azure AD) för att VMware-administratörer ska få åtkomst till vCenter. När identitets källan för enkel inloggning har kon figurer ATS kan **cloudowner** -användaren lägga till användare från identitets källan till vCenter.  

Du kan konfigurera din Active Directory domän och domänkontrollanter på något av följande sätt:

* Active Directory domän och domänkontrollanter som körs lokalt
* Active Directory domän och domänkontrollanter som körs på Azure som virtuella datorer i din Azure-prenumeration
* Ny Active Directory domän och domänkontrollanter som körs i ditt CloudSimple privata moln
* Azure Active Directory tjänst

Den här guiden förklarar de uppgifter som krävs för att konfigurera Azure AD som en identitets källa.  Information om hur du använder lokala Active Directory eller Active Directory som körs i Azure finns i [Konfigurera vCenter identitets källor för att använda Active Directory](set-vcenter-identity.md) för detaljerade instruktioner för hur du konfigurerar identitets källan.

## <a name="about-azure-ad"></a>Om Azure AD

Azure AD är Microsoft-tjänsten för hantering av molnbaserade kataloger och identitets hantering för flera innehavare.  Azure AD tillhandahåller en skalbar, konsekvent och tillförlitlig autentiseringsmekanism för användare för att autentisera och komma åt olika tjänster i Azure.  Den ger också säkra LDAP-tjänster för alla tjänster från tredje part som använder Azure AD som en autentiserings-/identitets källa.  Azure AD kombinerar kärn katalogs tjänster, avancerad identitets styrning och program åtkomst hantering som kan användas för att ge åtkomst till ditt privata moln för användare som administrerar det privata molnet.

Om du vill använda Azure AD som identitets källa med vCenter måste du konfigurera Azure AD och Azure AD Domain Services. Följ dessa anvisningar:

1. [Så här konfigurerar du Azure AD och Azure AD Domain Services](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Så här konfigurerar du en identitets källa i ditt privata moln vCenter](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Konfigurera Azure AD och Azure AD Domain Services

Innan du börjar måste du ha åtkomst till din Azure-prenumeration med globala administratörs behörigheter.  Följande steg ger allmänna rikt linjer. Mer information finns i Azure-dokumentationen.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Om du redan har Azure AD kan du hoppa över det här avsnittet.

1. Konfigurera Azure AD på din prenumeration enligt beskrivningen i  [Azure AD-dokumentationen](../active-directory/fundamentals/active-directory-whatis.md).
2. Aktivera Azure Active Directory Premium på din prenumeration enligt beskrivningen i [Registrera dig för Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Konfigurera ett anpassat domän namn och kontrol lera det anpassade domän namnet enligt beskrivningen i [Lägg till ett anpassat domän namn i Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Konfigurera en DNS-post på din domän registrator med den information som finns på Azure.
    2. Ange att det anpassade domän namnet ska vara den primära domänen.

Du kan också konfigurera andra funktioner i Azure AD.  Dessa krävs inte för att aktivera vCenter-autentisering med Azure AD.

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services

> [!NOTE]
> Detta är ett viktigt steg för att aktivera Azure AD som identitets källa för vCenter.  Se till att alla steg utförs korrekt för att undvika eventuella problem.

1. Aktivera Azure AD Domain Services enligt beskrivningen i [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
2. Konfigurera nätverket som ska användas av Azure AD Domain Services enligt beskrivningen i [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
3. Konfigurera administratörs gruppen för hantering av Azure AD Domain Services enligt beskrivningen i [aktivera Azure Active Directory Domain Services med hjälp av Azure Portal](../active-directory-domain-services/tutorial-create-instance.md).
4. Uppdatera DNS-inställningarna för din Azure AD Domain Services enligt beskrivningen i [aktivera Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).  Om du vill ansluta till AD via Internet konfigurerar du DNS-posten för den offentliga IP-adressen för Azure AD Domain Services till domän namnet.
5. Aktivera synkronisering av lösen ords-hash för användare.  Det här steget gör det möjligt att synkronisera lösen ords-hashar som krävs för NTLM (NT LAN Manager) och Kerberos-autentisering till Azure AD Domain Services. När du har konfigurerat lösenordshashsynkronisering kan användarna logga in till den hanterade domänen med sina företagsuppgifter. Se [aktivera hash-synkronisering av lösen ord för att Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).
    1. Om enbart moln användare finns, måste de ändra sitt lösen ord med hjälp av <a href="https://myapps.microsoft.com/" target="_blank">Azure AD Access panel</a> för att säkerställa att lösen ords-hashar lagras i det format som krävs av NTLM eller Kerberos.  Följ anvisningarna i [aktivera hash-synkronisering av lösen ord till din hanterade domän för användar konton som endast är moln](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Det här steget måste göras för enskilda användare och alla nya användare som har skapats i Azure AD-katalogen med hjälp av Azure Portal eller Azure AD PowerShell-cmdletar. Användare som behöver åtkomst till Azure AD Domain Services måste använda <a href="https://myapps.microsoft.com/" target="_blank">Azure AD-åtkomst panelen</a> och komma åt sin profil för att ändra lösen ordet.

        > [!NOTE]
        > Om organisationen endast har molnbaserade användarkonton måste användare som behöver använda Azure Active Directory Domain Services ändra sina lösenord. Ett endast molnbaserat användarkonto är ett konto som skapats i Azure AD-katalogen med antingen Azure Portal eller Azure AD PowerShell-cmdletar. Dessa användarkonton är inte synkroniserade från en lokal katalog.

    2. Om du synkroniserar lösen ord från din lokala Active Directory följer du stegen i [Active Directory-dokumentationen](../active-directory-domain-services/tutorial-configure-password-hash-sync.md).

6.  Konfigurera säker LDAP på Azure Active Directory Domain Services enligt beskrivningen i [Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services hanterad domän](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Ladda upp ett certifikat för användning av säker LDAP enligt beskrivningen i Azure-ämnet [Skaffa ett certifikat för säker LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  CloudSimple rekommenderar att du använder ett signerat certifikat som utfärdats av en certifikat utfärdare för att säkerställa att vCenter kan lita på certifikatet.
    2. Aktivera säker LDAP enligt beskrivningen [Aktivera säker LDAP (LDAPS) för en Azure AD Domain Services hanterad domän](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Spara den offentliga delen av certifikatet (utan den privata nyckeln) i. cer-format för användning med vCenter när du konfigurerar identitets källan.
    4. Om Internet åtkomst till Azure AD Domain Services krävs aktiverar du alternativet Tillåt säker åtkomst till LDAP över Internet.
    5. Lägg till regeln för inkommande säkerhet för Azure AD Domain Services-NSG för TCP-port 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Konfigurera en identitets källa i ditt privata moln vCenter

1. [Eskalera privilegier](escalate-private-cloud-privileges.md) för ditt privata moln vCenter.
2. Samla in de konfigurations parametrar som krävs för att konfigurera identitets källan.

    | **Alternativ** | **Beskrivning** |
    |------------|-----------------|
    | **Namn** | Namnet på identitets källan. |
    | **Grundläggande DN för användare** | Grundläggande unikt namn för användare.  För Azure AD använder du: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  exempel: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com` .|
    | **Domän namn** | FQDN för domänen, till exempel example.com. Ange ingen IP-adress i den här text rutan. |
    | **Domän Ali Aset** | *(valfritt)* Domänens NetBIOS-namn. Lägg till NetBIOS-namnet för Active Directory domän som ett alias för identitets källan om du använder SSPI-autentiseringar. |
    | **Bas-DN för grupper** | Det grundläggande unika namnet för grupper. För Azure AD använder du: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  exempel: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Primär server-URL** | Primär domänkontrollantens LDAP-server för domänen.<br><br>Använd formatet `ldaps://hostname:port`. Porten är vanligt vis 636 för LDAPs-anslutningar. <br><br>Ett certifikat som upprättar förtroende för slut punkten för LDAPs-slutpunkten för Active Directory-servern krävs när du använder `ldaps://` i den primära eller sekundära LDAP-URL: en. |
    | **Sekundär server-URL** | Adress till en sekundär domänkontrollant LDAP-server som används för redundans. |
    | **Välj certifikat** | Om du vill använda LDAPs med din Active Directory LDAP-server eller OpenLDAP-serverns identitets källa visas knappen Välj certifikat när du har skrivit `ldaps://` i text rutan URL. Det krävs ingen sekundär URL. |
    | **Användarnamn** | ID för en användare i domänen som har minst skrivskyddad åtkomst till bas-DN för användare och grupper. |
    | **Lösenord** | Lösen ordet för den användare som anges av användar namnet. |

3. Logga in i ditt privata moln vCenter när behörigheterna har eskalerats.
4. Följ anvisningarna i [lägga till en identitets källa på vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) med värdena från föregående steg för att konfigurera Azure Active Directory som identitets källa.
5. Lägg till användare/grupper från Azure AD till vCenter-grupper enligt beskrivningen i VMware-avsnittet [lägga till medlemmar i en vCenter Single Sign-On-grupp](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Nya användare får endast läggas till i *moln-ägar-grupp*, *Cloud-global-Cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* eller, *Cloud-global-VM-admin-Group*.  Användare som lagts till i gruppen *Administratörer* tas då bort automatiskt.  Endast tjänst konton måste läggas till i gruppen *Administratörer* .

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om behörighets modellen för privata moln](learn-private-cloud-permissions.md)
