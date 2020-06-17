# Practice your `git rebase`

Learn to rebase with git using this mock project.

There are 3 exercise of increasing difficulty. For each exercise, you must start from this initial state of the branches:

```
branch-1 ---A---B---C
            \
branch-2     D---E---F
                 \
branch-3          G---H
```

The objective for each exercise is to go from the `Before` state and try to reach the `After` state. There are many way to do this. Here we will use the power of [`git rebase`](https://git-scm.com/docs/git-rebase).

## Table of Content

[Rules](#rules)  
[Tips](#tips)  
[Exercise 1](#exercise-1)  
[Exercise 2](#exercise-2)  
[Exercise 3](#exercise-3)  
[Notes](#notes)  

## Rules

During a merge conflict in you can only delete the _conflict marker lines_. Other than that, you are not allowed to add, remove or even rearrange anything else in the file.
```
// Those are the git conflict markers you can delete
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

The content of `changelog.md` must always reflect the state of your current branch. For example, while on `branch-3` it will be:
```
Added in commit A
Added in commit D
Added in commit E
Added in commit G
Added in commit H

// The branches for reference:
branch-1 ---A---b---c
            \
branch-2     D---E---f
                 \
branch-3          G---H
```

## Exercise 1

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

## Exercise 2

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

  Git rebase accepts some very useful additional options. You should check the documentation.

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
  or also the following, from any branch:
  
    git rebase -onto b7fb633 7b26bca7 branch-2

</details>

## Exercise 3

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

This exercise is about rebranching `branch-3` on `branch-2` so that __`branch-3` starts from `branch-2`__ just as before. It should look like this:
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
  
  You can be explicit and tell git exactly what you want to do.

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
            
  If you are on `branch-3` this can be done with

    git rebase --onto branch-2 branch-2@{1}

  If you have done it correctly, you should only rebase 2 commits `(REBASE 1/2)` &mdash; the commits we are moving `G` and `F` &mdash; and the final state should not have any duplicated commit name.
  
  There are many other alternatives that translate to the same thing:
  
    git rebase --onto F' F

  By expliciting the last argument, you can even call this from any branch:

    git rebase --onto F' F branch-3
    git rebase --onto branch-2 branch-2@{1} branch-3
    
</details>


# Notes
[1] You can think of a branch as a simple pointer on the last commit of a bunch. That's why you can reference both `branch-2` and `F`

[2] `branch-2@{1}` is the state of `branch-2` before the last change (i.e. before the rebase). 
Check `git reflog` for more information
