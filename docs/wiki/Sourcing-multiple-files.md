Normally `src''` can be used to specify additional file to source:

```zsh
zi ice pick"powerless.zsh" src"utilities.zsh"
zi light martinrotter/powerless
```

- `pick''` – provide main file to source (can be a pattern like `*.sh` –
  alphabetically first matched file is sourced),
- `src''` – provide second file to source (not a pattern, plain file name)

---

However, via `atload''` ice one can provide simple loop to source more files:

```zsh
zi ice svn pick"completion.zsh" \
  atload'local f; for f in git.zsh misc.zsh; do \
        source $f \
    done'
zi snippet OMZ::lib
```

- `svn` – use Subversion to clone `OMZ::lib` (the whole Oh My Zsh `lib/`
  directory),
- note that `atload''` uses apostrophes not double quotes, to literally put `$f`
  into the string,
- `atload`'s code is automatically being run **within the snippet's (or
  plugin's) directory**,
- `atload''` code isn't tracked by ZI, i.e. cannot be unloaded, unless you
  load a plugin (not a snippet) with `zi load …` and prepend the value of
  the ice with exclamation mark, i.e. `atload'!local f; for …'`,
- `atload''` is executed after loading main files (`pick''` and `src''` ones).

---

The `multisrc''` ice, which loads **multiple** files enumerated with
spaces as the separator (e.g. `multisrc'misc.zsh grep.zsh'`) and also using
brace-expansion syntax (e.g. `multisrc'{misc,grep}.zsh')`. Example:

```zsh
zi ice svn pick"completion.zsh" multisrc'git.zsh \
    functions.zsh {history,grep}.zsh'
zi snippet OMZ::lib
```

The all possible ways to use the `multisrc''` ice-mod:

```zsh
zi ice depth"1" multisrc="lib/{functions,misc}.zsh" pick"/dev/null"
zi load robbyrussell/oh-my-zsh

# Can use patterns
zi ice svn multisrc"{funct*,misc}.zsh" pick"/dev/null"
zi snippet OMZ::lib

array=({functions,misc}.zsh)
zi ice svn multisrc"$array" pick"/dev/null"
zi snippet OMZ::lib

# Will use the array's value at the moment of plugin load
# – this can matter in case of using Turbo mode
array=({functions,misc}.zsh)
zi ice svn multisrc"\$array" pick"/dev/null"
zi snippet OMZ::lib

# Compatible with KSH_ARRAYS option
array=({functions,misc}.zsh)
zi ice svn multisrc"${array[*]}" pick"/dev/null"
zi snippet OMZ::lib

# Compatible with KSH_ARRAYS option
array=({functions,misc}.zsh)
zi ice svn multisrc"\${array[*]}" pick"/dev/null"
zi snippet OMZ::lib

zi ice svn multisrc"misc.zsh functions.zsh" pick"/dev/null"
zi snippet OMZ::lib

# Also – hack ZI: the ice's contents is simply `eval'-uated
# like follows: eval "reply=($multisrc)". So it might get handy on
# an occasion to pass code there, but first you must close the paren
# and then don't forget to assign `reply', and to provide a trailing
# opening paren. In the code be careful to not redefine any variable
# used internally by ZI – e.g.: `i' is safe:

array=({functions,misc}.zsh)
zi ice svn multisrc'); local i; for i in $array; do \
            reply+=( ${i/.zsh/.sh} ); \
        done; ((1)' pick"/dev/null"
zi snippet OMZ::lib
```

--

## Changes In The Recent ZI

Recently, ZI has been extended with the [For-Syntax](../For-Syntax/) which
can in some situations replace a typical `multisrc''` loading. The point is that
this syntax allows to easily specify snippets to source – and do this within a
single ZI command. Thus, instead of:

```shell
zi ice multisrc'(functions|misc|completion).zsh'
zi snippet OMZ::lib
```

it's possible to write:

```shell
zi for \
  OMZL::functions.zsh \
  OMZL::misc.zsh \
  OMZL::completion.zsh
```

which is somewhat easier on eyes. Also – an **important** property: the multiple
snippets loaded with the for-syntax are being loaded _separately_, which means
that they will not cause a longer keyboard blockage, which could have been
noticeable – when using Turbo. The ZI scheduler will distribute the work over
time and will allow activation of keyboard in between the snippets. The
`multisrc''` way doesn't work this way – sourcing many files can cause
noticeable keyboard freezes (in Turbo).

[]( vim:set ft=markdown tw=80: )