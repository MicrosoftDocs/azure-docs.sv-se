---
title: Felsöka domän koppling med Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du felsöker vanliga problem när du försöker att ansluta till en virtuell dator eller ansluta ett program till Azure Active Directory Domain Services och du inte kan ansluta eller autentisera till den hanterade domänen.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618611"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Felsök problem med domän anslutning med en Azure Active Directory Domain Services hanterad domän

När du försöker ansluta till en virtuell dator (VM) eller ansluta ett program till en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän, kan du få ett fel meddelande om att du inte kan göra det. Om du vill felsöka problem med domän anslutning kan du läsa om vilka av följande saker du får problem:

* Om du inte får någon autentiserings-prompt kan inte den virtuella datorn eller programmet ansluta till den hanterade Azure AD DS-domänen.
    * Starta för att felsöka [anslutnings problem för domän anslutning](#connectivity-issues-for-domain-join).
* Om du får ett fel under autentiseringen lyckas anslutningen till den hanterade domänen.
    * Starta för att felsöka [problem som rör autentiseringsuppgifter under domän anslutning](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Anslutnings problem för domän anslutning

Om den virtuella datorn inte kan hitta den hanterade domänen finns det vanligt vis en nätverks anslutning eller konfigurations problem. Granska följande fel söknings steg för att hitta och lösa problemet:

1. Se till att den virtuella datorn är ansluten till samma eller ett peer-kopplat virtuellt nätverk som den hanterade domänen. Om inte, kan den virtuella datorn inte hitta och ansluta till domänen för att ansluta.
    * Om den virtuella datorn inte är ansluten till samma virtuella nätverk bekräftar du att den virtuella nätverks-peering eller VPN-anslutningen är *aktiv* eller *ansluten* så att trafiken kan flöda korrekt.
1. Försök att pinga domänen med domän namnet för den hanterade domänen, till exempel `ping aaddscontoso.com` .
    * Om ping-svaret Miss lyckas kan du försöka pinga IP-adresserna för domänen som visas på sidan Översikt i portalen för din hanterade domän, till exempel `ping 10.0.0.4` .
    * Om du kan pinga IP-adressen, men inte domänen, kan DNS vara felaktigt konfigurerat. Kontrol lera att du har [konfigurerat de hanterade domänernas DNS-servrar för det virtuella nätverket][configure-dns].
1. Försök att tömma DNS-matcharens cacheminne på den virtuella datorn, till exempel `ipconfig /flushdns` .

### <a name="network-security-group-nsg-configuration"></a>Konfiguration av nätverks säkerhets grupp (NSG)

När du skapar en hanterad domän skapas även en nätverks säkerhets grupp med lämpliga regler för en lyckad domän åtgärd. Om du redigerar eller skapar ytterligare regler för nätverks säkerhets grupper kan du oavsiktligt blockera portar som krävs för att Azure AD DS ska kunna tillhandahålla anslutningar och autentisera tjänster. Dessa regler för nätverks säkerhets grupper kan orsaka problem som att synkronisering av lösen ord inte slutförs, användare som inte kan logga in eller problem med domän anslutning.

Om du fortfarande har anslutnings problem kan du läsa följande fel söknings steg:

1. Kontrol lera hälso statusen för den hanterade domänen i Azure Portal. Om du har en avisering för *AADDS001* blockerar en regel för nätverks säkerhets grupp åtkomst.
1. Granska de [nödvändiga portarna och reglerna för nätverks säkerhets gruppen][network-ports]. Kontrol lera att inga regler för nätverks säkerhets grupper tillämpas på den virtuella datorn eller det virtuella nätverk som du ansluter från blockera dessa nätverks portar.
1. När eventuella konfigurations problem för nätverks säkerhets grupper har lösts försvinner *AADDS001* -aviseringen från hälso sidan om 2 timmar. När nätverks anslutningen nu är tillgänglig kan du försöka att ansluta den virtuella datorn igen.

## <a name="credentials-related-issues-during-domain-join"></a>Problem relaterade till autentiseringsuppgifter under domän anslutning

Om du får en dialog ruta som frågar efter autentiseringsuppgifter för att ansluta till den hanterade domänen, kan den virtuella datorn ansluta till domänen med hjälp av det virtuella Azure-nätverket. Domän anslutnings processen Miss lyckas med att autentisera till domänen eller auktoriseringen för att slutföra domän anslutnings processen med hjälp av autentiseringsuppgifterna.

Information om hur du felsöker problem med autentiseringsuppgifter finns i följande fel söknings steg:

1. Försök att använda UPN-formatet för att ange autentiseringsuppgifter, till exempel `dee@contoso.onmicrosoft.com` . Kontrol lera att detta UPN är korrekt konfigurerat i Azure AD.
    * *SAMAccountName* för ditt konto kan skapas automatiskt om det finns flera användare med samma UPN-prefix i din klient eller om ditt UPN-prefix är för långt. Därför kan *sAMAccountName* -formatet för ditt konto skilja sig från vad du förväntar dig eller använder i din lokala domän.
1. Försök att använda autentiseringsuppgifterna för ett användar konto som är en del av den hanterade domänen för att ansluta virtuella datorer till den hanterade domänen.
1. Kontrol lera att du har [aktiverat][enable-password-sync] Lösenordssynkronisering och väntar tillräckligt länge för att den första lösen ords synkroniseringen ska slutföras.

## <a name="next-steps"></a>Nästa steg

En djupare förståelse för Active Directory processer som en del av åtgärden för domän anslutning finns i [sammanfogning och autentisering][join-authentication-issues].

Om du fortfarande har problem med att ansluta den virtuella datorn till den hanterade domänen kan du [hitta hjälp och öppna ett support ärende för Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
