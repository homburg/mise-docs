# IDE Integration

IDEs work better with shims than they do environment variable modifications. The simplest way is
to add the mise shim directory to PATH.

For IntelliJ and VSCode—and likely others, you can modify `~/.zprofile`
with the following:

```sh
export PATH="$HOME/.local/share/mise/shims:$PATH"
```

This won't work for all of mise's functionality. For example, arbitrary env vars in `[env]` will only be set
if a shim is executed. For this we need tighter integration with the IDE and a custom plugin. If you feel
ambitious, take a look at existing direnv extensions for your IDE and see if you can modify it to work for mise.
Direnv and mise work similarly and there should be a direnv extension that can be used as a starting point.

Alternatively, you may be able to get tighter integration with a direnv extension and using the
[`use_mise`](/direnv) direnv function.


## emacs

```lisp
;; CLI tools installed by Mise
;; See: https://www.emacswiki.org/emacs/ExecPath
(setenv "PATH" (concat (getenv "PATH") ":/home/user/.local/share/mise/shims"))
(setq exec-path (append exec-path '("/home/user/.local/share/mise/shims")))
```

## Xcode

Xcode projects can run system commands from script build phases and schemes. Since Xcode sandboxes the execution of the script using the tool `/usr/bin/sandbox-exec`, don't expect Mise and the automatically-activated tools to work out of the box. First, you'll need to add `$(SRCROOT)/.mise.toml` to the list of **Input files**. This is necessary for Xcode to allow reads to that file. Then, you'll need to run your commands through the `mise x` command: 

```bash
# Add Mise to the PATH
export PATH="$HOME/.local/bin/bin:$PATH"

# -C ensures that Mise loads the configuration from the Mise configuration 
# file in the project's root directory.
mise x -C $SRCROOT swiftlint
```

If you don't want to prefix your commands with `mise x -C $SRCROOT`, you can add the shims directory to the `$PATH` environment variable:

```bash
export PATH="$HOME/.local/bin:$PATH"
export PATH="$HOME/.local/share/mise/shims:$PATH"

mise reshim -C $SRCROOT
swiftlint
```


::: tip NOTE
The `mise reshim` command is important to ensure mise creates shims for the tools and versions specified in the project's mise configuration file. Otherwise the script might end up using other versions of the tools.
:::


## [YOUR IDE HERE]

I am not a heavy IDE user. I use JetBrains products but I don't actually
like to execute code directly inside of them often so I don't have much
personal advice to offer for IDEs generally. That said, people often
ask about how to get their IDE to work with mise so if you've done this
for your IDE, please consider sending a PR to this page with some
instructions (however rough they are, starting somewhere is better than
nothing).
