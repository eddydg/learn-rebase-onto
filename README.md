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
