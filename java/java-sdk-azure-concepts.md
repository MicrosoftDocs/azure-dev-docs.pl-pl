---
title: Programowanie przy użyciu za pomocą bibliotek zarządzania platformy Azure dla języka Java
description: Wzorce i koncepcje dotyczące użycia bibliotek zarządzania języka Java do zarządzania zasobami w chmurze na platformie Azure.
keywords: Azure, Java, SDK, API, Maven, Gradle, uwierzytelnianie, active directory, jednostka usługi
author: rloutlaw
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: f452468b-7aae-4944-abad-0b1aaf19170d
ms.custom: seo-java-july2019, seo-java-september2019
ms.openlocfilehash: 2bf2630c5fef6c399e2642e1ae153630f48874a9
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "77000124"
---
# <a name="patterns-and-best-practices-for-development-with-the-azure-libraries-for-java"></a>Wzorce i najlepsze rozwiązania dotyczące programowania przy użyciu bibliotek platformy Azure dla języka Java 

W tym artykule przedstawiono szereg wzorców i najlepszych rozwiązań dotyczących używania bibliotek platformy Azure dla języka Java w projektach. Programuj przy użyciu tych wzorców i wskazówki, aby zmniejszyć ilość kodu do utrzymania oraz ułatwić dodawanie i konfigurowanie dodatkowych zasobów w przyszłych aktualizacjach w bibliotekach zarządzania.

## <a name="build-resources-through-a-fluent-interface"></a>Kompilowanie zasobów za pomocą płynnego interfejsu

Płynny interfejs to wzorzec służący do tworzenia obiektów przy użyciu łańcucha metod, który poprawnie konfiguruje atrybuty obiektu. Aby na przykład utworzyć nowe konto usługi Azure Storage

```java
StorageAccount storage = azure.storageAccounts().define(storageAccountName)
    .withRegion(region)
    .withNewResourceGroup(resourceGroup)
    .create();
```

Podczas przechodzenia przez łańcuch metod środowisko IDE sugeruje następną metodę do wywołania w ramach płynnej konwersacji.   

![Obraz GIF przedstawiający proces wykonywania poleceń platformy IntelliJ przechodzący przez płynny łańcuch](media/intellij-fluent-method-chain.gif)

Utwórz łańcuch metod sugerowanych przez środowisko IDE, o ile mają one sens w przypadku definiowanego zasobu platformy Azure. W przypadku braku wymaganej metody w łańcuchu środowisko IDE wyróżni ją jako błąd.

## <a name="resource-collections"></a>Kolekcje zasobów

Biblioteka zarządzania ma pojedynczy punkt wejścia za pośrednictwem obiektu najwyższego poziomu `com.microsoft.azure.management.Azure`, który umożliwia tworzenie i aktualizowanie zasobów. Wybierz typ zasobów, z którym chcesz pracować przy użyciu metod zbierania zasobów zdefiniowanych w obiekcie `Azure`. Na przykład w usłudze SQL Database:

```java
SqlServer sqlServer = azure.sqlServers().define(sqlServerName)
    .withRegion(Region.US_EAST)
    .withNewResourceGroup(rgName)
    .withAdministratorLogin(administratorLogin)
    .withAdministratorPassword(administratorPassword)
    .create();
```

## <a name="lists-and-iterations"></a>Listy i iteracje

Każda kolekcja zasobów ma metodę `list()`, która zwraca każde wystąpienie danego zasobu w bieżącej subskrypcji. Na przykład metoda `azure.sqlServers().list()` zwraca wszystkie bazy danych SQL w subskrypcji.

Użyj metody `listByResourceGroup(String groupname)`, aby określić zakres zwracanej listy jako określoną [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).  

Wyszukaj i wykonaj iterację zwracanej kolekcji `PagedList<T>`, tak samo jak w przypadku zwykłego elementu `List<T>`:

```java
PagedList<VirtualMachine> vms = azure.virtualMachines().list();
for (VirtualMachine vm : vms) {
    System.out.println("Found virtual machine with ID " + vm.id());
}
```   

## <a name="collections-returned-from-queries"></a>Kolekcje zwracane z zapytań

Biblioteki zarządzania zwracają określone typy kolekcji z zapytań w oparciu o strukturę wyników.

- `List<T>`: Nieuporządkowane dane, które można łatwo wyszukiwać i iterować.
- `Map<T>`: Mapy to pary klucz/wartość z unikatowymi kluczami, ale niekoniecznie unikatowymi wartościami. Przykładem mapy są ustawienia aplikacji internetowej usługi App Service.
- `Set<T>`: Zestawy mają unikatowe klucze i wartości. Przykładem zestawu są sieci dołączone do maszyny wirtualnej, które mają unikatowy identyfikator (klucz) i unikatową konfigurację sieci (wartość).

## <a name="actionable-verbs"></a>Czasowniki umożliwiające działanie

Metody z czasownikami w nazwach powodują natychmiastowe podjęcie akcji na platformie Azure. Metody te działają synchronicznie i blokują wykonywanie w bieżącym wątku, dopóki nie zostaną ukończone. 

| Czasownik   |  Przykładowe użycie |
|--------|---------------|
| create | `azure.virtualMachines().create(listOfVMCreatables)` |
| apply  | `virtualMachineScaleSet.update().withCapacity(6).apply()` |
| delete | `azure.disks().deleteById(id)` | 
| list   | `azure.sqlServers().list()` | 
| get    | `VirtualMachine vm  = azure.virtualMachines().getByResourceGroup(group, vmName)` |

>[!NOTE]
> Elementy `define()` i `update()` są czasownikami, ale powodują blokowania, chyba że występuje po nich metoda `create()` lub `apply()`.
 
Asynchroniczne wersje niektórych z tych metod mają sufiks `Async`, jeśli korzystasz z [rozszerzeń reaktywnych](https://github.com/ReactiveX/RxJava). 

Niektóre obiekty mają inne metody, które w ten sposób zmieniają stan zasobu na platformie Azure. Na przykład metoda `restart()` w obiekcie `VirtualMachine`.

```java
VirtualMachine vmToRestart = azure.getVirtualMachines().getById(id);
vmToRestart.restart();
```
Metody te nie zawsze mają wersje asynchroniczne i blokują wykonywanie w odpowiednim wątku, dopóki nie zostaną ukończone.

<a name="Creatables"></a>

## <a name="lazy-resource-creation"></a>Tworzenie zasobu z opóźnieniem

Wyzwaniem, które może wystąpić podczas tworzenia zasobów platformy Azure, jest sytuacja, gdy nowy zasób zależy od innego zasobu, który jeszcze nie istnieje. Przykładem tego scenariusza jest zastrzeganie publicznego adresu IP i konfigurowanie dysku podczas tworzenia nowej maszyny wirtualnej. Nie chcesz weryfikować zastrzegania adresu ani tworzenia dysku — chcesz po prostu upewnić się, że utworzona maszyna wirtualna będzie mieć te zasoby.

Obiekty `Creatable<T>` umożliwiają definiowanie zasobów platformy Azure do użycia w kodzie bez czekania na ich utworzenie w ramach subskrypcji. Biblioteki zarządzania opóźniają tworzenie obiektów `Creatable<T>` do momentu, aż będą one wymagane.

Do generowania obiektów `Creatable<T>` dla zasobów platformy Azure służy czasownik `define()`:

```java
Creatable<PublicIPAddress> publicIPAddressCreatable = azure.publicIPAddresses().define(publicIPAddressName)
    .withRegion(Region.US_EAST)
    .withNewResourceGroup(rgName);
```

Zasób platformy Azure zdefiniowany przez element `Creatable<PublicIPAddress>` w tym przykładzie nie istnieje jeszcze w subskrypcji w trakcie wykonywania kodu.  Użyj obiektu `publicIPAddressCreatable`, aby utworzyć inne zasoby platformy Azure za pomocą tego adresu IP. 

```java
Creatable<VirtualMachine> vmCreatable = azure.virtualMachines().define("creatableVM")
    .withNewPrimaryPublicIPAddress(publicIPAddressCreatable)
```

Zasoby `Creatable<T>` są generowane w ramach subskrypcji, gdy dowolny zasób zdefiniowany przy użyciu obiektu jest kompilowany na platformie Azure przy użyciu metody `create()`. Kontynuujmy przykład dotyczący adresu IP i maszyny wirtualnej:

```java
CreatedResources<VirtualMachine> virtualMachine = azure.virtualMachines().create(vmCreatable);
```

Przekazanie elementu `Creatable<T>` do wywołań metody `create()` zwraca obiekt `CreatedResources` zamiast pojedynczego obiektu zasobu.  Obiekt `CreatedResources<T>` umożliwia dostęp do wszystkich zasobów utworzonych przez wywołanie metody `create()`, a nie tylko do zasobu określonego typu w wywołaniu. Aby uzyskać dostęp do publicznego adresu IP utworzonego na platformie Azure dla maszyny wirtualnej utworzonej w powyższym przykładzie:

```java
PublicIPAddress pip = (PublicIPAddress) virtualMachine.createdRelatedResource(publicIPAddressCreatable.key());
```    

## <a name="exception-handling"></a>Obsługa wyjątków

Klasy wyjątków bibliotek zarządzania rozszerzają element `com.microsoft.rest.RestException`. Przechwytuj wyjątki wygenerowane przez biblioteki zarządzania z blokiem `catch (RestException exception)` po odpowiedniej instrukcji `try`.

## <a name="logs-and-trace"></a>Dzienniki i śledzenie

Skonfiguruj zakres rejestrowania z biblioteki zarządzania podczas kompilowania obiektu `Azure` punktu wejścia przy użyciu metody `withLogLevel()`. Istnieją następujące poziomy śledzenia:

| Poziom śledzenia | Rejestrowanie włączone 
| ------------ | ---------------
| com.microsoft.rest.LogLevel.NONE | Brak danych wyjściowych
| com.microsoft.rest.LogLevel.BASIC | Rejestruje adresy URL do podstawowych wywołań REST, kody odpowiedzi i godziny
| com.microsoft.rest.LogLevel.BODY | Wszystkie elementy na poziomie BASIC oraz treści żądań i odpowiedzi dla wywołań REST
| com.microsoft.rest.LogLevel.HEADERS | Wszystkie elementy na poziomie BASIC oraz nagłówki żądań i odpowiedzi dla wywołań REST
| com.microsoft.rest.LogLevel.BODY_AND_HEADERS | Wszystkie elementy na poziomie dziennika BODY i HEADERS

Powiąż [implementację rejestrowania SLF4J](https://www.slf4j.org/manual.html), jeśli chcesz rejestrować dane wyjściowe do struktury rejestrowania, takiej jak [Log4J 2](https://logging.apache.org/log4j/2.x/).
