<!-- title: Yet Another Guide on Managing Dotfiles -->
<!-- author: Nimai Patel -->

A few months ago I came across this
[ article ](https://www.atlassian.com/git/tutorials/dotfiles)
which explained how a bare git repository can be used to backup
dotfiles in the \*NIX home directory. I messed around with it for a while
and wrote a script which reduces the task of backing up dotfiles to simply
adding the file path to a text file and running a script -- way easier than
messing around with symlinks!

A bare git repository is almost like a regular git folder. The only difference
is that it doesn't have a specific working tree but _only_ the actual git repo
(i.e. the .git folder). You can watch this
[ video ](https://www.youtube.com/watch?v=8aZW9mYOxhc) by Engineer Man if you
want to learn more about them.

# Step One: Initializing the git repository

This is as simple as running the command:

```
$ git init --bare $HOME/.cfg
```

If you've used git for any period of time then you know that the `git init`
command initializes an empty repo in the folder that you're currently in.
Since we want to create a bare repo, we pass the `--bare` flag and the
location of the git folder. This should create a .cfg folder in your home
directory. If you inspects its contents you'll find that they are similar
to any other repository's .git folder.

# Step Two: Accessing the repo via an alias

Add the following line to your .zshrc or .bashrc along with the rest of your
aliases:

```
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```

This alias will allow us to stage and commit files to our backup repository.
The `--git-dir` flag specifies the git repository we made in step one and
`--work-tree=$HOME` will allow us to track any file in our home directory
using the repo.

# Step Three: Ignoring files we don't want to backup

Run `exec $SHELl` or restart your terminal to make the alias we made in the
previouse step available. Then, run the following command, so that the
repository doesn't display the hundreds of files in your home directory every
time you check it's `status`:

```
$ config config --local status.showUntrackedFiles no
```

# Step Four: Setup a remote repository

Create an empty remote repository (I will be using GitHub) and get its SSH or
HTTP. On GitHub this is immediately available after the repo is created.

![GitHub Clone Repo](https://dev-to-uploads.s3.amazonaws.com/i/9gj90f7d2w351wcfrofw.png)

And then add the remote with:

```
$ config remote add origin <URL>
```

At this point you can simply do something like:

```
$ config add ~/.vimrc
$ config commit -m "Adding .vimrc"
$ config push origin master
```

This will make the repository start tracking your .vimrc and you can commit
and push the file to GitHub. Pretty much all git commands like `status` and
`diff` should now be available to you with the `config` alias.

This is already better (in my opinion) than more common ways of backing up
dotfiles but **we can do better**.

# Step Five: Shell script to automate adding, commiting and pushing dotfiles to GitHub

First create a file called .backuplist in your home directory using
`$ touch ~/.backuplist`. Add the paths of all the files you want to backup
on a new line (you can include comments with \#), something like this:

```
# list of files to backup
/home/nimai/.backuplist

# neovim
/home/nimai/.config/nvim/init.vim
/home/nimai/.config/nvim/coc-settings.json
/home/nimai/.vim/tasks.ini

# zshrc
/home/nimai/.zshrc

# tmux
/home/nimai/.tmux.conf

# i3 config
/home/nimai/.i3/config

# vscode settings and bindings
/home/nimai/.config/Code/User/settings.json
/home/nimai/.config/Code/User/keybindings.json

# kitty
/home/nimai/.config/kitty/kitty.conf

# scripts directory
/home/nimai/scripts

# README
/home/nimai/README.md
```

Then create a file called `update_config` somewhere and add it to PATH.

On zsh, for example, you can add it to path by adding this line to your .zshrc:

```
export PATH=$PATH:<path to where you created update_config>
```

Paste this script into the `update_config` file you just created:

UPDATE: While the script below does work, I've changed it a bit and if you're interested in having a look at what it looks now, check it out over [here](https://github.com/nimainimaii/arch-dotfiles/blob/master/.scripts/update-config).

```
#!/usr/bin/sh
alias config="/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME"
sed 's/[ \t]*\#.*//g ; /^[ \t]*$/d' $HOME/.backuplist | xargs /usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME add
config commit -m "$(date)"
nc -z 8.8.8.8 53 && echo "Pushing to GitHub" && config push origin master || echo "Not able to push to GitHub, try again later"
```

NOTE: You will need to have xargs and netcat on your system for this to work

Don't forget to make it an executable by running `$ chmod +x update_config`!

This script just stages the files in the ~/.backuplist folder if it finds any
changes, commits them with the current date as the message
(redundant, I know) and pushes them to GitHub if you have internet access.

Now every time you make any changes to your dotfiles you can just run
`update_config` in your shell and your remote repo will be up to date
with your system dotfiles! 

# Getting your dotfiles on a new system:

Now all you have to do on a new system is run the following commands
and you'll have all your settings back:

```
git init --bare $HOME/.cfg
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
config config --local status.showUntrackedFiles no
config remote add origin <URL>
config pull origin master
```

You might get some errors if you already have, say, a .zshrc in your new
system. In this case you probably would want to delete this before pulling
your files from the remote.

That's it we're done 🥳. Please do tell me if you face any issues with the
script, in case you use it!
