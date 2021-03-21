---
title: Azure AD Domain Services för leverantörer av moln lösningar | Microsoft Docs
description: Lär dig hur du aktiverar och hanterar Azure Active Directory Domain Services hanterade domäner för Azure Cloud solution providers
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619907"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure Active Directory Domain Services distribution och hantering av Azure Cloud solution providers

Azure Cloud solution providers (CSP) är ett program för Microsoft-partner och tillhandahåller en licens kanal för olika Microsoft-molntjänster. Azure CSP gör det möjligt för partner att hantera försäljning, äga fakturerings relationen, ge teknisk support och fakturerings support och vara kundens enda kontakt punkt. Dessutom tillhandahåller Azure CSP en fullständig uppsättning verktyg, inklusive en självbetjänings Portal och medföljande API: er. Med dessa verktyg kan CSP-partner enkelt etablera och hantera Azure-resurser och tillhandahålla fakturering för kunder och deras prenumerationer.

[Partner Center-portalen](/partner-center/azure-plan-lp) är start punkten för alla Azure CSP-partner och ger omfattande funktioner för kund hantering, automatisk bearbetning med mera. Azure CSP-partner kan använda Partner Center-funktioner med hjälp av ett webbaserat användar gränssnitt eller med hjälp av PowerShell och olika API-anrop.

Följande diagram illustrerar hur CSP-modellen fungerar på en hög nivå. Här har Contoso en Azure Active Directory (Azure AD)-klient. De har ett partnerskap med en CSP, som distribuerar och hanterar resurser i sin Azure CSP-prenumeration. Contoso kan också ha vanliga (direkta) Azure-prenumerationer som faktureras direkt till contoso.

![Översikt över CSP-modellen](./media/csp/csp_model_overview.png)

CSP-partnerns klient organisation har tre särskilda agent grupper – *Administratörs* agenter, *support* agenter och *försäljnings* agenter.

Gruppen *Administratörs* agenter tilldelas rollen klient organisations administratör i Contosos Azure AD-klient. Därför har en användare som tillhör CSP-partnerns administratörs agenter gruppen klient organisations administratörs behörighet i Contosos Azure AD-klient.

När CSP-partnern etablerar en Azure CSP-prenumeration för Contoso tilldelas gruppen administratörs agenter rollen som ägare för den prenumerationen. Därför har CSP-partnerns administratörs agenter de behörigheter som krävs för att etablera Azure-resurser, till exempel virtuella datorer, virtuella nätverk och Azure AD Domain Services å uppdrag av contoso.

Mer information finns i [Översikt över Azure CSP](/partner-center/azure-plan-lp)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Fördelar med att använda Azure AD DS i en Azure CSP-prenumeration

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibelt med Windows Server Active Directory Domain Services. Under årtionden har många program skapats för att fungera mot AD med hjälp av dessa funktioner. Många oberoende program varu leverantörer (ISV: er) har skapat och distribuerat program till sina kunders lokaler. Dessa program är svåra att stödja eftersom du ofta behöver åtkomst till de olika miljöer där programmen distribueras. Med Azure CSP-prenumerationer har du ett enklare alternativ med skalbarhet och flexibilitet i Azure.

Azure AD DS har stöd för Azure CSP-prenumerationer. Du kan distribuera ditt program i en Azure CSP-prenumeration som är kopplad till kundens Azure AD-klient. Det innebär att dina anställda (support personal) kan hantera, administrera och underhålla de virtuella datorer som programmet distribueras på med företagets företags uppgifter.

Du kan även distribuera en Azure AD DS-hanterad domän i kundens Azure AD-klient. Ditt program är sedan anslutet till kundens hanterade domän. Funktioner i ditt program som är beroende av Kerberos/NTLM, LDAP eller [system. DirectoryServices-API: et](/dotnet/api/system.directoryservices) fungerar sömlöst mot kundens domän. Slutanvändare har nytta av att använda programmet som en tjänst, utan att behöva oroa sig för att underhålla infrastrukturen som programmet distribueras på.

All fakturering för Azure-resurser som du förbrukar i den prenumerationen, inklusive Azure AD DS, debiteras till dig. Du behåller full kontroll över relationen med kunden när den kommer till försäljning, fakturering, teknisk support osv. Med flexibiliteten i Azure CSP-plattformen kan en liten grupp support agenter betjäna många sådana kunder som har instanser av ditt program distribuerade.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>CSP-distributions modeller för Azure AD DS

Det finns två sätt att använda Azure AD DS med en Azure CSP-prenumeration. Välj rätt baserat på säkerhets-och förenklings överväganden som dina kunder har.

### <a name="direct-deployment-model"></a>Direkt distributions modell

I den här distributions modellen är Azure AD DS aktiverat i ett virtuellt nätverk som tillhör Azure CSP-prenumerationen. CSP-partnerns administratörs agenter har följande behörigheter:

* *Global administratörs* behörighet i kundens Azure AD-klient.
* *Prenumerations ägarens* behörigheter för Azure CSP-prenumerationen.

![Direkt distributions modell](./media/csp/csp_direct_deployment_model.png)

I den här distributions modellen kan CSP-providerns administratörs agenter administrera identiteter för kunden. Dessa administratörs agenter kan utföra uppgifter som att etablera nya användare eller grupper, eller lägga till program i kundens Azure AD-klient.

Den här distributions modellen kan vara lämplig för mindre organisationer som inte har en särskild identitets administratör eller som föredras för CSP-partnern för att administrera identiteter för deras räkning.

### <a name="peered-deployment-model"></a>Modell för peer-distribution

I den här distributions modellen är Azure AD DS aktiverat i ett virtuellt nätverk som tillhör kunden – en direkt Azure-prenumeration som betalas av kunden. CSP-partnern kan distribuera program i ett virtuellt nätverk som tillhör kundens CSP-prenumeration. De virtuella nätverken kan sedan anslutas med hjälp av Azure Virtual Network-peering.

Med den här distributionen kan arbets belastningar eller program som distribueras av CSP-partnern i Azure CSP-prenumerationen ansluta till kundens hanterade domän etablerade i kundens direkta Azure-prenumeration.

![Modell för peer-distribution](./media/csp/csp_peered_deployment_model.png)

Den här distributions modellen ger en separation av privilegier och gör det möjligt för CSP-partnerns helpdesk-agenter att administrera Azure-prenumerationen och distribuera och hantera resurser i den. CSP-partnerns helpdesk-agenter behöver dock inte ha global administratörs behörighet för kundens Azure AD-katalog. Kundens identitets administratörer kan fortsätta att hantera identiteter för organisationen.

Den här distributions modellen kan vara lämplig för scenarier där en ISV tillhandahåller en värdbaserad version av sitt lokala program, som också måste ansluta till kundens Azure AD.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Administrera Azure AD DS i CSP-prenumerationer

Följande viktiga överväganden gäller när du administrerar en hanterad domän i en Azure CSP-prenumeration:

* **CSP admin-agenter kan etablera en hanterad domän med sina autentiseringsuppgifter:** Azure AD DS har stöd för Azure CSP-prenumerationer. Användare som tillhör en CSP-partners grupp med administratörs agenter kan etablera en ny hanterad domän.

* **Kryptografiproviders kan skapa skript för nya hanterade domäner för sina kunder med hjälp av PowerShell:** Mer information finns i [Aktivera Azure AD DS med hjälp av PowerShell](powershell-create-instance.md) .

* **CSP admin-agenter kan inte utföra pågående hanterings uppgifter på den hanterade domänen med sina autentiseringsuppgifter:** Användare av CSP-administratörer kan inte utföra rutin hanterings uppgifter i den hanterade domänen med sina autentiseringsuppgifter. Dessa användare är externa för kundens Azure AD-klient och deras autentiseringsuppgifter är inte tillgängliga i kundens Azure AD-klient. Azure AD DS har inte åtkomst till Kerberos-och NTLM-hashvärden för dessa användare, vilket innebär att användarna inte kan autentiseras på hanterade domäner.

  > [!WARNING]
  > Du måste skapa ett användar konto i kundens katalog för att utföra pågående administrations uppgifter på den hanterade domänen.
  >
  > Du kan inte logga in på den hanterade domänen med hjälp av en CSP admin-användares autentiseringsuppgifter. Använd autentiseringsuppgifterna för ett användar konto som tillhör kundens Azure AD-klient för att göra detta. Du behöver dessa autentiseringsuppgifter för uppgifter som att ansluta virtuella datorer till den hanterade domänen, administrera DNS eller administrera grupprincip.

* **Användar kontot som skapas för pågående administration måste läggas till i *Administratörs* gruppen för AAD** -domänkontrollanter: gruppen *AAD DC-administratörer* har behörighet att utföra vissa delegerade administrations uppgifter på den hanterade domänen. I dessa uppgifter ingår konfigurering av DNS, skapande av organisationsenheter och administration av grup principer.
    
    För att en CSP-partner ska kunna utföra dessa uppgifter på en hanterad domän måste du skapa ett användar konto i kundens Azure AD-klient. Autentiseringsuppgifterna för det här kontot måste delas med CSP-partnerns administratörs agenter. Dessutom måste det här användar kontot läggas till i *Administratörs gruppen för AAD-domänkontrollanten* så att konfigurations aktiviteter på den hanterade domänen kan utföras med det här användar kontot.

## <a name="next-steps"></a>Nästa steg

Kom igång genom att [Registrera dig i Azure CSP-programmet](/partner-center/enrolling-in-the-csp-program). Du kan sedan aktivera Azure AD Domain Services med hjälp [av Azure Portal](tutorial-create-instance.md) eller [Azure PowerShell](powershell-create-instance.md).