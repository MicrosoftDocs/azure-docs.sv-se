---
title: Översikt – lokal AD DS-autentisering till Azure-filresurser
description: Lär dig mer om AD DS-autentisering (Active Directory Domain Services) till Azure-filresurser. Den här artikeln går igenom support scenarier, tillgänglighet och förklarar hur behörigheterna fungerar mellan AD DS och Azure Active Directory.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 03/15/2021
ms.author: rogarana
ms.openlocfilehash: 26932d05cd3d2ef7704b48463c895e05524d87f1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472134"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Översikt – lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser

[Azure Files](storage-files-introduction.md)   stöder identitets-baserad autentisering över Server Message Block (SMB) via två typer av domän tjänster: lokala Active Directory Domain Services (AD DS) och Azure Active Directory Domain Services (Azure AD DS). Vi rekommenderar starkt att du läser [avsnittet hur det fungerar](./storage-files-active-directory-overview.md#how-it-works) för att välja rätt domän tjänst för autentisering. Installations programmet skiljer sig åt beroende på vilken domän tjänst du väljer. Den här serien med artiklar fokuserar på att aktivera och konfigurera lokala AD DS för autentisering med Azure-filresurser.

Om du är nybörjare på Azure-filresurser rekommenderar vi att du läser vår [planerings guide](storage-files-planning.md) innan du läser följande serie artiklar.

## <a name="supported-scenarios-and-restrictions"></a>Scenarier och begränsningar som stöds

- AD DS-identiteter som används för Azure Files lokal AD DS-autentisering måste synkroniseras med Azure AD. Hash-synkronisering av lösen ord är valfritt. 
- Stöder Azure-filresurser som hanteras av Azure File Sync.
- Stöder Kerberos-autentisering med AD med RC4-HMAC och [AES 256-kryptering](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). Krypterings stöd för AES 256 är för närvarande begränsat till lagrings konton med namn <= 15 tecken långt. Kerberos-kryptering med AES 128 stöds inte ännu.
- Stöder enkel inloggning.
- Stöds endast på klienter som kör på OS-versioner som är nyare än Windows 7 eller Windows Server 2008 R2.
- Stöds endast mot den AD-skog som lagrings kontot är registrerat på. Du kan bara komma åt Azure-filresurser med AD DS-autentiseringsuppgifter från en enda skog som standard. Om du behöver åtkomst till Azure-filresursen från en annan skog kontrollerar du att rätt skogs förtroende har kon figurer ATS. mer information finns i [vanliga frågor och svar](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) .
- Stöder inte autentisering mot dator konton som skapats i AD DS.
- Stöder inte autentisering mot NFS-filresurser (Network File System).

När du aktiverar AD DS för Azure-filresurser över SMB kan AD DS-anslutna datorer montera Azure-filresurser med dina befintliga AD DS-autentiseringsuppgifter. Den här funktionen kan aktive ras med en AD DS-miljö som finns på lokal datorer eller som finns i Azure.

## <a name="videos"></a>Video

För att hjälpa dig att konfigurera Azure Files AD-autentisering för några vanliga användnings fall publicerade vi två videor med stegvis vägledning i följande scenarier:

| Ersätta lokala fil servrar med Azure Files (inklusive installations programmet på privat länk för filer och AD-autentisering) | Använda Azure Files som profil behållare för Windows Virtual Desktop (inklusive installations programmet för AD-autentisering och FsLogix-konfiguration)  |
|-|-|
| [![Skärm bild av video om att ersätta lokala fil servrar – Klicka för att spela upp.](./media/storage-files-identity-auth-active-directory-enable/replace-on-prem-server-thumbnail.png)](https://www.youtube.com/watch?v=jd49W33DxkQ) | [![Skärm utsändning av den använda Azure Files som profil behållar video-Klicka för att spela upp.](./media/storage-files-identity-auth-active-directory-enable/files-ad-ds-fslogix-thumbnail.png)](https://www.youtube.com/watch?v=9S5A1IJqfOQ) |


## <a name="prerequisites"></a>Förutsättningar 

Innan du aktiverar AD DS-autentisering för Azure-filresurser måste du kontrol lera att du har slutfört följande krav: 

- Välj eller skapa din [AD DS-miljö](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) och [synkronisera den till Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) med Azure AD Connect. 

    Du kan aktivera funktionen på en ny eller befintlig lokal AD DS-miljö. Identiteter som används för åtkomst måste synkroniseras med Azure AD. Azure AD-klienten och fil resursen som du använder måste vara associerad med samma prenumeration.

- Domän – Anslut en lokal dator eller en virtuell Azure-dator till en lokal AD DS. Information om hur du ansluter till en domän finns i [ansluta en dator till en domän](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

    Om datorn inte är domänansluten till en AD DS kan du fortfarande använda autentiseringsuppgifter för AD för autentisering om din dator har en detaljerad uppsättning av AD-domänkontrollanten.

- Välj eller skapa ett Azure Storage-konto.  För bästa prestanda rekommenderar vi att du distribuerar lagrings kontot i samma region som den klient som du planerar att komma åt resursen från. Montera sedan [Azure-filresursen](storage-how-to-use-files-windows.md) med din lagrings konto nyckel. Genom att montera med lagrings konto nyckeln verifieras anslutningen.

    Kontrol lera att lagrings kontot som innehåller dina fil resurser inte redan har kon figurer ATS för Azure AD DS-autentisering. Om Azure Files Azure AD DS-autentisering är aktiverat på lagrings kontot måste den inaktive ras innan den kan ändras till att använda lokala AD DS. Detta innebär att befintliga ACL: er som kon figurer ATS i Azure AD DS-miljön måste konfigureras om för korrekt behörighets tillämpning.


    Om du får problem med att ansluta till Azure Files kan du läsa [fel söknings verktyget som vi publicerade för Azure Files monterings fel i Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Vi ger också [vägledning](./storage-files-faq.md#on-premises-access) för att lösa scenarier när port 445 är blockerad. 


- Gör en relevant nätverks konfiguration innan du aktiverar och konfigurerar AD DS-autentisering till dina Azure-filresurser. Mer information finns i [Azure Files nätverks överväganden](storage-files-networking-overview.md) .

## <a name="regional-availability"></a>Regional tillgänglighet

Azure Files autentisering med AD DS är tillgängligt i [alla Azures offentliga, Kina och gov regioner](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Översikt

Om du planerar att aktivera alla nätverkskonfigurationer på din fil resurs, rekommenderar vi att du läser artikeln [nätverks överväganden](./storage-files-networking-overview.md) och slutför den relaterade konfigurationen innan du aktiverar AD DS-autentisering.

Genom att aktivera AD DS-autentisering för dina Azure-filresurser kan du autentisera till dina Azure-filresurser med dina lokal AD DS-autentiseringsuppgifter. Dessutom kan du hantera dina behörigheter bättre genom att tillåta detaljerad åtkomst kontroll. Detta kräver att du synkroniserar identiteter från lokal AD DS till Azure AD med AD Connect. Du styr åtkomsten till delnings nivå med identiteter som synkroniseras med Azure AD samtidigt som du hanterar åtkomsten på fil-/delnings nivå med lokal AD DS-autentiseringsuppgifter.

Följ sedan stegen nedan för att konfigurera Azure Files för AD DS-autentisering: 

1. [Del ett: Aktivera AD DS-autentisering på ditt lagrings konto](storage-files-identity-ad-ds-enable.md)

1. [Del två: tilldela åtkomst behörigheter för en resurs till Azure AD-identiteten (en användare, grupp eller tjänstens huvud namn) som är synkroniserad med mål-AD-identiteten](storage-files-identity-ad-ds-assign-permissions.md)

1. [Del tre: Konfigurera Windows ACL: er över SMB för kataloger och filer](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Del fyra: montera en Azure-filresurs till en virtuell dator som är ansluten till AD DS](storage-files-identity-ad-ds-mount-file-share.md)

1. [Uppdatera lösen ordet för din lagrings konto identitet i AD DS](storage-files-identity-ad-ds-update-password.md)

Följande diagram illustrerar arbets flödet från slut punkt till slut punkt för att aktivera Azure AD-autentisering över SMB för Azure-filresurser. 

![AD Workflow-diagram för filer](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Identiteter som används för att få åtkomst till Azure-filresurser måste synkroniseras med Azure AD för att upprätthålla fil behörigheter på resurs nivå via Azure RBAC-modellen [(rollbaserad åtkomst kontroll)](../../role-based-access-control/overview.md) . [DACL i Windows-format](/previous-versions/technet-magazine/cc161041(v=msdn.10)) på filer/kataloger som överförs från befintliga fil servrar bevaras och tillämpas. Detta erbjuder sömlös integrering med din Enterprise AD DS-miljö. När du ersätter lokal-filservrar med Azure-filresurser kan befintliga användare komma åt Azure-filresurser från sina aktuella klienter med enkel inloggning, utan någon ändring av de autentiseringsuppgifter som används.  

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa artikel om du vill aktivera lokal AD DS-autentisering för Azure-filresursen:

[Del ett: Aktivera AD DS-autentisering för ditt konto](storage-files-identity-ad-ds-enable.md)
