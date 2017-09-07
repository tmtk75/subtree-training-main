# subtree-training-main

Git subtree training (main repo)

## 1. Add subtree.

Add [subtree-training-sub](https://github.com/satoshikumano/subtree-training-sub) repository as subtree in ./sub directory using --prefix option.

```
$ git checkout -b subtree-training # Make a feature branch
$ git subtree add --prefix sub git@github.com:satoshikumano/subtree-training-sub.git master --squash
```

You can confirm sub/README.md is added in the main repository.
And there's new commits in the main repository made by subtree command.

```
$ git log
commit e902fbdab9827809666a4499a15454c2828a5414 (HEAD -> sample)
Merge: fef3203 f86ad4d
Author: satoshi <satoshi.kumano@kii.com>
Date:   Thu Sep 7 15:15:40 2017 +0900

    Merge commit 'f86ad4db4dfac8804b54f030006a87659e8ff922' as 'sub'

commit f86ad4db4dfac8804b54f030006a87659e8ff922
Author: satoshi <satoshi.kumano@kii.com>
Date:   Thu Sep 7 15:15:40 2017 +0900

    Squashed 'sub/' content from commit 8b63f9a
    
    git-subtree-dir: sub
    git-subtree-split: 8b63f9a11dd3713e260d8300035773158a6c8906
```

## 2. Fetch updates in sub repository.

Assume `branch1` branch in the sub repository is pushed and you want to pull the changes in the branch to the main repository.

- Add remote to shoten the command line inputs.

```
$ git remote add sub-origin git@github.com:satoshikumano/subtree-training-sub.git
```

- Pull the changes in the sub repository

```
$ git pull -s subtree sub-origin branch1 --allow-unrelated-histories --squash
From github.com:satoshikumano/subtree-training-sub
 * branch            branch1    -> FETCH_HEAD
Auto-merging sub/README.md
CONFLICT (add/add): Merge conflict in sub/README.md
Squash commit -- not updating HEAD
```

Since specified `--squash` option, it causes conflict.
To deal with the situation,

Edit sub/README.md and resolve conflict

```
$ git add sub/README.md
$ git commit
```

Alternatively, you can omit `--squash` option.
In this case, all historiy objects in the subrepository are copied to the main repo.

### 2.1 Using `git subtree pull` to pull the changes in the sub repository.

It doesn't works well.

Pull the changes in the sub repository with `--squash` option
```
$ git subtree pull --prefix ./sub sub-origin branch1 --squash
warning: no common commits
remote: Counting objects: 6, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 1), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (6/6), done.
From github.com:satoshikumano/subtree-training-sub
 * branch            branch1    -> FETCH_HEAD
 * [new branch]      branch1    -> sub-origin/branch1
Can't squash-merge: './sub' was never added.
```

Pull the changes in the sub repository without `--squash` option

```
$ git subtree pull --prefix ./sub sub-origin branch1 --squash
From github.com:satoshikumano/subtree-training-sub
 * branch            branch1    -> FETCH_HEAD
fatal: refusing to merge unrelated histories
```

It seems git has an [issue](https://stackoverflow.com/questions/39281079/git-subtree-error-fatal-refusing-to-merge-unrelated-histories) dealing with it.

I'm using `git version 2.14.1` installed by homebrew.
It would not happens in later or older version.

## 3. Push changes to the main repository

After you have updated the sub repository reference, push the working branch and make a PR to master or deveplop branch
So that you team member can use the main repository code with the latest sub repository references.

```
$ git push origin subtree-training
```

And make a PR in github with the branch!
