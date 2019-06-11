---
layout: page
title: Technologie programowania
---
Główny prowadzący kurs: dr inż. Karol Tarnowski

### Materiały

[Zasady zaliczenia](../tp_zz.pdf)

Wykłady:
- [Wykład 1](../tp_w01.pdf)
- [Wykład 2](../tp_w02.pdf)
- [Wykład 3](../tp_w03.pdf)
- [Wykład 4](../tp_w04.pdf)
- [Wykład 5-6](../tp_w05.pdf)
- [Wykład 7](../tp_w07.pdf)
- [Wykład 8](../tp_w08.pdf)

Listy zadań:
- [Lista 1](../tp_l01.pdf)
- [Lista 2](../tp_l02.pdf)
- [Lista 3](../tp_l03.pdf)
- [Lista 4](../tp_l04.pdf)
- [Lista 5](../tp_l05.pdf)

# Uwagi

## Lista 4

### Używanie konstruktora klasy bazowej oraz różnica między funkcją wirtualną a zwykłą

Rozpatrzmy następujące klasy (kod pochodzący z [tej](https://www.studytonight.com/cpp/order-of-constructor-call.php) i [tej](https://www.geeksforgeeks.org/virtual-function-cpp/) strony):

```
#include<iostream>
using namespace std;

class Base {
public:
    Base() {
	     cout << "Constructor of base class" << endl;
    }

    virtual void print () {
      cout<< "Print base class" << endl;
    }

    void show () {
      cout<< "Show base class" <<endl;
    }
};

class Derived: public Base {
public:
    Derived() {
	     cout << "Constructor of derived class" << endl;
    }

    void print () {
      cout<< "Print derived class" <<endl;
    }

    void show () {
      cout<< "Show derived class" <<endl;
    }
};
```
oraz prostą funkcję główną:
```
int main() {
    Derived d;
    return 0;
}
```

Wynik tego programu będzie następujący:
```
Constructor of base class
Constructor of derived class
```

Pokazuje to, że przy tworzeniu obiektu klasy, która dziedziczy po innej klasie, konstruktor klasy bazowej również zostaje uruchomiony.


Rozbudujmy trochę naszą funkcję główną:
```
int main() {
    Base *bptr;
    Derived d;
    bptr = &d;

    // Virtual function, binded at runtime
    bptr->print();

    // Non-virtual function, binded at compile time
    bptr->show();
    return 0;
}
```
Wynikiem działania tak zmienionego kodu będzie:
```
Constructor of base class
Constructor of derived class
Print derived class
Show base class
```
Możemy zauważyć, że funkcja `print()` została przypisana do obiektu na etapie kompilacji, natomiast funkcja `show()` została przypisana już na etapie dzialania programu (runtime). Więcej informacji można znaleźć pod powyższymi linkami.

### Zadanie 2

Na moim repozytorium: [gitlab.com/brzepkowski/dydaktyka/tree/master/2019_summer/Techniki_programowania/virtual_methods](https://gitlab.com/brzepkowski/dydaktyka/tree/master/2019_summer/Techniki_programowania/virtual_methods) umieściłem przykładowy kod, implementujący to zadanie. Można go skompilować w następujący sposób:
```
g++ main.cpp rectangle.cpp circle.cpp
```

Zauważmy, że funkcja `print()` pozwala na zapisywanie obszaru figury do dowolnego strumienia wyjściowego. Wykonanie tego fragmentu:
```
ofstream some_file("rectangle.txt");
rectangle->print(some_file);
rectangle->print(cout);
```
Spowoduje wypisanie obszaru prostokąta do pliku o nazwie `rectangle.txt` oraz do strumienia `std::cout`.

=======================================

W klasie `Figure`
```
class Figure {
public:
  virtual double area() const = 0;
  virtual std::ostream& print(std::ostream&) const = 0;
  friend std::ostream& operator<<(std::ostream& out, Figure& f) {
    return f.print(out);
  }
};
```
przeciążony `operator<<` korzysta z wirtualnej metody `print()`, która zostanie przypisana do obiektu już na etapie działania programu, dlatego możliwe będzie wypisanie pola koła lub prostokąta w prawidlowy sposób, jedynie na podstawie stwierdzenia instancją jakiej klasy jest dany obiekt.

## Lista 5

### Instalowanie GSL na systemie Linux

Poniżej umieściłem przykład instalowania GSL'a na systemie Ubuntu 18.04 (wzorowałem się przykładami ze strony [pl.wikibooks.org/wiki/Programowanie_w_systemie_UNIX/GSL#cite_note-3](https://pl.wikibooks.org/wiki/Programowanie_w_systemie_UNIX/GSL#cite_note-3)).

*Instalacja*
```
sudo apt-get install libgsl0ldbl
sudo apt-get install libgsl0-dev
```
*Sprawdzenie bibliotek*
```
gsl-config --libs
```
*Przykładowe informacje o katalogu i opcjach linkera*
```
-L/usr/lib/x86_64-linux-gnu -lgsl -lgslcblas -lm
```

*Pierwszy przykładowy program*

Nasz przykladowy program umieśćmy w pliku `main.cpp`:
```
#include <stdio.h>
#include <gsl/gsl_sf_bessel.h>

int main(void)
{
  double x = 5.0;
  double y = gsl_sf_bessel_J0(x);
  printf("J0(%g) = %.18e\n", x, y);
  return 0;
}
```
W katalogu, w którym znajduje się `main.cpp` możemy umieścić dodatkowo plik `Makefile`. Jego przykładowa treść może wyglądać następująco:
```
edit: main.o
        g++ -L/usr/lib main.o -lgsl -lgslcblas -lm
main.o: main.cpp
        g++ -Wall -I/usr/include -c main.cpp
clean:
        rm *.o
```
Aby skompilować nasz program, możemy po prostu wykonać polecenie:
```
make
```
Jako wynik, otrzymamy nowe pliki `main.o` oraz `a.out`. Teraz uruchamiając polecenie:
```
make clean
```
pozbędziemy się pliku `main.o`.

`Makefile` został napisany w ten sposób, aby uwydatnić różne etapy przetwarzania programu źródłowego. Poczatkowo następuje etap [kompilacji](https://en.wikipedia.org/wiki/Compiler), a następnie zostaje uruchomiony tzw. [linker/konsolidator](https://en.wikipedia.org/wiki/Linker_(computing)). O różnicach między kompilatorem i linkerem można przeczytać [tutaj](https://stackoverflow.com/questions/6264249/how-does-the-compilation-linking-process-work) (dodatkowo jest tam omówiony tzw. **preprocessing**).

Aby uruchomić nasz końcowy program wykonujemy polecenie:
```
./a.out
```
