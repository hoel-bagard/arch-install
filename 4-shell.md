# Shell

## zsh

```console
sudo pacman -S zsh fastfetch
```

### oh-my-zsh

```zsh
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

### powerlevel10k

#### Fonts

Follow the instructions [here](https://github.com/romkatv/powerlevel10k#manual-font-installation) corresponding to the terminal you use.

For WezTerm no change is required.

<details>
<summary>Konsole</summary>

```zsh
mkdir temp_fonts
cd temp_fonts
wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttf
wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold.ttf
wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Italic.ttf
wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold%20Italic.ttf
sudo mv *.ttf  /usr/share/fonts/
cd ..
rmdir temp_fonts
```

</details>

### Clone .dotfiles

See instructions [here](https://github.com/hoel-bagard/.dotfiles).
