---
title: 'Azure AD Connect Cloud Provisioning agent: hantera register alternativ | Microsoft Docs'
description: Den här artikeln beskriver hur du hanterar register alternativ i Azure AD Connect Cloud Provisioning-agenten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.reviewer: chmutali
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f39ef611e2ea15ef3bc3dbfcf09e9624cbcf8b3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678229"
---
# <a name="manage-agent-registry-options"></a>Hantera agentens register alternativ

I det här avsnittet beskrivs register alternativ som du kan ange för att styra körnings beteendet för Azure AD Connect etablerings agenten. 

## <a name="configure-ldap-connection-timeout"></a>Konfigurera tids gräns för LDAP-anslutning
När du utför LDAP-åtgärder på konfigurerade Active Directory domänkontrollanter använder etablerings agenten som standard tids gräns värdet för anslutning på 30 sekunder. Om domänkontrollanten tar längre tid att svara kan följande fel meddelande visas i agent logg filen: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

LDAP search-åtgärder kan ta längre tid om sökattributet inte är indexerat. Som ett första steg, om du får ovanstående fel, kontrollerar du först om attributet search/lookup är [Indexerat](/windows/win32/ad/indexed-attributes). Om sökattributen är indexerade och felet kvarstår kan du öka tids gränsen för LDAP-anslutningen med hjälp av följande steg: 

1. Logga in som administratör på Windows Server som kör Azure AD Connect etablerings agenten.
1. Använd meny alternativet *Kör* för att öppna registereditorn (regedit.exe) 
1. Leta upp mappen **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Högerklicka och välj "New-> sträng värde"
1. Ange namnet: `LdapConnectionTimeoutInMilliseconds`
1. Dubbelklicka på **värde namnet** och ange värde data som `60000` millisekunder.
    > [!div class="mx-imgBorder"]
    > ![Timeout för LDAP-anslutning](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Starta om Azure AD Connect etablerings tjänsten från *tjänst* konsolen.
1. Om du har distribuerat flera etablerings agenter ska du tillämpa den här register ändringen på alla agenter för konsekvens. 

## <a name="configure-referral-chasing"></a>Konfigurera hänvisnings jaga
Som standard visar Azure AD Connect etablerings agenten inte [hänvisningar](/windows/win32/ad/referrals). Du kanske vill aktivera hänvisnings-jaga för att stödja vissa HR-scenarier för inkommande etablering, till exempel: 
* Kontrol lera unikt UPN över flera domäner
* Lösa kors domän hanterarens referenser

Använd följande steg för att aktivera hänvisnings jaga:

1. Logga in som administratör på Windows Server som kör Azure AD Connect etablerings agenten.
1. Använd meny alternativet *Kör* för att öppna registereditorn (regedit.exe) 
1. Leta upp mappen **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Högerklicka och välj "New-> sträng värde"
1. Ange namnet: `ReferralChasingOptions`
1. Dubbelklicka på **värde namnet** och ange värde data som `96` . Det här värdet motsvarar det konstanta värdet för `ReferralChasingOptions.All` och anger att både under träd och referenser på grund nivå ska följas av agenten. 
    > [!div class="mx-imgBorder"]
    > ![Hänvisnings jaga](media/how-to-manage-registry-options/referral-chasing.png)
1. Starta om Azure AD Connect etablerings tjänsten från *tjänst* konsolen.
1. Om du har distribuerat flera etablerings agenter ska du tillämpa den här register ändringen på alla agenter för konsekvens.

## <a name="skip-gmsa-configuration"></a>Hoppa över GMSA-konfiguration
Med agent version 1.1.281.0 +, som standard, när du kör guiden agent konfiguration, uppmanas du att konfigurera [grupphanterat tjänst konto (gMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview). GMSA-installationen av guiden används vid körning för alla synkroniserings-och etablerings åtgärder. 

Om du uppgraderar från en tidigare version av agenten och har konfigurerat ett anpassat tjänst konto med delegerade behörigheter för OU-nivå som är unik för din Active Directory topologi, kanske du vill hoppa över/skjuta upp GMSA-konfigurationen och planera för den här ändringen. 

> [!NOTE]
> Den här vägledningen gäller specifikt för kunder som har konfigurerat inkommande etablering för HR (Workday/SuccessFactors) med agent versioner före 1.1.281.0 och har konfigurerat ett anpassat tjänst konto för agent åtgärder. I lång körning rekommenderar vi att du växlar till GMSA som bästa praxis.  

I det här scenariot kan du fortfarande uppgradera agentens binärfiler och hoppa över GMSA-konfigurationen med följande steg: 

1. Logga in som administratör på Windows Server som kör Azure AD Connect etablerings agenten.
1. Kör agent installations programmet för att installera de nya binärfilerna för agenten. Stäng guiden agent konfiguration som öppnas automatiskt när installationen har slutförts. 
1. Använd meny alternativet *Kör* för att öppna registereditorn (regedit.exe) 
1. Leta upp mappen **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Högerklicka och välj "New-> DWORD-värde"
1. Ange namnet: `UseCredentials`
1. Dubbelklicka på **värde namnet** och ange värde data som `1` .  
    > [!div class="mx-imgBorder"]
    > ![Använd autentiseringsuppgifter](media/how-to-manage-registry-options/use-credentials.png)
1. Starta om Azure AD Connect etablerings tjänsten från *tjänst* konsolen.
1. Om du har distribuerat flera etablerings agenter ska du tillämpa den här register ändringen på alla agenter för konsekvens.
1. Kör guiden agent konfiguration från Skriv bordet kort. Guiden hoppar över GMSA-konfigurationen. 


> [!NOTE]
> Du kan bekräfta att register alternativen har angetts genom att aktivera [utförlig loggning](how-to-troubleshoot.md#log-files). Loggarna som genereras under agent starten visar de konfigurations värden som valts från registret. 

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)

