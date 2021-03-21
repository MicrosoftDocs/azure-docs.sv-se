---
title: Transport Layer Security i Azure Backup
description: Lär dig hur du aktiverar Azure Backup att använda krypterings protokollet Transport Layer Security (TLS) för att skydda data när de överförs över ett nätverk.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96327125"
---
# <a name="transport-layer-security-in-azure-backup"></a>Transport Layer Security i Azure Backup

Transport Layer Security (TLS) är ett krypterings protokoll som skyddar data vid överföring över ett nätverk. Azure Backup använder säkerhet på transport nivå för att skydda sekretessen för säkerhetskopierade data som överförs. Den här artikeln beskriver steg för att aktivera TLS 1,2-protokollet, vilket ger bättre säkerhet jämfört med tidigare versioner.

## <a name="earlier-versions-of-windows"></a>Tidigare versioner av Windows

Om datorn kör tidigare versioner av Windows måste motsvarande uppdateringar som anges nedan vara installerade och register ändringarna som beskrivs i KB-artiklarna måste tillämpas.

|Operativsystem  |KB-artikel |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>Uppdateringen kommer att installera de protokoll komponenter som krävs. Efter installationen måste du göra register nyckel ändringarna som nämns i KB-artiklarna ovan för att aktivera de nödvändiga protokollen på rätt sätt.

## <a name="verify-windows-registry"></a>Verifiera Windows-registret

### <a name="configuring-schannel-protocols"></a>Konfigurera SChannel-protokoll

Följande register nycklar kontrollerar att TLS 1,2-protokollet är aktiverat på SChannel-komponent nivån:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>De värden som visas anges som standard i Windows Server 2012 R2 och senare versioner. Om register nycklarna saknas i dessa versioner av Windows behöver de inte skapas.

### <a name="configuring-net-framework"></a>Konfigurera .NET Framework

Följande register nycklar konfigurerar .NET Framework som stöder stark kryptering. Du kan läsa mer om att [konfigurera .NET Framework här](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry).

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-enable-tls-12"></a>Varför ska du Aktivera TLS 1,2?

TLS 1,2 är säkrare än tidigare kryptografiska protokoll som SSL 2,0, SSL 3,0, TLS 1,0 och TLS 1,1. Azure Backup tjänster har redan fullständigt stöd för TLS 1,2.

### <a name="what-determines-the-encryption-protocol-used"></a>Vad bestämmer det krypterings protokoll som används?

Den högsta protokoll versionen som stöds av både klienten och servern förhandlas om att upprätta den krypterade konversationen. Mer information om TLS-handskakning-protokollet finns i [upprätta en säker session med hjälp av TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Vad är effekten av att inte aktivera TLS 1,2?

För ökad säkerhet från angrepp med protokoll nedgradering, börjar Azure Backup att inaktivera TLS-versioner som är äldre än 1,2 i ett stegvist sätt. Detta är en del av en långsiktig Skift över tjänster som inte tillåter äldre protokoll och chiffersviter. Azure Backup tjänster och komponenter har fullständigt stöd för TLS 1,2. Windows-versioner som saknar nödvändiga uppdateringar eller vissa anpassade konfigurationer kan dock fortfarande förhindra att TLS 1,2-protokoll erbjuds. Detta kan orsaka fel inklusive men inte begränsat till ett eller flera av följande:

- Säkerhets kopierings-och återställnings åtgärder kan Miss lyckas.
- Säkerhets kopierings komponenter anslutningar fel med fel 10054 (en befintlig anslutning tvingades stänga av den fjärranslutna värden).
- Tjänster som är relaterade till Azure Backup stoppas eller startas inte som vanligt.

## <a name="additional-resources"></a>Ytterligare resurser

- [Transport Layer Säkerhetsprotokoll](/windows/win32/secauthn/transport-layer-security-protocol)
- [Garantera stöd för TLS 1,2 över distribuerade operativ system](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Metod tips för TLS (Transport Layer Security) med .NET Framework](/dotnet/framework/network-programming/tls)