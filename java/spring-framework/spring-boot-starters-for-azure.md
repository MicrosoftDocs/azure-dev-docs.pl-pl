---
title: Szablony startowe Spring Boot dla platformy Azure
description: W tym artykule opisano różne szablony startowe Spring Boot, które są dostępne dla platformy Azure.
documentationcenter: java
ms.date: 12/19/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 624aaff3022bd40068ce0e0032ab676bd85b200b
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999697"
---
# <a name="spring-boot-starters-for-azure"></a>Szablony startowe Spring Boot dla platformy Azure

W tym artykule opisano różne szablony startowe Spring Boot dla narzędzia [Spring Initializr], które udostępniają deweloperom języka Java funkcje integracji umożliwiające pracę z platformą Microsoft Azure.

![Szablony startowe Spring Boot platformy Azure][spring-boot-starters]

Obecnie dla platformy Azure są dostępne następujące szablony startowe Spring Boot:

* **[Pomoc techniczna platformy Azure](#azure-support)**

   Zapewnia obsługę automatycznej konfiguracji dla usług platformy Azure, takich jak Service Bus, Storage, Active Directory itd.

* **[Azure Active Directory](#azure-active-directory)**

   Zapewnia obsługę integracji rozwiązania Spring Security z usługą Azure Active Directory na potrzeby uwierzytelniania.

* **[Azure Key Vault](#azure-key-vault)**

   Zapewnia obsługę adnotacji wartości rozwiązania Spring na potrzeby integracji z wpisami tajnymi usługi Azure Key Vault.

* **[Azure Storage](#azure-storage)**

   Zapewnia obsługę rozwiązania Spring Boot dla usług Azure Storage.

<a name="azure-support"></a>
## <a name="azure-support"></a>Pomoc techniczna platformy Azure

Ten szablon startowy Spring Boot zapewnia obsługę automatycznej konfiguracji dla usług platformy Azure, takich jak: Service Bus, Storage, Active Directory, Cosmos DB, Key Vault itd.

Aby zapoznać się z przykładami użycia różnych funkcji platformy Azure, które są dostępne w tym szablonie startowym, zobacz:

* <https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples>

Po dodaniu tego szablonu startowego do projektu Spring Boot w pliku *pom.xml* wprowadzane są następujące zmiany:

* Do elementu `<properties>` dodawana jest następująca właściwość:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* Domyślna zależność `spring-boot-starter` jest zastępowana następującą:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-spring-boot</artifactId>
   </dependency>
   ```

* Do pliku jest dodawana następująca sekcja:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-active-directory"></a>
## <a name="azure-active-directory"></a>Usługa Azure Active Directory

Ten szablon startowy Spring Boot zapewnia obsługę automatycznej konfiguracji rozwiązania Spring Security w celu zapewnienia integracji z usługą Azure Active Directory na potrzeby uwierzytelniania.

Aby zapoznać się z przykładami użycia funkcji usługi Azure Active Directory, które są dostępne w tym szablonie startowym, zobacz:

* <https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-active-directory-spring-boot-sample>

Po dodaniu tego szablonu startowego do projektu Spring Boot w pliku *pom.xml* wprowadzane są następujące zmiany:

* Do elementu `<properties>` dodawana jest następująca właściwość:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* Domyślna zależność `spring-boot-starter` jest zastępowana następującą:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-active-directory-spring-boot-starter</artifactId>
   </dependency>
   ```

* Do pliku jest dodawana następująca sekcja:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-key-vault"></a>
## <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Ten szablon startowy Spring Boot zapewnia obsługę adnotacji wartości rozwiązania Spring na potrzeby integracji z wpisami tajnymi usługi Azure Key Vault.

Aby zapoznać się z przykładami użycia funkcji usługi Azure Key Vault, które są dostępne w tym szablonie startowym, zobacz:

* <https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-keyvault-secrets-spring-boot-sample>

Po dodaniu tego szablonu startowego do projektu Spring Boot w pliku *pom.xml* wprowadzane są następujące zmiany:

* Do elementu `<properties>` dodawana jest następująca właściwość:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* Domyślna zależność `spring-boot-starter` jest zastępowana następującą:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
   </dependency>
   ```

* Do pliku jest dodawana następująca sekcja:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-storage"></a>
## <a name="azure-storage"></a>Azure Storage

Ten szablon startowy Spring Boot zapewnia obsługę integracji rozwiązania Spring Boot dla usług Azure Storage.

Aby zapoznać się z przykładami użycia funkcji usługi Azure Storage, które są dostępne w tym szablonie startowym, zobacz:

* [Używanie szablonu startowego Spring Boot dla usługi Azure Storage](configure-spring-boot-starter-java-app-with-azure-storage.md)

* <https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-storage-spring-boot-sample>

Po dodaniu tego szablonu startowego do projektu Spring Boot w pliku *pom.xml* wprowadzane są następujące zmiany:

* Do elementu `<properties>` dodawana jest następująca właściwość:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <azure.version>0.2.0</azure.version>
   </properties>
   ```

* Domyślna zależność `spring-boot-starter` jest zastępowana następującą:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-storage-spring-boot-starter</artifactId>
   </dependency>
   ```

* Do pliku jest dodawana następująca sekcja:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji o korzystaniu z aplikacji [Spring Boot] na platformie Azure, zobacz [Oprogramowanie Spring na platformie Azure].

Aby uzyskać więcej informacji na temat używania platformy Azure z językiem Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] i [Praca z narzędziami Azure DevOps i językiem Java].

Aby uzyskać pomoc dotyczącą rozpoczynania pracy z własnymi aplikacjami Spring Boot, zapoznaj się z narzędziem **Spring Initializr** na stronie https://start.spring.io/.

<!-- URL List -->

[Azure dla deweloperów języka Java]: /azure/java/
[Praca z narzędziami Azure DevOps i językiem Java]: /azure/devops/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Oprogramowanie Spring na platformie Azure]: /azure/java/spring-framework/
[Spring Framework]: https://spring.io/
[Spring Initializr]: https://start.spring.io/

<!-- IMG List -->

[spring-boot-starters]: media/spring-boot-starters-for-azure/spring-boot-starters-cropped.png
