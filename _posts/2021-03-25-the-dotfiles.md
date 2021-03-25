---
layout: post
author: andy
---

## AKA how to automate your Mac

If you’ve been in IT long enough (probably about a week), you can’t help but notice those little files that start with a dot (unless you're on Windows).

Show all files:

![All files](/assets/images/all_files.png)

Don't show hidden files:

![No hidden files](/assets/images/no_hidden.png)


Most IT bods know this, even me. However most probably don’t know there’s a whole community around this stuff. Who knew?! Go and search on Github and you’ll see people have contributed their own repos that everyone calls ‘dotfiles’. These ‘dotfiles’ are often used as configuration files for various applications and you can tinker with them to your heart's content (although I don’t accept any responsibility for breaking anything!).

I must admit, the pandemic must be really getting to me as I’ve got quite excited by hidden files, perhaps it was the lack of sunlight and intelligent conversation… but you can do cool or maybe useful things. For example, you can pimp up your terminal a bit, I found this bit quite fun. For example, this is the standard boring Mac terminal:

![Boring terminal](/assets/images/boring.png)


We can set a environment variable called CLICOLOR to 1 and we get a nicer view (blue denotes a directory):

![Bit of colour](/assets/images/bit_of_colour.png)

You can also mess with the prompt, like so (this example is zsh), run:

```
export PROMPT="%n %F{green}%1~%f "
```

![Colourful prompt](/assets/images/colour_prompt.png)



In this case the %n is my username and the green bit is my current working directory. In this case the %F is the start of the green text and the %f delimits the end. If you want more info on these options, have a look here http://zsh.sourceforge.net/Doc/Release/Prompt-Expansion.html. There is even a ‘right prompt’ that you can set and you can do something cool and really useful like this:

```
export RPROMPT="%F{magenta}%*%f"
```

Which gives us the time when we ran something:

![Colourful prompt](/assets/images/right_prompt.png)


How useful is that? You can do all sorts of stuff like showing the git branch you’re currently working on. But what use is that as when you close the terminal and reload it you lose your changes. Obviously you chuck those commands in a dotfile, in this case .zshrc in your home directory. Ours would look like this:

```
export CLICOLOR=1
export PROMPT="%n %F{green}%1~%f "
export RPROMPT="%F{magenta}%*%f"
```

I should add here you can do similar sorts of things in other shells such as bash, but MacOS has now moved to zsh.

There’s other stuff you can do. Vim is a bit dull as standard, so pimp that up as well. Create yourself another dotfile in your home directory called .vimrc and chuck this in it:

```
set tabstop=4
syntax on
set number
set autoindent
set expandtab
```

Et voila, syntax highlighting for bash scripts (the content isn’t relevant):

![Colourful vim](/assets/images/vim.png)

You can literally tinker with this stuff for hours, seriously the fun never stops! But what happens when you get yourself a new Mac (or Linux machine) you have to do this all again. This is why people upload their configurations into repos on Github called dotfiles. The idea is you chuck your files onto github and when you need to reinstall, you clone it and run a script (that you write) to copy the files into the correct place. Please be careful not to put your credentials in Github though!

This does raise an interesting point though in that you still have to reinstall all your apps. Wouldn’t it be cool if we could automate the installation of those apps. We can! [Homebrew](https://brew.sh/) to the rescue. Head to that link and follow the one-liner of instructions.

I won’t go into too much detail on Homebrew but there’s 2 ways of installing stuff. Generally, if you want to install a command line tool like wget or Node.js you can do:

```
brew install wget
brew install node
```

If it’s something bigger like an app with a UI on it, such as Firefox or even Microsoft Word, you can install it as a ‘cask’:

```
brew install --cask firefox
brew install --cask microsoft-teams
```

These are the official apps and not dodgy ones, Homebrew just wraps the installation of these things with some scripts via the command line. I still can’t chuck my Mac out the window yet though as we still need to automatically install this stuff. But, if you create a Brewfile with contents like:

```
brew wget
brew node
cask firefox
cask microsoft-teams
```

And run ‘```brew bundle```’, then that will install everything in that file. Bung that in your dotfiles repo, add ‘brew bundle’ into your setup script et voila, you may have automated your Mac setup.

If you want to get an idea of what I did for mine, then have a look at my repo at my [dotfiles repo](https://github.com/daddyshortlegs/dotfiles) for inspiration and join the dotfiles party. In current Covid times, it’s really the only sort of party you should be going to!

