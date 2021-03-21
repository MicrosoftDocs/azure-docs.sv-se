---
title: Azure VMware-lösning av CloudSimple – Ställ in vCenter Identity Sources i privat moln
description: Beskriver hur du konfigurerar ditt privata moln vCenter för att autentisera med Active Directory för VMware-administratörer att komma åt vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a76fecb942c5c6da926e37149245e82dcbc4661b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899158"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Konfigurera vCenter-identitets källor som ska använda Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Om VMware vCenter-identitets källor

VMware vCenter stöder olika identitets källor för autentisering av användare som har åtkomst till vCenter.  Din CloudSimple privata moln vCenter kan konfigureras att autentisera med Active Directory för dina VMware-administratörer att komma åt vCenter. När installationen är klar kan **cloudowner** -användaren lägga till användare från identitets källan till vCenter.  

Du kan konfigurera din Active Directory domän och domänkontrollanter på något av följande sätt:

* Active Directory domän och domänkontrollanter som körs lokalt
* Active Directory domän och domänkontrollanter som körs på Azure som virtuella datorer i din Azure-prenumeration
* Ny Active Directory domän och domänkontrollanter som körs i ditt privata moln
* Azure Active Directory tjänst

I den här guiden beskrivs aktiviteter för att konfigurera Active Directory domän-och domän kontrollers som kör antingen lokalt eller som virtuella datorer i dina prenumerationer.  Om du vill använda Azure AD som identitets källa kan du läsa mer i [använda Azure AD som identitets leverantör för vCenter i CloudSimple Private Cloud](azure-ad.md) för detaljerade anvisningar om hur du konfigurerar identitets källan.

Innan du [lägger till en identitets källa](#add-an-identity-source-on-vcenter)måste [du tillfälligt eskalera vCenter-privilegierna](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Nya användare får endast läggas till i *moln-ägar-grupp*, *Cloud-global-Cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* eller, *Cloud-global-VM-admin-Group*.  Användare som lagts till i gruppen *Administratörer* tas då bort automatiskt.  Endast tjänst konton måste läggas till i *Administratörs* gruppen och tjänst kontona får inte användas för att logga in på vSphere-webbgränssnittet.   


## <a name="identity-source-options"></a>Alternativ för identitets källa

* [Lägg till lokala Active Directory som en identitets källa för enkel inloggning](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Konfigurera nya Active Directory i ett privat moln](#set-up-new-active-directory-on-a-private-cloud)
* [Konfigurera Active Directory på Azure](#set-up-active-directory-on-azure)

> [!IMPORTANT]
> **Active Directory (Windows-integrerad autentisering) stöds inte.** Endast Active Directory över LDAP-alternativ stöds som identitets källa.

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Lägg till lokalt Active Directory som en enda Sign-On identitets källa

Om du vill konfigurera din lokala Active Directory som en enda Sign-On identitets källa behöver du:

* [Plats-till-plats-VPN-anslutning](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) från ditt lokala data Center till ditt privata moln.
* Den lokala DNS-serverns IP-adress har lagts till i vCenter och Platform Services Controller (PSC).

Använd informationen i följande tabell när du konfigurerar din Active Directory-domän.

| **Alternativ** | **Beskrivning** |
|------------|-----------------|
| **Namn** | Namnet på identitets källan. |
| **Grundläggande DN för användare** | Grundläggande unikt namn för användare. |
| **Domän namn** | FQDN för domänen, till exempel example.com. Ange ingen IP-adress i den här text rutan. |
| **Domän Ali Aset** | Domänens NetBIOS-namn. Lägg till NetBIOS-namnet för Active Directory domän som ett alias för identitets källan om du använder SSPI-autentiseringar. |
| **Bas-DN för grupper** | Det grundläggande unika namnet för grupper. |
| **Primär server-URL** | Primär domänkontrollantens LDAP-server för domänen.<br><br>Använd formatet `ldap://hostname:port` eller `ldaps://hostname:port` . Porten är vanligt vis 389 för LDAP-anslutningar och 636 för LDAPs-anslutningar. För att Active Directory distributioner av flera domänkontrollanter är porten vanligt vis 3268 för LDAP och 3269 för LDAPs.<br><br>Ett certifikat som upprättar förtroende för slut punkten för LDAPs-slutpunkten för Active Directory-servern krävs när du använder `ldaps://` i den primära eller sekundära LDAP-URL: en. |
| **Sekundär server-URL** | Adress till en sekundär domänkontrollant LDAP-server som används för redundans. |
| **Välj certifikat** | Om du vill använda LDAPs med din Active Directory LDAP-server eller OpenLDAP-serverns identitets källa visas knappen Välj certifikat när du har skrivit `ldaps://` i text rutan URL. Det krävs ingen sekundär URL. |
| **Användarnamn** | ID för en användare i domänen som har minst skrivskyddad åtkomst till bas-DN för användare och grupper. |
| **Lösenord** | Lösen ordet för den användare som anges av användar namnet. |

När du har informationen i föregående tabell kan du lägga till din lokala Active Directory som en enda Sign-On identitets källa på vCenter.

> [!TIP]
> Du hittar mer information om enskilda Sign-On identitets källor på [sidan VMware-dokumentation](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Konfigurera nya Active Directory i ett privat moln

Du kan skapa en ny Active Directory domän i ditt privata moln och använda den som identitets källa för enkel inloggning.  Active Directory domän kan ingå i en befintlig Active Directory skog eller konfigureras som en oberoende skog.

### <a name="new-active-directory-forest-and-domain"></a>Ny Active Directory skog och domän

Om du vill konfigurera en ny Active Directory skog och domän måste du:

* En eller flera virtuella datorer som kör Microsoft Windows Server som ska användas som domänkontrollanter för den nya Active Directory skogen och domänen.
* En eller flera virtuella datorer som kör DNS-tjänsten för namn matchning.

Detaljerade anvisningar finns i [installera en ny Windows Server 2012 Active Directory-skog](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) .

> [!TIP]
> För hög tillgänglighet för tjänster rekommenderar vi att du konfigurerar flera domänkontrollanter och DNS-servrar.

När du har konfigurerat Active Directory skog och domän kan du [lägga till en identitets källa på vCenter](#add-an-identity-source-on-vcenter) för den nya Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Ny Active Directory domän i en befintlig Active Directory skog

Om du vill konfigurera en ny Active Directory domän i en befintlig Active Directory skog behöver du:

* Plats-till-plats-VPN-anslutning till din Active Directory skogs plats.
* DNS-server för att matcha namnet på din befintliga Active Directory skog.

Se [installera en ny Windows Server 2012 Active Directory underordnad eller träd domän](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) för detaljerade steg.

När du har konfigurerat Active Directory-domänen kan du [lägga till en identitets källa på vCenter](#add-an-identity-source-on-vcenter) för den nya Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Konfigurera Active Directory på Azure

Active Directory som körs på Azure liknar Active Directory som körs lokalt.  Om du vill konfigurera Active Directory som körs på Azure som en enda Sign-On identitets källa på vCenter måste vCenter-servern och PSC: en ha nätverks anslutning till Azure-Virtual Network där Active Directory-tjänsterna körs.  Du kan upprätta den här anslutningen med [azure Virtual Network-anslutning med ExpressRoute](azure-expressroute-connection.md) från det virtuella Azure-nätverket där Active Directory-tjänsterna körs till CloudSimple-privata moln.

När nätverks anslutningen har upprättats följer du stegen i [lägga till lokala Active Directory som en enda Sign-On identitets källa](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) för att lägga till den som en identitets källa.  

## <a name="add-an-identity-source-on-vcenter"></a>Lägg till en identitets källa på vCenter

1. [Eskalera privilegier](escalate-private-cloud-privileges.md) i ditt privata moln.

2. Logga in på vCenter för ditt privata moln.

3. Välj **start > administration**.

    ![Administration](media/OnPremAD01.png)

4. Välj **enkel inloggning > konfiguration**.

    ![Enkel inloggning](media/OnPremAD02.png)

5. Öppna fliken **identitets källor** och klicka **+** för att lägga till en ny identitets källa.

    ![Identitets källor](media/OnPremAD03.png)

6. Välj **Active Directory som en LDAP-server** och klicka på **Nästa**.

    ![Skärm bild som visar Active Directory som ett alternativ för LDAP-server.](media/OnPremAD04.png)

7. Ange parametrarna för identitets källan för din miljö och klicka på **Nästa**.

    ![Active Directory](media/OnPremAD05.png)

8. Granska inställningarna och klicka på **Slutför**.
