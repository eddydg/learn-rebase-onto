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

## Exercice 3

Target state of the branches:
```
branch-1 ---A---B---C
            \
branch-2     D---E---F
                 \
branch-3          G---H
```

## Bonus

Change `branch-3` parent to `B`. Notice that it kept commit `D` which originally was from `branch-2`:
```
branch-1 ---A---B---C
                 \
branch-3          D---G---H
```
