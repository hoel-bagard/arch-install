## Github
### Generating keys and adding them to github
#### SSH key
```
ssh-keygen -t ed25519 -C "hoel.bagard@gmail.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```
Finally, add the key [here](https://github.com/settings/keys)

#### GPG key
Use the following command to generate the key. All the defaults can be kept, exept for the key length that must be 4096 bits.
```
gpg --full-generate-key
```

From the list of GPG keys, copy the long form of the GPG key ID you'd like to use. In this example, the GPG key ID is 3AA5C34371567BD2:
```
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot 
ssb   4096R/42B317FD4BA89E7A 2016-03-10
```

Paste the text below, substituting in the GPG key ID you'd like to use. In this example, the GPG key ID is 3AA5C34371567BD2:
```
$ gpg --armor --export 3AA5C34371567BD2
```

Copy your GPG key, beginning with -----BEGIN PGP PUBLIC KEY BLOCK----- and ending with -----END PGP PUBLIC KEY BLOCK-----, and paste it [here](https://github.com/settings/keys).

Tell git about the GPG key:
```
git config --global user.signingkey <GPG key ID>
git config --global commit.gpgsign true
```


## Python
```
sudo pacman -S python-pip ipython
```
### Flake8 setup
```
sudo pacman -S flake8 python-flake8-docstrings
pip install pep8-naming flake8-import-order flake8-bugbear
```

Default `setup.cfg` file:
```
[flake8]
max-line-length = 120
docstring-convention = google
import-order-style = smarkets

ignore =
    D1      # Do not require docstrings
    W503  # Line break occurred before a binary operator  (Should already be gone so...)
    E226  # Missing whitespace around arithmetic operator  --> 0.1*b + 0.2*c is considered "wrong"


[mypy]
ignore_missing_imports = True

per-file-ignores =
    config/*: E221
```
TODO: see what using `max-complexity = 10` would do.

### Optional
```
sudo pacman -S mypy
```
To use importmagic within spacemacs (I usually don't, it floods the buffers):
```
pip install importmagic epc 
```

#### Packages on the community repo:
```
sudo pacman -S python-opencv fmt tensorboard python-pytorch
```
Notes:
- `fmt` is required for python-opencv
- Use `python-pytorch-cuda` if using a GPU
 
## Spacemacs
```
sudo pacman -S emacs
git clone --single-branch --branch develop https://github.com/syl20bnr/spacemacs ~/.emacs.d
```
