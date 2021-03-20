---
title: Felsöka säker LDAP i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du felsöker säker LDAP (LDAPs) för en Azure Active Directory Domain Services hanterad domän
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e21ec7d8fdf7a5c7a8ba7a9f22aa5709581cbae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618407"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Felsöka problem med säker LDAP-anslutning till en Azure Active Directory Domain Services hanterad domän

Program och tjänster som använder LDAP (Lightweight Directory Access Protocol) för att kommunicera med Azure Active Directory Domain Services (Azure AD DS) kan [konfigureras för att använda säker LDAP](tutorial-configure-ldaps.md). Ett lämpligt certifikat och nödvändiga nätverks portar måste vara öppna för att säker LDAP ska fungera korrekt.

Den här artikeln hjälper dig att felsöka problem med säker LDAP-åtkomst i Azure AD DS.

## <a name="common-connection-issues"></a>Vanliga anslutningsproblem

Om du har problem med att ansluta till en Azure AD DS-hanterad domän med säker LDAP kan du läsa följande fel söknings steg. Efter varje fel söknings steg försöker du ansluta till den hanterade domänen igen:

* Utfärdarens kedja av det säkra LDAP-certifikatet måste vara betrodd på klienten. Du kan lägga till rot certifikat utfärdaren (CA) i det betrodda rot certifikat arkivet på klienten för att upprätta förtroendet.
    * Se till att [Exportera och tillämpa certifikatet på klient datorer][client-cert].
* Kontrol lera att det säkra LDAP-certifikatet för din hanterade domän har DNS-namnet i attributen *subject* eller *Alternativt namn på certifikat mottagare* .
    * Granska [kraven för säkra LDAP-certifikat][certs-prereqs] och skapa ett ersättnings certifikat om det behövs.
* Kontrol lera att LDAP-klienten, till exempel *ldp.exe* ansluter till en säker LDAP-slutpunkt med ett DNS-namn, inte IP-adressen.
    * Certifikatet som tillämpas på den hanterade domänen innehåller inte tjänstens IP-adresser, bara DNS-namnen.
* Kontrol lera DNS-namnet som LDAP-klienten ansluter till. Den måste matcha den offentliga IP-adressen för säker LDAP på den hanterade domänen.
    * Om DNS-namnet matchar den interna IP-adressen uppdaterar du DNS-posten för att matcha den externa IP-adressen.
* För extern anslutning måste nätverks säkerhets gruppen innehålla en regel som tillåter trafik till TCP-port 636 från Internet.
    * Om du kan ansluta till den hanterade domänen med hjälp av säker LDAP från resurser som är direkt anslutna till det virtuella nätverket, men inte externa anslutningar, måste du se till att [skapa en regel för nätverks säkerhets grupper för att tillåta säker LDAP-trafik][ldaps-nsg].

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
