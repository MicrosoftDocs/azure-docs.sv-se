---
title: Krav för Azure AD Connect Cloud Sync i Azure AD
description: I den här artikeln beskrivs de krav och maskin varu krav du behöver för molnbaserad synkronisering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0277d4ce263610576178e3844a0665ab6506fbfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579169"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Krav för Azure AD Connect Cloud Sync
Den här artikeln innehåller information om hur du väljer och använder Azure Active Directory (Azure AD) Connect Cloud Sync som din identitets lösning.

## <a name="cloud-provisioning-agent-requirements"></a>Krav för moln etablerings agent
Du behöver följande för att kunna använda Azure AD Connect Cloud Sync:

- Domän administratörs-eller företags administratörs behörighet för att skapa Azure AD Connect Cloud Sync-gMSA (grupphanterat tjänst konto) för att köra Agent tjänsten. 
- Ett administratörs konto med hybrid identitet för din Azure AD-klient som inte är en gäst användare.
- En lokal server för etablerings agenten med Windows 2016 eller senare.  Den här servern måste vara en nivå 0-server som baseras på [Active Directory administratörs nivå modell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Konfigurationer för lokala brand väggar.

## <a name="group-managed-service-accounts"></a>Grupphanterade tjänstkonton
Ett grupphanterat tjänst konto är ett hanterat domän konto som tillhandahåller automatisk lösen ords hantering, förenklad hantering av tjänst huvud namn (SPN), möjlighet att delegera hanteringen till andra administratörer och även utöka den här funktionaliteten över flera servrar.  Azure AD Connect Cloud Sync stöder och använder en gMSA för att köra agenten.  Du uppmanas att ange administratörs behörighet under installationen för att kunna skapa det här kontot.  Kontot visas som (domain\provAgentgMSA $).  Mer information om en gMSA finns i [gruppera hanterade tjänst konton](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### <a name="prerequisites-for-gmsa"></a>Krav för gMSA:
1.  Active Directory-schemat i gMSA-domänens skog måste uppdateras till Windows Server 2016.
2.  [PowerShell-moduler för RSAT](/windows-server/remote/remote-server-administration-tools) på en domänkontrollant
3.  Minst en domänkontrollant i domänen måste köra Windows Server 2016.
4.  En domänansluten server där agenten installeras måste vara antingen Windows Server 2016 eller senare.

### <a name="custom-gmsa-account"></a>Anpassat gMSA-konto
Om du skapar ett anpassat gMSA-konto måste du kontrol lera att kontot har följande behörigheter.

|Typ |Name |Access |Gäller för| 
|-----|-----|-----|-----|
|Tillåt |gMSA-konto |Läsa alla egenskaper |Underordnade enhets objekt| 
|Tillåt |gMSA-konto|Läsa alla egenskaper |Objekt för underordnade InetOrgPerson| 
|Tillåt |gMSA-konto |Läsa alla egenskaper |Underordnade dator objekt| 
|Tillåt |gMSA-konto |Läsa alla egenskaper |Underordnade foreignSecurityPrincipal-objekt| 
|Tillåt |gMSA-konto |Fullständig behörighet |Underordnade grupp objekt| 
|Tillåt |gMSA-konto |Läsa alla egenskaper |Underordnade användar objekt| 
|Tillåt |gMSA-konto |Läsa alla egenskaper |Underordnade kontakt objekt| 
|Tillåt |gMSA-konto |Skapa/ta bort användar objekt|Det här objektet och alla underordnade objekt| 

Anvisningar för hur du uppgraderar en befintlig agent för att använda ett gMSA-konto finns i [gruppera hanterade tjänst konton](how-to-install.md#group-managed-service-accounts).

### <a name="in-the-azure-active-directory-admin-center"></a>I Azure Active Directory administrations Center

1. Skapa ett hybrid identitets administratörs konto med enbart moln på din Azure AD-klient. På så sätt kan du hantera konfigurationen av din klient om dina lokala tjänster inte fungerar eller blir otillgängliga. Lär dig mer om hur du [lägger till ett moln med Hybrid identiteter som endast är moln](../fundamentals/add-users-azure-active-directory.md). Att slutföra det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
1. Lägg till ett eller flera [anpassade domän namn](../fundamentals/add-custom-domain.md) i Azure AD-klienten. Användarna kan logga in med ett av dessa domän namn.

### <a name="in-your-directory-in-active-directory"></a>I din katalog i Active Directory

Kör [IdFix-verktyget](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) för att förbereda katalog-attributen för synkronisering.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en domänansluten värd server som kör Windows Server 2016 eller senare med minst 4 GB RAM-minne och .NET 4.7.1 + Runtime.

2. Körnings principen för PowerShell på den lokala servern måste vara inställd på odefinierad eller RemoteSigned.

3. Om det finns en brand vägg mellan dina servrar och Azure AD konfigurerar du följande objekt:
    - Se till att agenter kan göra *utgående* begär anden till Azure AD över följande portar:

      | Portnummer | Hur den används |
      | --- | --- |
      | **80** | Hämtar listor över återkallade certifikat (CRL) När TLS/SSL-certifikatet verifierades.  |
      | **443** | Hanterar all utgående kommunikation med tjänsten. |
      | **8080** (valfritt) | Agenter rapporterar sin status var 10: e minut via port 8080, om port 443 inte är tillgänglig. Den här statusen visas i Azure AD-portalen. |

    - Om brandväggen framtvingar regler baserat på de användare som genererar den öppnar du dessa portar för trafik från Windows-tjänster som körs som en nätverkstjänst.
    - Om din brand vägg eller proxy låter dig ange säkra suffix lägger du till anslutningar till \* . msappproxy.net och \* . ServiceBus.Windows.net. Om inte, Tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)som uppdateras varje vecka.
    - Dina agenter behöver åtkomst till login.windows.net och login.microsoftonline.com för inledande registrering. Öppna brand väggen för dessa URL: er även.
    - För certifikat validering, avblockera följande URL: er: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 och www \. Microsoft.com:80. Dessa URL:er används för certifikatvalidering med andra Microsoft-produkter, och därför har du kanske redan avblockerat dem.

    >[!NOTE]
    > Det finns inte stöd för att installera moln etablerings agenten på Windows Server Core.

### <a name="additional-requirements"></a>Ytterligare krav

- [Microsoft .NET Framework-4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-krav

> [!NOTE]
> Transport Layer Security (TLS) är ett protokoll som möjliggör säker kommunikation. Att ändra TLS-inställningarna påverkar hela skogen. Mer information finns i [Uppdatera för att aktivera tls 1,1 och tls 1,2 som standard säkra protokoll i WinHTTP i Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Windows Server som är värd för Azure AD Connect Cloud Provisioning agent måste ha TLS 1,2 aktiverat innan du installerar det.

Följ dessa steg om du vill aktivera TLS 1,2.

1. Ange följande registernycklar:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starta om servern.

## <a name="known-limitations"></a>Kända begränsningar

Följande är kända begränsningar:

### <a name="delta-synchronization"></a>Deltasynkronisering

- Grupp definitions filtrering för delta-synkronisering stöder inte fler än 1500 medlemmar.
- När du tar bort en grupp som används som en del av ett grupp omfångs filter, tas inte användare som är medlemmar i gruppen bort. 
- När du byter namn på ORGANISATIONSENHETen eller gruppen som är inom omfånget tar delta synkronisering inte bort användarna.

### <a name="provisioning-logs"></a>Etableringsloggar
- Etablerings loggar skiljer sig inte tydligt mellan åtgärderna skapa och uppdatera.  Du kan se en skapa-åtgärd för en uppdatering och en uppdaterings åtgärd för en Create.

### <a name="group-re-naming-or-ou-re-naming"></a>Namnbyte för grupper eller ORGANISATIONSENHET
- Om du byter namn på en grupp eller ORGANISATIONSENHET i AD som är inom omfånget för en specifik konfiguration kan inte molnet Sync-jobbet identifiera namn ändringen i AD. Jobbet hamnar inte i karantän och fortsätter att vara felfritt.


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)