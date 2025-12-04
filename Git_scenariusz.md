# Systemy kontroli wersji na przykładzie GIT
Poznamy tutaj w praktyce systemy kontroli wersji (version control systems, VCS).

## Praca lokalnie
Pracujemy na razie w konsoli.

 *  `git init` – tworzy puste repozytorium
 *  `git status` – sprawdza status repozytorium
 *  `git add <paths>` – dodaje pliki do repozytorium
 *  `git add -u` – dodaje zmodyfikowane pliki
 *  `git add -A` – dodaje wszystkie pliki 
 *  `git rm <path>` – usuwa pliki z repozytorium
 *  `git rm --cached <paths>` – usuwa pliki z repozytorium ale przechowuje lokalnie kopie.
 *  `git commit -m "<message>"` – tworzy commit z aktualnie dodanymi plikami z `<message>` jako opisem
 *  `git log` – pokazuje historię commit-ów w obecnej gałęzi

Najprawdopodobniej git już jest zainstalowany, można to sprawdzić `git --version`.
Jeśli nie jest zainstalowany, to standardowo `sudo apt install git` lub `brew install git` (na macOS).


### Ćwiczenie 1
Podstawy pracy z VCS lokalnie. 

* 1.1 Stwórz katalog `prmgr`. Przejdź do niego, i upewnij się, że jest pusty (`mkdir`, `cd`, `ls -a`).
I, że nie jest to repozytorium git (`git status`, `git log`). Rozpocznij pracę z git-em (`git init`).
Uwaga, za pierwszym razem zostaniesz poproszony o skonfigurowanie użytkownika:
```
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

$\qquad$ Po stworzeniu repozytorium w `prmgr` obejrzyj zmiany (`ls -a`, `git status`, `git log`).

* 1.2 Stwórz plik `mgr.txt` (`echo "Ala\n ma\n kota\n ." > mgr.txt`). Obejrzyjmy ten plik (`cat mgr.txt`).
Zobaczmy co podpowiada git (`git status`). I posłuchajmy go, poleceniem `git add .` dodamy wszystkie nowe pliki.
Sytuacja się zmieniła (`git status`), ale praktycznie nic nie zrobiliśmy, wynik `git log` pozostaje bez zmian.

* 1.3 Zapamiętajmy wersję naszej pracy. (`git commit -m "pierwsza wersja"`) I obejrzyjmy (`git status`, `git log`).

* 1.4 Jednak w naszej pracy powinno być inaczej, więc ją zmieńmy (`echo "Ala\n ma\n psa\n ." > mgr.txt`). 
Zmianę zauważa git (`git status`).

* 1.5 Zapamiętajmy poprawioną wersję naszej pracy. Najpierw dodajmy zmianę (`git add .`) 
a potem zapamiętajny ze znaczącym komentarzem (`git commit -m "to ma być pies"`). Komunikat opisuje podmianę dokładnie
jednej linii:
```
[master 2dfa1f2] to ma być pies
 1 file changed, 1 insertion(+), 1 deletion(-)
```

* 1.6 Na koniec obejrzmy sobie podsumowanie:
```
commit 2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6 (HEAD -> master)
Author: j-paszek <tu mail>
Date:   Sat Nov 29 12:53:55 2025 +0100

    to ma być pies

commit 0cf64e63a83f0c82c99380f7cc3284901dcc42f8
Author: j-paszek <tu mail>
Date:   Sat Nov 29 12:47:28 2025 +0100

    pierwsza wersja
```

_______________________________

*  `git restore <path>` – cofamy zmiany wprowadzone lokalnie do poprzedniego commit-a
*  `git show <commit hash>:<path>` - wyświetla plik ze ścieżki path, w wersji zapisanej we wskazanym commit-cie 
*  `git restore --source=<commit hash> <path>` - cofamy zmiany wprowadzone lokalnie do wskazanego commit-a

### Ćwiczenie 2
Odtwarzanie poprzedniej wersji.
* 2.1 Wprowadźmy kolejną zmianę `echo "Ala\n ma\n jeża\n i\n nietoperza\n ." > mgr.txt`. Upewnijmy się, że
plik się zmienił `cat mgr.txt`. Zmiany nam się nie podobają. Aby wrócić lokalnie do ostatniego zapisu wywołujemy
`git restore mgr.txt` i upewniamy się, że mamy poprzednią wersję `cat mgr.txt`. W ten sposób cofnęliśmy
wszystkie zmiany wykonane w pliku `mgr.txt`, których nie zapisaliśmy.
* 2.2 Wprowadzamy zmianę `echo "Ela\n ma\n trzmiela\n ." > mgr.txt`. I zapisujemy `git -add .`, 
`git commit -m "wersja z owadem"`. Sprawdzamy `git log`:
```
commit 42f26bdcc432b4fbb77cdbac1ca813ff14ccbc0c (HEAD -> master)
Author: j-paszek <wycinam mail>
Date:   Sat Nov 29 14:52:36 2025 +0100

    wersja z owadem

commit 2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6
Author: j-paszek <wycinam mail>
Date:   Sat Nov 29 12:53:55 2025 +0100

    to ma być pies

commit 0cf64e63a83f0c82c99380f7cc3284901dcc42f8
Author: j-paszek <wycinam mail>
Date:   Sat Nov 29 12:47:28 2025 +0100

    pierwsza wersja
```
W bardziej skomplikowanych projektach z wieloma plikami, warto doprecyzować o zmiany w jakim pliku nam chodzi
`git log -- mgr.txt`. Załóżmy, że najnowsze zmiany nam się nie podobają, i chemy wrócić do poprzedniej wersji.
Sytuacja jest inna, bo nasze zmiany już zapisaliśmy za pomocą commit-a. Potrzebny jest nam hash interesującego nas 
commita, z wersją, do której chcemy wrócić (w naszym przykładzie to `2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6`, 
użyj własnego). 
Upewnijmy się, czy o tę wersję pliku nam chodzi:
`git show 2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6:mgr.txt`. 
To jest ta wersja, uaktualniamy werjsę roboczą w naszym katalogu:
`git restore --source=2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6 mgr.txt`.
Możemy zweryfikować zmianę `cat mgr.txt` i zapisać `git add .`, `git commit -m "znowu pies"`. 
Zastosujmy teraz `git log --graph --oneline --all`:
```
* b8340fc (HEAD -> master) znowu pies
* 42f26bd wersja z owadem
* 2dfa1f2 to ma być pies
* 0cf64e6 pierwsza wersja
```
Zauważmy, że możemy korzystać z tych krótszych wersji: `git show 42f26bd:mgr.txt`.

___________________________________

### Warto wiedzieć o:

**`git reset`**  
- Cofnia zmiany *lokalnie* i przesuwa wskaźnik `HEAD`.  
- Nie tworzy nowego commitu.  
- Może usuwać historię (`--hard`), dlatego jest potencjalnie destrukcyjne.  
- Zalecane tylko do zmian, które nie zostały jeszcze wypchnięte do zdalnego repozytorium.

**`git revert`**  
- Tworzy nowy commit odwracający skutki wskazanego commitu.  
- Nie narusza historii — jest bezpieczne w pracy zespołowej.  
- Idealne do cofania zmian, które są już publiczne.

**`git rebase`**  
- Przenosi commity z jednej gałęzi na koniec innej, przekształcając historię na liniową.  
- Modyfikuje historię commitów (przepisuje je), co jest ryzykowne po publikacji zmian.  
- Używane do uporządkowania historii przed scaleniem.

**`git restore`**  
- Przywraca pliki z historii, stagingu lub ostatniego commitu.  
- Nie zmienia gałęzi.  
- Nowoczesny i bezpieczniejszy zamiennik części funkcjonalności `git checkout`.

**`git checkout`**  
- Starsze polecenie o dwóch funkcjach: zmiana gałęzi **i** przywracanie plików.  
- Mylone przez szeroki zakres działania — obecnie zalecane głównie do zmiany gałęzi.


### Podsumowanie

- **reset** niszczy lub cofa historię lokalnie,  
- **revert** odwraca zmiany tworząc nowy commit,  
- **rebase** przepisuje historię,  
- **restore** przywraca pliki,  
- **checkout** zmienia gałęzie (i historycznie przywracał pliki, ale dziś zastępowany przez `restore`).

_____________________________

 *  `git branch <name>` – tworzy nową gałąź `<name>`
 *  `git checkout <name>` – zmienia obecną gałąź na gałąź `<name>` 
 *  `git checkout -b <name>` – tworzy nową gałąź `<name>` i zmienia na nią 
(odpowiednik wykonania kolejno: `git branch` i `git checkout`)
 *  `git switch` - nowoczesny sposób na zmianę gałęzi
 *  `git merge` – tworzy commit, który łączy zawartość dwóch gałęzi. 

###  Ćwiczenie 3
Gałęzie są naturalnym narzędziem podczas pracy nad oprogramowaniem. Przykładowo, możemy mieć gałąź główną, 
odpowiadającą wersji produkcyjnej, czyli działającej aplikacji, oraz gałąź przeznaczoną do implementacji nowej 
funkcjonalności. W międzyczasie gałąź główna może otrzymywać zmiany wynikające 
z poprawek błędów wykrytych w środowisku produkcyjnym.

Możemy sobie wyobrazić, że tworzymy gałąź do naszej pracy magisterskiej, gdzie postanowiliśmy zmienić oznaczenia w 
definicji, co oznacza przeredagowanie większości twierdzeń i dowodów. 
* 3.1 Tworzymy `git checkout -b zmiana`.  Teraz `git log --graph --oneline --all` pokaże:
```
* b8340fc (HEAD -> zmiana, master) znowu pies
* 42f26bd wersja z owadem
* 2dfa1f2 to ma być pies
* 0cf64e6 pierwsza wersja
```
* 3.2 Dodajmy zmiany. `echo "Ela\n ma\n psa\n ." > mgr.txt`, `git add .`, `git commit -m "jednak Ela"`, 
`git log --graph --oneline --all`:
```
* 7d184a3 (HEAD -> zmiana) jednak Ela
* b8340fc (master) znowu pies
* 42f26bd wersja z owadem
* 2dfa1f2 to ma być pies
* 0cf64e6 pierwsza wersja
```
* 3.3 Wprowadzamy kolejne zmiany. `echo "Ela\n ma\n trzmiela\n ." >> mgr.txt`, `git add .`, `git commit -m "trzmiel"`, 
`git log --graph --oneline --all`
* 3.4 Jednak musimy przedstawić pracę promotorowi, więc wracamy do głównej gałęzi `git switch master`
(albo `git checkout master`; `git log --graph --oneline --all`).
* 3.5 Ale musimy nanieść poprawkę, która jest niezbędna w wersji dla promotora 
`echo "Ela\n ma\n trzmiela\n ." >> mgr.txt`, `git add .`, `git commit -m "dodatek trzmiel"`. 
Jako wynik `git log --graph --oneline --all` widzimy ładne drzewko:
```
* f956e31 (HEAD -> master) dodatek trzmiel
| * 5b49053 (zmiana) trzmiel
| * 7d184a3 jednak Ela
|/  
* b8340fc znowu pies
* 42f26bd wersja z owadem
* 2dfa1f2 to ma być pies
* 0cf64e6 pierwsza wersja
```
* 3.6 Ostatecznie próbujemy przygotować spójną wersję do dalszych prac i połączyć gałęzie. 
> Uwaga, `git merge zmiana` doprowadzi nas do vim-a, aby wyjść i zapisać zmiany należy wpisać `ESC :wq`. 
> Aby przerwać niedokończonego merge-a możemy wykonać `git merge --abort`.

$\qquad$ Najszybsza opcja to zastosowanie standardowego opisu połączenia poprzez `git merge zmiana --no-edit`.


###  Ćwiczenie 4
Doprowadzimy teraz do konfliktu, który rozwiążemy.
* 4.1 Stwórzmy mniejsze repozytorium `cd`, `mkdir konflikt`, `cd konflikt`, `git init`. Przygotujmy plik
`echo "Ala\n ma\n kota\n." > test.txt`, `git add .`, `git commit -m "poczatek"`.
* 4.2 Stwórzmy gałąź `git switch -c zmiana` (obecnie preferowany sposób zamiast checkout). I zmieńmy plik
`echo "Ala\n ma\n psa\n." > test.txt`, `git add test.txt`, `git commit -m "to pies"`.
* 4.3 Wróćmy na gałąź główną `git switch master` i zmieńmy plik `echo "Ala\n ma\n chomika\n." > test.txt`, 
`git add test.txt`, `git commit -m "teraz chomik"`
* 4.4 Przy próbie połączenia będziemy mieli konflikt `git merge zmiana --no-edit`
```
Auto-merging test.txt
CONFLICT (content): Merge conflict in test.txt
Automatic merge failed; fix conflicts and then commit the result.
```
$\qquad$ który możemy obejrzeć w naszym pliku `cat test.txt ` :
```
Ala
 ma
<<<<<<< HEAD
 chomika
=======
 psa
>>>>>>> zmiana
.
```

* 4.5 Rozwiązać konflikt można przez ręczną edycję pliku (np. zastąpienie fragmentu "<<...>> zmiana" przez "psa").
Konfliktów może być wiele, zatem najwygodniej będzie korzystać z IDE jak PyCharm, czy VS Code. Można użyć też 
`git mergetool` tyle, że odnajdziemy się wtedy w `vimdiff`. Jeśli nie chcemy uczyć się komend, tylko za pomocą 
strzałek wybierać interesującą nas wersję, polecam `meld` (`brew install --cask meld`, 
`git config --global merge.tool meld`, `git config --global mergetool.prompt true`, `git mergetool`).

* 4.6 Jakkolwiek wyedytowaliśmy nasze konflikty, możemy teraz zakończyć połączenie `git add .`, `git commit -m "merge"`.
Uff, udało się `git log --graph --oneline --all`:
```
*   532867d (HEAD -> master) merge
|\  
| * 54de264 (zmiana) to pies
* | 9e63bbb teraz chomik
|/  
* 33f0c56 poczatek
```

______________________________

## Praca zdalnie
Oczywiście wszystkie polecenia można wpisywać do konsoli, jednak tę część polecamy wykonać przy pomocy ulubionego IDE. 
 
 *  `git clone` – clone a remote repository locally
 *  `git pull` – pull changes from remote and update the local branch
 *  `git fetch` – update information from remote repository
 *  `git push` – push changes to the remote repository
 
### Ćwiczenie 5
Stwórz projekt lokalnie na komputerze z istniejącego już repozytorium.

* 5.1 W PyCharm `File->Project from version control` pojawi się okno `clone repository`. Po lewej stronie będą znajdować 
się profile np. połączone konto z GitHuba. Na razie korzystamy z `repository url` i version control ustawionego na `git`.
Z istniejącego już repozytorium, np. [https://gitlab.uw.edu.pl/python-tools/git-example-repo](https://gitlab.uw.edu.pl/python-tools/git-example-repo), 
klikamy na stronie przycisk `Code` i kopiujemy adres z `Clone with https` 
(`https://gitlab.uw.edu.pl/python-tools/git-example-repo.git`) i wracając do PyCharma 
wklejamy go w pole `url` i klikamy przycisk `clone`.
* 5.2 Jeśli klikniemy w `master` w naszym nowym projekcie możemy obejrzeć że znajdujemy się w `local >master` i że są 
widoczne też `remote >origin >>master >>develop`. Czyli w repozytorium zdalnym mamy dwie gałęzie. Jeśli wybierzemy
`remote>origin>>develop>>>checkout`, to przejdziemy do nowej gałęzi. W katalogu projektu pojawi się nam plik `test.py`, 
oraz w górnym menu będzie `develop` zamiast `master`.
* 5.3 Po lewej stronie na samym dole jest guzik `version control`, tutaj w naszym projekcie będzie już on oznaczony 
jako `git`. Po kliknięciu w dolnym oknie: Po lewej możemy nawigować po gałęziach. W centrum mamy listę commitów.
Kliknijmy `sample data`. Po kliknięciu zmienia się zawartość okna po lewej. Znajdują się tam na zielono (czyli dodane 
w tym commit-cie nowe) pliki `file1.txt` i `file2.txt`. Po podwójnym kliknięciu w jeden z tych plików u góry w oknie 
pojawi się okno `Repository Diff: file2.txt`. Można tam śledzić zmiany pomiędzy plikami.
* 5.4 Na koniec wróćmy do górnego menu tam `VCS` został zastąpiony przez `Git`. Wybierając 
`Git->current file->annotate with git blame` w naszym pliku po lewej stronie pojawią się daty i autorzy zmian.
 
### Ćwiczenie 6
Stwórz repozytorium na [https://gitlab.uw.edu.pl](https://gitlab.uw.edu.pl) (lub GitHub lub ...).
Sklonuj go lokalnie, zmodyfikuj i za pomocą `git push` przekaż modyfikacje na zdalny serwer. 

### Ćwiczenie 7
Dokonaj zmian w wersji zdalnej - z innego komputera, lub prościej w interfejsie webowym zmodyfikuj plik i zakomituj
zmiany. Ten sam plik zmodyfikuj lokalnie. Przetestuj `git pull` i `git fetch`.

__________________________________
To be continued
- git pull request z np. Claude
- github wydawanie release-ów

 
## .gitignore
_Files which name starts from `.` are hidden on unix systems_

To avoid unintentionally adding files to the repository (like temporary files and local data),
a mechanism of file exclusion, based on the simple path matching to a set of expressions, was introduced. 
There are two locations where the user can put these expressions:

_Exclusions are applied only to new files. So if a file is tracked by git, then any changes in `.gitignore` will not affect it._

* `.gitignore` – will be synchronised with other computers,
* `.git/info/exclude` – apply only on local machines.

A few examples of rules:

* `*.tmp` – ignore all files with extension `.tmp`
* `__pycache__/` - ignore content of all directories named `__pycache__`
* `/__pycache__/` - ignore content of top level directory named `__pycache__` 

To keep a folder in the structure of repository, but not trace its content (e.g. data storage) you need to use two expression in `.gitignore`:

```
data/*
!data/.keep
```

as well as keep an empty file in the directory, which in this example is named `.keep`.



Here is a good generator of gitignore files, which is a good starting point in project 
[https://gitignore.io](https://gitignore.io)


### Exercise 7 
Initialize a new repository then add to it a .gitignore file which excludes `.txt` files. 
Then create the following files: `test.py` and `test.txt`. Try to add both of them to the repository. 
Confirm that only `test.py` was added. (Do not use the `--force` option when commiting)


## Additional materials

Visualization of basic commands: [https://onlywei.github.io/explain-git-with-d3](https://onlywei.github.io/explain-git-with-d3)

Big file (e.g. binary blobs) storage: Git lfs [https://git-lfs.github.com/](https://git-lfs.github.com/)

### GIT GUI 

Let's check out the following ones:

 * IDE built-in GUI (PyCharm, VSCode, etc)
 * GitEye 
 * GitKraken

A longer list of clients can be found here: [https://git-scm.com/downloads/guis](https://git-scm.com/downloads/guis)

### Git Services 

 * GitHub – [https://github.com/](https://github.com/)
 * Gitlab – [https://about.gitlab.com/](https://about.gitlab.com/)
 * Bitbucket - [https://bitbucket.org/](https://bitbucket.org/)
 * Overleaf - [https://www.overleaf.com/](https://www.overleaf.com/); an online LaTeX editor with git support [https://www.overleaf.com/user/bonus](https://www.overleaf.com/user/bonus) 

### Links
 * Official manual [https://git-scm.com/book/](https://git-scm.com/book/)
 * Tutorials from gitkraken authors [https://www.gitkraken.com/resources/learn-git](https://www.gitkraken.com/resources/learn-git)
 * Github help [https://guides.github.com/](https://guides.github.com/)
