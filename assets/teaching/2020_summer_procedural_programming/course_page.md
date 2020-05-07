---
layout: page
title: Programowanie proceduralne
---
Główny prowadzący kurs: dr inż. Karol Tarnowski

Główna strona kursu: <http://www.if.pwr.edu.pl/~tarnowski/201920l_pp.html>

Termin i miejsce zajęć: piątek 7:30 - 9:00, sala 250 b. A-1


# Uwagi ogólne

[Zasady zaliczenia](../pp_zz.pdf)

**Dodatkowe uwagi**: Na każdym zaliczeniu cząstkowym oraz kolokwium można korzystać jedynie z własnych notatek oraz ze slajdów z wykładów. Zabronione jest korzystanie z internetu lub wcześniej napisanych programów.

### Szczegółowy kalendarz zajęć


Data | Rodzaj zajęć
------------ | -------------
28.02 | Zajęcia zwykłe
06.03 | Zajęcia zwykłe
13.03 | Zajęcia zwykłe
20.03 | **Zaliczenie cząstkowe**
27.03 | Zajęcia zwykłe
03.04 | Zajęcia zwykłe
17.04 | **Zaliczenie cząstkowe**
24.04 | Zajęcia zwykłe
08.05 | Zajęcia zwykłe
15.05 | Zajęcia zwykłe
22.05 | **Zaliczenie cząstkowe**
29.05 | Zajęcia zwykłe
05.06 | Zajęcia zwykłe
12.06 | **Kolokwium**
17.06 | **Kolokwium poprawkowe**


# Uwagi odnośnie list zadań

### Rekurencja - ciąg Fibonacciego

Poniżej zamieszczam kod z dwoma funkcjami obliczającymi n-ty element ciągu Fibonacciego. Pierwsza wersja opiera się wyłącznie na rekurencji, natomiast druga jest przykładem programowania dynamicznego. W wielkim skrócie programowanie dynamiczne polega na rozbiciu dużego problemu na jego mniejsze wersje, rozwiązaniu tych mniejszych zadań, zapisaniu wyników i obliczeniu ostatecznego rozwiązania (pierwotnego problemu) na podstawie uzyskanych wyników cząstkowych.

```
import time

def fibonacci_series_elem(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fibonacci_series_elem(n-1) + fibonacci_series_elem(n-2)

def fibonacci_elem_dynamic(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        arr = [0, 1]
        for i in range(2, n+1):
            arr.append(arr[i-2] + arr[i-1])
        return arr[-1]


n = int(input("Który wyraz ciągu Fibonacciego ma zostać obliczony? "))
start_time = time.time()
print("Pierwsza metoda: ", fibonacci_series_elem(n))
first_version_time = time.time() - start_time
start_time = time.time()
print("Druga metoda: ", fibonacci_elem_dynamic(n))
second_version_time = time.time() - start_time

print("Czas obliczeń dla pierwszej wersji: ", first_version_time)
print("Czas obliczeń dla drugiej wersji: ", second_version_time)
```

Po uruchomieniu powyższego programu otrzymujemy następujący rezultat:

```
Który wyraz ciągu Fibonacciego ma zostać obliczony? 35
Pierwsza metoda:  9227465
Druga metoda:  9227465
Czas obliczeń dla pierwszej wersji:  3.528287172317505
Czas obliczeń dla drugiej wersji:  2.4080276489257812e-05
```

Możemy zauważyć, że czas wykonania pierwszej funkcji wynosił nieco ponad 3 sekundy, natomiast drugiej był rzędu $10^{-5}$ s! Krótszy czas wykonania funkcji zawdzięczamy jednak zwiększonemu wykorzystaniu pamięci.
