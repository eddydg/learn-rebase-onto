# Practice your `git rebase --onto`

Initial state of the branches:
```
branch-1 ---A---B---C
            \
branch-2     D---E---F
                 \
branch-3          G---H
```

You can use `git log --graph --oneline --branches=branch-*` to check your branches state at any time.

After each exercice, don't forget to reset your branches to the initial state:
```bash
git reset --hard origin/branch-1
git reset --hard origin/branch-2
git reset --hard origin/branch-3
```

## Exercice 1

Rebase `branch-2` on top of `branch-1`:
```
branch-1 ---A---B---C
                    \
branch-2             D---E---F
```

<details>
  <summary>Solution</summary>

    git checkout branch-2
    git rebase branch-1

</details>

## Exercice 2

Move commits in `branch-2` on their new parent `B`:
```
branch-1 ---A---B---C
                \
branch-2         D---E---F
```

<details>
  <summary>Hint 1</summary>

    --onto

</details>

<details>
  <summary>Hint 2</summary>

    git rebase --onto <new-parent> <old-parent>

</details>

<details>
  <summary>Solution</summary>

    git checkout branch-2
    git rebase --onto b7fb633 7b26bca7
                        ^         ^
                        B         A

</details>

## Exercice 3

Initial:
```
branch-1 ---A---B---C
            \
branch-2     D---E---F
                     \
branch-3              G---H
```

`git checkout branch-2`
`git rebase branch-1`
```
branch-1   ---A-----B-----C
              \           \
branch-2       \           D'---E'---F'
                \
branch-3         D---E---F---G---H
```

If you try to rebase branch-3 on branch-2 now, you will have to fix potential conflicts coming from B to F' (`REBASE 1/5`), before reaching  to this state:
```
branch-1   ---A-----B-----C
                          \
branch-2                   D'---E'---F'
                                     \
branch-3                              D---E---F---G---H
```
That's because even though D', E' and F' have the same name as before (the prime symbol is only added here for clearness), they changed slightly when fixing the conflicts from the branch-2 to branch-1 rebase. This means that they cannot be fast-forwarded: since they are not strictly identical, git cannot allow the possibility of loosing a change implicitely :
```
branch-1   ---A-----B-----C
                          \
branch-2                   D'---E'---F'
                                     \
branch-3                              G---H // <-- have we just lost D and E?
```

But we know that it's okay to do so, because they were the same commits in a past life (before the rebase of branch-2 on branch-1). Thus we can explicitely tell git to put on top of the new branch-2 (`F'` or `branch-2` [1]) every commit from the old branch-2 (`F` or `branch-2@{1}` [2]) to the head of branch-3 (`H` or `branch-3`).

If you are on `branch-3` this can be done with `git rebase --onto branch-2 branch-2@{1}`

If you have done it correctly, you should only rebase 2 commits (`REBASE 1/2`) and the final state should not have any duplicated commit name.

`git checkout branch-3`
`git rebase --onto F' F`

or

`git rebase --onto F' F branch-3`

or

`git rebase --onto branch-2 branch-2@{1} branch-3`

```
branch-1 ---A---B---C
                    \
branch-2             D'---E'---F'
                               \
branch-3                        G---H
```



# Notes
[1] You can think of a branch as a simple pointer on the last commit of a bunch. That's why you can reference both `branch-2` and `F`

[2] `branch-2@{1}` is the state of `branch-2` before the last change (i.e. before the rebase the rebase). 
Check `git reflog` for more information
