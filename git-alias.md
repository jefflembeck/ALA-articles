Part of getting comfortable with the command line is making it your own. Small customizations, shortcuts, and time saving techniques become second nature once you spend enough time fiddling around in your terminal. Since [Git](http://git-scm.com) is my [Version Control System](http://en.wikipedia.org/wiki/Revision_control) of choice (due partially to its incredible popularity via [GitHub](https://github.com)), I like to spend lots of time optimizing my experience there.

Once you've become comfortable enough with Git to `push`, `pull`, `add`, and `commit`, and you feel like you'd like to pursue more, you can customize it to make it your own. A great way to start doing this is with aliases. Aliases can help you by providing shorthand commands so you can move faster and have to remember less of Git's sometimes very murky UI. Luckily, Git makes itself easy to customize by setting global options in a file named `.gitconfig` in our home directory.

Quick note: for me, the home directory is `/Users/jlembeck`, you can get there on OSX or most any other Unix platform by typing `cd ~` and hitting `enter` or `return`. On Windows, if you're using Powershell, you can get there with the same command and if you're not using Powershell, `cd %userprofile%` should do the trick.

Now, let's take a look. First, open your `.gitconfig` file (from your home directory):

```
~/code/grunticon master* $ cd ~
~ $ open .gitconfig
```

You might see a file that looks similar to this:

```
[user]
  name = Jeff Lembeck
  email = your.email.address@host.com
[alias]
  st = status
  ci = commit
  di = diff
  co = checkout
  amend = commit --amend
  b = branch
```


Let's look at the different lines and what they mean. 

```
[user]
  name = Jeff Lembeck
  email = your.email.address@host.com
```

First up, the global user configuration. This is what Git references to say who you are when you make commits.


```
[alias]
  st = status
  ci = commit
  di = diff
  co = checkout
  amend = commit --amend
  b = branch
```

Following the user information is what we're here for, aliases.

Any command given in that screen is prefaced with `git`. Example: `git st` is an alias for `git status` and `git ci` is `git commit`. This allows you to save a little time while you're typing out commands. Soon, the muscle memory kicks in and `git ci -m "Update version to 1.0.2"` becomes your keystroke-saving go-to.

Ok, so aliases can be used to shorten commands you normally type and that's nice, but a lot of people don't really care about saving 10 keystrokes here and there. For them, I submit the use case of aliases for those ridiculous functions that you can never remember how to do. As an example, let's make one for learning about a file that was deleted. I use this all of the time.

Now, to check the information on a deleted file, you can use `git log --diff-filter=D -- path/to/file`. Using this information I can create an alias.
```
d = log --diff-filter=D -- $1
```
Let's break that down piece by piece.

This should look pretty familiar. It is almost the exact command from above, with a few changes. The first change you'll notice is that it is missing `git`. Since we are in the context of `git`, it is assumed in the alias. Next, you'll see a `$1`, this allows you to pass an argument into the alias command and it will be referenced there.

Now, with an example. `git d lib/fileIDeleted.js`. `d` is not a normal command in git, so git checks your config file for an alias. It finds one. It calls `git log --diff-filter=D -- $1`. And passes the argument `lib/fileIDeleted.js` into it. That will be the equivalent of calling `git log --diff-filter=D -- lib/fileIDeleted.js`.

Now you never have to remember how to do that again. Time to celebrate the time you saved that would normally be spent on Google trying to figure out how to even search for this. I suggest ice cream.

For further digging into this stuff: I got most of my ideas from Gary Bernhardt's [wonderful dotfiles repository](https://github.com/garybernhardt/dotfiles). I strongly recommend checking out dotfiles repos to see what wild stuff you can do out there with your command line. Gary's is an excellent resource and [Mathias' might be the most famous](https://github.com/mathiasbynens/dotfiles). To learn more about Git Aliases from the source, check them out in the [Git documentation](http://git-scm.com/book/en/Git-Basics-Tips-and-Tricks#Git-Aliases).

