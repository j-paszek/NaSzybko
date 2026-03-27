# Version Control Systems Using GIT as an Example
Here we will learn version control systems (VCS) in practice.

## Working Locally
For now, we work in the console.

 *  `git init` – creates an empty repository
 *  `git status` – checks the repository status
 *  `git add <paths>` – adds files to the repository
 *  `git add -u` – adds modified files
 *  `git add -A` – adds all files
 *  `git rm <path>` – removes files from the repository
 *  `git rm --cached <paths>` – removes files from the repository but keeps local copies
 *  `git commit -m "<message>"` – creates a commit from the currently added files, using `<message>` as the description
 *  `git log` – shows the history of commits in the current branch

Git is most likely already installed; you can check this with `git --version`.
If it is not installed, use the standard `sudo apt install git` or `brew install git` (on macOS).


### Exercise 1
Basics of working with VCS locally.

* 1.1 Create a directory `prmgr`. Go into it and make sure it is empty (`mkdir`, `cd`, `ls -a`).
Also make sure it is not a git repository (`git status`, `git log`). Start working with git (`git init`).
Note: the first time, you will be asked to configure the user:
```
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

$\qquad$ After creating the repository in `prmgr`, inspect the changes (`ls -a`, `git status`, `git log`).

* 1.2 Create the file `mgr.txt` (`echo "Ala\n ma\n kota\n ." > mgr.txt`). Let us view this file (`cat mgr.txt`).
Let us see what git suggests (`git status`). And let us follow its advice: with `git add .` we will add all new files.
The situation has changed (`git status`), but in practice we have not done much yet, and the result of `git log` remains unchanged.

* 1.3 Let us save the current version of our work. (`git commit -m "first version"`) Then inspect it (`git status`, `git log`).

* 1.4 However, our work should look different, so let us change it (`echo "Ala\n ma\n psa\n ." > mgr.txt`).
Git notices the change (`git status`).

* 1.5 Let us save the corrected version of our work. First add the change (`git add .`)
and then save it with a meaningful message (`git commit -m "it should be a dog"`). The message describes the replacement of exactly
one line:
```
[master 2dfa1f2] it should be a dog
 1 file changed, 1 insertion(+), 1 deletion(-)
```

* 1.6 Finally, let us look at a summary:
```
commit 2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6 (HEAD -> master)
Author: j-paszek <email here>
Date:   Sat Nov 29 12:53:55 2025 +0100

    it should be a dog

commit 0cf64e63a83f0c82c99380f7cc3284901dcc42f8
Author: j-paszek <email here>
Date:   Sat Nov 29 12:47:28 2025 +0100

    first version
```

_______________________________

*  `git restore <path>` – undo changes introduced locally back to the previous commit
*  `git show <commit hash>:<path>` - displays the file at `path` in the version saved in the indicated commit
*  `git restore --source=<commit hash> <path>` - undo local changes back to the indicated commit

### Exercise 2
Restoring a previous version.
* 2.1 Let us introduce another change `echo "Ala\n ma\n jeża\n i\n nietoperza\n ." > mgr.txt`. Let us make sure
the file has changed `cat mgr.txt`. We do not like these changes. To return locally to the last saved version, run
`git restore mgr.txt` and make sure we have the previous version `cat mgr.txt`. In this way we have undone
all changes made in `mgr.txt` that we had not saved.
* 2.2 We introduce a change `echo "Ela\n ma\n trzmiela\n ." > mgr.txt`. And save it `git -add .`,
`git commit -m "version with an insect"`. Check `git log`:
```
commit 42f26bdcc432b4fbb77cdbac1ca813ff14ccbc0c (HEAD -> master)
Author: j-paszek <email removed>
Date:   Sat Nov 29 14:52:36 2025 +0100

    version with an insect

commit 2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6
Author: j-paszek <email removed>
Date:   Sat Nov 29 12:53:55 2025 +0100

    it should be a dog

commit 0cf64e63a83f0c82c99380f7cc3284901dcc42f8
Author: j-paszek <email removed>
Date:   Sat Nov 29 12:47:28 2025 +0100

    first version
```
In more complex projects with many files, it is worth narrowing this down to the changes in a specific file:
`git log -- mgr.txt`. Assume we do not like the latest changes and want to return to the previous version.
The situation is different now, because we have already saved our changes with a commit. We need the hash of the
commit we are interested in, with the version we want to return to (in our example this is `2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6`,
use your own).
Let us make sure this is the version of the file we want:
`git show 2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6:mgr.txt`.
This is the right version, so we update the working copy in our directory:
`git restore --source=2dfa1f2fa4e2791945098b56e376e7fa1a39fcc6 mgr.txt`.
We can verify the change with `cat mgr.txt` and save it with `git add .`, `git commit -m "dog again"`.
Now let us apply `git log --graph --oneline --all`:
```
* b8340fc (HEAD -> master) dog again
* 42f26bd version with an insect
* 2dfa1f2 it should be a dog
* 0cf64e6 first version
```
Notice that we can use these shorter versions too: `git show 42f26bd:mgr.txt`.

___________________________________

### Worth Knowing About:

**`git reset`**
- Undoes changes *locally* and moves the `HEAD` pointer.
- Does not create a new commit.
- Can remove history (`--hard`), so it is potentially destructive.
- Recommended only for changes that have not yet been pushed to a remote repository.

**`git revert`**
- Creates a new commit that reverses the effects of the indicated commit.
- Does not damage history, so it is safe in team work.
- Ideal for undoing changes that are already public.

**`git rebase`**
- Moves commits from one branch to the end of another, transforming the history into a linear one.
- Modifies commit history (rewrites it), which is risky after changes have been published.
- Used to clean up history before merging.

**`git restore`**
- Restores files from history, the staging area, or the last commit.
- Does not change branches.
- A modern and safer replacement for part of the functionality of `git checkout`.

**`git checkout`**
- An older command with two functions: switching branches **and** restoring files.
- Confusing because of its broad scope; currently recommended mainly for switching branches.


### Summary

- **reset** destroys or rewinds history locally,
- **revert** reverses changes by creating a new commit,
- **rebase** rewrites history,
- **restore** restores files,
- **checkout** switches branches (and historically also restored files, but today that role is replaced by `restore`).

_____________________________

 *  `git branch <name>` – creates a new branch `<name>`
 *  `git checkout <name>` – switches the current branch to `<name>`
 *  `git checkout -b <name>` – creates a new branch `<name>` and switches to it
(equivalent to running `git branch` and then `git checkout`)
 *  `git switch` - a modern way to switch branches
 *  `git merge` – creates a commit that combines the contents of two branches.

###  Exercise 3
Branches are a natural tool when working on software. For example, we may have a main branch
corresponding to the production version, that is, the working application, and another branch dedicated
to implementing a new feature. In the meantime, the main branch may receive changes resulting
from bug fixes discovered in the production environment.

We can imagine creating a branch for work on a master's thesis, where we decide to change the notation in
a definition, which means rephrasing most of the theorems and proofs.
* 3.1 Create `git checkout -b zmiana`. Now `git log --graph --oneline --all` will show:
```
* b8340fc (HEAD -> zmiana, master) dog again
* 42f26bd version with an insect
* 2dfa1f2 it should be a dog
* 0cf64e6 first version
```
* 3.2 Let us add changes. `echo "Ela\n ma\n psa\n ." > mgr.txt`, `git add .`, `git commit -m "but Ela"`,
`git log --graph --oneline --all`:
```
* 7d184a3 (HEAD -> zmiana) but Ela
* b8340fc (master) dog again
* 42f26bd version with an insect
* 2dfa1f2 it should be a dog
* 0cf64e6 first version
```
* 3.3 Introduce more changes. `echo "Ela\n ma\n trzmiela\n ." >> mgr.txt`, `git add .`, `git commit -m "bumblebee"`,
`git log --graph --oneline --all`
* 3.4 However, we need to present the work to the thesis supervisor, so we return to the main branch `git switch master`
(or `git checkout master`; `git log --graph --oneline --all`).
* 3.5 But we must add a correction that is necessary in the version for the supervisor
`echo "Ela\n ma\n trzmiela\n ." >> mgr.txt`, `git add .`, `git commit -m "added bumblebee"`.
As a result, `git log --graph --oneline --all` shows a nice tree:
```
* f956e31 (HEAD -> master) added bumblebee
| * 5b49053 (zmiana) bumblebee
| * 7d184a3 but Ela
|/
* b8340fc dog again
* 42f26bd version with an insect
* 2dfa1f2 it should be a dog
* 0cf64e6 first version
```
* 3.6 Finally, we try to prepare a consistent version for further work and merge the branches.
> Note: `git merge zmiana` will take us into vim; to exit and save the changes, type `ESC :wq`.
> To abort an unfinished merge, run `git merge --abort`.

$\qquad$ The fastest option is to use the standard merge description with `git merge zmiana --no-edit`.


###  Exercise 4
Now we will produce a conflict and resolve it.
* 4.1 Let us create a smaller repository `cd`, `mkdir konflikt`, `cd konflikt`, `git init`. Prepare the file
`echo "Ala\n ma\n kota\n." > test.txt`, `git add .`, `git commit -m "start"`.
* 4.2 Create a branch `git switch -c zmiana` (currently the preferred way instead of checkout). And change the file
`echo "Ala\n ma\n psa\n." > test.txt`, `git add test.txt`, `git commit -m "it is a dog"`.
* 4.3 Return to the main branch `git switch master` and change the file `echo "Ala\n ma\n chomika\n." > test.txt`,
`git add test.txt`, `git commit -m "now a hamster"`
* 4.4 When trying to merge, we will get a conflict `git merge zmiana --no-edit`
```
Auto-merging test.txt
CONFLICT (content): Merge conflict in test.txt
Automatic merge failed; fix conflicts and then commit the result.
```
$\qquad$ which we can inspect in our file `cat test.txt `:
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

* 4.5 You can resolve the conflict by manually editing the file (for example, replacing the fragment `<<...>> zmiana` with `psa`).
There may be many conflicts, so it is usually most convenient to use an IDE such as PyCharm or VS Code. You can also use
`git mergetool`, but then you will find yourself in `vimdiff`. If you do not want to learn commands and prefer simply
using arrow keys to choose the version you want, I recommend `meld` (`brew install --cask meld`,
`git config --global merge.tool meld`, `git config --global mergetool.prompt true`, `git mergetool`).

* 4.6 However we edit our conflicts, we can now finish the merge with `git add .`, `git commit -m "merge"`.
Phew, it worked `git log --graph --oneline --all`:
```
*   532867d (HEAD -> master) merge
|\
| * 54de264 (zmiana) it is a dog
* | 9e63bbb now a hamster
|/
* 33f0c56 start
```

______________________________

## Working Remotely
Of course, all commands can be typed in the console, but for this part we recommend using your favorite IDE.

 *  `git clone` – clone a remote repository locally
 *  `git pull` – pull changes from remote and update the local branch
 *  `git fetch` – update information from the remote repository
 *  `git push` – push changes to the remote repository

### Exercise 5
Create a local project on your computer from an existing repository.

* 5.1 In PyCharm, `File->Project from version control` opens the `clone repository` window. On the left side there will be
profiles such as a connected GitHub account. For now, use `repository url` with version control set to `git`.
From an existing repository, for example [https://gitlab.uw.edu.pl/python-tools/git-example-repo](https://gitlab.uw.edu.pl/python-tools/git-example-repo),
click the `Code` button on the website and copy the address from `Clone with https`
(`https://gitlab.uw.edu.pl/python-tools/git-example-repo.git`), then return to PyCharm,
paste it into the `url` field, and click the `clone` button.
* 5.2 If we click `master` in our new project, we can see that we are in `local >master` and that
`remote >origin >>master >>develop` are also visible. That means the remote repository has two branches. If we select
`remote>origin>>develop>>>checkout`, we move to the new branch. The file `test.py` will appear in the project directory,
and the top menu will show `develop` instead of `master`.
* 5.3 On the left side at the very bottom there is a `version control` button; in our project it will already be labeled
as `git`. After clicking it, in the bottom window: on the left we can navigate through branches. In the center we have a list of commits.
Click `sample data`. After clicking, the contents of the left pane change. There are green (that is, newly added
in that commit) files `file1.txt` and `file2.txt`. After double-clicking one of these files, the top of the window
will show `Repository Diff: file2.txt`. There you can track changes between file versions.
* 5.4 Finally, let us return to the top menu, where `VCS` has been replaced by `Git`. By choosing
`Git->current file->annotate with git blame`, dates and authors of changes will appear on the left side of the file.

### Exercise 6
Create a repository on [https://gitlab.uw.edu.pl](https://gitlab.uw.edu.pl) (or GitHub, or ...).
Clone it locally, modify it, and use `git push` to send the modifications to the remote server.

### Exercise 7
Make changes in the remote version, from another computer or, more simply, modify a file in the web interface and commit
the changes. Modify the same file locally. Test `git pull` and `git fetch`.

__________________________________
To be continued
