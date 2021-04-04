---
title: Lös säkra LDAP-aviseringar i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du felsöker och löser vanliga aviseringar med säker LDAP för Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15c1f3a1731edf7b45061646d43688b4aacc6104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620315"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Kända problem: säkert LDAP aviseringar i Azure Active Directory Domain Services

Program och tjänster som använder LDAP (Lightweight Directory Access Protocol) för att kommunicera med Azure Active Directory Domain Services (Azure AD DS) kan [konfigureras för att använda säker LDAP](tutorial-configure-ldaps.md). Ett lämpligt certifikat och nödvändiga nätverks portar måste vara öppna för att säker LDAP ska fungera korrekt.

Den här artikeln hjälper dig att förstå och lösa vanliga aviseringar med säker LDAP-åtkomst i Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: säkert LDAP nätverks konfiguration

### <a name="alert-message"></a>Aviserings meddelande

*Säkert LDAP över Internet är aktiverat för den hanterade domänen. Åtkomst till port 636 är dock inte låst med en nätverks säkerhets grupp. Detta kan innebära att användar konton på den hanterade domänen används för lösen ords brutet angrepp.*

### <a name="resolution"></a>Lösning

När du aktiverar säker LDAP rekommenderar vi att du skapar ytterligare regler som begränsar inkommande LDAPs åtkomst till vissa IP-adresser. Reglerna skyddar den hanterade domänen från brute force-attacker. Utför följande steg för att uppdatera nätverks säkerhets gruppen så att den begränsar TCP-port 636-åtkomst för säker LDAP:

1. Sök efter och välj **nätverks säkerhets grupper** i Azure Portal.
1. Välj den nätverks säkerhets grupp som är kopplad till din hanterade domän, t. ex. *AADDS-contoso.com-NSG*, och välj sedan **inkommande säkerhets regler**
1. Välj **+ Lägg** till för att skapa en regel för TCP-port 636. Om det behövs väljer du **Avancerat** i fönstret för att skapa en regel.
1. För **källan** väljer du *IP-adresser* på den nedrullningsbara menyn. Ange de käll-IP-adresser som du vill bevilja åtkomst för säker LDAP-trafik.
1. Välj *valfri* som **mål** och ange sedan *636* för **mål ports intervall**.
1. Ange **protokollet** som *TCP* och **åtgärden** som ska *tillåtas*.
1. Ange regelns prioritet och ange sedan ett namn som *RestrictLDAPS*.
1. När du är klar väljer du **Lägg till** för att skapa regeln.

Den hanterade domänens hälsa uppdateras automatiskt inom två timmar och aviseringen tas bort.

> [!TIP]
> TCP-port 636 är inte den enda regeln som krävs för att Azure AD DS ska kunna köras smidigt. Mer information finns i [nätverks säkerhets grupper för Azure AD DS och de portar som krävs](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: säkert LDAP certifikat upphör att gälla

### <a name="alert-message"></a>Aviserings meddelande

*Det säkra LDAP-certifikatet för den hanterade domänen upphör att gälla [datum]].*

### <a name="resolution"></a>Lösning

Skapa ett nytt, säkert LDAP-certifikat genom att följa stegen för att [skapa ett certifikat för säker LDAP](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Tillämpa ersättnings certifikatet på Azure AD DS och distribuera certifikatet till alla klienter som ansluter med hjälp av säker LDAP.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
