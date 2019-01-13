# Configuration Management
First and the most important thing when using multiple remote servers is maintaining the same configuration on them. It helps to save your time and brain cells. A lot actually. 
Quite typical approach is having all configuration files that you create and change managed with version control. You can do it manually, but in my experience, it's a very tidy job, so you'd better use some  existing helper tools. There are plenty of them, and you can choose the one you like yourself. From here and further I will use the one called [dotgit](https://github.com/kobus-v-schoor/dotgit) by Kobus van Schoor.

This part is complex and boring, but as soon as you start using this things, you will never be able to stop.

### Installation
```bash 
git clone https://github.com/kobus-v-schoor/dotgit.git
mkdir -p ~/.local/{xbin,share/bash-completion/completions}
cp -r dotgit/bin/dotgit* ~/.local/xbin/
cp dotgit/bin/bash_completion ~/.local/share/bash-completion/completions/dotgit
# Add new place to $PATH
echo 'PATH=~/.local/xbin/:$PATH' >> .bashrc
```
Note directories we just created: `xbin` is intended for manually installed scripts and executables like `dotgit`. The another one will contain `bash_completion` scripts for them.

After this operations you need to re-login to your server or restart your shell any other way.Ensure now that `dotgit` command is available and it's completion is working by writing `dotgi` and pressing <Tab><Tab><Tab>. You shoud see dotgit subcommands like `clean` or `decrypt`.

If everything is ok you are ready to create your configuration files repository.
Run the commands below:
```bash
mkdir ~/.dotfiles/
cd  ~/.dotfiles/
dotgit init
```
If you encountered errors running this commands git is either not installed or not yet configured. Fix this and rerun this commands. 
Now you have some initial files `dotgit` created for you.

### Initializing repository
Now, finally, we will add `dotgit` files to our repository, making it self-contained.
I assume you will need it on every host this repository will be used. If for some reason you are not, you will be able change this behavior later.

Open `filelist` file with your favorite text editor and lines below:

```
.local/xbin/dotgit:common
.local/xbin/dotgit_headers/:common
.local/share/bash-completion/completions/dotgit:common
.bashrc
```

As you can see, mentioned files are exactly ones that we installed/modified. 
Then run 

	dotgit update 

command inside repository. dotgit will move files from filelist to repository and replace them symlinks to their new location. Now they all reside under `dotfiles` directory of repository with path preserved relatively to your `$HOME`. You can ensure that by running 

	find dotfiles/

To commit those changes run

	dotgit generate

You can use all other `git` commands like `git add` and `git commit` to manage staged changes manually, `git push` and `git pull` to have your changes exchanged between your hosts. How to use them along with remote repositories on github or anywhere else is outside of this tutorial. 

### `filelist` syntax
You could have noticed that last line has no `:common` suffix. It's because it can be omitted, as default  value.
In dotgit `common` is something called category. You can assign file to multiple categories separating them with comma. Files from common category will be installed on every host. Using categories you can control what files to use on any particular host flexibly. Categories can be joined by creating special category called group. Groups can be declared like that:

	group=cat1,cat2

There are some default categories that simplify dotgit usage. Read about them in  full filelist syntax documentation [here](https://github.com/kobus-v-schoor/dotgit/blob/master/bin/dotgit_headers/help.txt#L22).


### Using your configs on new host
Assume you have your configs pushed somewhere like github, and now you want them deployed on new machine.
Remember I said this repository will be self-contained? It means that it can it can deploy itself on new machine. After you cloned it somewhere (I recommend you to clone it to the same place on every host, `~/.dotfiles` for example), you simply `cd` into this directory and run (PLEASE!!! be careful, dotgit will ask you before replacing files already exist on this machine (like `.bashrc`, because they can some of your useful settings, so you can move them to the safe place)

	dotfiles/common/.local/xbin/dotgit restore 
 
 Quite complex one, yeah? Put it in bash script [like me](https://gitlab.com/hexfaker/dotfiles/blob/master/init.sh) and add to git. Now you can simply run this script on new host. Don't forget to add a README. 
 
Now, after you have your basic configs, you can edit `filelist` to tell dotgit which specific categories of files you want to be deployed. If you still don't know what you need to edit, read filelist syntax docs I mentioned earlier.
Commit it and then rerun dotgit, now as usually:

	dotgit restore

Congrats, now you have your files deployed!!! Feel yourself at home.

In next part, I will show you how to have your `bash` shell configuration fit the flexibility of dotgit as well as other cool hacks and useful aliases.