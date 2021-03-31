---
title: Lägg till anpassning till din organisations inloggnings sida – Azure AD
description: Anvisningar om hur du lägger till din organisations anpassning till Azure Active Directory inloggnings sida.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b69daf9e9fbbb3f6d6597a8e8349dc8bec61fbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008782"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Lägg till anpassning till din organisations Azure Active Directory inloggnings sida
Använd organisationens logo typ och egna färg scheman för att ge ett enhetligt utseende på dina Azure Active Directory-inloggnings sidor (Azure AD). Dina inloggnings sidor visas när användarna loggar in i din organisations webbaserade appar, till exempel Microsoft 365, som använder Azure AD som identitets leverantör.

>[!NOTE]
>Om du lägger till anpassad anpassning måste du ha Azure Active Directory Premium 1-eller Premium 2-licenser. Mer information om licenser och versioner finns i [Registrera dig för Azure AD Premium](active-directory-get-started-premium.md).<br><br>Azure AD Premium-versioner är tillgängliga för kunder i Kina som använder den globala instansen av Azure Active Directory. Azure AD Premium-versioner stöds för närvarande inte i Azure-tjänsten som drivs av 21Vianet i Kina. Om du vill ha mer information kontaktar du oss via [Azure Active Directory-forumet](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customize-your-azure-ad-sign-in-page"></a>Anpassa din inloggnings sida för Azure AD
Du kan anpassa dina inloggnings sidor för Azure AD, som visas när användarna loggar in till din organisations klientbaserade appar, till exempel `https://outlook.com/contoso.com` eller när de skickar en domän variabel, till exempel `https://passwordreset.microsoftonline.com/?whr=contoso.com` .

Din anpassade anpassning visas inte direkt när användarna går till webbplatser som www- \. Office.com. Användaren måste i stället logga in innan din anpassade anpassning visas. När användaren har loggat in kan anpassningen ta 15 minuter eller längre visas. 

> [!NOTE]
> Alla anpassnings element är valfria. Om du t. ex. anger en banderoll med ingen bakgrunds bild visas logo typen på inloggnings sidan med en standard bakgrunds bild från mål webbplatsen (till exempel Microsoft 365).<br><br>Dessutom överförs inte inloggnings sidan till personliga Microsoft-konton. Om dina användare eller affärs gäster loggar in med en personlig Microsoft-konto, visar inte inloggnings sidan anpassningen av din organisation.

### <a name="to-customize-your-branding"></a>Anpassa din anpassning
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory** och välj sedan **företags anpassning** och välj sedan **Konfigurera**.

    ![Contoso-sidan för företags anpassning, konfigurera alternativ markerat](media/customize-branding/company-branding-configure-button.png)

3. På sidan **Konfigurera företags anpassning** anger du någon eller alla av följande uppgifter.

    >[!IMPORTANT]
    >Alla anpassade avbildningar som du lägger till på den här sidan har bild storlek (bild punkter) och potentiellt fil storlek (KB), begränsningar. På grund av dessa begränsningar behöver du förmodligen använda ett foto redigerings program för att skapa bilder med rätt storlek.

    - **Allmänna inställningar**

        ![Sidan konfigurera företags anpassning, där allmänna inställningar har slutförts](media/customize-branding/configure-company-branding-general-settings.png)

        - **Språk.** Språket anges automatiskt som standard och kan inte ändras.
        
        - **Bakgrunds bild på inloggnings sidan.** Välj en PNG-eller jpg-bildfil som ska visas som bakgrund för dina inloggnings sidor. Bilden fästs i mitten av webbläsaren och skalas till storleken på det visnings bara utrymmet. Du kan inte välja en bild som är större än 1920x1080 bild punkter i storlek eller som har en fil storlek på över 300 KB.
        
            Vi rekommenderar att du använder bilder utan ett starkt ämnes fokus, t. ex., en ogenomskinlig vit ruta visas i mitten av skärmen och kan ta upp vilken del som helst av bilden beroende på storleken på det visnings bara utrymmet.

        - **Banderoll-logotyp.** Välj en. png-eller. jpg-version av logo typen som ska visas på inloggnings sidan när användaren har angett ett användar namn och på Portal sidan **Mina appar** .
            
            Bilden får inte vara högre än 60 bild punkter eller bredare än 280 bild punkter och filen får inte vara större än 10 kB. Vi rekommenderar att du använder en transparent bild eftersom bakgrunden kanske inte matchar din logo typ bakgrund. Vi rekommenderar också att du inte lägger till utfyllnad runt bilden eller ser till att din logo typ är liten. 

        - **Användar tips.** Skriv tips texten som visas för användarna om de glömmer bort sitt användar namn. Den här texten måste vara Unicode, utan länkar eller kod, och får inte innehålla fler än 64 tecken. Om gäster loggar in på din app rekommenderar vi inte att du lägger till det här tipset.

        - **Text och formatering för inloggnings sidan.** Skriv texten som visas längst ned på inloggnings sidan. Du kan använda den här texten för att kommunicera ytterligare information, t. ex. telefonnumret till supportavdelningen eller ett juridiskt meddelande. Den här texten måste vara Unicode och får inte överstiga 1024 tecken.

           Du kan anpassa text för inloggnings sidan som du har angett. Om du vill börja med ett nytt stycke använder du tangenten Enter två gånger. Du kan också ändra textformateringen så att den innehåller fetstil, kursiv stil, en understruken eller klicknings bara länk. Använd följande syntax för att lägga till formatering i text: 

          > Aktuell ```[text](link)``` 
          
          > Fet: ``` **text** ``` eller ``` __text__ ``` 
          
          > Kursiv stil: ``` *text* ``` eller ``` _text_ ``` 
          
          > Strykning ``` ++text++ ``` 

    - **Avancerade inställningar**
            
        ![Sidan konfigurera företags anpassning med avancerade inställningar slutfört](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Bakgrunds färg på inloggnings sidan.** Ange den hexadecimala färgen (till exempel vitt är #FFFFFF) som ska visas i stället för bakgrunds bilden i anslutnings situationer med låg bandbredd. Vi rekommenderar att du använder den primära färgen på banderollen eller din organisations färg.

        - **Bild av kvadratisk logo typ.** Välj en. png-bild (önskad) eller. jpg-bild av organisationens logo typ som visas för användarna under installationen för nya Windows 10 Enterprise-enheter. Den här avbildningen används endast för Windows-autentisering och visas bara på klienter som använder [Windows autopilot]( /windows/deployment/windows-autopilot/windows-10-autopilot) för distribution eller för lösen ords post sidor i andra Windows 10-upplevelser. I vissa fall kan den också visas i dialog rutan medgivande.
        
            Bilden får inte vara större än 240x240 bild punkter och måste ha en fil storlek på mindre än 10 KB. Vi rekommenderar att du använder en transparent bild eftersom bakgrunden kanske inte matchar din logo typ bakgrund. Vi rekommenderar också att du inte lägger till utfyllnad runt bilden eller ser till att din logo typ är liten.
    
        - **Bild av kvadratisk logo typ, mörkt tema.** Samma som fyrkant logo typ bilden ovan. Logo typ bilden tar platsen för fyrkant logo bilden när den används med en mörk bakgrund, till exempel med Windows 10 Azure AD-anslutna skärmar under OOBE (out-of-Box Experience).  Om din logo typ ser bra ut på vita, mörka blå och svarta bakgrunder, behöver du inte lägga till den här bilden. 
        
        - **Visa alternativet för att förbli inloggad.** Du kan välja att låta dina användare förbli inloggade på Azure AD tills de uttryckligen loggar ut. Om du väljer **Nej** är det här alternativet dolt och användarna måste logga in varje gången webbläsaren stängs och öppnas igen.

            Den här funktionen är endast tillgänglig för det anpassade varumärkes objekt och inte för språkspecifika objekt. Mer information om hur du konfigurerar och felsöker alternativet för att förbli inloggad finns i avsnittet [om att förbli inloggad? "för Azure AD-konton](keep-me-signed-in.md)
        
            >[!NOTE]
            >För vissa funktioner i SharePoint Online och Office 2010 måste användarna kunna välja att fortsätta vara inloggade. Om du ställer in den här inställningen till **No** (Nej) kan eventuellt ytterligare och oväntade uppmaningar att logga in visas för dina användare.
   

3. När du har lagt till din anpassning väljer du **Spara**.

    Om den här processen skapar din första anpassade anpassnings konfiguration blir den standard för din klient. Om du har ytterligare konfigurationer kan du välja din standard konfiguration.
    
    >[!IMPORTANT]
    >Om du vill lägga till fler konfigurationer för företags anpassning till din klient måste du välja **nytt språk** på sidan **contoso-Company varumärkes anpassning** . Då öppnas sidan **Konfigurera företags anpassning** där du kan följa samma steg som ovan.

## <a name="update-your-custom-branding"></a>Uppdatera din anpassade anpassning
När du har skapat din anpassade anpassning kan du gå tillbaka och ändra vad du vill.

### <a name="to-edit-your-custom-branding"></a>Redigera din anpassade anpassning
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory** och välj sedan **företags anpassning** och välj sedan **Konfigurera**.

    ![Contoso-sidan för företags anpassning, där standard konfigurationen visas](media/customize-branding/company-branding-default-config.png)

3. På sidan **Konfigurera företags anpassning** lägger du till, tar bort eller ändrar någon information baserat på beskrivningarna i avsnittet [Anpassa din Azure AD-inloggnings sida](#customize-your-azure-ad-sign-in-page) i den här artikeln.

4. Välj **Spara**.

   Det kan ta upp till en timme innan ändringar som du har gjort på inloggningssidan visas.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Lägga till språkspecifik varumärkesanpassning till din katalog
Du kan inte ändra den ursprungliga konfigurationens språk från standard språket. Men om du behöver en konfiguration på ett annat språk kan du skapa en ny konfiguration.

### <a name="to-add-a-language-specific-branding-configuration"></a>Så här lägger du till en språkspecifik anpassnings konfiguration

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory** och välj sedan **företags anpassning** och välj sedan **nytt språk**.

    ![Contoso-sidan för företags anpassning, med nytt språk alternativ markerat](media/customize-branding/company-branding-new-language.png)

3. På sidan **Konfigurera företags anpassning** väljer du ditt språk (till exempel franska) och lägger sedan till översatt information baserat på beskrivningarna i avsnittet [Anpassa din Azure AD-inloggning](#customize-your-azure-ad-sign-in-page) i den här artikeln.

4. Välj **Spara**.

    Sidan **contoso – företagets varumärkes anpassning** för att visa den nya franska konfigurationen.

    ![Contoso-sidan för företags anpassning med den nya språk konfigurationen som visas](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Lägg till anpassad anpassning till sidor
Lägg till anpassad anpassning till sidor genom att ändra slutet på URL: en med texten `?whr=yourdomainname` . Den här ändringen fungerar på flera sidor, inklusive installations sidan Multi-Factor Authentication (MFA), installations sidan för lösen ords återställning via självbetjäning (SSPR) och inloggnings sidan.

**Exempel:**

**Ursprunglig URL:**https://aka.ms/MFASetup<br>
**Anpassad URL:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Ursprunglig URL:**https://aka.ms/SSPR<br>
**Anpassad URL:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`
