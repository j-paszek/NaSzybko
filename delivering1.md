# Część A — Tworzenie paczek PIP dla narzędzi bioinformatycznych

W bioinformatyce bardzo szybko kończy się „skryptologia”: na początku mamy kilka prostych funkcji do 
przetwarzania danych, potem dochodzą kolejne moduły, poprawki, 
testy, wersjonowanie oraz potrzeba uruchamiania kodu w różnych środowiskach 
(lokalnie, na klastrze obliczeniowym, w kontenerach). 
W pewnym momencie luźne pliki `.py` przestają być wystarczające i konieczne staje się uporządkowanie 
kodu w postaci **paczki Pythona**.

Paczka instalowalna przez `pip` daje wiele kluczowych korzyści:
- powtarzalną i jednoznaczną instalację kodu,
- jawnie zdefiniowane zależności,
- metadane projektu (nazwa, wersja, autor, licencja),
- możliwość dystrybucji w formie `wheel`/`sdist`,
- łatwą integrację z innymi narzędziami.

W kolejnych częściach zajęć ta sama paczka będzie:
- wykorzystywana jako **narzędzie wiersza poleceń (CLI)** oparte o `argparse`,
- używana jako komponent w **workflow Snakemake**,
- uruchamiana w sposób powtarzalny także w środowiskach **HPC**.

---

### Zadanie 1 (skrypt.py -> paczka)
Masz pojedynczy skrypt `biohello.py`, który zawiera jedną funkcję.
Twoim zadaniem jest tak go „opakować”, aby:

- dało się wykonać:
  `pip install .`
- po instalacji dało się napisać:
  `import biohello`
- i wywołać funkcję z tego modułu.

---

#### 1. Utwórz katalog projektu

Polecenia:
1) mkdir biohello  
2) cd biohello

Struktura na tym etapie:
biohello/

---

#### 2. Utwórz prosty skrypt Pythona

Plik: `biohello.py`

Zawartość pliku:

    def hello(name: str) -> str:
        return f"Hello, {name}!"

    if __name__ == "__main__":
        print(hello("Bioinformatics"))

Na tym etapie to jest **zwykły plik `.py`**.

---

#### 3. Dodaj minimalny plik `pyproject.toml`

To jest **kluczowy moment** — bez tego `pip` nie wie, jak zainstalować projekt.

Plik: `pyproject.toml`

Zawartość pliku:

    [build-system]
    requires = ["setuptools"]
    build-backend = "setuptools.build_meta"

    [project]
    name = "biohello"
    version = "0.0.1"

Komentarz:
- `setuptools` wystarczy do najprostszego przypadku,
- nie podajemy jeszcze autorów, opisu, zależności itd.

---

#### 4. Zainstaluj paczkę lokalnie

Najlepiej w środowisku wirtualnym.

Polecenia (przykład):

1) python -m venv .venv  
2) aktywuj środowisko:  
   - Windows: .venv\Scripts\activate  
   - macOS/Linux: source .venv/bin/activate  
3) pip install .

Jeśli instalacja przebiegła bez błędów — **masz paczkę**.

---

#### 5. Sprawdź, że paczka faktycznie działa

Uruchom Python:
python

I wpisz:

    import biohello
    biohello.hello("Student")

Powinieneś zobaczyć:
'Hello, Student!'

To oznacza, że:
- plik `biohello.py` został zainstalowany jako **moduł Pythona**,
- `pip install .` faktycznie coś zrobił.

Obejrzyj:

    .venv/lib/pythonX.Y/site-packages/

---

#### Co tu się naprawdę wydarzyło? (intuicja)

- `pip install .`:
  - odczytał `pyproject.toml`,
  - użył `setuptools`,
  - skopiował `biohello.py` do środowiska Pythona.
- Od tej chwili `biohello` jest traktowany jak każda inna paczka (np. `numpy`).

Nie ma tu jeszcze:
- kontroli wersji w praktyce,
- testów,
- zależności,
- porządnej struktury.

Ale **mechanizm jest dokładnie ten sam**, który działa w dużych projektach.

---

### Od paczki PIP do programu uruchamianego z terminala (console scripts)
W poprzednim zadaniu nauczyliśmy się, jak zamienić pojedynczy plik `.py` w **instalowalną paczkę**, 
którą można importować w Pythonie.  
W praktyce bioinformatycznej bardzo często **nie chcemy używać paczki jak biblioteki (`import …`)**, 
lecz jak **narzędzia wiersza poleceń**, np.:

    biohello <parametry>

zamiast:

    python biohello.py <parametry>

To ćwiczenie pokazuje **najprostszy możliwy mechanizm**, który pozwala na takie wywołanie, 
bez używania jeszcze `argparse` (to będzie osobna część zajęć).

---

### Zadanie 2

Rozszerz paczkę z zadania 1 (`biohello`) tak, aby:

- po instalacji paczki można było uruchomić w terminalu:

      biohello Jarek

- program wypisał na ekran:

      hi Jarek

- wywołanie **nie używało** polecenia `python biohello.py`.

---

### Rozwiązanie krok po kroku

#### 1. Zmodyfikuj plik `biohello.py`

Dodajemy funkcję `main()`, która będzie punktem wejścia programu.

Plik: `biohello.py`

    import sys

    def hello(name: str) -> str:
        return f"hi {name}"

    def main() -> None:
        if len(sys.argv) < 2:
            print("Użycie: biohello IMIĘ")
            sys.exit(1)

        name = sys.argv[1]
        print(hello(name))

    if __name__ == "__main__":
        main()

Komentarz:
- `main()` jest funkcją startową programu,
- `sys.argv` zawiera argumenty przekazane z linii poleceń,
- plik nadal może być uruchamiany jako zwykły skrypt Pythona.

---

#### 2. Dodaj definicję komendy terminalowej w `pyproject.toml`

To jest **kluczowy krok** tego ćwiczenia.

Otwórz `pyproject.toml` i dodaj sekcję:

    [project.scripts]
    biohello = "biohello:main"

Pełna minimalna zawartość `pyproject.toml`:

    [build-system]
    requires = ["setuptools"]
    build-backend = "setuptools.build_meta"

    [project]
    name = "biohello"
    version = "0.0.2"

    [project.scripts]
    biohello = "biohello:main"

Znaczenie zapisu:
- `biohello` — nazwa komendy, którą wpisujesz w terminalu,
- `biohello:main` — moduł `biohello`, funkcja `main()`.

---

#### 3. Zainstaluj paczkę ponownie

Zmiany w `pyproject.toml` **zawsze wymagają reinstalacji** paczki.

Wykonaj:

    pip uninstall -y biohello
    pip install .

---

#### 4. Sprawdź działanie programu

W terminalu wpisz:

    biohello Jarek

Oczekiwany wynik:

    hi Jarek

Jeżeli tak się dzieje, oznacza to, że:
- `pip` utworzył nową komendę systemową,
- komenda ta uruchamia funkcję `main()` z Twojej paczki.

---

#### Co tu się naprawdę wydarzyło? (intuicja)

Podczas `pip install .`:

- zarejestrowany został **console script**,
- w katalogu środowiska (`bin/` lub `Scripts/`) powstał mały plik uruchamialny,
- plik ten wywołuje Pythona i uruchamia `biohello.main()`.

To jest oficjalny mechanizm Pythona, używany m.in. przez:
- `pip`,
- `pytest`,
- `snakemake`,
- `black`,
- wiele narzędzi bioinformatycznych.

---

### „Wydanie” paczki PIP w repozytorium GitHub (najprostszy możliwy sposób)

W tym ćwiczeniu jeszcze **nie korzystamy z Dockera, CI ani automatyzacji**.  
Celem jest bardzo prosta, ręczna odpowiedź na pytanie:

> „Mam paczkę Pythona w repozytorium.  
> Jak *konkretnie* wydać jej wersję tak, żeby istniał **jeden, nazwany artefakt paczki**?”

To jest **najbardziej podstawowe „wydanie paczki”**, jakie można zrobić — ręcznie, świadomie, bez magii.

---

### Co rozumiemy tutaj przez „wydanie paczki”?

W tym ćwiczeniu:

- **wydanie paczki** =  
  zbudowanie plików dystrybucyjnych i umieszczenie ich w repozytorium jako oficjalna wersja,
- nie publikujemy niczego do globalnego internetu,
- paczka jest „wydana”, bo:
  - ma numer wersji,
  - ma gotowy plik `.whl`,
  - można ją zainstalować z tego pliku.

---

### Zadanie 3 — Ręczne wydanie paczki `biohello`

Dla swojej paczki `biohello`:

- zbuduj pliki dystrybucyjne,
- powiąż je z wersją projektu,
- umieść je w repozytorium GitHub jako **oficjalne wydanie paczki**.

---

### Rozwiązanie krok po kroku

#### 1. Upewnij się, że wersja paczki jest ustawiona

W pliku `pyproject.toml`:

    [project]
    name = "biohello"
    version = "0.0.2"

To jest **wersja paczki**, którą właśnie wydajesz.

---

#### 2. Zainstaluj narzędzie do budowania paczek

W środowisku wirtualnym:

    pip install -U build

---

#### 3. Zbuduj paczkę

W katalogu projektu wykonaj:

    python -m build

Po tej komendzie powinien powstać katalog:

    dist/

z plikami typu:

- `biohello-0.0.2-py3-none-any.whl`
- `biohello-0.0.2.tar.gz`

To są **konkretne, gotowe paczki**.

---

#### 4. Sprawdź, że paczka faktycznie działa

Zainstaluj ją **z pliku**, nie z kodu:

    pip uninstall -y biohello
    pip install dist/biohello-0.0.2-py3-none-any.whl

Sprawdź:

    biohello Jarek

Jeśli działa — masz poprawnie zbudowaną paczkę.

---

#### 5. Dodaj pliki paczki do repozytorium GitHub

Teraz wykonujemy **najprostszą możliwą formę „wydania”**:

1. Wejdź do repozytorium na GitHubie.
2. Przejdź do sekcji **Releases**.
3. Utwórz nowe wydanie (bez automatyzacji).
> Releases -> Draft a new release
4. Dołącz pliki z katalogu `dist/`:
   - `.whl`
   - `.tar.gz`
5. Opisz wydanie jednym zdaniem, np.:
> Pierwsze wydanie paczki biohello w wersji 0.0.2

W tym momencie:
- paczka ma **konkretną wersję**,
- istnieje **konkretny plik do instalacji**,
- repozytorium zawiera **oficjalny artefakt**.

#### 6. Instalacja paczki bezpośrednio z GitHuba i uruchomienie programu

W tym momencie:
- paczka ma **konkretną wersję**,
- istnieje **konkretny plik do instalacji** (plik `.whl`),
- repozytorium zawiera **oficjalny artefakt paczki**.

Poniżej pokazano, jak **praktycznie użyć wydanej paczki**, bez klonowania repozytorium i bez budowania jej lokalnie.

---

#### 6.1 Pobranie paczki z GitHuba

1. Wejdź na stronę repozytorium na GitHubie.
2. Przejdź do zakładki *Releases*.
3. Pobierz plik paczki w formacie wheel, np.:

    biohello-0.0.2-py3-none-any.whl

Jest to gotowy artefakt, który można bezpośrednio zainstalować.

---

#### 6.2 Instalacja paczki z pliku wheel

W terminalu (najlepiej w środowisku wirtualnym) przejdź do katalogu z pobranym plikiem i wykonaj polecenie:

    pip install biohello-0.0.2-py3-none-any.whl

Po tej operacji:
- paczka jest zainstalowana w środowisku Pythona,
- instalowana jest **dokładnie ta wersja**, która została wydana,
- nie jest używany kod źródłowy z repozytorium.

---

#### 6.3 Uruchomienie programu po instalacji

Jeżeli paczka zawiera komendę terminalową (console script), można ją teraz uruchomić bezpośrednio:

    biohello Jarek

Oczekiwany wynik działania programu:

    hi Jarek

---

#### 6.4 Dlaczego to podejście jest istotne?

- instalacja z pliku wheel jest **powtarzalna i jednoznaczna**,
- ten sam plik paczki może być:
  - użyty lokalnie,
  - przekazany innemu użytkownikowi,
  - skopiowany na klaster HPC,
  - wykorzystany w workflow Snakemake,
- zawsze wiadomo, **jakiej wersji narzędzia użyto**.

Jest to najprostsza, ręczna forma „wydania paczki”, która w kolejnych etapach zajęć zostanie zastąpiona automatyzacją.

---

### Temat paczki

Budujemy paczkę **bioseqkit**, która realizuje prostą, ale realistyczną operację bioinformatyczną:

- wczytuje sekwencje z pliku FASTA,
- oblicza GC-content,
- zwraca wyniki jako struktury danych (bez CLI).

---

### Zadanie 4 — Paczka `bioseqkit`
Paczka ma dostarczać:
- funkcję `read_fasta(path)` → lista `(id, sequence)`,
- funkcję `gc_content(sequence)` → liczba z zakresu `[0, 1]`,
- funkcję `gc_for_fasta(path)` → lista `(id, gc)`.

Na tym etapie:
- nie wypisujemy wyników do plików,
- nie parsujemy argumentów,
- nie robimy workflow.

---

#### Krok 1 — Utworzenie projektu i środowiska

1. Utwórz katalog projektu:

    mkdir bioseqkit  
    cd bioseqkit

2. Utwórz środowisko wirtualne i je aktywuj:

    python -m venv .venv  
    (Windows) .venv\Scripts\activate  
    (Linux/macOS) source .venv/bin/activate

3. Zaktualizuj narzędzia:

    pip install -U pip setuptools

---

#### Krok 2 — Zbudowanie struktury `src layout`

Utwórz strukturę katalogów:

    bioseqkit/
    ├── pyproject.toml
    ├── README.md
    ├── src/
    │   └── bioseqkit/
    │       ├── __init__.py
    │       └── core.py
    └── tests/
        └── test_core.py

Kluczowa obserwacja:
- **kod produkcyjny znajduje się w `src/`**,
- katalog główny projektu nie jest importowalny.

---

#### Krok 3 — Implementacja logiki analizy

Plik: `src/bioseqkit/core.py`

    def read_fasta(path: str) -> list[tuple[str, str]]:
        records = []
        header = None
        seq_chunks = []

        with open(path, "r", encoding="utf-8") as f:
            for line in f:
                line = line.strip()
                if not line:
                    continue
                if line.startswith(">"):
                    if header is not None:
                        records.append((header, "".join(seq_chunks)))
                    header = line[1:].strip()
                    seq_chunks = []
                else:
                    seq_chunks.append(line)

            if header is not None:
                records.append((header, "".join(seq_chunks)))

        return records


    def gc_content(seq: str) -> float:
        if not seq:
            return 0.0
        seq = seq.upper()
        gc = sum(1 for c in seq if c in ("G", "C"))
        return gc / len(seq)


    def gc_for_fasta(path: str) -> list[tuple[str, float]]:
        results = []
        for seq_id, seq in read_fasta(path):
            results.append((seq_id, gc_content(seq)))
        return results

---

#### Krok 4 — Eksport publicznego API paczki

Plik: `src/bioseqkit/__init__.py`

    from .core import read_fasta, gc_content, gc_for_fasta

    __all__ = ["read_fasta", "gc_content", "gc_for_fasta"]

To definiuje, **co jest oficjalnym API paczki**.

---

#### Krok 5 — Konfiguracja `pyproject.toml`

Plik: `pyproject.toml`

    [build-system]
    requires = ["setuptools>=68"]
    build-backend = "setuptools.build_meta"

    [project]
    name = "bioseqkit"
    version = "0.1.0"
    description = "Proste narzędzia do analizy sekwencji FASTA"
    requires-python = ">=3.10"

    [tool.setuptools]
    package-dir = {"" = "src"}

    [tool.setuptools.packages.find]
    where = ["src"]

---

#### Krok 6 — Instalacja developerska paczki

Zainstaluj paczkę w trybie developerskim:

    pip install -e .

Sprawdź w Pythonie:

    python
    >>> from bioseqkit import gc_content
    >>> gc_content("ACGTGC")

Zmiany w kodzie będą od razu widoczne bez reinstalacji.

---

#### Krok 7 — Dodanie testów jednostkowych

Plik: `tests/test_core.py`

    from bioseqkit import gc_content, gc_for_fasta


    def test_gc_content_basic():
        assert abs(gc_content("GGCC") - 1.0) < 1e-9
        assert abs(gc_content("AATT") - 0.0) < 1e-9


    def test_gc_for_fasta(tmp_path):
        fasta = tmp_path / "test.fasta"
        fasta.write_text(
            ">seq1\nGGCC\n>seq2\nAATT\n",
            encoding="utf-8"
        )

        result = gc_for_fasta(str(fasta))
        assert result == [("seq1", 1.0), ("seq2", 0.0)]

Zainstaluj pytest i uruchom testy:

    pip install pytest  
    pytest

---

#### Krok 8 — Interpretacja wyników

Jeżeli:
- paczka instaluje się poprawnie,
- testy przechodzą,
- kod jest czytelny i podzielony logicznie,

to paczka jest:
- poprawnie zaprojektowana,
- gotowa do użycia przez CLI,
- gotowa do użycia w workflow.

<hr style="height:7px; border:none; background-color:currentColor;">

# Część B — Interfejs wiersza poleceń z użyciem biblioteki argparse

Biblioteka `argparse` jest częścią standardowej biblioteki Pythona i służy do tworzenia **programów uruchamianych z wiersza poleceń**, które przyjmują argumenty i opcje w sposób uporządkowany, czytelny i bezpieczny. W praktyce bioinformatycznej `argparse` jest jednym z podstawowych narzędzi, ponieważ większość analiz uruchamiana jest:
- z terminala,
- w skryptach bashowych,
- w workflowach (np. Snakemake),
- na klastrach HPC.

W porównaniu do ręcznego użycia `sys.argv`, `argparse` zapewnia:
- automatyczne generowanie pomocy (`--help`),
- sprawdzanie poprawności argumentów,
- jasną definicję argumentów pozycyjnych i opcjonalnych,
- spójny interfejs użytkownika.

Najważniejsze elementy biblioteki `argparse` to:
- `ArgumentParser` — obiekt opisujący całe CLI,
- argumenty pozycyjne — wymagane dane wejściowe (np. plik FASTA),
- argumenty opcjonalne — parametry sterujące działaniem programu (np. `--output`),
- automatyczna obsługa błędów i komunikatów dla użytkownika.

W tej części wykorzystamy paczkę `bioseqkit` zbudowaną w A1 i dodamy do niej **czytelny interfejs CLI**, bez zmiany logiki analizy.

---

### Zadanie B — Program `bioseqkit gc` oparty o argparse

Rozszerz paczkę `bioseqkit` tak, aby można było uruchomić ją z terminala w postaci:

    bioseqkit gc input.fasta

Program powinien:
- przyjąć ścieżkę do pliku FASTA jako argument,
- obliczyć GC-content dla każdej sekwencji,
- wypisać wynik na standardowe wyjście w formacie:

    sequence_id<TAB>gc_content

Logika obliczeń ma pochodzić z funkcji zdefiniowanych w części A1 (`gc_for_fasta`).

---

### Rozwiązanie krok po kroku

#### Krok 1 — Dodanie modułu CLI

W katalogu paczki utwórz plik:

    src/bioseqkit/cli.py

---

#### Krok 2 — Implementacja CLI z argparse

Plik: `src/bioseqkit/cli.py`

    import argparse
    from bioseqkit.core import gc_for_fasta


    def main() -> None:
        parser = argparse.ArgumentParser(
            prog="bioseqkit",
            description="Proste narzędzia do analizy sekwencji FASTA"
        )

        subparsers = parser.add_subparsers(dest="command", required=True)

        gc_parser = subparsers.add_parser(
            "gc",
            help="Oblicz GC-content dla sekwencji FASTA"
        )
        gc_parser.add_argument(
            "fasta",
            help="Plik wejściowy w formacie FASTA"
        )

        args = parser.parse_args()

        if args.command == "gc":
            results = gc_for_fasta(args.fasta)
            for seq_id, gc in results:
                print(f"{seq_id}\t{gc:.3f}")

---

#### Krok 3 — Podpięcie CLI jako komendy terminalowej

Otwórz plik `pyproject.toml` i dodaj sekcję:

    [project.scripts]
    bioseqkit = "bioseqkit.cli:main"

To powoduje, że po instalacji paczki pojawi się komenda `bioseqkit`.

---

#### Krok 4 — Reinstalacja paczki

Ponieważ zmieniliśmy konfigurację CLI, wykonaj ponownie instalację developerską:

    pip install -e .

---

#### Krok 5 — Uruchomienie programu

Sprawdź działanie programu:

    bioseqkit gc example.fasta

Oczekiwany wynik (przykład):

    seq1    0.667
    seq2    0.421

---

#### Krok 6 — Sprawdzenie pomocy programu

Sprawdź automatycznie wygenerowaną pomoc:

    bioseqkit --help

oraz pomoc dla subkomendy:

    bioseqkit gc --help

Zwróć uwagę, że:
- `argparse` sam generuje opisy argumentów,
- błędne wywołania skutkują czytelnym komunikatem,
- CLI jest gotowe do użycia w skryptach i workflowach.

<hr style="height:7px; border:none; background-color:currentColor;">

# Część C — Podstawowy workflow Snakemake wykorzystujący paczkę `bioseqkit`

Snakemake jest systemem do definiowania **workflowów obliczeniowych**, w których opisujemy:
- jakie pliki mają powstać,
- z jakich plików wejściowych,
- jakimi poleceniami.

Kluczową ideą Snakemake jest to, że **nie opisujemy kolejności poleceń**, lecz **zależności między plikami**.  
Snakemake sam decyduje:
- co trzeba uruchomić,
- w jakiej kolejności,
- co jest już aktualne, a co wymaga przeliczenia.

W bioinformatyce Snakemake jest powszechnie używany do:
- analiz sekwencji,
- pipeline’ów RNA-seq i WGS,
- kontroli powtarzalności analiz,
- uruchamiania narzędzi CLI na HPC.

W tej części użyjemy **narzędzia CLI z części B (`bioseqkit gc`)** jako elementu workflow.

---

### Zadanie C — Workflow GC-content dla pliku FASTA

Zbuduj prosty workflow Snakemake, który:
- przyjmuje plik FASTA jako dane wejściowe,
- uruchamia narzędzie `bioseqkit gc`,
- zapisuje wynik do pliku TSV,
- umożliwia ponowne uruchomienie bez ponownego liczenia, jeśli dane się nie zmieniły.

Workflow ma składać się **z jednej reguły**, bez równoległości i bez zaawansowanych funkcji.

---

#### Struktura katalogów

Struktura projektu powinna wyglądać następująco:

    workflow/
    ├── Snakefile
    ├── data/
    │   └── example.fasta
    └── results/

Plik `example.fasta` może zawierać na przykład:

    >seq1
    ACGTGCGC
    >seq2
    AATTATTA

---

### Rozwiązanie krok po kroku

#### Krok 1 — Przygotowanie środowiska

Upewnij się, że:
- paczka `bioseqkit` jest zainstalowana (najlepiej przez `pip install -e .`),
- Snakemake jest dostępny w środowisku (zainstalowany przez `pip`).

---

#### Krok 2 — Zdefiniowanie pliku wynikowego

W Snakemake zaczynamy od określenia **co ma powstać**.

Plikiem wynikowym workflow będzie:

    results/gc.tsv

---

#### Krok 3 — Utworzenie pliku `Snakefile`

Plik `Snakefile` powinien zawierać:

    rule all:
        input:
            "results/gc.tsv"


    rule gc_content:
        input:
            fasta="data/example.fasta"
        output:
            tsv="results/gc.tsv"
        shell:
            "bioseqkit gc {input.fasta} > {output.tsv}"

Znaczenie reguł:
- `rule all` określa cel całego workflow,
- `rule gc_content` opisuje:
  - plik wejściowy,
  - plik wyjściowy,
  - polecenie generujące wynik.

---

#### Krok 4 — Uruchomienie workflow

W katalogu `workflow/` uruchom:

    snakemake -j 1

Snakemake:
- sprawdzi, czy plik wynikowy istnieje,
- jeśli nie — uruchomi regułę `gc_content`,
- zapisze wynik do pliku.

---

#### Krok 5 — Sprawdzenie powtarzalności

Uruchom Snakemake ponownie:

    snakemake -j 1

Zwróć uwagę, że:
- Snakemake **nie uruchamia ponownie** reguły,
- ponieważ plik wynikowy jest aktualny względem danych wejściowych.

To jest kluczowa cecha workflowów bioinformatycznych.

---

#### Interpretacja wyniku

Plik `results/gc.tsv` może zawierać na przykład:

    seq1    0.750
    seq2    0.000

Oznacza to, że:
- narzędzie CLI działa poprawnie,
- workflow poprawnie zarządza zależnościami,
- paczka PIP została użyta jako **czarne pudełko obliczeniowe**.

---

#### Co jest istotne w tym przykładzie?

- Snakemake **nie zna implementacji Pythona w paczce** — zna tylko komendę `bioseqkit`,
- workflow nie zależy od szczegółów implementacyjnych narzędzia,
- ten model bezpośrednio skaluje się do:
  - wielu plików wejściowych,
  - wielu reguł,
  - środowisk HPC.




