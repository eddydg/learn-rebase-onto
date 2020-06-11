# Practice your `git rebase`

## Introduction

Learn to rebase with git using this mock project.

Initial state of the branches:
```
branch-1 ---A---B---C
            \
branch-2     D---E---F
                 \
branch-3          G---H
```

The content of `changelog.md` must always reflect the state of your current branch. For example, while on `branch-3` it will be:
```
Added in commit A
Added in commit D
Added in commit E
Added in commit G
Added in commit H
```

## Rules

 - During a merge conflict you can only delete the conflict marker lines. You are not allowed to rearrange or write in it.
 ```
// Those are the git conflict markers
<<<<<<< HEAD
=======
```

## Tips

You can use `git log --graph --oneline --branches=branch-*` to check your branches state at any time.

After each exercice, don't forget to reset your branches to the initial state:
```bash
git reset --hard origin/branch-1
git reset --hard origin/branch-2
git reset --hard origin/branch-3
```

This can also be useful if you are completely lost in your branches state and want to restart on a clean plate.

You will encounter merge conflicts in `changelog.md` after each rebase. Don't worry: they are necessary for the exercise and you should only need to remove the markers.

## Exercice 1

Rebase `branch-2` on top of `branch-1`:
```
// Before
branch-1 ---A---B---C
            \
branch-2     D---E---F

// After
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
// Before
branch-1 ---A---B---C
            \
branch-2     D---E---F

// After
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

Let's change things up a bit by messing with out initial state: rebase `branch-2` on `branch1` as seen in [Exercice 1](#exercise-1), but this time let's keep track of `branch-3`:
```
// Before
branch-1 ---A---B---C
            \
branch-2     D---E---F
                     \
branch-3              G---H

// After
branch-1   ---A-----B-----C
              \           \
branch-2       \           D'---E'---F'
                \
branch-3         D---E---F---G---H
```

What we get is a bit odd: `branch-3` used to have `F` as a parent but now it is not connected to `branch-2` anymore.

This exercise is about rebranching `branch-3` on `branch-2` so that no one would know that `branch-3` had its own life for a while. It should look like this:
```
// Before
branch-1   ---A-----B-----C
              \           \
branch-2       \           D'---E'---F'
                \
branch-3         D---E---F---G---H

// After
branch-1 ---A---B---C
                    \
branch-2             D'---E'---F'
                               \
branch-3                        G---H
```

<details>
  <summary>Hint 1</summary>

  If you try to rebase `branch-3` on `branch-2` now, you will put all commits from `branch-3` to the top of `branch-2` `(REBASE 1/5)`, reaching this state:
  
    branch-1   ---A-----B-----C
                              \
    branch-2                   D'---E'---F'
                                         \
    branch-3                              D---E---F---G---H

  This is not what we are looking for since `D', E', F'` and `D, E, F` are redondant. That happens because, even though `D'`, `E'` and `F'` have the same name as before (the prime `'` symbol is only added here for clearness), they changed slightly when fixing the conflicts from the `branch-2` to `branch-1` rebase. This means that they are now different from `D`, `E` and `F` and git cannot fast-forward them anymore.

</details>

<details>
  <summary>Hint 2</summary>
            
  We do know that they are the same commits and that `branch-3` shares its commits with `branch-2` right up until `F'/F`. So we can explicitely tell git to:
   - put on top of the new `branch-2` (`F'` or `branch-2` [1])
   - every commit from the old `branch-2` (`F` or `branch-2@{1}` [2])
   - upto the head of `branch-3` (`H` or `branch-3`).
</details>

<details>
  <summary>Solution</summary>
            
  If you are on `branch-3` this can be done with `git rebase --onto branch-2 branch-2@{1}`

  If you have done it correctly, you should only rebase 2 commits `(REBASE 1/2)` -- the commits we are moving `G` and `F` -- and the final state should not have any duplicated commit name.
</details>


`git checkout branch-3`
`git rebase --onto F' F`

or

`git rebase --onto F' F branch-3`

or

You can be more explicit with that last argument, letting you call the command from any branch you are currently on:
`git rebase --onto branch-2 branch-2@{1} branch-3`




# Notes
[1] You can think of a branch as a simple pointer on the last commit of a bunch. That's why you can reference both `branch-2` and `F`

[2] `branch-2@{1}` is the state of `branch-2` before the last change (i.e. before the rebase the rebase). 
Check `git reflog` for more information
