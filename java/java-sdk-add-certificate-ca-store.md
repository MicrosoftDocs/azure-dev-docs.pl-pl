---
title: Dodawanie certyfikatu głównego dla platformy Azure do magazynu urzędu certyfikacji języka Java
description: Dowiedz się, jak dodać certyfikat główny urzędu certyfikacji do magazynu certyfikatów urzędu certyfikacji języka Java (cacerts), który ma być używany z platformą Microsoft Azure.
documentationcenter: java
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2018
ms.openlocfilehash: 849e4cb65311078fa694151fa8295e6c31d52eb5
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "77000180"
---
# <a name="adding-a-root-certificate-to-the-java-ca-certificates-store"></a>Dodawanie certyfikatu głównego do magazynu certyfikatów urzędu certyfikacji języka Java

Aplikacje korzystające z usług platformy Azure (takich jak Azure Service Bus) muszą ufać certyfikatowi głównemu programu Baltimore CyberTrust. Ten certyfikat może już być zainstalowany w systemie, ale jeśli tak nie jest, kroki opisane w tym samouczku przedstawiają sposób użycia narzędzia **keytool** firmy Oracle do dodania certyfikatu głównego urzędu certyfikacji języka Java (cacerts), który będzie używany na potrzeby usług platformy Azure.

Narzędzie keytool firmy Oracle to _narzędzie do zarządzania kluczami i certyfikatami_, które umożliwia deweloperom zarządzanie listą zaufanych certyfikatów do użycia z językiem Java. Narzędzia keytool można użyć do dodania certyfikatu urzędu certyfikacji przed zapakowaniem zestawu JDK i dodaniem go do folderu **approot** projektu platformy Azure. Można również uruchomić zadanie uruchamiania platformy Azure, które używa narzędzia keytool do dodawania certyfikatu.

Od 15 kwietnia 2013 r. platforma Azure rozpoczęła migrację z globalnego certyfikatu głównego GTE CyberTrust do certyfikatu głównego Baltimore CyberTrust. Poniższe kroki pokazują, jak za pomocą narzędzia keytool dodać certyfikat główny Baltimore CyberTrust do magazynu certyfikatów urzędu certyfikacji języka Java (cacerts).

> [!NOTE]
> 
> Korzystając z kroków opisanych w tym artykule, można skonfigurować zestaw Java SDK, aby ufać certyfikatom głównym od innych zaufanych urzędów certyfikacji. Można na przykład wybrać certyfikat główny z listy certyfikatów na stronie [certyfikatów głównych GeoTrust](https://www.geotrust.com/resources/root-certificates/).
> 

## <a name="determining-which-root-certificates-are-installed"></a>Określanie, które certyfikaty główne zostały zainstalowane

Certyfikat Baltimore może już być zainstalowany w magazynie cacerts, dlatego należy wykonać poniższe kroki, aby określić, czy został on już zainstalowany.

1. W wierszu polecenia administratora przejdź do folderu **jdk\jre\lib\security** zestawu JDK, a następnie uruchom następujące polecenie, aby wyświetlić listę certyfikatów zainstalowanych w systemie:

   ```shell
   keytool -list -keystore cacerts
   ```

1. Jeśli zostanie wyświetlony monit o hasło magazynu, hasło domyślne to **changeit**.

   > [!NOTE]
   > 
   > Jeśli chcesz zmienić hasło magazynu, zapoznaj się z dokumentacją narzędzia keytool pod adresem <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.
   > 

1. Jeśli nie widzisz certyfikatu z odciskiem palca `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`, wykonaj kroki opisane w poniższej sekcji, aby pobrać i zainstalować certyfikat.

## <a name="to-add-a-root-certificate-to-the-cacerts-store"></a>Aby dodać certyfikat główny do magazynu cacerts

1. Pobierz certyfikat główny Baltimore CyberTrust ze strony <https://cacert.omniroot.com/bc2025.crt> i zapisz go w pliku lokalnym z rozszerzeniem **cer** w folderze **jdk\jre\lib\security**. W tym przykładzie załóżmy, że plik certyfikatu głównego Baltimore CyberTrust został pobrany jako **bc2025.cer**.

   > [!NOTE]
   > 
   > Certyfikat główny Baltimore CyberTrust ma numer seryjny `02:00:00:b9` i odcisk palca SHA1 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.
   > 

2. Zaimportuj certyfikat do magazynu cacerts przy użyciu następującego polecenia:

   ```shell
   keytool -keystore cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```
   Gdzie:

   |  Parametr   |                              Opis                               |
   |--------------|------------------------------------------------------------------------|
   | `keystore`   | Określa magazyn certyfikatów.                                       |
   | `importcert` | Określa, że odbywa się importowanie certyfikatu.                        |
   | `alias`      | Określa alias certyfikatu.                                |
   | `file`       | Określa nazwę pliku certyfikatu głównego, który jest importowany. |


3. Jeśli zostanie wyświetlony monit o zaufanie certyfikatowi, sprawdź odcisk palca jako `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74` i wpisz **y**, jeśli odcisk palca jest prawidłowy.

4. Uruchom następujące polecenie, aby upewnić się, że certyfikat urzędu certyfikacji został pomyślnie zaimportowany:

   ```shell
   keytool -list -keystore cacerts
   ```

Po pomyślnym dodaniu certyfikatu głównego do zestawu JDK możesz spakować zawartość zestawu JDK i dodać ją do folderu **approot** projektu platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o narzędziu keytool, zobacz temat <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

Aby uzyskać więcej informacji na temat języka Java, zobacz temat [Platforma Azure dla deweloperów języka Java](/azure/java).

<!-- For more information about the root certificates used by Azure, see [Azure Root Certificate Migration](https://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx). -->

Aby uzyskać więcej informacji na temat obsługiwanych zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz temat <https://aka.ms/azure-jdks>.