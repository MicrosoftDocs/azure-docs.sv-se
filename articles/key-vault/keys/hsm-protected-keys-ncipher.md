---
title: Så här genererar och överför du HSM-skyddade nycklar för Azure Key Vault-Azure Key Vault
description: Använd den här artikeln för att hjälpa dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller ta med din egen nyckel.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: de14cf8cc79b4e1387950a2ae048da41738f5db1
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589945"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Importera HSM-skyddade nycklar för Key Vault (hjälp programmet nCipher)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

När du använder Azure Key Vault kan du med extra garantier importera eller generera nycklar i HSM: er (Hardware Security modules) som aldrig lämnar HSM-gränser. Det här scenariot kallas ofta *BYOK* (Bring Your Own Key). Azure Key Vault använder hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien HSM: er (FIPS 140-2 nivå 2 verifieras) för att skydda dina nycklar.

> [!NOTE]
> Den HSM-nyckel import metod som beskrivs i det här dokumentet fungerar bara med hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien HSM: er. Information om hur du importerar HSM-nycklar från andra HSM: er [finns här](hsm-protected-keys-byok.md).

Använd informationen i det här avsnittet när du planerar för, genererar och överför dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. 

Den här funktionen är inte tillgänglig för Azure Kina 21Vianet.

> [!NOTE]
> Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../general/overview.md)  
> En vägledning för att komma igång, vilket innefattar att skapa ett nyckel valv för HSM-skyddade nycklar, finns i [Azure Key Vault?](../general/overview.md).

Mer information om att skapa och överföra en HSM-skyddad nyckel via Internet:

* Du genererar nyckeln från en offline-arbetsstation, vilket minskar angrepps ytan.
* Nyckeln krypteras med en nyckel utbytes nyckel (KEK), som förblir krypterad tills den överförs till Azure Key Vault HSM: er. Endast den krypterade versionen av nyckeln lämnar den ursprungliga arbets stationen.
* Verktygs uppsättningen anger egenskaper för din klient nyckel som binder din nyckel till Azure Key Vault säkerhets världen. Så när Azure Key Vault HSM: er tar emot och dekrypterar nyckeln kan bara dessa HSM: er använda den. Det går inte att exportera nyckeln. Den här bindningen tillämpas av hjälp programmet nCipher-HSM: er.
* Nyckel utbytes nyckeln (KEK) som används för att kryptera nyckeln genereras i Azure Key Vault HSM: er och kan inte exporteras. HSM:erna genomdriver att det inte kan finnas någon klartextversion av KEK utanför HSM:erna. Dessutom innehåller verktygs uppsättningen attestering från hjälp programmet nCipher att KEK inte kan exporteras och genererades i en äkta HSM som tillverkats av hjälp programmet nCipher.
* Verktygs uppsättningen innehåller attestering från hjälp programmet nCipher som den Azure Key Vault säkerhets världen också har skapats på en äkta HSM tillverkad av hjälp programmet nCipher. Den här attesteringen visar att Microsoft använder äkta maskin vara.
* Microsoft använder separata KeyExchange och separata säkerhets världar i varje geografisk region. Den här separationen säkerställer att din nyckel bara kan användas i Data Center i den region där du krypterade den. Till exempel kan en nyckel från en europeisk kund inte användas i Data Center i Nord Amerika eller Asien.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Mer information om hjälp programmet nCipher-HSM: er och Microsoft-tjänster

Hjälp programmet nCipher Security, ett Entrust Datacard företag, är en ledare på den allmänna HSM-marknaden, vilket ger världs ledande organisationer genom att leverera förtroende, integritet och kontroll över sin affärs kritiska information och program. Hjälp programmet nCipher är en säker ny teknik – Cloud, IoT, blockchain, digitala betalningar – och hjälper till att möta nya regelefterlevnad, med samma beprövade teknik som globala organisationer är beroende av idag för att skydda mot känsliga data, nätverkskommunikation och företags infrastruktur. Hjälp programmet nCipher levererar förtroende för affärs kritiska program, vilket säkerställer integriteten hos data och placerar kunderna i fullständig kontroll – idag, i morgon, hela tiden.

Microsoft har samarbetat med hjälp programmet nCipher Security för att förbättra den tekniska situationen för HSM: er. Med dessa förbättringar kan du få de vanliga fördelarna med värdtjänster utan att behöva lämna ifrån dig kontrollen över dina nycklar. Framför allt medför förbättringarna att Microsoft kan hantera HSM:erna så att du inte behöver göra det. Som en moln tjänst kan Azure Key Vault skalas upp på kort sätt för att uppfylla din organisations användnings toppar. På samma tid skyddas din nyckel i Microsofts HSM: er: du behåller kontrollen över nyckel livs cykeln eftersom du genererar nyckeln och överför den till Microsofts HSM: er.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementera BYOK (Bring Your Own Key) för Azure Key Vault

Använd följande information och procedurer om du vill skapa en egen HSM-skyddad nyckel och sedan överföra den till Azure Key Vault – BYOK-scenariot (ta med din egen nyckel).

## <a name="prerequisites-for-byok"></a>Krav för BYOK

I följande tabell finns en lista över förutsättningar för att ta med din egen nyckel (BYOK) för Azure Key Vault.

| Krav | Mer information |
| --- | --- |
| En prenumeration på Azure |Om du vill skapa en Azure Key Vault behöver du en Azure-prenumeration: [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/) |
| Tjänst nivån Azure Key Vault Premium som stöder HSM-skyddade nycklar |Mer information om tjänst nivåer och funktioner för Azure Key Vault finns på webbplatsen för [Azure Key Vault prissättning](https://azure.microsoft.com/pricing/details/key-vault/) . |
| Hjälp programmet nCipher nshield maskinvarusäkerhetsmodul HSM: er, SmartCards och support Software |Du måste ha åtkomst till en hjälp programmet nCipher för maskin vara och grundläggande operativa kunskaper om hjälp programmet nCipher nshield maskinvarusäkerhetsmodul HSM: er. Se [Hjälp programmet nCipher nshield maskinvarusäkerhetsmodul Hardware Security Module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) i listan över kompatibla modeller eller Köp en HSM om du inte har någon. |
| Följande maskin-och program vara:<ol><li>En offline x64-arbets station med minst Windows-operativsystem av Windows 7-och hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-programvara som är minst version 11,50.<br/><br/>Om den här arbets stationen kör Windows 7 måste du [installera Microsoft .NET Framework 4,5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>En arbets station som är ansluten till Internet och som har minst Windows-operativsystemet Windows 7 och [Azure PowerShell](/powershell/azure/?view=azps-1.2.0) **lägsta version 1.1.0** installerad.</li><li>En USB-enhet eller annan bärbar lagrings enhet som har minst 16 MB ledigt utrymme.</li></ol> |Av säkerhetsskäl rekommenderar vi att den första arbetsstationen inte är ansluten till ett nätverk. Den här rekommendationen tillämpas dock inte program mässigt.<br/><br/>I instruktionerna som följer kallas den frånkopplade arbets stationen.</p></blockquote><br/>Om din klient nyckel är för ett produktions nätverk, rekommenderar vi dessutom att du använder en andra, separat arbets station för att ladda ned verktygs uppsättningen och laddar upp klient nyckeln. För testsyften kan du emellertid använda samma arbetsstation som den första.<br/><br/>I anvisningarna som följer kallas den här andra arbets stationen som den Internet-anslutna arbets stationen.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generera och överför din nyckel till Azure Key Vault HSM

Du kommer att använda följande fem steg för att generera och överföra din nyckel till en Azure Key Vault HSM:

* [Steg 1: Förbered din Internet-anslutna arbets Station](#step-1-prepare-your-internet-connected-workstation)
* [Steg 2: Förbered din frånkopplade arbets Station](#step-2-prepare-your-disconnected-workstation)
* [Steg 3: generera din nyckel](#step-3-generate-your-key)
* [Steg 4: Förbered din nyckel för överföring](#step-4-prepare-your-key-for-transfer)
* [Steg 5: Överför nyckeln till Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Steg 1: Förbered din Internet-anslutna arbets Station

För det första steget utför du följande procedurer på din arbets station som är ansluten till Internet.

### <a name="step-11-install-azure-powershell"></a>Steg 1,1: installera Azure PowerShell

Från den Internet-anslutna arbets stationen laddar du ned och installerar Azure PowerShell-modulen som innehåller cmdletarna för att hantera Azure Key Vault. Installations anvisningar finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

### <a name="step-12-get-your-azure-subscription-id"></a>Steg 1,2: Hämta ditt Azure-prenumerations-ID

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med hjälp av följande kommando:

```Powershell
   Connect-AzAccount
```
Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Använd sedan kommandot [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) :

```powershell
   Get-AzSubscription
```
Från utdata letar du reda på ID: t för den prenumeration som du ska använda för Azure Key Vault. Du kommer att behöva detta prenumerations-ID senare.

Stäng inte Azure PowerShell fönstret.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Steg 1,3: Hämta BYOK-verktygen för Azure Key Vault

Gå till Microsoft Download Center och [hämta Azure Key Vault BYOK-verktyg](https://www.microsoft.com/download/details.aspx?id=45345) för din geografiska region eller instans av Azure. Använd följande information för att identifiera det paket namn som ska laddas ned och dess motsvarande SHA-256-paket-hash:

---
**USA:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Östeuropa**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Asien**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latinamerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japan:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Sydafrika:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**Förenade Arabemiraten**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australien:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**AMERIKANSKA myndigheters DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Tyskland:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Tyskland, offentliga:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**Indien:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Frankrike:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Storbritannien:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Schweiz:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Använd cmdleten [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) för att kontrol lera integriteten för de hämtade BYOK-verktygen från Azure PowerShell-sessionen.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Verktygs uppsättningen innehåller:

* Ett nyckel utbytes nyckel paket (KEK) med ett namn som börjar med **BYOK-KEK-pkg-.**
* Ett säkerhets världs paket som har ett namn som börjar med **BYOK-SecurityWorld-pkg-.**
* Ett Python-skript med namnet **verifykeypackage.py.**
* En kommando rads körbar fil med namnet **KeyTransferRemote.exe** och tillhör ande DLL-filer.
* Ett Visual C++ Redistributable-paket med namnet **vcredist_x64.exe.**

Kopiera paketet till en USB-enhet eller annan bärbar lagringsenhet.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Steg 2: Förbered din frånkopplade arbets Station

För det här andra steget utför du följande procedurer på arbets stationen som inte är ansluten till ett nätverk (antingen Internet eller ditt interna nätverk).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Steg 2,1: förbereda den frånkopplade arbets stationen med hjälp programmet nCipher nshield maskinvarusäkerhetsmodul HSM

Installera hjälp programmet nCipher Support Software på en Windows-dator och Anslut sedan en hjälp programmet nCipher nshield maskinvarusäkerhetsmodul HSM till den datorn.

Se till att hjälp programmet nCipher-verktygen finns i sökvägen (**% nfast_home% \ bin**). Ange till exempel följande:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Mer information finns i användar handboken som medföljer nshield maskinvarusäkerhetsmodul HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Steg 2,2: installera BYOK-verktygen på den frånkopplade arbets stationen

Kopiera BYOK-verktygspaketet från USB-enheten eller en annan bärbar lagringsenhet och gör sedan följande:

1. Extrahera filerna från det hämtade paketet till valfri mapp.
2. Kör vcredist_x64.exe från denna mapp.
3. Följ instruktionerna för att installera Visual C++ runtime-komponenterna för Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Steg 3: generera din nyckel

För det här tredje steget utför du följande procedurer på den frånkopplade arbets stationen. För att slutföra det här steget måste HSM vara i initierings läge. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Steg 3,1: ändra HSM-läget till "I"

Om du använder hjälp programmet nCipher nshield maskinvarusäkerhetsmodul Edge för att ändra läget: 1. Använd knappen läge för att markera nödvändigt läge. 2. Om några sekunder trycker du på och håller ned rensnings knappen under några sekunder. Om läget ändras slutar det nya läges INDIKATORn att blinka och förblir tänd. Status LAMPAn kan vara oförutsägbart under några sekunder och blinkar sedan regelbundet när enheten är klar. Annars förblir enheten i det aktuella läget, med rätt läges indikator tänd.

### <a name="step-32-create-a-security-world"></a>Steg 3,2: skapa en säkerhets värld

Starta en kommando tolk och kör hjälp programmet nCipher-programmet New-World.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Det här programmet skapar en **säkerhetsvärldsfil** i %NFAST_KMDATA%\local\world, vilket motsvarar mappen C:\ProgramData\nCipher\Key Management Data\local. Du kan använda olika värden för kvorumet, men i vårt exempel uppmanas du att ange tre tomma kort och PIN-koder för var och en. Sedan ger alla två kort fullständig åtkomst till säkerhets världen. Dessa kort blir **administratörskortsuppsättningen** för den nya säkerhetsvärlden.

> [!NOTE]
> Om din HSM inte stöder den nyare chiffer Suite-DLf3072s256mRijndael kan du ersätta--cipher-Suite = DLf3072s256mRijndael med--cipher-Suite = DLf1024s160mRijndael
> 
> Säkerhets värld som skapats med new-world.exe som levereras med hjälp programmet nCipher-program version 12,50 är inte kompatibel med den här BYOK-proceduren. Det finns två alternativ tillgängliga:
> 1) Nedgradera hjälp programmet nCipher Software-versionen till 12.40.2 för att skapa en ny säkerhets värld.
> 2) Kontakta hjälp programmet nCipher-supporten och be dem att tillhandahålla en snabb korrigering för 12,50-program version, som gör att du kan använda 12.40.2-versionen av new-world.exe som är kompatibel med den här BYOK-proceduren.

Gör något av följande:

* Säkerhetskopiera världfilen. Säkra och skydda världfilen, administratörskorten och tillhörande PIN-koder och se till att ingen enskild person får åtkomst till mer än ett kort.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Steg 3,3: ändra HSM-läget till "O"

Om du använder hjälp programmet nCipher nshield maskinvarusäkerhetsmodul Edge för att ändra läget: 1. Använd knappen läge för att markera nödvändigt läge. 2. Om några sekunder trycker du på och håller ned rensnings knappen under några sekunder. Om läget ändras slutar det nya läges INDIKATORn att blinka och förblir tänd. Status LAMPAn kan vara oförutsägbart under några sekunder och blinkar sedan regelbundet när enheten är klar. Annars förblir enheten i det aktuella läget, med rätt läges indikator tänd.

### <a name="step-34-validate-the-downloaded-package"></a>Steg 3,4: validera det hämtade paketet

Det här steget är valfritt men rekommenderas så att du kan validera följande:

* Nyckel utbytes nyckeln som ingår i verktygs uppsättningen har genererats från en äkta hjälp programmet nCipher-nshield maskinvarusäkerhetsmodul HSM.
* Hashen för säkerhets världen som ingår i verktygs uppsättningen har genererats i en äkta hjälp programmet nCipher-nshield maskinvarusäkerhetsmodul HSM.
* KeyExchange-nyckeln kan inte exporteras.

> [!NOTE]
> För att verifiera det hämtade paketet måste HSM vara ansluten, påslagen och måste ha en säkerhets värld (till exempel den som du nyss skapade).

Verifiera det hämtade paketet:

1. Kör verifykeypackage.py-skriptet genom att skriva något av följande, beroende på din geografiska region eller Azure-instans:

   * För Nordamerika:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
      ```
   * För Europa:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
      ```
   * För Asien:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
        ```
   * För Latinamerika:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * För Japan:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * För Korea:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * För Sydafrika:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * För Förenade Arabemiraten:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * För Australien:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * För [Azure Government](https://azure.microsoft.com/features/gov/)som använder amerikanska myndigheters instansen av Azure:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * För amerikanska myndigheters DOD:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * För Kanada:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * För Tyskland:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * För Tyskland, offentliga:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * För Indien:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * För Frankrike:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * För Storbritannien:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * För Schweiz:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > Hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-programvaran innehåller python på% NFAST_HOME% \ python\bin
     >
     >
2. Bekräfta att du ser följande, vilket indikerar att verifieringen lyckades: **resultat: lyckades**

Det här skriptet validerar den som loggar in till rot nyckeln för nshield maskinvarusäkerhetsmodul. Hashen för den här rotnyckeln är inbäddad i skriptet och dess värde bör vara **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Du kan också bekräfta det här värdet separat genom att besöka [Hjälp programmet nCipher-webbplatsen](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Nu är du redo att skapa en ny nyckel.

### <a name="step-35-create-a-new-key"></a>Steg 3,5: skapa en ny nyckel

Generera en nyckel med hjälp av hjälp programmet nCipher nshield maskinvarusäkerhetsmodul **generatekey** -programmet.

Kör följande kommando för att generera nyckeln:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

Använd följande anvisningar när du kör det här kommandot:

* Parametern *protect* måste anges till värdet **module**, så som det visas. Detta skapar en modulskyddad nyckel. BYOK-verktygen har inte stöd för OCS-skyddade nycklar.
* Ersätt värdet för *contosokey* för **ident** och **plainname** med valfritt strängvärde. För att minimera de administrativa omkostnaderna och minska risken för fel rekommenderar vi att du använder samma värde för båda. Värdet för **identitet** får bara innehålla siffror, bindestreck och gemener.
* Pubexp lämnas tomt (standard) i det här exemplet, men du kan ange specifika värden. Mer information finns i hjälp programmet nCipher- [dokumentationen.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Det här kommandot skapar en nyckel fil med nycklar i mappen% NFAST_KMDATA% \ Local med ett namn som börjar med **key_simple_** följt av den **identitet** som angavs i kommandot. Exempel: **key_simple_contosokey**. Den här filen innehåller en krypterad nyckel.

Säkerhetskopiera filen för tokeniserad nyckel till en säker plats.

> [!IMPORTANT]
> När du senare överför din nyckel till Azure Key Vault kan Microsoft inte exportera den här nyckeln till dig, så det blir mycket viktigt att du säkerhetskopierar din nyckel och säkerhets världen på ett säkert sätt. Kontakta [Hjälp programmet nCipher](https://www.ncipher.com/about-us/contact-us) för vägledning och bästa praxis för att säkerhetskopiera din nyckel.
>


Du är nu redo att överföra din nyckel till Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Steg 4: Förbered din nyckel för överföring

För det här fjärde steget utför du följande procedurer på den frånkopplade arbets stationen.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Steg 4,1: skapa en kopia av din nyckel med minskad behörighet

Öppna en ny kommando tolk och ändra den aktuella katalogen till den plats där du zippade zip-filen för BYOK. Du kan minska behörigheterna för nyckeln genom att köra något av följande i en kommando tolk, beroende på din geografiska region eller Azure-instans:

* För Nordamerika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-
   ```
* För Europa:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
   ```
* För Asien:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
   ```
* För Latinamerika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* För Japan:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* För Korea:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* För Sydafrika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* För Förenade Arabemiraten:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* För Australien:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* För [Azure Government](https://azure.microsoft.com/features/gov/)som använder amerikanska myndigheters instansen av Azure:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* För amerikanska myndigheters DOD:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* För Kanada:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* För Tyskland:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* För Tyskland, offentliga:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* För Indien:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* För Frankrike:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* För Storbritannien:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* För Schweiz:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

När du kör det här kommandot ersätter du *contosokey* med samma värde som du angav i **steg 3,5: skapa en ny nyckel** från steget [generera din nyckel](#step-3-generate-your-key) .

Du uppmanas att ansluta till ditt Security World admin-kort.

När kommandot har slutförts visas **resultat: lyckades** och kopian av din nyckel med minskad behörighet finns i filen med namnet key_xferacId_ \<contosokey> .

Du kan kontrol lera ACL: er med hjälp av följande kommandon med hjälp av hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-verktygen:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  När du kör de här kommandona ersätter du contosokey med samma värde som du angav i **steg 3,5: skapa en ny nyckel** från steget [generera din nyckel](#step-3-generate-your-key) .

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Steg 4,2: kryptera din nyckel med hjälp av Microsofts nyckel utbytes nyckel

Kör något av följande kommandon, beroende på din geografiska region eller Azure-instans:

* För Nordamerika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Europa:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Asien:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Latinamerika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Japan:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Korea:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Sydafrika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Förenade Arabemiraten:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Australien:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För [Azure Government](https://azure.microsoft.com/features/gov/)som använder amerikanska myndigheters instansen av Azure:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För amerikanska myndigheters DOD:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Kanada:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Tyskland:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Tyskland, offentliga:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Indien:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Frankrike:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Storbritannien:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För Schweiz:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


Använd följande anvisningar när du kör det här kommandot:

* Ersätt *contosokey* med den identifierare som du använde för att generera nyckeln i **steg 3,5: skapa en ny nyckel** från steget [generera din nyckel](#step-3-generate-your-key) .
* Ersätt *SubscriptionID* med ID: t för den Azure-prenumeration som innehåller ditt nyckel valv. Du har hämtat det här värdet tidigare i **steg 1,2: Hämta ditt Azure-prenumerations-ID** från steget [förbereda din Internet-anslutna arbets Station](#step-1-prepare-your-internet-connected-workstation) .
* Ersätt *ContosoFirstHSMKey* med en etikett som används för fil namnet för utdatafilen.

När detta har slutförts visas **resultatet: lyckades** och det finns en ny fil i den aktuella mappen med följande namn: KeyTransferPackage-*ContosoFirstHSMkey*. BYOK

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Steg 4,3: kopiera ditt nyckel överförings paket till den Internet-anslutna arbets stationen

Använd en USB-enhet eller annan bärbar lagrings plats för att kopiera utdatafilen från föregående steg (KeyTransferPackage-ContosoFirstHSMkey. BYOK) till din Internet-anslutna arbets Station.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Steg 5: Överför nyckeln till Azure Key Vault

I det här sista steget använder du cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att ladda upp det nyckel överförings paket som du kopierade från den frånkopplade arbets stationen till Azure Key Vault HSM:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Om uppladdningen är klar visas egenskaperna för den nyckel som du nyss lagt till.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddade nyckeln i ditt nyckel valv. Mer information finns i detta pris-och funktions [jämförelse](https://azure.microsoft.com/pricing/details/key-vault/).
