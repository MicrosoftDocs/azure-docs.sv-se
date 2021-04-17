---
title: Generera och överföra HSM-skyddade nycklar för Azure Key Vault – Azure Key Vault
description: Använd den här artikeln för att planera för, generera och sedan överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: e8230404d71b0fe46e8a7cc536d2b5f3a4c5004f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588402"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Importera HSM-skyddade nycklar för Key Vault (nCipher)

> [!WARNING]
> Importmetoden för HSM-nycklar som  beskrivs i det här dokumentet är inaktuell och kommer inte att stödjas efter den 30 juni 2021. Det fungerar bara med nCipher nShield-familjen HSM:er med inbyggd programvara 12.40.2 eller 12.50 med en snabbkorrigering. Vi [rekommenderar starkt att du använder en ny metod för att importera HSM-nycklar.](hsm-protected-keys-byok.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

För ytterligare säkerhet när du använder Azure Key Vault kan du importera eller generera nycklar i maskinvarusäkerhetsmoduler (HSM:er) som aldrig lämnar HSM-gränsen. Det här scenariot kallas ofta *BYOK* (Bring Your Own Key). Azure Key Vault nCipher nShield-familjen HSM:er (FIPS 140-2 Level 2-verifierad) för att skydda dina nycklar.


Använd informationen i det här avsnittet när du planerar för, genererar och överför dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. 

Den här funktionen är inte tillgänglig för Azure China 21Vianet.

> [!NOTE]
> Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../general/overview.md)  
> En självstudiekurs som hjälper dig att komma igång med att skapa ett nyckelvalv för HSM-skyddade nycklar finns i [Vad är Azure Key Vault?](../general/overview.md).

Mer information om att generera och överföra en HSM-skyddad nyckel via Internet:

* Du genererar nyckeln från en arbetsstation offline, vilket minskar risken för angrepp.
* Nyckeln krypteras med en nyckelutbytesnyckel (KEK), som förblir krypterad tills den överförs till Azure Key Vault HSM:erna. Endast den krypterade versionen av din nyckel lämnar den ursprungliga arbetsstationen.
* Verktygsuppsättningen anger egenskaper för din klientnyckel som binder nyckeln till Azure Key Vault säkerhetsvärlden. Så efter att Azure Key Vault HSM:er tar emot och dekrypterar din nyckel kan endast dessa HSM:er använda den. Din nyckel kan inte exporteras. Den här bindningen tillämpas av nCipher HSM:erna.
* Nyckelutbytesnyckeln (KEK) som används för att kryptera din nyckel genereras i de virtuella Azure Key Vault HSM:erna och kan inte exporteras. HSM:erna genomdriver att det inte kan finnas någon klartextversion av KEK utanför HSM:erna. Dessutom inkluderar verktygsuppsättningen attestation från nCipher att KEK inte kan exporteras och genererades i en äkta HSM som har tillverkats av nCipher.
* Verktygsuppsättningen innehåller attestation från nCipher som Azure Key Vault säkerhetsvärld också genererades på en äkta HSM som tillverkats av nCipher. Denna attestation bevisar för dig att Microsoft använder äkta maskinvara.
* Microsoft använder separata KEK:er och separata säkerhetsvärldar i varje geografisk region. Den här separationen säkerställer att din nyckel endast kan användas i datacenter i den region där du krypterade den. En nyckel från en europeisk kund kan till exempel inte användas i datacenter i Nordamerika eller Asien.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Mer information om nCipher HSM:er och Microsoft-tjänster

nCipher Security, ett Entrust Datacard-företag, är ledande inom HSM-marknaden för generell användning, vilket ger världsledande organisationer genom att leverera förtroende, integritet och kontroll till sin affärskritiska information och sina program. Chiffers kryptografiska lösningar skyddar nya tekniker – moln, IoT, blockkedja, digitala betalningar – och hjälper till att uppfylla nya efterlevnadskrav, med samma beprövade teknik som globala organisationer förlitar sig på i dag för att skydda mot hot mot känsliga data, nätverkskommunikation och företagsinfrastruktur. nCipher ger förtroende för affärskritiska program, vilket säkerställer integriteten för data och ger kunderna fullständig kontroll – idag, i morgon, hela tiden.

Microsoft har samarbetat med nCipher Security för att förbättra den senaste tekniken för HSM:er. Med dessa förbättringar kan du få de vanliga fördelarna med värdtjänster utan att behöva lämna ifrån dig kontrollen över dina nycklar. Framför allt medför förbättringarna att Microsoft kan hantera HSM:erna så att du inte behöver göra det. Som molntjänst kan Azure Key Vault skalas upp med kort varsel för att uppfylla organisationens användningstoppar. Samtidigt skyddas din nyckel i Microsofts HSM:er: Du behåller kontrollen över nyckelns livscykel eftersom du genererar nyckeln och överför den till Microsofts HSM:er.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementera BYOK (Bring Your Own Key) för Azure Key Vault

Använd följande information och procedurer om du ska generera en egen HSM-skyddad nyckel och sedan överföra den till Azure Key Vault – BYOK-scenariot (Bring Your Own Key).

## <a name="prerequisites-for-byok"></a>Krav för BYOK

I följande tabell finns en lista över förhandskrav för BYOK (Bring Your Own Key) för Azure Key Vault.

| Krav | Mer information |
| --- | --- |
| En prenumeration på Azure |Om du vill skapa Azure Key Vault behöver du en Azure-prenumeration: [Registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) |
| Den Azure Key Vault Premium-tjänstnivån för att stödja HSM-skyddade nycklar |Mer information om tjänstnivåer och funktioner för Azure Key Vault finns på Azure Key Vault [pricing.](https://azure.microsoft.com/pricing/details/key-vault/) |
| nCipher nShield HSM:er, smartkort och stödprogram |Du måste ha åtkomst till en nCipher Hardware Security-modul och grundläggande kunskaper om nCipher nShield HSM:er. I [nCipher nShield Hardware Security Module (nCipher nShield Hardware Security Module)](https://go.ncipher.com/rs/104-QOX-775/images/nCipher_nShield_Family_Brochure.pdf?_ga=2.106120835.1607422418.1590478092-577009923.1587131206) finns en lista över kompatibla modeller, eller om du vill köpa en HSM om du inte har någon. |
| Följande maskin- och programvara:<ol><li>En x64-arbetsstation offline med minst Windows-operativsystemet Windows 7 och nCipher nShield-programvara som är minst version 11.50.<br/><br/>Om den här arbetsstationen kör Windows 7 måste [du installera Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>En arbetsstation som är ansluten till Internet och har minst Windows-operativsystem på Windows 7 [och Azure PowerShell](/powershell/azure/) **version 1.1.0** installerat.</li><li>En USB-enhet eller annan bärbar lagringsenhet som har minst 16 MB ledigt utrymme.</li></ol> |Av säkerhetsskäl rekommenderar vi att den första arbetsstationen inte är ansluten till ett nätverk. Den här rekommendationen tillämpas dock inte programmässigt.<br/><br/>I anvisningarna som följer kallas den här arbetsstationen för den frånkopplade arbetsstationen.</p></blockquote><br/>Om din klientnyckel är för ett produktionsnätverk rekommenderar vi dessutom att du använder en andra, separat arbetsstation för att ladda ned verktygsuppsättningen och ladda upp klientnyckeln. För testsyften kan du emellertid använda samma arbetsstation som den första.<br/><br/>I instruktionerna som följer kallas den här andra arbetsstationen den Internetanslutna arbetsstationen.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generera och överföra din nyckel till Azure Key Vault HSM

Du använder följande fem steg för att generera och överföra din nyckel till en Azure Key Vault HSM:

* [Steg 1: Förbered din Internetanslutna arbetsstation](#step-1-prepare-your-internet-connected-workstation)
* [Steg 2: Förbered den frånkopplade arbetsstationen](#step-2-prepare-your-disconnected-workstation)
* [Steg 3: Generera din nyckel](#step-3-generate-your-key)
* [Steg 4: Förbered din nyckel för överföring](#step-4-prepare-your-key-for-transfer)
* [Steg 5: Överför nyckeln till Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Steg 1: Förbered din Internetanslutna arbetsstation

I det här första steget gör du följande på den arbetsstation som är ansluten till Internet.

### <a name="step-11-install-azure-powershell"></a>Steg 1.1: Installera Azure PowerShell

Från den Internetanslutna arbetsstationen laddar du ned och installerar modulen Azure PowerShell som innehåller cmdletarna för att hantera Azure Key Vault. Installationsanvisningar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

### <a name="step-12-get-your-azure-subscription-id"></a>Steg 1.2: Hämta ditt Azure-prenumerations-ID

Starta en Azure PowerShell och logga in på ditt Azure-konto med hjälp av följande kommando:

```Powershell
   Connect-AzAccount
```
Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Använd sedan kommandot [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
Leta upp ID:t för den prenumeration som du ska använda för Azure Key Vault. Du behöver det här prenumerations-ID:t senare.

Stäng inte Azure PowerShell fönstret.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Steg 1.3: Ladda ned BYOK-verktygsuppsättningen för Azure Key Vault

Gå till Microsoft Download Center och ladda [ned Azure Key Vault BYOK-verktygsuppsättningen](https://www.microsoft.com/download/details.aspx?id=45345) för din geografiska region eller instans av Azure. Använd följande information för att identifiera paketnamnet som ska laddas ned och dess motsvarande SHA-256-pakethash:

---
**USA:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Asien:**

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
**Uae:**

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
**DOD för amerikanska myndigheter:**

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


För att verifiera integriteten för din nedladdade BYOK-verktygsuppsättning från din Azure PowerShell-session använder du cmdleten [Get-FileHash.](/powershell/module/microsoft.powershell.utility/get-filehash)

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Verktygsuppsättningen innehåller:

* Ett Key Exchange Key-paket (KEK) som har ett namn som börjar **med BYOK-KEK-pkg-.**
* Ett säkerhetsvärldspaket som har ett namn som börjar med **BYOK-SecurityWorld-pkg-.**
* Ett Python-skript med **namnet verifykeypackage.py.**
* En körbar kommandoradsfil med namnet **KeyTransferRemote.exe** tillhörande DLL:er.
* Ett Visual C++ Redistributable Package med **namnetvcredist_x64.exe.**

Kopiera paketet till en USB-enhet eller annan bärbar lagringsenhet.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Steg 2: Förbered den frånkopplade arbetsstationen

I det här andra steget gör du följande procedurer på arbetsstationen som inte är ansluten till ett nätverk (antingen Internet eller ditt interna nätverk).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Steg 2.1: Förbered den frånkopplade arbetsstationen med nCipher nShield HSM

Installera nCipher-stödprogramvaran på en Windows-dator och anslut sedan en nCipher nShield HSM till datorn.

Kontrollera att nCipher-verktygen finns i sökvägen (**%nfast_home%\bin**). Ange till exempel följande:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Mer information finns i användarhandboken som ingår i nShield HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Steg 2.2: Installera BYOK-verktygsuppsättningen på den frånkopplade arbetsstationen

Kopiera BYOK-verktygspaketet från USB-enheten eller en annan bärbar lagringsenhet och gör sedan följande:

1. Extrahera filerna från det hämtade paketet till valfri mapp.
2. Kör vcredist_x64.exe från denna mapp.
3. Följ instruktionerna för att installera Visual C++-körningskomponenterna för Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Steg 3: Generera din nyckel

I det här tredje steget gör du följande på den frånkopplade arbetsstationen. HSM måste vara i initieringsläge för att slutföra det här steget. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Steg 3.1: Ändra HSM-läget till "I"

Om du använder nCipher nShield Edge för att ändra läget: 1. Använd knappen Läge för att markera det läge som krävs. 2. Inom några sekunder trycker du på och håller ned knappen Rensa i några sekunder. Om läget ändras slutar lysdioden för det nya läget att blinka och förblir tänd. Statusindikatorn kan blinka oregelbundet i några sekunder och sedan regelbundet när enheten är klar. Annars förblir enheten i det aktuella läget, med rätt led-lampa belyst.

### <a name="step-32-create-a-security-world"></a>Steg 3.2: Skapa en säkerhetsvärld

Starta en kommandotolk och kör programmet nCipher new-world.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Det här programmet skapar en **säkerhetsvärldsfil** i %NFAST_KMDATA%\local\world, vilket motsvarar mappen C:\ProgramData\nCipher\Key Management Data\local. Du kan använda olika värden för kvorum, men i vårt exempel uppmanas du att ange tre tomma kort och stift för var och en. Sedan ger två kort fullständig åtkomst till säkerhetsvärlden. Dessa kort blir **administratörskortsuppsättningen** för den nya säkerhetsvärlden.

> [!NOTE]
> Om din HSM inte stöder den nyare cypher-sviten DLf3072s256mRijndael kan du ersätta --cipher-suite= DLf3072s256mRijndael med --cipher-suite=DLf1024s160mRijndael
> 
> Säkerhetsvärld som skapats med new-world.exe som levereras med nCipher-programvaruversion 12.50 är inte kompatibel med den här BYOK-proceduren. Det finns två alternativ tillgängliga:
> 1) Nedgradera nCipher-programvaruversionen till 12.40.2 för att skapa en ny säkerhetsvärld.
> 2) Kontakta nCipher-supporten och be om en snabbkorrigering för programvaruversionen 12.50, som gör att du kan använda 12.40.2-versionen av new-world.exe som är kompatibel med den här BYOK-proceduren.

Gör något av följande:

* Säkerhetskopiera världfilen. Säkra och skydda världfilen, administratörskorten och tillhörande PIN-koder och se till att ingen enskild person får åtkomst till mer än ett kort.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Steg 3.3: Ändra HSM-läget till "O"

Om du använder nCipher nShield Edge för att ändra läget: 1. Använd knappen Läge för att markera det läge som krävs. 2. Inom några sekunder trycker du på och håller ned knappen Rensa i några sekunder. Om läget ändras slutar lysdioden för det nya läget att blinka och förblir tänd. Statusindikatorn kan flasha oregelbundet i några sekunder och sedan regelbundet när enheten är klar. Annars förblir enheten i det aktuella läget, med rätt led-lampa tänd.

### <a name="step-34-validate-the-downloaded-package"></a>Steg 3.4: Verifiera det hämtade paketet

Det här steget är valfritt men rekommenderas så att du kan validera följande:

* Key Exchange-nyckeln som ingår i verktygsuppsättningen har genererats från en äkta nCipher nShield HSM.
* Hashen för säkerhetsvärlden som ingår i verktygsuppsättningen har genererats i en äkta nCipher nShield HSM.
* KeyExchange-nyckeln kan inte exporteras.

> [!NOTE]
> För att validera det hämtade paketet måste HSM vara anslutet, påslaget och ha en säkerhetsvärld (till exempel den som du nyss skapade).

Så här validerar du det hämtade paketet:

1. Kör skriptet verifykeypackage.py genom att skriva något av följande, beroende på din geografiska region eller instans av Azure:

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
   * För [Azure Government](https://azure.microsoft.com/features/gov/), som använder den amerikanska myndighetsinstansen av Azure:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * För US Government DOD:

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
   * För tyskland, offentlig:

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
     > NCipher nShield-programvaran innehåller python på %NFAST_HOME%\python\bin
     >
     >
2. Bekräfta att du ser följande, vilket indikerar lyckad verifiering: **Resultat: LYCKADES**

Det här skriptet verifierar signeringskedjan upp till nShield-rotnyckeln. Hashen för den här rotnyckeln är inbäddad i skriptet och dess värde bör vara **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Du kan också bekräfta det här värdet separat genom att besöka [nCipher-webbplatsen](https://www.ncipher.com).

Nu är du redo att skapa en ny nyckel.

### <a name="step-35-create-a-new-key"></a>Steg 3.5: Skapa en ny nyckel

Generera en nyckel med hjälp av nCipher nShield **generatekey-programmet.**

Kör följande kommando för att generera nyckeln:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

Använd följande anvisningar när du kör det här kommandot:

* Parametern *protect* måste anges till värdet **module**, så som det visas. Detta skapar en modulskyddad nyckel. BYOK-verktygen har inte stöd för OCS-skyddade nycklar.
* Ersätt värdet för *contosokey* för **ident** och **plainname** med valfritt strängvärde. För att minimera de administrativa kostnaderna och minska risken för fel rekommenderar vi att du använder samma värde för båda. **Ident-värdet** får bara innehålla siffror, bindestreck och gemener.
* Pubexp lämnas tomt (standard) i det här exemplet, men du kan ange specifika värden. Mer information finns i [nCipher-dokumentationen.](https://www.entrust.com/-/media/documentation/brochures/entrust-nshield-general-purpose-hsms-br-a4.pdf)

Det här kommandot skapar en fil för tokeniserad nyckel i mappen %NFAST_KMDATA%\local med ett namn som börjar med **key_simple_**, följt av **den ident** som angavs i kommandot. Exempel: **key_simple_contosokey**. Den här filen innehåller en krypterad nyckel.

Säkerhetskopiera filen för tokeniserad nyckel till en säker plats.

> [!IMPORTANT]
> När du senare överför din nyckel till Azure Key Vault, kan Microsoft inte exportera den här nyckeln tillbaka till dig, så det blir mycket viktigt att du säkerhets- och nyckelvärlden säkerhetsvärlden på ett säkert sätt. Kontakta [nCipher](https://www.ncipher.com/about-us/contact-us) om du vill ha vägledning och metodtips för att rekommendationer för att rekommendationer ska gå att rekommendationer för.
>


Nu är du redo att överföra nyckeln till Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Steg 4: Förbered din nyckel för överföring

I det här fjärde steget gör du följande på den frånkopplade arbetsstationen.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Steg 4.1: Skapa en kopia av din nyckel med minskade behörigheter

Öppna en ny kommandotolk och ändra den aktuella katalogen till den plats där du uppackade BYOK-zipfilen. Om du vill minska behörigheterna för din nyckel från en kommandotolk kör du något av följande, beroende på din geografiska region eller instans av Azure:

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
* För [Azure Government](https://azure.microsoft.com/features/gov/), som använder den amerikanska myndighetsinstansen av Azure:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* För DOD för amerikanska myndigheter:

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
* För tyskland, offentlig:

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

När du kör det här kommandot ersätter du *contosokey* med samma värde som du angav i **steg 3.5: Skapa** en ny nyckel från steget [Generera](#step-3-generate-your-key) din nyckel.

Du uppmanas att ansluta dina säkerhetsvärldsadministratörskort.

När kommandot har slutförts visas **Resultat: LYCKADES** och kopian av nyckeln med minskade behörigheter finns i filen med namnet key_xferacId_ \<contosokey> .

Du kan inspektera ACL:erna med hjälp av följande kommandon med hjälp av nCipher nShield-verktygen:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  När du kör dessa kommandon ersätter du contosokey med samma värde som du angav i **steg 3.5: Skapa** en ny nyckel från [steget Generera](#step-3-generate-your-key) din nyckel.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Steg 4.2: Kryptera din nyckel med hjälp av Microsofts Nyckelutbytesnyckel

Kör något av följande kommandon, beroende på din geografiska region eller instans av Azure:

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
* För [Azure Government](https://azure.microsoft.com/features/gov/), som använder den amerikanska myndighetsinstansen av Azure:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* För DOD för amerikanska myndigheter:

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
* För tyskland, offentlig:

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

* Ersätt *contosokey* med den identifierare som du använde för att generera nyckeln i **steg 3.5:** Skapa en ny nyckel från [steget Generera](#step-3-generate-your-key) din nyckel.
* Ersätt *SubscriptionID* med ID:t för den Azure-prenumeration som innehåller ditt nyckelvalv. Du hämtade det här värdet tidigare i **Steg 1.2: Hämta** ditt [Azure-prenumerations-ID](#step-1-prepare-your-internet-connected-workstation) från steget Förbered din Internetanslutna arbetsstation.
* Ersätt *ContosoFirstHSMKey* med en etikett som används för namnet på utdatafilen.

När det här är klart visas **Resultat: LYCKADES** och det finns en ny fil i den aktuella mappen med följande namn: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Steg 4.3: Kopiera nyckelöverföringspaketet till den Internetanslutna arbetsstationen

Använd en USB-enhet eller annan bärbar lagring för att kopiera utdatafilen från föregående steg (KeyTransferPackage-ContosoFirstHSMkey.byok) till din Internetanslutna arbetsstation.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Steg 5: Överför nyckeln till Azure Key Vault

I det här sista steget på den Internetanslutna arbetsstationen använder du cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att ladda upp nyckelöverföringspaketet som du kopierade från den frånkopplade arbetsstationen till Azure Key Vault HSM:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Om uppladdningen lyckas visas egenskaperna för den nyckel som du nyss lade till.

## <a name="next-steps"></a>Nästa steg

Nu kan du använda den här HSM-skyddade nyckeln i ditt nyckelvalv. Mer information finns i pris- och [funktionsjämförelsen](https://azure.microsoft.com/pricing/details/key-vault/).