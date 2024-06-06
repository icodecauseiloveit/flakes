# Setup for reproducible development environments using Nix

Nix is a package manager for Linux and MacOs. We are going to use one of its features called "flakes" in order to create reproducible development environments for this project.

## Add current user to /etc/sudoers

This command must be executed from root user

```su root```

```echo "gary ALL=(ALL) NOPASSWD:ALL" | sudo tee -a /etc/sudoers```


## Installing Nix

Go to the [Nix download page](https://nixos.org/download) and install `Nix: the package manager` for your specific platform.

## Activating flakes

Since flakes is a "expermental feature", we need to manually allow its usage.
To do so, first create a configuration folder for Nix:

```
mkdir -p ~/.config/nix
```

Create a `nix.conf` configuration file allowing the usage of flakes:

```
echo 'experimental-features = nix-command flakes' > ~/.config/nix/nix.conf
```

## Running a development environment

Go to the directory of the project to work on, it should have a `flake.nix` file declaring the configuration and contents of the development environment.
Start the Nix dev shell:

```
nix develop
```

Now you should be on a temporary shell with all the packages and configurations for this specific project, in order to leave this shell use the `exit` command or press `ctrl + d`.

## Optional: using `direnv`

`direnv` is a tool for automatically loading development environments once we enter a directory that uses one, we can use it to avoid typing `nix develop` and for faster creation of the Nix dev shell.

### Installing `direnv`

Nix can be used to install `direnv`:

```
nix profile install nixpkgs#direnv
```

You can also use your system's default package manager. In the case of Ubuntu/Debian:

```
apt install direnv
```

**WARNING:** you need to be using direnv >=2.29.0 for flakes integration. You can check this with:

```
direnv --version
```

### Configuring `direnv` for your shell

For bash:

```
echo 'eval "$(direnv hook bash)"' >> ~/.bashrc && . ~/.bashrc
```

For zsh:

```
echo 'eval "$(direnv hook zsh)"' >> ~/.zshrc && . ~/.zshrc
```

For other shells check the manual page:

```
man direnv
```

### Using `direnv`

Go to the directory of a project containing a `flake.nix` file declaring a Nix dev shell, then create a `.envrc` file and allow its usage:

```
echo 'use flake' > .envrc && direnv allow
```

Now `direnv` should automatically load the development environment each time you enter that directory and exit the shell when you leave it.

We can also use the `.envrc` file to declare custom settings for the shell, such as environment variables or aliases. Each time we make changes to the file we need to run `direnv allow`:

```
echo 'export FOO="bar"' >> .envrc && direnv allow
```

Now `echo $FOO` will print the value of the variable we just defined inside the `.envrc` file.
