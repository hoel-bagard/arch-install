## zsh
`sudo pacman -S zsh neofetch`

### oh-my-zsh
`sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"`
#### Changes in config
TODO: should already be done through the .dotfiles now.
In `~/.oh-my-zsh/lib/misc.zsh`, change:\
`env_default 'LESS' '-R'`   (when using git diff (or other similar commands), shows results in an alternate screen)\
to:\
`env_default 'LESS' '-FRSX'`   ("Usual" console output)

### powerlevel10k
#### Install fonts
```
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
Note: follow the instructions [here](https://github.com/romkatv/powerlevel10k#manual-font-installation) corresponding to the terminal you use.

#### Install powerlevel10k
```
yay -S --noconfirm zsh-theme-powerlevel10k-git
echo 'source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme' >> ~/.zshrc
```

### Clone .dotfiles
```
git clone --separate-git-dir=$HOME/.dotfiles git@github.com:hoel-bagard/.dotfiles.git $HOME/myconf-tmp --recurse-submodules
mv -v ~/myconf-tmp/.* ~/
mv -v ~/myconf-tmp/README.md ~/
rmdir myconf-tmp
source ~/.zshrc
```
#### Install plugins
```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
source ~/.zshrc
```
