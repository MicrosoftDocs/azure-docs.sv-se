---
title: Styr vad en användare kan göra på filnivå – Azure-filresurser
description: Lär dig hur du konfigurerar Windows ACL-behörigheter för lokal AD DS-autentisering till Azure-filresurser. Gör att du kan dra nytta av detaljerad åtkomst kontroll.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 698b4ebedfc9b41e8c5732a0a81226a971d65585
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470762"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Del tre: Konfigurera behörigheter för kataloger och filnivå över SMB 

Innan du börjar den här artikeln ser du till att du har slutfört föregående artikel, [tilldelar behörigheter på resurs nivå till en identitet](storage-files-identity-ad-ds-assign-permissions.md) för att se till att dina behörigheter på resurs nivå är på plats.

När du har tilldelat behörigheter på resurs nivå med Azure RBAC måste du konfigurera lämpliga Windows ACL: er på rot-, katalog-eller filnivå, så att du kan dra nytta av detaljerad åtkomst kontroll. Tänk på Azures RBAC-behörighet på resurs nivå som den övergripande gatekeepern som avgör om en användare har åtkomst till resursen. Även om Windows ACL: er använder en mer detaljerad nivå för att avgöra vilka åtgärder användaren kan göra på katalog-eller filnivå. Behörigheter för både delnings-och fil-/katalog nivå tillämpas när en användare försöker få åtkomst till en fil/katalog, så om det finns en skillnad mellan någon av dem kommer bara den mest restriktiva att tillämpas. Om en användare till exempel har Läs-/Skriv behörighet på filnivå, men bara läser på resurs nivå, kan de bara läsa filen. Samma sak gäller om den har återförts och en användare hade Läs-/Skriv behörighet på delnings nivå, men bara läst på filnivå, de kan fortfarande bara läsa filen.

## <a name="azure-rbac-permissions"></a>Azure RBAC-behörigheter

Följande tabell innehåller de Azure RBAC-behörigheter som är relaterade till den här konfigurationen:


| Inbyggd roll  | NTFS-behörighet  | Resulterande åtkomst  |
|---------|---------|---------|
|Storage-fildata för SMB-resursläsare | Fullständig behörighet, ändra, läsa, skriva, köra | Läs och kör  |
|     |   Läsa |     Läsa  |
|Storage-fildata för SMB-resursdeltagare  |  Fullständig behörighet    |  Ändra, läsa, skriva, köra |
|     |  Ändra         |  Ändra    |
|     |  Läs och kör |  Läs och kör |
|     |  Läsa           |  Läsa    |
|     |  Skriva          |  Skriva   |
|Storage-fildata för upphöjd SMB-resursdeltagare | Fullständig behörighet  |  Ändra, läsa, skriva, redigera, köra |
|     |  Ändra          |  Ändra |
|     |  Läs och kör  |  Läs och kör |
|     |  Läsa            |  Läsa   |
|     |  Skriva           |  Skriva  |



## <a name="supported-permissions"></a>Behörigheter som stöds

Azure Files stöder en fullständig uppsättning grundläggande och avancerade Windows-ACL: er. Du kan visa och konfigurera Windows ACL: er på kataloger och filer i en Azure-filresurs genom att montera resursen och sedan använda Windows Utforskaren, köra kommandot Windows [icacls](/windows-server/administration/windows-commands/icacls) eller kommandot [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Om du vill konfigurera ACL: er med behörighet för superanvändare måste du montera resursen med hjälp av lagrings konto nyckeln från din domänanslutna VM. Följ anvisningarna i nästa avsnitt för att montera en Azure-filresurs från kommando tolken och konfigurera Windows ACL: er.

Följande behörigheter ingår i rot katalogen för en fil resurs:

- BUILTIN\Administrators: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (IO) (GR, GE)
- NT Instans\autentiserade-användare: (OI) (CI) (M)
- NT INSTANS\SYSTEM: (OI) (CI) (F)
- NT INSTANS\SYSTEM: (F)
- SKAPARE ÄGARE: (OI) (CI) (IO) (F)

|Användare|Definition|
|---|---|
|BUILTIN\Administrators|Alla användare som är domän administratörer av lokal AD DS-miljön.
|BUILTIN\Users|Inbyggd säkerhets grupp i AD. Den innehåller NT Instans\autentiserade-användare som standard. För en traditionell fil Server kan du konfigurera medlemskaps definitionen per server. För Azure Files finns det ingen värd Server, och därför innehåller BUILTIN\Users samma uppsättning användare som NT Instans\autentiserade-användare.|
|NT INSTANS\SYSTEM|Tjänst kontot för fil serverns operativ system. Detta tjänst konto gäller inte i Azure Files-kontexten. Den ingår i rot katalogen så att den överensstämmer med Windows-filer Server upplevelse för Hybrid scenarier.|
|NT Instans\autentiserade-användare|Alla användare i AD som kan få en giltig Kerberos-token.|
|SKAPARE ÄGARE|Varje objekts katalog eller fil har en ägare för objektet. Om det finns ACL: er tilldelade till "skapare ägare" på objektet, har användaren som äger det här objektet behörighet till objektet som definieras av ACL: en.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Montera en fil resurs från kommando tolken

Använd Windows- `net use` kommandot för att montera Azure-filresursen. Kom ihåg att ersätta plats hållarnas värden i följande exempel med dina egna värden. Mer information om hur du monterar fil resurser finns i [använda en Azure-filresurs med Windows](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Om du får problem med att ansluta till Azure Files kan du läsa [fel söknings verktyget som vi publicerade för Azure Files monterings fel i Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Vi ger också [vägledning](./storage-files-faq.md#on-premises-access) för att lösa scenarier när port 445 är blockerad. 

## <a name="configure-windows-acls"></a>Konfigurera Windows ACL: er

När fil resursen har monterats med lagrings konto nyckeln måste du konfigurera Windows ACL: er (kallas även NTFS-behörigheter). Du kan konfigurera Windows-ACL: er med hjälp av Windows Utforskaren eller icacls.

Om du har kataloger eller filer på lokala fil servrar med Windows-DACL: er som kon figurer ATS mot AD DS-identiteter, kan du kopiera den till Azure Files att bevara ACL: er med traditionella fil kopierings verktyg som Robocopy eller [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases). Om dina kataloger och filer är i nivå av Azure Files via Azure File Sync, förs dina ACL: er över och sparas i sitt ursprungliga format.

### <a name="configure-windows-acls-with-icacls"></a>Konfigurera Windows ACL: er med icacls

Använd följande Windows-kommando för att ge fullständig behörighet till alla kataloger och filer under fil resursen, inklusive rot katalogen. Kom ihåg att ersätta plats hållarnas värden i exemplet med dina egna värden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Mer information om hur du använder icacls för att ange Windows ACL: er och de olika typerna av behörigheter som stöds finns i [kommando rads referens för icacls](/windows-server/administration/windows-commands/icacls).

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Konfigurera Windows ACL: er med Windows Utforskaren

Använd Utforskaren i Windows för att ge fullständig behörighet till alla kataloger och filer under fil resursen, inklusive rot katalogen. Om du inte kan läsa in informationen om AD-domänen på rätt sätt i Utforskaren i Windows kan detta bero på en förtroende konfiguration i din lokal AD-miljö. Klient datorn kunde inte ansluta till AD-domänkontrollanten som registrerats för Azure Files autentisering. I det här fallet använder du icacls för configurating Windows ACL: er.

1. Öppna Utforskaren i Windows och högerklicka på filen/katalogen och välj **Egenskaper**.
1. Välj fliken **säkerhet** .
1. Välj **Redigera...** ändra behörigheter.
1. Du kan ändra behörigheter för befintliga användare eller välja **Lägg till...** om du vill ge nya användare behörigheter.
1. I fönstret prompt för att lägga till nya användare anger du det användar namn som du vill bevilja behörigheter i rutan **Ange de objekt namn som ska väljas** och väljer **kontrol lera namn** för att hitta det fullständiga UPN-namnet för mål användaren.
1.    Välj **OK**.
1.    På fliken **säkerhet** väljer du alla behörigheter som du vill ge den nya användaren.
1.    Välj **Använd**.


## <a name="next-steps"></a>Nästa steg

Nu när funktionen är aktive rad och konfigurerad fortsätter du till nästa artikel, där du monterar Azure-filresursen från en domänansluten virtuell dator.

[Del fyra: montera en fil resurs från en domänansluten virtuell dator](storage-files-identity-ad-ds-mount-file-share.md)
