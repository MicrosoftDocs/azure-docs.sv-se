---
title: Användare som har flaggats för risk i Azure Active Directory Portal | Microsoft Docs
description: Lär dig mer om användare som flaggats i säkerhetsrapporten i Azure Active Directory-portalen
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23bd7a26bc6215f50a2860ab4aac84f1b46301f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92308948"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Åtgärda användare som flaggats för risk i Azure Active Directory-portalen

Med säkerhets rapporterna i Azure Active Directory (Azure AD) kan du mäta sannolikheten för att användar konton i din miljö har komprometterats. En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Det är viktigt för Microsoft att hålla dina miljöer säkra. Som en del av detta åtagande övervakar Microsoft kontinuerligt för att hitta aktiviteter som är ovanliga eller som följer kända angreppsmönster. 

Om ovanliga aktiviteter som kan indikera obehörig åtkomst till vissa av användarnas konton identifieras, får du aviseringar som gör att du kan vidta åtgärder. Detta innebär inte att Microsofts egna system har komprometterats.

## <a name="access-the-users-flagged-for-risk-report"></a>Åtkomst till användare som har flaggats för risk

Du kan granska användare som har flaggats för risk genom [rapporten användare i fara](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) i Azure Portal. Om du inte har Azure AD kan du registrera dig kostnads fritt på [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) . 

Från användare som har flaggats för risk rapport kan du utföra följande åtgärder för varje användare:

- skapa ett tillfälligt lösenord
- kräva att användarna måste återställa sina lösenord på ett säkert sätt nästa gång de loggar in
- minimera användarrisken utan att behöva vidta någon åtgärd.

Mer information finns i [användare som har flaggats för risk säkerhets rapporten](../identity-protection/overview-identity-protection.md).

### <a name="azure-ad-subscription-for-microsoft-365-customers"></a>Azure AD-prenumeration för Microsoft 365 kunder

Du kan också använda dina Microsoft 365 autentiseringsuppgifter för att få åtkomst till **Azure administrations centret**. När du har aktiverat din åtkomst till Azure AD omdirigeras du till Azure AD-portalen. Lägg märke till att mängden detaljer som tillhandahålls i rapporterna är begränsade på Basic-nivån. Ytterligare data och analyser är tillgängliga för Azure Premium-prenumeranter.

För att få åtkomst till de användare som har **flaggats för risk** rapporter i Microsoft 365 administrations centret:

1.  I navigerings menyn på vänster sida väljer du **administrations Center**. 
2.  Välj **Azure AD**.
3.  Logga in på **Azure Active Directory-administratörcentret**.
4.  Om en banderoll visas överst på sidan som visar en **titt på den nya portalen** väljer du länken.
4.  I navigerings menyn på vänster sida väljer du **Azure Active Directory**. 
5.  I navigerings fönstret väljer **du användare som har flaggats för risk** i avsnittet **säkerhet** .

## <a name="remediation-actions"></a>Åtgärder

Gör följande för att åtgärda de berörda kontona och göra miljön säker:

1.  [Verifiera korrekt information](https://aka.ms/MFAValid) för Multi-Factor Authentication och återställning av lösen ord för självbetjäning. 
2.  [Aktivera Multi-Factor Authentication](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication) för alla användare. 
3.  Använd det här [reparations skriptet](https://aka.ms/remediate) för varje påverkat konto för att automatiskt utföra följande steg: 

    a. Återställ lösen ordet för att skydda kontot och avsluta aktiva sessioner.

    b. Ta bort postlådedelegater.

    c. Inaktivera vidarebefordran av e-post för externa domäner.

    d. Ta bort egenskapen för global vidarebefordran av e-post i brevlådan.

    e. Aktivera MFA för användarens konto.

    f. Ange att lösenordskomplexiteten för kontot ska vara högt.

    ex. Aktivera granskning av postlådan.

    h. Skapa en Gransknings logg som administratören kan granska.

4. Undersök din Microsoft 365 klient och annan IT-infrastruktur, inklusive en granskning av alla klient inställningar, användar konton och konfigurations inställningarna per användare för möjlig ändring. Leta efter indikatorer på beständighetsmetoder och på att en inkräktare kan ha skapat ett första fäste för att få tag i VPN-autentiseringsuppgifter eller få åtkomst till andra resurser i organisationen. 

5.  Som en del av din undersökning bör du överväga om du bör meddela myndighets myndigheter, inklusive rätts tvång.

Dessutom bör du:

- Läs och implementera den här [vägledningen om hur du löser ovanliga aktiviteter](/archive/blogs/office365security/how-to-fix-a-compromised-hacked-microsoft-office-365-account). 
- [Aktivera gransknings pipelinen](/archive/blogs/office365security/using-office-365-activity-data-to-improve-your-cybersecurity-stance-and-capability) för att hjälpa dig att analysera aktiviteten i din klient organisation. När du är klar börjar gransknings lagret fylla med aktivitets loggar. I det här läget kan du också använda [Sök-och gransknings resurserna i Security och Compliance Center](https://aka.ms/sccsearch). 
- Granska reglerna för delegeringsbehörigheter och vidarebefordran av e-post för alla brevlådor. Du kan använda det här [PowerShell-skriptet](https://aka.ms/delegateforwardrules) för at utföra den här uppgiften. 

## <a name="next-steps"></a>Nästa steg

* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)
* [Användare som har flaggats för risk](../identity-protection/overview-identity-protection.md)