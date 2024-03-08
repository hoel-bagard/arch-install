G# Dev environment setup
## GitHub
### Generating keys and adding them to github
#### SSH key
Either re-use a key, or generate a new one with:
```console
ssh-keygen -t ed25519 -C "hoel.bagard@gmail.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```
Finally, add the key [here](https://github.com/settings/keys)

#### GPG key
<details>
<summary>Expand</summary>
If you already have a key, then it is better to re-use it. Otherwise the follow the instructions below to generate a new one and register it to GitHub.

Use the following command to generate the key. All the defaults can be kept, exept for the key length that must be 4096 bits.
```console
gpg --full-generate-key
```

From the list of GPG keys, copy the long form of the GPG key ID you'd like to use. In this example, the GPG key ID is `3AA5C34371567BD2`:
```console
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10
uid                          Hoel Bagard <hoel.bagard@gmail.com>
ssb   4096R/42B317FD4BA89E7A 2016-03-10
```

Export the key with:
```console
gpg --armor --export <GPG key ID>
```

Copy your GPG key, beginning with `-----BEGIN PGP PUBLIC KEY BLOCK-----` and ending with `-----END PGP PUBLIC KEY BLOCK-----`, and paste it [here](https://github.com/settings/keys).

Tell git about the GPG key:
```console
git config --global user.signingkey <GPG key ID>
git config --global commit.gpgsign true
```
</details>

### Pre-commit
```console
sudo pacman -S pre-commit
```

## Rust
```console
sudo pacman -S rustup rust-analyzer
rustup default stable
```

## Python
```console
sudo pacman -S python-pip ipython tensorboard
```

### Linting
#### Ruff
```console
sudo pacman ruff
```

#### Typing
```console
sudo pacman -S pyright
```

<details>
<summary>Old configs</summary>

#### Flake8 setup (obsolete)

```console
sudo pacman -S flake8 python-flake8-docstrings
pip install pep8-naming flake8-import-order flake8-bugbear flake8-quotes flake8-comprehensions
```

#### Mypy
Note: I don't use it anymore since pyright is much better.
```console
sudo pacman -S mypy pylsp-mypy
```

#### Packages on the community repo:
Use poetry to manage the packages for the project.
```console
sudo pacman -S python-opencv fmt python-pytorch
```
Notes:
- `fmt` is required for python-opencv
- Use `python-pytorch-cuda` if using a GPU

</details>

## Tools
### Tmux
```console
sudo pacman -S tmux
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

### Spacemacs
```console
sudo pacman -S emacs aspell-en shfmt bash-language-server yaml-language-server
git clone --single-branch --branch develop https://github.com/syl20bnr/spacemacs ~/.emacs.d
```

(aspell is for spell checking, `shfmt`, `bash-language-server` and `shellcheck` for bash scripts)

#### Misc
To use importmagic within spacemacs (I usually don't, it floods the buffers):
```console
pip install importmagic epc
```
