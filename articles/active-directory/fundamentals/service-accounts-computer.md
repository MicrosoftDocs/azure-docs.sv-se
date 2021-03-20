---
title: Skydda dator konton | Azure Active Directory
description: En guide för att skydda lokala dator konton.
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417983"
---
# <a name="securing-computer-accounts"></a>Skydda dator konton

Dator kontot eller LocalSystem-kontot är ett inbyggt, högt privilegierat konto med åtkomst till praktiskt taget alla resurser på den lokala datorn. Det här kontot är inte kopplat till något inloggat användar konto. Tjänster som körs som LocalSystem-åtkomst till nätverks resurser genom att presentera datorns autentiseringsuppgifter på fjärrservrar. Den visar autentiseringsuppgifter i formatet <domain_name>\<computer_name> $. Ett fördefinierat namn för dator kontot är NT AUTHORITY\SYSTEM. Den kan användas för att starta en tjänst och tillhandahålla säkerhets kontext för den tjänsten.

![[Bild 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Fördelar med att använda dator kontot

Dator kontot ger följande fördelar.

* **Obegränsad lokal åtkomst**: dator kontot ger fullständig åtkomst till datorns lokala resurser.

* **Automatisk lösen ords hantering**: dator kontot tar bort behovet av att manuellt ändra lösen ord. I stället är det här kontot medlem i Active Directory och konto lösen ordet ändras automatiskt. Det eliminerar också behovet av att registrera tjänstens huvud namn för tjänsten.

* **Begränsad åtkomst behörighet utanför datorn**: standard Access Control List (ACL) i Active Directory Domain Services ger minimal åtkomst för dator konton. Om den här tjänsten skulle hackas har den bara begränsad åtkomst till resurser i nätverket.

## <a name="assess-security-posture-of-computer-accounts"></a>Utvärdera säkerhets position för dator konton

Potentiella utmaningar och tillhör ande åtgärder när du använder dator konton. 

| Ärenden| Åtgärder |
| - | - |
| Dator konton är föremål för borttagning och fritid när datorn lämnar och återansluter till domänen.| Verifiera behovet av att lägga till en dator i en AD-grupp och kontrol lera vilket dator konto som har lagts till i en grupp med hjälp av de exempel skript som anges på den här sidan.| 
| Om du lägger till ett dator konto i en grupp får alla tjänster som körs som LocalSystem på den datorn åtkomst rättigheter för gruppen.| Var noga med grupp medlemskapet för ditt dator konto. Undvik att göra dator konton medlemmar i alla domän administratörs grupper eftersom den associerade tjänsten har fullständig åtkomst till Active Directory Domain Services. |
| Felaktiga standardinställningar för nätverk för LocalSystem| Anta inte att dator kontot har standard begränsad åtkomst till nätverks resurser. Kontrol lera i stället grupp medlemskapen för det här kontot noggrant. |
| Okända tjänster som körs som LocalSystem| Se till att alla tjänster som körs under kontot LocalSystem är Microsoft-tjänster eller betrodda tjänster från tredje part. |


## <a name="find-services-running-under-the-computer-account"></a>Hitta tjänster som körs under dator kontot

Använd följande PowerShell-cmdlet för att hitta tjänster som körs under LocalSystem-kontext

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Hitta dator konton som är medlemmar i en speciell grupp**

Använd följande PowerShell-cmdlet för att hitta dator konton som är medlemmar i en speciell grupp.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Hitta datorer konton som är medlemmar i privilegierade grupper**

Använd följande PowerShell-cmdlet för att hitta dator konton som är medlemmar i gruppen identitets administratörer (domän administratörer, företags administratörer, administratörer)

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Flytta från dator konton

> [!IMPORTANT]
> Dator konton är mycket privilegierade konton och bör endast användas när tjänsten behöver obegränsad åtkomst till lokala resurser på datorn och du kan inte använda ett hanterat tjänst konto (MSA).

* Kontrol lera med tjänstens ägare om tjänsten kan köras med en MSA och Använd ett grupphanterat tjänst konto (gMSA) eller ett fristående hanterat tjänst konto (sMSA) om tjänsten stöder det.

* Använd ett domän användar konto med bara de behörigheter som krävs för att köra tjänsten.

## <a name="next-steps"></a>Nästa steg 

Se följande artiklar om hur du skyddar tjänst konton

* [Introduktion till lokala tjänst konton](service-accounts-on-premises.md)

* [Skydda grupphanterade tjänst konton](service-accounts-group-managed.md)

* [Skyddade fristående hanterade tjänst konton](service-accounts-standalone-managed.md)

* [Skydda dator konton](service-accounts-computer.md)

* [Säkra användar konton](service-accounts-user-on-premises.md)

* [Styr lokala tjänst konton](service-accounts-govern-on-premises.md)

 

 
