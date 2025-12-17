<hr style="height:7px; border:none; background-color:currentColor;">

W praktyce równoległość pojawia się wtedy, gdy:
- mamy wiele niezależnych danych wejściowych (np. wiele plików FASTA),
- te same operacje wykonujemy niezależnie dla każdego pliku,
- workflow może wykonać je jednocześnie, jeśli dostępne są zasoby.

Snakemake realizuje równoległość na poziomie reguł i plików, a nie wewnątrz pojedynczego programu.

---

#### Idea równoległości w Snakemake

Snakemake:
- buduje graf zależności plików,
- uruchamia jednocześnie te reguły, które nie zależą od siebie i produkują różne pliki wyjściowe.

Polecenie:

    snakemake -j N

oznacza:
„uruchom maksymalnie N zadań jednocześnie”.

---

#### Zadanie C.1 — Równoległe liczenie GC-content dla wielu plików FASTA

Rozszerz workflow tak, aby:
- analizował wiele plików FASTA,
- tworzył osobny plik wynikowy dla każdego FASTA,
- umożliwiał uruchamianie obliczeń równolegle.

---

#### Dane wejściowe

W katalogu `data/` znajdują się pliki:

    data/
    ├── sample1.fasta
    ├── sample2.fasta
    └── sample3.fasta

Każdy plik zawiera niezależne sekwencje.

---

#### Oczekiwane pliki wynikowe

Dla każdego pliku FASTA powstaje osobny wynik:

    results/
    ├── sample1.gc.tsv
    ├── sample2.gc.tsv
    └── sample3.gc.tsv

---

#### Rozwiązanie krok po kroku

##### Krok 1 — Lista próbek

W pliku `Snakefile` zdefiniuj listę próbek:

    SAMPLES = ["sample1", "sample2", "sample3"]

---

##### Krok 2 — Reguła `all`

Reguła celu workflow:

    rule all:
        input:
            expand("results/{sample}.gc.tsv", sample=SAMPLES)

Workflow jest zakończony, gdy wszystkie pliki wynikowe istnieją.

---

##### Krok 3 — Reguła przetwarzająca pojedynczy FASTA

    rule gc_content:
        input:
            fasta="data/{sample}.fasta"
        output:
            tsv="results/{sample}.gc.tsv"
        shell:
            "bioseqkit gc {input.fasta} > {output.tsv}"

Każda wartość `{sample}` generuje niezależne zadanie.

---

##### Krok 4 — Uruchomienie równoległe

Uruchom workflow z dwoma zadaniami równolegle:

    snakemake -j 2

Snakemake:
- utworzy trzy niezależne zadania,
- uruchomi maksymalnie dwa jednocześnie,
- trzecie uruchomi po zwolnieniu zasobów.

---

#### Co warto zaobserwować

- zadania pojawiają się i wykonują równolegle,
- kolejność przetwarzania próbek nie ma znaczenia,
- workflow kończy się, gdy wszystkie pliki wynikowe zostaną utworzone,
- równoległość w Snakemake nie wymaga zmiany kodu narzędzia.

---

#### Zadanie C.2 
Jak powiedzieć Snakemake’owi, **ile zasobów** (CPU) zużywa *pojedyncze zadanie*.

##### Krok 1 — Zmodyfikuj regułę `gc_content`

W pliku `Snakefile` dodaj linię `threads:`:

    rule gc_content:
        input:
            fasta="data/{sample}.fasta"
        output:
            tsv="results/{sample}.gc.tsv"
        threads: 2
        shell:
            "bioseqkit gc {input.fasta} > {output.tsv}"

Ta linia mówi:
> „To zadanie zużywa 2 wątki CPU”.

---

##### Krok 2 — Uruchom workflow z ograniczeniem globalnym

Uruchom Snakemake z łącznym limitem 4 wątków:

    snakemake -j 4

Snakemake teraz:
- **nie uruchomi więcej niż 2 zadań jednocześnie**,
- bo każde z nich deklaruje `threads: 2`,
- mimo że `-j 4` pozwalałby na więcej zadań.

Uwaga, wykonując to zadanie, po poprzednich snakemake odpowie `Nothing to be done`.
Jeśli chcemy wymusić działanie użyjmy:

    snakemake -j 4 --forcerun gc_content

---

#### Co warto zaobserwować?

- Snakemake sam ogranicza równoległość,
- nie trzeba ręcznie liczyć, ile zadań wolno uruchomić,
- zmiana `threads:` **nie wymaga zmiany kodu narzędzia**.

_______

#### Część C.3 — Parametryzacja workflow: plik `config.yaml`

W poprzednich zadaniach (C.1, C.2) workflow był „zaszyty na sztywno”:
- lista próbek była wpisana w `Snakefile`,
- ścieżki do danych były stałe.

To działa, ale **nie skaluje się praktycznie**.  
W realnych pipeline’ach:
- dane się zmieniają,
- liczba próbek się zmienia,
- workflow powinien być **konfigurowalny bez edycji kodu**.

Do tego służy **plik konfiguracyjny**.

---

#### Idea pliku `config.yaml`

- `Snakefile` opisuje **logikę workflow**,
- `config.yaml` opisuje **konkretne dane i parametry analizy**.

Dzięki temu:
- ten sam workflow można uruchomić na różnych zestawach danych,
- użytkownik nie musi znać Pythona ani Snakemake’a,
- zmiany danych ≠ zmiany kodu.

---

#### Zadanie C.3 

Zmodyfikuj workflow z części C.1 i C.2 tak, aby:
- lista próbek nie była wpisana w `Snakefile`,
- była wczytywana z pliku `config.yaml`,
- workflow działał dokładnie tak samo jak wcześniej.

---

#### Struktura katalogów

Rozszerzona struktura projektu:

    workflow/
    ├── Snakefile
    ├── config.yaml
    ├── data/
    │   ├── sample1.fasta
    │   ├── sample2.fasta
    │   └── sample3.fasta
    └── results/

---

#### Rozwiązanie krok po kroku

##### Krok 1 — Utwórz plik `config.yaml`

Plik: `config.yaml`

    samples:
      - sample1
      - sample2
      - sample3

To jest jedyne miejsce, w którym użytkownik definiuje,
jakie próbki mają być przetwarzane.

---

##### Krok 2 — Załaduj konfigurację w `Snakefile`

Na początku pliku `Snakefile` dodaj:

    configfile: "config.yaml"

Następnie zdefiniuj listę próbek na podstawie konfiguracji:

    SAMPLES = config["samples"]

___

##### Krok 3 — Uruchomienie workflow

Uruchom workflow jak wcześniej:

    snakemake -j 4 --forcerun gc_content

Workflow:
- wczyta listę próbek z `config.yaml`,
- automatycznie wygeneruje zadania,
- wykona je równolegle zgodnie z deklaracją `threads`.

---

#### Co warto zaobserwować?

- aby dodać nową próbkę, **nie edytujesz `Snakefile`**,
- wystarczy dopisać nazwę w `config.yaml`,
- workflow staje się **konfigurowalny i wielokrotnego użytku**,
- Ten krok oddziela **logikę pipeline’u** od **konkretnych danych**.

---

#### Kluczowa myśl

Dobry workflow:
- nie zawiera „twardych” nazw próbek,
- nie wymaga edycji kodu przy zmianie danych,
- jest sterowany przez konfigurację.
______

#### Część C.4 — Instalacja narzędzia z pliku wheel w workflow Snakemake

W praktyce bioinformatycznej bardzo często:
- pracujemy na klastrach bez dostępu do internetu,
- używamy **lokalnie zbudowanych wersji narzędzi**,
- chcemy dokładnie kontrolować, *jaki artefakt* został użyty w analizie.

Dlatego kolejnym naturalnym krokiem jest użycie **pliku wheel (`.whl`)** jako źródła instalacji.

---

#### Idea: wheel jako artefakt analizy

- plik `.whl` jest **konkretną, zamkniętą wersją narzędzia**,
- może być:
  - skopiowany do katalogu workflow,
  - przechowywany na HPC,
  - archiwizowany razem z analizą,
- Snakemake instaluje go tak samo jak każdą inną zależność.

Kluczowa zasada:
> **workflow używa dokładnie tego pliku `.whl`, który mu wskażemy**.

---

#### Zadanie C.5

Zmodyfikuj workflow tak, aby:
- paczka `bioseqkit` była instalowana **z lokalnego pliku wheel**,
- plik wheel był częścią projektu workflow,
- workflow działał bez dostępu do internetu.

---

#### Struktura katalogów

Rozszerzona struktura projektu:

    workflow/
    ├── Snakefile
    ├── config.yaml
    ├── wheels/
    │   └── bioseqkit-0.1.0-py3-none-any.whl
    ├── envs/
    │   └── bioseqkit.yaml
    ├── data/
    │   ├── sample1.fasta
    │   ├── sample2.fasta
    │   └── sample3.fasta
    └── results/

Plik wheel pochodzi z ręcznego wydania paczki.

---

#### Rozwiązanie krok po kroku

##### Krok 0
W katalogu projektu (tam, gdzie jest `pyproject.toml`) uruchom:

    python -m build

Po poprawnym wykonaniu polecenia pojawi się katalog:

    dist/

z plikami, np.:

    bioseqkit-0.1.0-py3-none-any.whl
    bioseqkit-0.1.0.tar.gz

**Interesuje nas plik `.whl`.**

Odinstaluj paczkę

    pip uninstall -y bioseqkit

Sprawdź, że komenda nie istnieje. Spróbuj:

    bioseqkit

Poprawne zachowanie:
- komenda **nie istnieje**,
- system zgłasza błąd typu *command not found*.

##### Krok 1 — Skopiuj plik wheel do workflow

Skopiuj plik:

    bioseqkit-0.1.0-py3-none-any.whl

do katalogu:

    workflow/wheels/

---

##### Krok 2 — Zmodyfikuj plik środowiska conda

Plik: `envs/bioseqkit.yaml`

    name: bioseqkit-env
    channels:
      - conda-forge
    dependencies:
      - python=3.11
      - pip

---

##### Krok 3 — Reguła w `Snakefile`

Reguła nadal wygląda tak samo:

    rule gc_content:
        input:
            fasta="data/{sample}.fasta",
            wheel="wheels/bioseqkit-0.1.0-py3-none-any.whl"
        output:
            tsv="results/{sample}.gc.tsv"
        threads: 2
        conda:
            "envs/bioseqkit.yaml"
        shell:
            """
            pip install {input.wheel}
            bioseqkit gc {input.fasta} > {output.tsv}
            """

---

##### Krok 4 — Uruchomienie workflow

Uruchom workflow:

    snakemake -j 4 --use-conda

Snakemake:
- utworzy środowisko conda,
- zainstaluje paczkę z pliku wheel,
- uruchomi workflow bez połączenia z internetem.

---

#### Co warto zaobserwować?

- workflow nie pobiera nic z zewnątrz,
- dokładnie ten sam plik `.whl` może być:
  - użyty lokalnie,
  - przeniesiony na HPC,
  - dołączony do archiwum analizy,
- zmiana wersji narzędzia = podmiana jednego pliku wheel.

---

#### Dlaczego to jest bardzo ważne?

Ten krok:
- zamyka pełny cykl **narzędzie → paczka → workflow**,
- jest standardem w analizach produkcyjnych,
- przygotowuje bezpośrednio do pracy na HPC,
- umożliwia pełną replikowalność analiz.


<hr style="height:7px; border:none; background-color:currentColor;">

