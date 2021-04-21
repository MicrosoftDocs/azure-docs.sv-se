---
title: Vanliga frågor och svar om Azure AD Domain Services | Microsoft Docs
description: Läs och förstå några av de vanliga frågorna om konfiguration, administration och tillgänglighet för Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/09/2021
ms.author: justinha
ms.openlocfilehash: 32dcf2b387231d50796de0036388b53cab83bf72
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749338"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Vanliga frågor och svar om Azure Active Directory (AD) Domain Services

Den här sidan besvarar vanliga frågor om Azure Active Directory Domain Services.

## <a name="configuration"></a>Konfiguration

* [Kan jag skapa flera hanterade domäner för en enda Azure AD-katalog?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Kan jag aktivera Azure AD Domain Services i ett klassiskt virtuellt nätverk?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Kan jag aktivera Azure AD Domain Services i ett Azure Resource Manager virtuellt nätverk?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Kan jag migrera min befintliga hanterade domän från ett klassiskt virtuellt nätverk till ett Resource Manager virtuellt nätverk?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Kan jag aktivera Azure AD Domain Services i en Azure CSP-prenumeration (Molnlösningsleverantör)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Kan jag aktivera Azure AD Domain Services i en federerad Azure AD-katalog? Jag synkroniserar inte lösenordshashar till Azure AD. Kan jag aktivera Azure AD Domain Services för den här katalogen?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Kan jag göra Azure AD Domain Services tillgängliga i flera virtuella nätverk i min prenumeration?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Kan jag aktivera Azure AD Domain Services med PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Kan jag aktivera Azure AD Domain Services en Resource Manager mall?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Kan jag lägga till domänkontrollanter i en Azure AD Domain Services hanterad domän?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Kan gästanvändare bjudas in till min katalog med Azure AD Domain Services?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Kan jag flytta en befintlig Azure AD Domain Services hanterad domän till en annan prenumeration, resursgrupp, region eller virtuellt nätverk?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Kan jag byta namn på Azure AD Domain Services ett befintligt domännamn?](#can-i-rename-an-existing-azure-ad-domain-services-domain-name)
* [Innehåller Azure AD Domain Services alternativ för hög tillgänglighet?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Kan jag skapa flera hanterade domäner för en enda Azure AD-katalog?
Nej. Du kan bara skapa en enda hanterad domän som hanteras av Azure AD Domain Services för en enda Azure AD-katalog.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Kan jag aktivera Azure AD Domain Services i ett klassiskt virtuellt nätverk?
Klassiska virtuella nätverk stöds inte för nya distributioner. Befintliga hanterade domäner som distribueras i klassiska virtuella nätverk fortsätter att stödjas tills de dras tillbaka den 1 mars 2023. För befintliga distributioner kan du migrera [Azure AD Domain Services från den klassiska virtuella nätverksmodellen till Resource Manager](migrate-from-classic-vnet.md).

Mer information finns i det [officiella meddelandet om utfasning.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Kan jag aktivera Azure AD Domain Services i ett Azure Resource Manager virtuellt nätverk?
Ja. Azure AD Domain Services kan aktiveras i ett Azure Resource Manager virtuellt nätverk. Klassiska virtuella Azure-nätverk är inte längre tillgängliga när du skapar en hanterad domän.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Kan jag migrera min befintliga hanterade domän från ett klassiskt virtuellt nätverk till ett Resource Manager virtuellt nätverk?
Ja. Mer information finns i Migrera [Azure AD Domain Services från den klassiska virtuella nätverksmodellen till Resource Manager](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Kan jag aktivera Azure AD Domain Services i en Azure CSP-prenumeration (Molnlösningsleverantör)?
Ja. Mer information finns i så [här aktiverar du Azure AD Domain Services i Azure CSP prenumerationer](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Kan jag aktivera Azure AD Domain Services i en federerad Azure AD-katalog? Jag synkroniserar inte lösenordshashar till Azure AD. Kan jag aktivera Azure AD Domain Services för den här katalogen?
Nej. För att autentisera användare via NTLM eller Kerberos Azure AD Domain Services åtkomst till lösenordshashar för användarkonton. I en federerad katalog lagras inte lösenordshashar i Azure AD-katalogen. Därför Azure AD Domain Services fungerar inte med sådana Azure AD-kataloger.

Men om du använder Azure AD Connect synkronisering av lösenordshashar kan du använda Azure AD Domain Services eftersom hashvärdena för lösenord lagras i Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Kan jag göra Azure AD Domain Services tillgängliga i flera virtuella nätverk i min prenumeration?
Själva tjänsten har inte direkt stöd för det här scenariot. Din hanterade domän är endast tillgänglig i ett virtuellt nätverk i taget. Du kan dock konfigurera anslutningen mellan flera virtuella nätverk för att exponera Azure AD Domain Services andra virtuella nätverk. Mer information finns i Ansluta [virtuella nätverk i Azure med HJÄLP av VPN-gatewayer eller](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) [peering för virtuella nätverk.](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Kan jag aktivera Azure AD Domain Services med PowerShell?
Ja. Mer information finns i så [här aktiverar du Azure AD Domain Services powershell.](powershell-create-instance.md)

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kan jag aktivera Azure AD Domain Services med en Resource Manager mall?
Ja, du kan skapa en Azure AD Domain Services hanterad domän med en Resource Manager mall. Ett huvudnamn för tjänsten och en Azure AD-grupp för administration måste skapas med hjälp Azure Portal eller Azure PowerShell innan mallen distribueras. Mer information finns i Skapa [en Azure AD DS hanterad domän med hjälp av en Azure Resource Manager mall](template-create-instance.md). När du skapar Azure AD Domain Services hanterad domän i Azure Portal finns det också ett alternativ för att exportera mallen för användning med ytterligare distributioner.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Kan jag lägga till domänkontrollanter i en Azure AD Domain Services hanterad domän?
Nej. Domänen som tillhandahålls av Azure AD Domain Services är en hanterad domän. Du behöver inte etablera, konfigurera eller på annat sätt hantera domänkontrollanter för den här domänen. Dessa hanteringsaktiviteter tillhandahålls som en tjänst av Microsoft. Därför kan du inte lägga till ytterligare domänkontrollanter (skrivskyddade eller skrivskyddade) för den hanterade domänen.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Kan gästanvändare bjudas in till min katalog med Azure AD Domain Services?
Nej. Gästanvändare som bjudits in till din Azure AD-katalog med hjälp av processen för [Azure AD B2B-inbjudan](../active-directory/external-identities/what-is-b2b.md) synkroniseras till din Azure AD Domain Services hanterade domän. Lösenord för dessa användare lagras dock inte i Azure AD-katalogen. Därför har Azure AD Domain Services möjlighet att synkronisera NTLM- och Kerberos-hash-värden för dessa användare till din hanterade domän. Sådana användare kan inte logga in eller ansluta datorer till den hanterade domänen.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Kan jag flytta en befintlig Azure AD Domain Services hanterad domän till en annan prenumeration, resursgrupp, region eller virtuellt nätverk?
Nej. När du har Azure AD Domain Services en hanterad domän kan du inte flytta den hanterade domänen till en annan resursgrupp, ett virtuellt nätverk, en prenumeration osv. Var noga med att välja den lämpligaste prenumerationen, resursgruppen, regionen och det virtuella nätverket när du distribuerar den hanterade domänen.

### <a name="can-i-rename-an-existing-azure-ad-domain-services-domain-name"></a>Kan jag byta namn på Azure AD Domain Services ett befintligt domännamn?
Nej. När du har skapat Azure AD Domain Services hanterad domän kan du inte ändra DNS-domännamnet. Välj DNS-domännamnet noggrant när du skapar den hanterade domänen. Överväganden när du väljer DNS-domännamn finns i självstudien för [att skapa och konfigurera en Azure AD Domain Services hanterad domän.](tutorial-create-instance.md#create-a-managed-domain)

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Innehåller Azure AD Domain Services alternativ för hög tillgänglighet?

Ja. Varje Azure AD Domain Services hanterad domän innehåller två domänkontrollanter. Du hanterar eller ansluter inte till dessa domänkontrollanter, de ingår i den hanterade tjänsten. Om du Azure AD Domain Services till en region som stöder Tillgänglighetszoner distribueras domänkontrollanterna mellan zoner. I regioner som inte stöder Tillgänglighetszoner distribueras domänkontrollanterna över tillgänglighetsuppsättningar. Du har inga konfigurationsalternativ eller hanteringskontroll över den här distributionen. Mer information finns i [Tillgänglighetsalternativ för virtuella datorer i Azure](../virtual-machines/availability.md).

## <a name="administration-and-operations"></a>Administration och åtgärder

* [Kan jag ansluta till domänkontrollanten för min hanterade domän med fjärrskrivbord?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Jag har aktiverat Azure AD Domain Services. Vilket användarkonto använder jag för domänkoppling av datorer till den här domänen?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Har jag domänadministratörsbehörighet för den hanterade domän som tillhandahålls av Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan jag ändra gruppmedlemskap med LDAP eller andra ADMINISTRATIVA AD-verktyg på hanterade domäner?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Hur lång tid tar det innan ändringar som jag gör i Azure AD-katalogen visas i min hanterade domän?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kan jag utöka schemat för den hanterade domän som tillhandahålls av Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Kan jag ändra eller lägga till DNS-poster i min hanterade domän?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Vad är principen för lösenordslivslängd på en hanterad domän?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Ger Azure AD Domain Services skydd mot AD-kontolåsning?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Kan jag konfigurera Distributed File System (DFS) och replikering inom Azure AD Domain Services?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Hur tillämpas Windows-uppdateringar i Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Kan jag ansluta till domänkontrollanten för min hanterade domän med fjärrskrivbord?
Nej. Du har inte behörighet att ansluta till domänkontrollanter för den hanterade domänen med hjälp av Fjärrskrivbord. Medlemmar i *gruppen AAD DC-administratörer* kan administrera den hanterade domänen med hjälp av AD-administrationsverktyg som Active Directory Administrationscenter (ADAC) eller AD PowerShell. Dessa verktyg installeras med hjälp verktyg för fjärrserveradministration *på* en Windows-server som är ansluten till den hanterade domänen. Mer information finns i Skapa [en virtuell hanterings-VM för att konfigurera och administrera en Azure AD Domain Services hanterad domän.](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Jag har aktiverat Azure AD Domain Services. Vilket användarkonto använder jag för domänkoppling av datorer till den här domänen?
Alla användarkonton som ingår i den hanterade domänen kan ansluta till en virtuell dator. Medlemmar i *gruppen AAD DC-administratörer* beviljas fjärrskrivbordsåtkomst till datorer som har anslutits till den hanterade domänen.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Har jag domänadministratörsbehörighet för den hanterade domän som tillhandahålls av Azure AD Domain Services?
Nej. Du beviljas inte administratörsbehörighet på den hanterade domänen. *Domänadministratörs-* *och* företagsadministratörsbehörigheter är inte tillgängliga för dig att använda i domänen. Medlemmar i domänadministratörs- eller företagsadministratörsgrupperna i din lokal Active Directory beviljas inte heller domän-/företagsadministratörsbehörighet på den hanterade domänen.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Kan jag ändra gruppmedlemskap med LDAP eller andra ADMINISTRATIVA AD-verktyg på hanterade domäner?
Användare och grupper som synkroniseras från Azure Active Directory till Azure AD Domain Services inte ändras eftersom deras ursprung är Azure Active Directory. Detta omfattar att flytta användare eller grupper från den hanterade organisationsenheten AADDC-användare till en anpassad organisationsenhet. Alla användare eller grupper som har sitt ursprung i den hanterade domänen kan ändras.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Hur lång tid tar det innan ändringar jag gör i min Azure AD-katalog visas i min hanterade domän?
Ändringar som görs i Azure AD-katalogen med hjälp av antingen Azure AD-användargränssnittet eller PowerShell synkroniseras automatiskt till din hanterade domän. Den här synkroniseringsprocessen körs i bakgrunden. Det finns ingen definierad tidsperiod för den här synkroniseringen för att slutföra alla objektändringar.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kan jag utöka schemat för den hanterade domän som tillhandahålls av Azure AD Domain Services?
Nej. Schemat administreras av Microsoft för den hanterade domänen. Schematillägg stöds inte av Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Kan jag ändra eller lägga till DNS-poster i min hanterade domän?
Ja. Medlemmar i *gruppen AAD DC-administratörer* beviljas DNS-administratörsbehörighet för att ändra DNS-poster i den hanterade domänen.  Dessa användare kan använda DNS Manager-konsolen på en dator som kör Windows Server som är ansluten till den hanterade domänen för att hantera DNS. Om du vill använda DNS Manager-konsolen installerar du *DNS Server Tools*, som är en del *verktyg för fjärrserveradministration* valfri funktion på servern. Mer information finns i Administrera [DNS i en Azure AD Domain Services hanterad domän.](manage-dns.md)

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Vad är principen för lösenordslivslängd på en hanterad domän?
Standardlivslängden för lösenord på en Azure AD Domain Services hanterad domän är 90 dagar. Lösenordets livslängd synkroniseras inte med lösenordets livslängd som konfigurerats i Azure AD. Därför kan du ha en situation där användarnas lösenord upphör att gälla i din hanterade domän, men fortfarande är giltiga i Azure AD. I sådana scenarier måste användarna ändra sitt lösenord i Azure AD och det nya lösenordet synkroniseras till din hanterade domän. Om du vill ändra standardlivslängden för lösenord i en hanterad domän kan [du skapa och konfigurera anpassade lösenordsprinciper.](password-policy.md).

Dessutom synkroniseras Azure AD-lösenordsprincipen *för DisablePasswordExpiration* till en hanterad domän. När *DisablePasswordExpiration* tillämpas på en användare i Azure AD tillämpas *userAccountControl-värdet* för den *synkroniserade användaren i den hanterade domänen DONT_EXPIRE_PASSWORD* tillämpas.

När användarna återställer sina lösenord i Azure AD tillämpas *attributet forceChangePasswordNextSignIn=True.* En hanterad domän synkroniserar det här attributet från Azure AD. När den hanterade domänen identifierar *forceChangePasswordNextSignIn* har angetts för en synkroniserad användare från Azure AD anges *attributet pwdLastSet* i den hanterade domänen till *0*, vilket gör lösenordet som angetts ogiltigt.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Ger Azure AD Domain Services skydd mot AD-kontolåsning?
Ja. Fem ogiltiga lösenordsförsök inom 2 minuter på den hanterade domänen gör att ett användarkonto blir utelåst i 30 minuter. Efter 30 minuter låses användarkontot upp automatiskt. Ogiltiga lösenordsförsök på den hanterade domänen låser inte användarkontot i Azure AD. Användarkontot är endast utelåst inom din Azure AD Domain Services hanterade domän. Mer information finns i Principer [för lösenords- och kontolåsning på hanterade domäner.](password-policy.md)

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Kan jag konfigurera Distributed File System och replikering inom Azure AD Domain Services?
Nej. Distributed File System (DFS) och replikering är inte tillgängliga när du använder Azure AD Domain Services.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Hur tillämpas Windows-uppdateringar i Azure AD Domain Services?
Domänkontrollanter i en hanterad domän tillämpar automatiskt nödvändiga Windows-uppdateringar. Det finns inget du kan konfigurera eller administrera här. Se till att du inte skapar regler för nätverkssäkerhetsgrupp som blockerar utgående trafik till Windows Updates. För dina egna virtuella datorer som är ansluten till den hanterade domänen ansvarar du för att konfigurera och tillämpa eventuella nödvändiga os- och programuppdateringar.

## <a name="billing-and-availability"></a>Fakturering och tillgänglighet

* [Är Azure AD Domain Services en betald tjänst?](#is-azure-ad-domain-services-a-paid-service)
* [Finns det en kostnadsfri utvärderingsversion av tjänsten?](#is-there-a-free-trial-for-the-service)
* [Kan jag pausa en Azure AD Domain Services hanterad domän?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan jag redundans redundans Azure AD Domain Services till en annan region för en DR-händelse?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kan jag få Azure AD Domain Services som en del av Enterprise Mobility Suite (EMS)? Behöver jag Azure AD Premium använda Azure AD Domain Services?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [I vilka Azure-regioner är tjänsten tillgänglig?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Är Azure AD Domain Services en betald tjänst?
Ja. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Finns det en kostnadsfri utvärderingsversion av tjänsten?
Azure AD Domain Services ingår i den kostnadsfria utvärderingsversionen för Azure. Du kan registrera dig för en [kostnadsfri enmånads utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Kan jag pausa en Azure AD Domain Services hanterad domän?
Nej. När du har aktiverat en Azure AD Domain Services hanterad domän är tjänsten tillgänglig i det valda virtuella nätverket tills du tar bort den hanterade domänen. Det går inte att pausa tjänsten. Faktureringen fortsätter varje timme tills du tar bort den hanterade domänen.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Kan jag redundans redundans Azure AD Domain Services till en annan region för en DR-händelse?
Ja, om du vill ge geografisk återhämtning för [](tutorial-create-replica-set.md) en hanterad domän kan du skapa ytterligare en replikuppsättning till ett peer-peer-erat virtuellt nätverk i valfri Azure-region som stöder Azure AD DS. Replikuppsättningar delar samma namnområde och konfiguration med den hanterade domänen.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kan jag få Azure AD Domain Services som en del av Enterprise Mobility Suite (EMS)? Behöver jag Azure AD Premium använda Azure AD Domain Services?
Nej. Azure AD Domain Services är en Azure-tjänst med betala per användning och ingår inte i EMS. Azure AD Domain Services kan användas med alla utgåvor av Azure AD (kostnadsfri och Premium). Du debiteras per timme, beroende på användning.

### <a name="what-azure-regions-is-the-service-available-in"></a>I vilka Azure-regioner finns tjänsten tillgänglig?
Se sidan [Azure-tjänster efter region för](https://azure.microsoft.com/regions/#services/) att se en lista över de Azure-regioner där Azure AD Domain Services är tillgänglig.

## <a name="troubleshooting"></a>Felsökning

Se [felsökningsguiden för lösningar](troubleshoot.md) på vanliga problem med att konfigurera eller administrera Azure AD Domain Services.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD Domain Services finns i [Vad är Azure Active Directory Domain Services?](overview.md).

Kom igång genom att gå [till Skapa och konfigurera en Azure Active Directory Domain Services hanterad domän.](tutorial-create-instance.md)
