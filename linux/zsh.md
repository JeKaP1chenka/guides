# configure zsh

## Content

1. [Install](#install)
1. [Configure](#configure-zsh-1)
1. [Links](#links)

## Install

Первым делом чтоб у вас отображались все иконки надо установить шрифт из набора [`HerdFonts`][2] (лично я установил `FiraMono Nerd Font`). Теперь надо установить его в терминал. Лично я делаю все из под Windows и в `Windows Terminal` для `wsl` так что пример будет от туда, все действия кроме установки шрифтов не должны отличаться на других платформах.

- Устанавливаем понравившийся шрифт с этого сайта [`HerdFonts`][2]. Прямая ссылка на скачку zip архив с [`FiraMono Nerd Font`][3]
- Распаковываем архив в папку
- Открываем настройки шрифтов (Параметры -> Персонализация -> Шрифты)
- Перетаскиваем все *.ttf файлы в `Добавление шрифтов`
- Открываем `Windows Terminal`, нажимаем на верхнюю стрелку направленную в низ, в выпадающем окне выбираем `параметры`
- Слева в блоке профили заходим в раздел `по умолчанию`. Выбираем в `Начертание шрифта` установленный нами шрифт.

### А теперь все действия будут происходит в `wsl`

Устанавливаем `zsh` и сразу переходим в него. `zsh` является удобным в использовании и гибким в настройки терминальной оболочкой UNIX систем.

```bash
sudo apt-get install -y zsh
```

Устанавливаем [`oh my zsh`][1]. Это крутейший фреймворк с открытым исходным кодом для настройки `zsh`, включающий в себя тысячи тем, плагинов и команд.

```bash
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

После установки соглашаемся со всем. Теперь у нас по умолчанию стоит терминальная оболочка `zsh` и создался файл `.zshrc` с конфигурацией `zsh`.

Устанавливаем `powerlevel10k`(является настраиваемой темой) для удобной, легкой и быстрой настройки `oh my zsh`.
> Если вы знаете как и хотите установить другую тему то вот [ссылка на галерею тем][4]. Я остановился на `powerlevel10k`

```bash
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```

## Configure zsh

Теперь начинается самое интересное, настройка `zsh`.

Открываем файл с конфигурацией `zsh`(псс можно использовать любимый текстовый редактор).

```bash
nano ~/.zshrc
```

Далее меняем настройку `ZSH_THEME` на

```
ZSH_THEME="powerlevel10k/powerlevel10k"
```

После чего сохраняем файл и выходим
Исполняем данную команду. Она позволит настроить иконки и сам `zsh`

```bash
source ~/.zshrc
```

Если вы настроили что то не так и хотите это поменять то можете исполнить данную команду

```bash
p10k configure
```

Установим полезные и красивые плагины плагины

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
sudo apt install -y exa
```

Откроем файл конфигурации и включим плагины

```bash
nano ~/.zshrc
```

заменим `plugins=(git)` на

```
plugins=(
  git
  zsh-autosuggestions
  zsh-syntax-highlighting
  extract # optional
  dirhistory # optional
  history # optional
)
```

> официальные плагины(которые не надо устанавливать) вы можете найти [тут][5]. А плагины от сторонних разработчиков можете поискать в [интернете][6]

А после `source $ZSH/oh-my-zsh.sh` вставим

```
if [ -x "$(command -v exa)" ]; then
    alias ls="exa --icons"
    alias la="exa --icons --long --all --group"
fi
```

Примечание: Если вы хотите более тонко настроить командную строку то вы можете попробовать поменять что то в `~/.p10k.zsh`.
**ВАЖНО сохраните этот файл перед тем как что то там менять**. Там более точно можно настроить отступы, иконки, пробелы, символы и тд.

## Links

1. [Видео с настройкой `zsh`][7]
1. [Текстовой вариант с настройкой `zsh`][8]
1. [Официальный сайт `oh my zsh`][1]
1. [Темы `oh my zsh`][4]
1. [Плагины `oh my zsh`][5]
1. [Шрифты Nerd Fonts][2]


[1]: https://ohmyz.sh/
[2]: https://www.nerdfonts.com/font-downloads
[3]: https://github.com/ryanoasis/nerd-fonts/releases/download/v3.2.1/FiraMono.zip
[4]: https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
[5]: https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins
[6]: https://google.com
[7]: https://www.youtube.com/watch?v=6qzyWLZha0E
[8]: https://dev.to/abdfnx/oh-my-zsh-powerlevel10k-cool-terminal-1no0