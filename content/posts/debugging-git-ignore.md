+++
title = 'Debugging Git Ignore'
date = 2024-10-25T23:29:44+01:00
draft = false
+++

Git can ignore files. Typically this is configured in the repo with a
`.gitignore` file - but there's a myriad of places where you can specify how to
ignore files. From the [gitignore(5)](https://git-scm.com/docs/gitignore) man page:
* From the CLI
* From the `.gitignore` files up the directory tree to the root of the repo
* From `$GIT_DIR/info/exclude` file
* From the files specified by the `core.excludesFile` config - which by default
  has a value of `$XDG_CONFIG_HOME/git/ignore`, if `$XDF_CONFIG_HOME` is not
  set or empty, then use `$HOME/.config/git/ignore`
 
Real shades of [giving instructions to the class](https://www.youtube.com/watch?v=MFYgiRYHic0)...

You can avoid thinking about this by using `git check-ignore --verbose`
[instead](https://git-scm.com/docs/git-check-ignore) - which will simply tell
you what file is instructing git to ignore your file.
