---
title: Introduktion till Active Directory tjänst konton | Azure Active Directory
description: En introduktion till typerna av tjänst konton i Active Directory och hur du skyddar dem.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645624"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Introduktion till Active Directory tjänst konton

En tjänst har en primär säkerhets identitet som avgör åtkomst rättigheterna för lokala och nätverks resurser. Säkerhets kontexten för en Microsoft Win32-tjänst bestäms av det tjänst konto som används för att starta tjänsten. Ett tjänst konto används för att:
* identifiera och autentisera en tjänst
* en tjänst har startats
* komma åt eller köra kod eller ett program
* Starta en process. 

## <a name="types-of-on-premises-service-accounts"></a>Typer av lokala tjänst konton

Baserat på ditt användnings fall kan du använda ett hanterat tjänst konto (MSA), ett dator konto eller ett användar konto för att köra en tjänst. Tjänsterna måste testas för att bekräfta att de kan använda ett hanterat tjänst konto. Om de kan det kan du använda en.

### <a name="group-msa-accounts"></a>Gruppera MSA-konton

Använd [grupphanterade tjänst konton](service-accounts-group-managed.md) (gMSAs) när det är möjligt för tjänster som körs i din lokala miljö. gMSAs tillhandahåller en enda identitets lösning för en tjänst som körs på en Server grupp eller bakom en utjämning av nätverks belastning. De kan också användas för en tjänst som körs på en enskild server. [gMSAs har särskilda krav som måste uppfyllas](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Fristående MSA-konton

Om du inte kan använda en gMSA använder du ett [fristående hanterade tjänst konton](service-accounts-standalone-managed.md)(SMSA). sMSAs kräver minst Windows Server 2008R2. Till skillnad från gMSAs körs sMSAs bara på en server. De kan användas för flera tjänster på den servern.

### <a name="computer-account"></a>Dator konto

Om du inte kan använda en MSA bör du undersöka användningen av ett [dator konto](service-accounts-computer.md). LocalSystem-kontot är ett fördefinierat lokalt konto som har omfattande behörigheter på den lokala datorn och fungerar som dator identitet i nätverket.   
Tjänster som körs som ett LocalSystem-konto får åtkomst till nätverks resurser genom att använda dator kontots autentiseringsuppgifter i formatet <domain_name>\<computer_name> .

NT instans\system är det fördefinierade namnet för LocalSystem-kontot. Den kan användas för att starta en tjänst och tillhandahålla säkerhets kontexten för den tjänsten.

> [!NOTE]
> När ett dator konto används kan du inte se vilken tjänst på datorn som använder det kontot och kan därför inte granska vilken tjänst som gör ändringar. 

### <a name="user-account"></a>Användar konto

Om du inte kan använda en MSA bör du undersöka användningen av ett [användar konto](service-accounts-user-on-premises.md). Användar konton kan vara ett domän användar konto eller ett lokalt användar konto.

Med ett domän användar konto kan tjänsten dra full nytta av säkerhetsfunktionerna i Windows och Microsoft Active Directory Domain Services. Tjänsten kommer att ha den lokala åtkomsten och nätverks åtkomsten beviljad till kontot. Den kommer också att ha behörighet för alla grupper som kontot är medlem i. Domän tjänst konton stöder ömsesidig Kerberos-autentisering.

Ett lokalt användar konto (namn format: ".\UserName") finns bara i SAM-databasen på värddatorn. Det finns inget användar objekt i Active Directory Domain Services. Ett lokalt konto kan inte autentiseras av domänen. Därför har en tjänst som körs i säkerhets kontexten för ett lokalt användar konto inte åtkomst till nätverks resurser (förutom som en anonym användare). Tjänster som körs i den lokala användar kontexten har inte stöd för ömsesidig Kerberos-autentisering i vilken tjänsten autentiseras av dess klienter. Av dessa skäl är lokala användar konton vanligt vis olämpliga för katalog tjänster.

> [!IMPORTANT]
> Tjänst konton bör inte vara medlemmar i några privilegierade grupper, eftersom privilegierade grupp medlemskap ger behörighet som kan vara en säkerhets risk. Varje tjänst bör ha sitt eget tjänst konto för gransknings-och säkerhets syfte.

## <a name="choose-the-right-type-of-service-account"></a>Välj rätt typ av tjänst konto


| Kriterie| gMSA| sMSA| Dator konto| Användar konto |
| - | - | - | - | - |
| Appen körs på en enskild server| Ja| Ja. Använd en gMSA om möjligt| Ja. Använd en MSA om möjligt| Ja. Använd MSA om möjligt. |
| Appen körs på flera servrar| Ja| Inga| Nej. Kontot är knutet till servern| Ja. Använd MSA om möjligt. |
| Appen körs bakom belastningsutjämnare| Ja| Inga| Nej| Ja. Använd endast om du inte kan använda en gMSA |
| Appen körs på Windows Server 2008 R2| Inga| Ja| Ja. Använd MSA om möjligt.| Ja. Använd MSA om möjligt. |
| Körs på Windows Server 2012| Ja| Ja. Använd gMSA om möjligt| Ja. Använd MSA om möjligt| Ja. Använd MSA om möjligt. |
| Krav för att begränsa tjänst kontot till en enskild server| Inga| Ja| Ja. Använd sMSA om möjligt| Nej. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Använd Server loggar och PowerShell för att undersöka

Du kan använda Server loggar för att avgöra vilka servrar, och hur många servrar, ett program körs på.

Du kan köra följande PowerShell-kommando för att få en lista över Windows Server-versionen för alla servrar i nätverket. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Hitta lokala tjänst konton

Vi rekommenderar att du lägger till ett prefix, till exempel "SVC". För alla konton som används som tjänst konton. Med den här namngivnings konventionen blir det enklare att hitta och hantera. Överväg också att använda ett beskrivnings-attribut för tjänst kontot och ägare av tjänst kontot, det kan vara ett team-alias eller en säkerhets grupp ägare.

Att hitta lokala tjänst konton är nyckeln för att säkerställa säkerheten. Och det kan vara svårt för icke-MSA konton. Vi rekommenderar att du kontrollerar alla konton som har åtkomst till dina viktiga lokala resurser och avgör vilka datorer eller användar konton som kan fungera som tjänst konton. Du kan också använda följande metoder för att hitta konton.

* I artiklarna för varje typ av konto finns detaljerade anvisningar för att hitta den konto typen. Länkar till de här artiklarna finns i avsnittet Nästa steg i den här artikeln.

## <a name="document-service-accounts"></a>Dokument tjänst konton

När du har hittat tjänst kontona i din lokala miljö ska du dokumentera följande information om varje konto. 

* Ägaren. Personens konto för att underhålla kontot.

* Syftet. Det program som kontot representerar eller något annat syfte. 

* Behörighets omfång. Vilka behörigheter har den och bör ha? Vad händer om några grupper är medlem i?

* Risk profil. Vad är risken för ditt företag om det här kontot komprometteras? Använd en MSA om hög risk.

* Förväntad livs längd och periodisk attestering. Hur länge förväntar du dig att det här kontot ska vara aktivt? Hur ofta måste ägaren granska och bekräfta för pågående behov?

* Lösen ords säkerhet. För användar konton och lokala dator konton där lösen ordet lagras. Se till att lösen ord hålls skyddade och dokument som har åtkomst. Överväg att använda [Privileged Identity Management](../privileged-identity-management/pim-configure.md) för säker lagring av lösen ord. 

  

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar tjänst konton

* [Introduktion till lokala tjänst konton](service-accounts-on-premises.md)

* [Skydda grupphanterade tjänst konton](service-accounts-group-managed.md)

* [Skyddade fristående hanterade tjänst konton](service-accounts-standalone-managed.md)

* [Skydda dator konton](service-accounts-computer.md)

* [Säkra användar konton](service-accounts-user-on-premises.md)

* [Styr lokala tjänst konton](service-accounts-govern-on-premises.md)

