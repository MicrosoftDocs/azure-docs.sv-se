---
title: Felsöka inloggnings problem i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du felsöker vanliga användar inloggnings problem och fel i Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618577"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Felsöka konto inloggnings problem med en Azure Active Directory Domain Services hanterad domän

De vanligaste orsakerna till ett användar konto som inte kan logga in till en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän innehåller följande scenarier:

* [Kontot är inte synkroniserat med Azure AD DS ännu.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS har inte lösen ordets hash-värden för att tillåta kontot att logga in.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Kontot är utelåst.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS kan inte synkronisera i autentiseringsuppgifter för konton som är externa för Azure AD-klienten. Externa användare kan inte logga in på den hanterade domänen i Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Kontot är inte synkroniserat med Azure AD DS ännu

Beroende på katalogens storlek kan det ta en stund innan användar konton och inloggnings-hashar är tillgängliga i en hanterad domän. För stora kataloger kan den här inledande enkelriktade synkroniseringen från Azure AD ta några timmar och upp till en dag eller två. Se till att du väntar tillräckligt länge innan du försöker autentisera igen.

Se till att du kör den senaste versionen av Azure AD Connect och har [konfigurerat Azure AD Connect för att utföra en fullständig synkronisering när du har aktiverat Azure AD DS][azure-ad-connect-phs]för Hybrid miljöer som användar Azure AD Connect synkronisera lokala katalog data i Azure AD. Om du inaktiverar Azure AD DS och sedan aktiverar igen måste du följa de här stegen igen.

Om du fortsätter att ha problem med konton som inte synkroniseras via Azure AD Connect startar du om Azure AD Sync tjänsten. Öppna ett kommando tolks fönster från datorn med Azure AD Connect installerat och kör sedan följande kommandon:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS har inte lösen ordets hash-värden

Azure AD genererar eller lagrar inte lösen ordets hash-värden i det format som krävs för NTLM-eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhets skäl lagrar Azure AD inte heller lösen ords referenser i klartext-format. Därför kan inte Azure AD automatiskt generera dessa NTLM-eller Kerberos-hashvärden utifrån användarnas befintliga autentiseringsuppgifter.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hybrid miljöer med lokal synkronisering

För Hybrid miljöer som använder Azure AD Connect för att synkronisera från en lokal AD DS-miljö kan du lokalt generera och synkronisera de obligatoriska NTLM-eller Kerberos-hashvärden i Azure AD. När du har skapat din hanterade domän [aktiverar du Azure Active Directory Domain Services med hash-synkronisering av lösen ord][azure-ad-connect-phs]. Det går inte att logga in på ett konto med den hanterade domänen utan att slutföra det här steget för synkronisering av lösen ord. Om du inaktiverar Azure AD DS och sedan aktiverar igen måste du följa dessa steg igen.

Mer information finns i [så här fungerar hash-synkronisering av lösen ord för Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Miljöer med enbart moln utan lokal synkronisering

Hanterade domäner utan lokal synkronisering, endast konton i Azure AD, måste också generera nödvändiga NTLM-eller Kerberos-hashvärden. Om ett moln konto inte kan logga in, har en process för lösen ords ändring slutförts för kontot när Azure AD DS har Aktiver ATS?

* **Nej, lösen ordet har inte ändrats.**
    * [Ändra lösen ordet för kontot][enable-user-accounts] för att generera nödvändiga lösen ords-hashar och vänta sedan i 15 minuter innan du försöker logga in igen.
    * Om du inaktiverar Azure AD DS och sedan aktiverar igen måste varje konto följa stegen igen för att ändra lösen ordet och generera nödvändiga lösen ords-hashar.
* **Ja, lösen ordet har ändrats.**
    * Försök att logga in med *UPN* -formatet, t `driley@aaddscontoso.com` . ex. i stället för *sAMAccountName* - `AADDSCONTOSO\deeriley` formatet.
    * *SAMAccountName* kan genereras automatiskt för användare vars UPN-prefix är över långt eller samma som en annan användare på den hanterade domänen. *UPN* -formatet garanterar att det är unikt i en Azure AD-klient.

## <a name="the-account-is-locked-out"></a>Kontot är utelåst

Ett användar konto i en hanterad domän är utelåst när ett definierat tröskelvärde för misslyckade inloggnings försök har uppfyllts. Detta konto utelåsning är utformat för att skydda dig från upprepade återkrävda inloggnings försök som kan tyda på ett automatiskt digitalt angrepp.

Om det finns fem dåliga lösen ords försök på 2 minuter låses kontot som standard i 30 minuter.

Mer information och hur du löser problem med konto utelåsning finns i [Felsöka problem med konto utelåsning i Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem med att ansluta den virtuella datorn till den hanterade domänen kan du [hitta hjälp och öppna ett support ärende för Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md