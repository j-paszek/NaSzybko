## Git 2: decydujące starcie*

*decydujące starcie/dzień sądu/reaktywacja/komnata tajemnic/atak clone-ów (wybierz swoją ulubioną serię filmową)

w przyszłości przy omawianiu AI świetnie pasować będzie -  Git 3: bunt maszyn

---
Słowniczek do dalszych zadań:

- PR => Pull/Merge Request 
- UI => interfejs użytkownika

<hr style="height:7px; border:none; background-color:currentColor;">

Zaczniemy od symulacji zwykłej pracy w firmie, gdzie senior akceptuje kod junior programisty. 
Podobnie przy pracy z AI, AI może zgłaszać zmiany w projekcie jako PR.
W zadaniach 1 i 2 raz przyjmujemy rolę seniora, raz juniora. W zadaniu 3 odegrajmy te role w parach.

### Zadanie 1 (Branch funkcjonalny + Pull/Merge Request)
Utwórz nową funkcjonalność na osobnym branchu, a następnie otwórz PR/MR do gałęzi main/master.
*Zobacz dokumentację (GitHub: **Pull Requests**, GitLab: **Merge Requests**).*

#### Rozwiązanie – krok po kroku

1. Inicjalizacja repozytorium:

        git init
        echo "print('Hello')" > app.py
        git add app.py
        git commit -m "Initial version"

2. Dodanie zdalnego repozytorium (powtórka - **mam lokalnie repozytorium, chce stworzyć projekt zdalnie**; 
**logowanie** zobacz uwagi C.1 i C.2 na dole):

        Na GitHub/GitLab: “New project / New repository”. Potem dodaj jego URL:

        git remote add origin <URL>
        git push -u origin master

3. Stworzenie brancha funkcjonalnego:

        git switch -c feature/greeting

4. Edycja kodu, commit i push:

        echo "print('Hello, User')" >> app.py
        git add app.py
        git commit -m "Add personalized greeting"
        git push -u origin feature/greeting

5. Po odświeżeniu strony pojawi się np. *feature/greeting had recent pushes 1 minute ago* → **Compare & Pull Request**
   klikamy; następnie wpisujemy tekst i klikamy **Create pull request**
   - base: master
   - compare: feature/greeting

6. Po przeglądzie scal PR.
   - w UI: **Merge pull request** (wybierz “Create merge commit”; wybierane automatycznie, strzałka w dół aby rozwinąć)
   - lokalnie: `git switch master` i `git pull` aby obejrzeć zmiany

---

### Zadanie 2 (Aktualizacja PR po recenzji)
Zasymuluj code review: wprowadź poprawki do istniejącego PR, nie zamykając go.
*Wskazówka: Każdy push do brancha PR automatycznie aktualizuje PR.*

#### Rozwiązanie

1. (Jako senior) Otwórz PR i dodaj komentarz do kodu. Np. “Użyj f-string zamiast konkatenacji”. Kliknij **Comment**
2. (Jako junior) W lokalnym repo zmodyfikuj kod:

        git switch feature/greeting
        # modyfikacja kodu
        git commit -am "Refactor greeting using f-string"
        git push

3. PR automatycznie się zaktualizuje.
4. Pojawi się link "Refactor greeting using f-string", można go kliknąć aby obejrzeć commita 
![pull request](pr.png)
5. Po zaakceptowaniu → Merge.

---

### Zadanie 3 (Fork i Pull Request)
Dobierz się w pary. Każda Osoba udostępnia publicznie dowolne repozytorium z poprzednich zadań/ćwiczeń.
Wykonaj zmianę w repozytorium, którego nie jesteś właścicielem, używając forka i Pull Requesta.
Następnie jako właściciel obsłuż PR.

### Motywacja
Zasymulujemy, jak współpracować z repozytoriami, do których nie masz uprawnień zapisu. 
Przykładowo, [biopython](https://github.com/biopython/biopython) ma 393 twórców (contributors; stan na 8.12.2025),
gdybyśmy chcieli dodać do projektu zaimplementowaną przez nas funkcjonalność, zgłosilibysmy PR.

*Wskazówka: Fork tworzy własną kopię zdalnego repozytorium pod Twoim kontem. Masz do niej pełne prawa push, 
a zmiany proponujesz oryginalnemu repozytorium poprzez Pull Request.*


### Rozwiązanie – krok po kroku

1. Znajdź repozytorium, do którego chcesz wnieść zmianę (od drugiej Osoby z pary), 
np. (o koncie `KontoInne` i nazwie `genome-analyzer`):

        https://github.com/KontoInne/genome-analyzer

2. Utwórz **fork** repozytorium na GitHubie:

        Fork → Create a copy under your account

   Powstaje Twoje repozytorium:

        https://github.com/TwojeKonto/genome-analyzer

3. Sklonuj własnego forka:

        git clone https://github.com/TwojeKonto/genome-analyzer
        cd genome-analyzer

4. Utwórz branch funkcjonalny:

        git switch -c feature/better-export

5. Wprowadź zmiany, commit, push:

        git add .
        git commit -m "Improve JSON exporter performance"
        git push -u origin feature/better-export

6. Otwórz Pull Request:

   - base repository: oryginalny projekt (np. `KontoInne/genome-analyzer (original)`)
   - compare branch: Twój branch z forka (np. `TwojeKonto:feature/better-export`)

        Create Pull Request

7. Przejdź code review, popraw kod jeżeli trzeba:

        git commit -am "Apply review comments"
        git push

   PR automatycznie się aktualizuje.

8. Po akceptacji maintainer scala Pull Request.

---

### Zadanie 3.A (Bonus: aktualizacja forka)
Dłuższa praca nad projektem wymaga aktualizacji fork-a. W wyżej opsianym rozwiązaniu dodajmy punkt:

5.1. Druga Osoba z pary dokonuje zmiany we własnym projekcie (`KontoInne/genome-analyzer`) 

5.2. Aktualizujemy fork-a:

        git remote add upstream https://github.com/KontoInne/genome-analyzer
        git fetch upstream
        git merge upstream/master
        git push

Fork ponownie jest aktualny.

5.3. Możemy wprowadzić kolejne zmiany jak w pkt 5

---

#### Podsumowanie

Fork jest Twoją zdalną kopią repozytorium.  
Pozwala na:

- pełną swobodę pracy,
- bezpieczne eksperymentowanie,
- udział w open-source,
- propozycje zmian poprzez Pull Request.

To podstawowy workflow w dużych projektach, gdzie miliony osób nie mają dostępu do głównego repozytorium.

<hr style="height:7px; border:none; background-color:currentColor;">

Punkt wydania (release point) to konkretne miejsce w historii projektu zapisane w repozytorium Git, 
które oznacza stabilną wersję programu gotową do użycia lub udostępnienia. Jest ona zwykle oznaczona tagiem i służy 
jako odniesienie do sprawdzonego, ukończonego etapu pracy.

O numerowaniu wersji warto przeczytać [tu](https://semver.org/). W skrócie, 
dla numeru wersji w formacie MAJOR.MINOR.PATCH należy zwiększać:  
-**MAJOR** — gdy wprowadzasz niezgodne wstecz zmiany w API  
-**MINOR** — gdy dodajesz nowe funkcje w sposób zgodny z poprzednimi wersjami  
-**PATCH** — gdy wprowadzasz poprawki błędów zgodne wstecz  
Dodatkowe oznaczenia dla wersji przedwydaniowych i metadanych kompilacji są dostępne jako 
rozszerzenia formatu MAJOR.MINOR.PATCH.

Więcej o tagowaniu jest [tu](https://git-scm.com/book/en/v2/Git-Basics-Tagging),
o releasach [tu](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository),
a artykuł o dobrych praktykach można znaleźć [tu](https://pmc.ncbi.nlm.nih.gov/articles/PMC3886731/).

---

Dobre zarządzanie danymi badawczymi opiera się na zasadach FAIR: dane powinny być Findable, Accessible, Interoperable, 
Reusable — czyli łatwe do odnalezienia, dostępne, kompatybilne i możliwe do ponownego wykorzystania. Zapewnienie 
identyfikowalności i trwałego odwołania do wyników badań jest kluczowe, zwłaszcza w projektach naukowych 
i programistycznych.

W tym kontekście powstało wiele archiwów jak np. Zenodo prowadzony przez CERN, który umożliwia archiwizację kodu, 
danych i 
dokumentacji z nadaniem im DOI (Digital Object Identifier). Dzięki temu zasoby stają się łatwo cytowalne i trwale 
dostępne, nawet wtedy, gdy zmienia się repozytorium lub jego struktura (zob 
[tu](https://www.software.ac.uk/blog/making-code-citable-zenodo-and-github)).

Powiązanie Zenodo z wydaniami (releases) w systemie Git pozwala na tworzenie stabilnych, jednoznacznie oznaczonych 
wersji projektu. Każdy release może zostać automatycznie zarchiwizowany w Zenodo i przypisany do niego DOI, co 
zapewnia spójność między konkretną wersją kodu, wynikami analiz oraz publikacjami naukowymi.

W praktyce oznacza to przejrzystość, replikowalność oraz profesjonalne podejście do udostępniania oprogramowania 
i danych zgodnie z zasadami FAIR.

___

### Zadanie 4 (Tworzenie tagów i Release)
Oznacz stabilną wersję projektu tagiem i utwórz Release.


#### Rozwiązanie

1. Dopilnuj że masz najnowszą wersję master:

        git switch master
        git pull

2. Utwórz tag:

        git tag -a v1.0.0 -m "Pierwsze stabilne wydanie"
        git push origin v1.0.0

3. Na głównej stronie po prawej poniżej *About*, jest *Releases*. Pojawiła się tam teraz informacja - **1 tags**
   (można kliknąć i obejrzeć). Klikamy **Releases → Create a new Release**.
   - wybierz tag v1.0.0
   - dodaj opis zmian
   - **Publish release**

---

### Zadanie 5 (Hotfix z wydanej wersji)
Napraw krytyczny błąd bez ingerowania w nowy rozwój projektu.

#### Uwaga
Tutaj zakładamy, że zauważyliśmy błąd, który warto poprawić na obecnie działającej wersji. 
Hotfix branch zaczynamy z tagu, a nie z develop.

#### Rozwiązanie

Hotfix zawsze zaczyna się od ostatniego wydanego tagu, np. v1.0.0

        git switch -c hotfix/null-pointer v1.0.0

Napraw błąd. Zmień coś w pliku repozytorium.

        git commit -am "Fix null pointer in JSON export"

Po naprawie błędu scalamy hotfix do master, bo tam znajdują się stabilne wydania.

        git switch master
        git merge --no-ff hotfix/null-pointer -m "Merge hotfix for null pointer"
        git tag -a v1.0.1 -m "Hotfix for null pointer"
        git push origin master v1.0.1

I mamy nasze wydanie. Na stronie projektu jeśli klikniemy **Releases** i przełączymy widok u góry strony 
z *Releases* na **Tags** v1.0.1 będzie już widoczna.

Niezależnie w naszym projekcie był rozwijany branch develop (np. nad przyszłą wersją 1.1.0), więc nie zawiera on hotfixa. 
A nie chcemy, aby kolejny release ponownie wprowadził ten sam błąd. Zatem uaktualniamy istniejące branch-e.

        git switch develop
        git merge hotfix/null-pointer
        git push


<hr style="height:7px; border:none; background-color:currentColor;">

Dodatkowe informacje można znaleźć [tu](https://gitlab.uw.edu.pl/python-tools/continous-integration).

### Zadanie 6 (CI – uruchamianie testów)
Skonfiguruj pipeline CI uruchamiający testy dla każdego push lub PR.

#### Wskazówka
- GitHub Actions → workflow pliki w `.github/workflows/*.yml`
- GitLab CI → `.gitlab-ci.yml`

#### Rozwiązanie – GitHub Actions

Plik: `.github/workflows/tests.yml`

        name: Tests
        on: [push, pull_request]
        jobs:
          test:
            runs-on: ubuntu-latest
            steps:
              - uses: actions/checkout@v4
              - uses: actions/setup-python@v5
                with:
                  python-version: "3.12"
              - run: pip install pytest
              - run: pytest

Po wypchnięciu commitów → GitHub uruchomi workflow.

#### Rozwiązanie - krok po kroku

1. Załóżmy że w naszym `app.py` mamy taki kod:
```python
def add(a, b):
    return a + b

if __name__ == "__main__":
    print(add(2, 3))
```

2. Tworzymy test `test_app.py`:
```python
from app import add

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
```

3. Uruchamiamy test lokalnie (może być potrzebne np uruchomienie w terminalu wpierw `pip install pytest`)
4. Tworzymy katalog worflows, np wpisując w terminalu:
```bash
mkdir -p .github/workflows
```
5. Tworzymy plik `tests.yaml` o treści:
```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - run: pip install pytest

      - run: pytest
```
i umiesczamy go w ścieżce `.github/workflows/tests.yml`

6. Korzysatmy:
```bash
git commit -am "add tests, CI pipeline" 
git push origin master
```

Efekty naszych działań możemy obejrzeć w zakładce Actions.
![ci1](ci1.png)
Klikamy w workflow `add tests, CI pipeline`, klikamy w `test`, klikamy w `run pytest`. Możemy tu obejrzeć logi
pytest-a.
![ci2](ci2.png)
Klikając u góry po prawej możemy ponownie uruchomić testy `re-run all jobs.`

---

### Zadanie 7 (CI tylko dla PR do master) 
Ogranicz testy tylko do PR kierowanych do master.

#### Rozwiązanie

        on:
          pull_request:
            branches: [ "master" ]

---

### Zadanie 8 (Budowanie artefaktów wydania na tagach)
Skonfiguruj CI tak, aby budował artefakty (np. paczki, archiwa) **tylko wtedy**, gdy wypchnięty commit posiada tag wersji.

#### Wskazówka
- GitHub: akcja `upload-artifact`
- GitLab: `artifacts` w `.gitlab-ci.yml`
- To oddzielny job/triggery dla tagów

#### Rozwiązanie – GitHub Actions

Stwórz nowy workflow:

Plik `.github/workflows/release.yml`

        name: Release build
        on:
          push:
            tags:
              - "v*"

        jobs:
          build:
            runs-on: ubuntu-latest
            steps:
              - uses: actions/checkout@v4
              - uses: actions/setup-python@v5
                with:
                  python-version: "3.12"
              - run: |
                  mkdir dist
                  echo "Dummy build for $GITHUB_REF" > dist/README.txt
              - uses: actions/upload-artifact@v4
                with:
                  name: release-dist
                  path: dist/

#### Wypchnięcie workflow

        git add .github/workflows/release.yml
        git commit -m "Add release build workflow"
        git push

#### Tworzenie wydania

        git tag -a v1.2.3 -m "Release 1.2.3"
        git push origin v1.2.3

#### Efekt
- CI uruchamia job
- build tworzy artefakt
- w interfejsie Actions → Arifacts → release-dist.zip

<hr style="height:7px; border:none; background-color:currentColor;">

### Zadanie 9 (Znajdowanie regresji – git bisect)
Zidentyfikuj commit, który zepsuł działającą funkcję.

#### Wskazówka
Bisect używa wyszukiwania binarnego po historii commitów.

#### Rozwiązanie

        git bisect start
        git bisect bad
        git bisect good <hash_poprawnej_wersji>
        pytest
        git bisect reset

Wynik wskaże pierwszy zły commit.


<hr style="height:7px; border:none; background-color:currentColor;">

#### Uwaga A &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **Mam już projekt, chcę dodać istniejące już wirtualne środowisko.**  
a) jeśli chcę stworzyć zupełnie nowe środowisko ręcznie w katalogu projektu to
klikam terminal (lewa strona na dole) i wpisuje np. `python -m venv recznie`.  
Oczywiście ten punkt można wyklikać w PyCharmie, ale chcemy mieć istniejące wirtualne środowisko 
zgodnie z naszym zadaniem.  
b) PyCharm->Settings->NazwaProjektu->Python Interpreter->Add Interpreter->Add local interpreter  
Zaznaczamy opcję *select existing* i w *Python path* wpisujemy (lub wyklikujemy)
<ścieżka>/recznie/bin/python (Linux, MacOS) lub <ścieżka>\venv\Scripts\python.exe (Windows)  
c) jeśli wykonywaliśmy punkt a) to w dolnej części PyCharma zamykamy terminal; 
po (ponownym) otwarciu w terminalu na początku linii powinno być widoczne że jesteśmy w naszym środowisku 
(`(recznie)`).

---

#### Uwaga B &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **Mam już projekt lokalnie, na którym pracuję. Chcę zacząć korzystać z git-a.**  
Zobacz zadanie 1 punkty 1 i 2.

---

#### Uwaga C.1 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Jak korzystać z git-a w terminalu PyCharm? Problemy z autentykacją.**  
W zadaniu 1, po wpisaniu w konsloli PyCharma `git push -u origin master` możemy dostać:
```bash
Username for 'https://github.com': wpisuje
Password for 'https://wpisuje@github.com': 
remote: Invalid username or token. Password authentication is not supported for Git operations.
```
Przejdź do **GitHub**: **Settings → Developer settings → Personal access tokens → Fine-grained tokens**  
Utwórz nowy **token**:  
- w **only select repositories** możesz ustawić, których repozytoriów ma dotyczyć
- w **Add permissions** zaznaczamy **contents**; w oknie poniżej pojawi się *Contents* i *Metadata*, dla *Contents*
zmieniamy Access na **read and write**; 
- Aby rozwiązać zadanie 6 powinniśmy dodać jeszcze **Workflow**, pojawi się ustawiony na *read and write*
- Tworzymy token;
- **Skopiuj token** (i trzymaj gdzieś bezpiecznie)  
Następnie w terminalu:
```bash
Username for 'https://github.com': wpisuje
Password for 'https://wpisuje@github.com': (tu wklej TOKEN)
```
---
#### Uwaga C.2 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Jak połączyć PyCharm z GitHub?**
Nie chcemy za każdym razem kopiować i wklejać tokena. Co wtedy?  
Rekomendowana metoda: logowanie przez PyCharm (GUI)  
-Otwórz **PyCharm**
-Przejdź do **File → Settings → Version Control → GitHub**  
-Kliknij **Add account**  
-Wybierz **Log in with GitHub (OAuth)** (PyCharm otworzy okno przeglądarki do autoryzacji)  
-**Gotowe — PyCharm zapisze bezpieczny token dostępu**  

Po tym jeśli będziemy wykonywać akcje przez GUI, nie będzie wymagane dodatkowe logowanie:  
-**GUI → Git → Push** działa automatycznie

---

#### Uwaga C.3 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Resetowanie tokena. 
Uruchom:
```bash
git credential-osxkeychain erase
host=github.com
protocol=https
```
Przy następnej akcji typu `git push origin master` zapyta znów o usera i hasło (jako hasło podajemy TOKEN 
z poprawionymi uprawnieniami).