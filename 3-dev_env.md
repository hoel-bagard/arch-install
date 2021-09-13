## Github
### Generating keys and adding them to github
TODO
https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

## Python
### Flake8 setup
```
sudo pacman -S flake8 python-flake8-docstrings
pip install pep8-naming flake8-import-order flake8-bugbear
```

Default `setup.cfg` file:
```
[flake8]
max-line-length = 120

ignore =
    D1      # Don't require docstrings

per-file-ignores =
    config/*: E221
```
TODO: see what using `max-complexity = 10` would do.

## Spacemacs
```
sudo pacman -S emacs
git clone --single-branch --branch develop https://github.com/syl20bnr/spacemacs ~/.emacs.d
```
