![the_ultimate_gitignore_guide](the_ultimate_gitignore_guide.svg)

<h1>THE ULTIMATE GUIDE TO <code>.gitignore</code></h1>

_in form of a Q&A_

<!-- TOC start (generated with https://derlin.github.io/bitdowntoc/) -->

- [TL;DR](#tldr)
- [What is `.gitignore`?](#what-is-gitignore)
   * [Note that `.gitignore` starts with a `.`](#note-that-gitignore-starts-with-a-)
- [Which files would I want to ignore?](#which-files-would-i-want-to-ignore)
   * [A mini-introduction to globbing](#a-mini-introduction-to-globbing)
      + [Is “glob” an English word?](#is-glob-an-english-word)
      + [And by the way, what does “git” stand for? Is it an acronym?](#and-by-the-way-what-does-git-stand-for-is-it-an-acronym)
- [Should I use `.gitignore` to safeguard sensitive data?](#should-i-use-gitignore-to-safeguard-sensitive-data)
- [Why do developers usually include executables in the `.gitignore` file?](#why-do-developers-usually-include-executables-in-the-gitignore-file)
- [Where can I find `.gitignore` templates?](#where-can-i-find-gitignore-templates)
   * [Online `.gitignore` generator](#online-gitignore-generator)
- [How to format a `.gitignore` file?](#how-to-format-a-gitignore-file)
   * [Some examples ](#some-examples)
- [How to check which files will be ignored?](#how-to-check-which-files-will-be-ignored)
   * [Example](#example)
   * [A useful command to check all ignored files](#a-useful-command-to-check-all-ignored-files)
- [What happens to files that were being tracked before adding `.gitignore`?](#what-happens-to-files-that-were-being-tracked-before-adding-gitignore)
- [How to stop tracking previously tracked files?](#how-to-stop-tracking-previously-tracked-files)
   * [Example](#example-1)
- [Why would I want to ignore `.gitignore`?](#why-would-i-want-to-ignore-gitignore)
- [Where should I put `.gitignore`?](#where-should-i-put-gitignore)
   * [Order of precedence of `.gitignore` patterns ](#order-of-precedence-of-gitignore-patterns)
- [And what is `.gitkeep`?](#and-what-is-gitkeep)
- [Sources / further reading](#sources-and-further-reading)

<!-- TOC end -->

# TL;DR

Add a `.gitignore` file to your project's working directory to tell Git which files have to be ignored. Each line in the file should contain a _glob_ pattern matching files that you do not want to track in your Git repository.

For instance, the line

    *.tmp
    
tells Git to ignore all files with the extension `.tmp`.

Comment lines in `.gitignore` start with a `#`, empty lines are ignored.

If `.gitignore` contains files that had previously been tracked, remove them with

    git rm --cached
    
Be aware that relying solely on `.gitignore` is not the recommended approach for preventing the accidental exposure of confidential data in your Git repository. 

Use [Github templates](https://github.com/github/gitignore), [Gitlab](https://gitlab.com/projects/new)'s pre-populated projects, or an [online generator](https://www.toptal.com/developers/gitignore) for inspiration on `.gitignore` file entries. 

# What is `.gitignore`?

`.gitignore` is a text file containing a list of all files that should be ignored by git, that is no change in those files should be recorded in the Git repository.

From the [Git documentation](https://git-scm.com/docs/gitignore):
> gitignore - Specifies intentionally untracked files to ignore

## Note that `.gitignore` starts with a `.`

Note that the `.gitignore` file starts with a dot (`.`). This means you might not be able to see it by default depending on your operating system. For instance, on Linux/Unix/Mac, if you type `ls` you won't see files starting with `.`. In order to see those files too you need to use the option `-a` like this:

    ls -a

Files starting with a `.` are also known as _dotfiles_ and they are usually configuration files. Typical examples of dotfiles are `.bashrc`, the configuration file for interactive bash sessions or the `.ssh` folder containing configuration files and keys for the SSH client.

See also: 

- [What are dot-files?](https://askubuntu.com/questions/94780/what-are-dot-files) on [askubuntu.com](https://askubuntu.com)
- [Awesome dotfiles](https://github.com/webpro/awesome-dotfiles), a curated list of dotfiles resources, and 
- [https://dotfiles.github.io/](https://dotfiles.github.io/), a guide to dotfiles on GitHub.

# Which files would I want to ignore?

Typically, output files generated from code runs, such as compiled code, logfiles, in general anything that's temporary and can be re-generated.

For example, the line:

    *.log
    
in a `.gitignore` file will tell Git to ignore all files with the extension `.log` (the asterisk `*` is known as a _globbing pattern_.

## A mini-introduction to globbing

Globbing is the operation that expands a wildcard pattern into the list of pathnames matching the pattern.  Matching is defined by:

 - `?` matches any single character.
 - `*` matches any string, including the
       empty string.
       
For instance, the command

    ls myFile?.txt
    
will return a list of all files such as `myFile1.txt`, `myFile2.txt`, `myFile_.txt`, `myFilea.txt`, etc.

and the command 

    ls *.csv

will return all files with the extension `.csv` (e.g. `myFile1.csv`, `myFile2.csv`, `anotherFile.csv`, `.csv`, etc.).

Source: [glob](https://man7.org/linux/man-pages/man7/glob.7.html)

Of course, if you have a logfile in your repository to showcase something about logfiles, then you won't want to ignore that file because of it being an integral part of your code release.

### Is “glob” an English word?

The name “glob” is the abbreviation of “global” and it originates from the 1971 Bell Labs' Unix version. Here is the description of `/etc/glob` from the Unix Programmer's Manual (K. Thompson and D. M. Ritchie, November 3, 1971 [available online](https://www.bell-labs.com/usr/dmr/www/1stEdman.html)):
> `glob` is used to expand arguments to the shell containing `*` or `?`. It is passed the argument list containing the metacharacters; glob expands the list and calls the command itself

### And by the way, what does “git” stand for? Is it an acronym?


From the [README](https://github.com/git/git/blob/e83c5163316f89bfbde7d9ab23ca2e25604af290/README) file in Git's Git repository:

> “git” can mean anything, depending on your mood

But its author Linus Torvalds also claims to have named Git after “git”, the British slang word for “unpleasant person” (as in: “that mean old git”).

# Should I use `.gitignore` to safeguard sensitive data?

Adding files containing sensitive information to `.gitignore` is not a good idea and you should better keep those files in a different location than your Git workspace.

`.gitignore` does not provide foolproof protection against unintentional disclosure of confidential data (think of the case that a collaborator deletes the `.gitignore` file). To mitigate the risk of disclosing sensitive information it crucial to implement additional security measures, such as proper access controls, encryption, and regular audits.

Here are some recommended practices for handling sensitive data in a Git repository:
- **Avoid Hardcoding Secrets:** refrain from hardcoding sensitive information directly into the source code. Use configuration files or environment variables instead. Save a template to your Git repository and the actual secrets locally or in a vault service.
- **Security Audits:** use tools like [`git-secrets`](https://github.com/awslabs/git-secrets) or [gitleaks](https://github.com/gitleaks/gitleaks) to scan your code for passwords and other sensitive information before committing it to a git repository.
- **Encrypt Sensitive Files:** use [`git-crypt`](https://github.com/AGWA/git-crypt) to protect sensitive files by encrypting them when committed.



# Why do developers usually include executables in the `.gitignore` file?

Adding executables to the `.gitignore` file is a common practice in software development for a few reasons:

- **Build Process Artifacts:** Including executables in version control is redundant, as they can be recreated whenever needed by anyone with the source code and the necessary build tools. Uploading executables to a Git repository defies the essential purpose of version control, which is designed for tracking changes in source code.
- **Platform Independence:** Executables are platform-specific. Including them in version control can cause issues when team members are using different operating systems. By ignoring executables, you avoid potential conflicts.
- **Repository Size and Speed:** executables tend to be large files, leading to slower repository operations.
- **Security:** The presence of executables in a public Git repository can pose security risks and may potentially be exploited in various ways:
  - **Malicious Code Injection:** Executables might be modified to include malicious functionalitys. Users who download and run these modified executables may inadvertently introduce security threats into their systems.
  - **Information Disclosure:** Executables might contain hardcoded credentials or other confidential information. 


# Where can I find `.gitignore` templates?

The [gitignore](https://github.com/github/gitignore) repository on Github provides a collection of `.gitignore` templates for many programming languages.

If you've ever worked with Latex you will be familiar with the `.log`, `.aux`, etc. files that can reasonably be excluded from your Git repository. Here's how an excerpt from the `Tex` template looks like:

```
## Core latex/pdflatex auxiliary files:
*.aux
*.lof
*.log
*.lot
*.fls
*.out
*.toc

## Intermediate documents:
*.dvi
*.xdv
*-converted-to.*
# these rules might exclude image files for figures etc.
# *.ps
# *.eps
# *.pdf

## Generated if empty string is given at "Please type another file name for output:"
.pdf
```

(the `.pdf` file at the end with no name, just an extension, is the typical file you would want to delete anyway)

**Note:** Github will propose to choose from its list of `.gitignore` templates when creating a new project. Gitlab does not have this option for generic projects but it includes a `.gitignore` file in pre-populated template projects.

## Online `.gitignore` generator

There's also an online generator of `.gitignore` files: [gitignore.io](https://www.toptal.com/developers/gitignore). Enter the programming languages used in your repository and the generator will create a `.ignore` file for you.

# How to format a `.gitignore` file?

Here are the formatting rules for `.gitignore`:

1. a line starting with a hash (`#`) is a comment and is ignored, for example:
   
   `# this is a comment line`
   
2. blank lines are ignored, and so they can be used as a separators for readability
3. backslash (`\`) is the [_escape character_](https://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_03.html), so for instance `\#` at the beginning of a patterns means that the pattern begins with a hash (`\#` undoes the special meaning of the hash character)
4. trailing spaces are ignored unless they are quoted with backslash (`\`)
5. a prefix `!` negates the pattern thus including files that were excluded by a previous pattern. Of course `!` can be escaped with a backslash (`\!`) for matching files that begin with an exclamation point.
6. the slash (`/`) is the directory separator and it has a different meaning depending on its position in the gitignore search pattern:
   - `/` at the end of the pattern means that the pattern only matches directories
   - `/` at the beginning or middle (or both) of the pattern means that the pattern is relative to the directory level of the particular `.gitignore` file itself. Otherwise the pattern may also match at any level below the `.gitignore` level.
6. globbing: 
	- an asterisk `*` matches anything except a slash (`/`)
	- the character `?` matches any one character except `/`
	- the range notation, e.g. `[a-zA-Z]`, can be used to match one of the characters in a range
	- two asterisks (`**`) match any number of subdirectories

## Some examples 

**Note:** `% ` represents my shell prompt.

Let's say you have in your Git repository a file named `myFile1` and a directory `myDir` containig three files: `myFile1`, `myFile2`, `myFile3` :

```
 % tree
.
├── myDir
│   ├── myFile1
│   ├── myFile2
│   └── myFile3
└── myFile1

1 directory, 4 files
```

Assume your `.gitignore` file contains the lines:

```
# skip all files like myFile[0-9]
myFile[0-9]
```

Then these files will be skipped:

```
./myFile1
./myDir/myFile1
./myDir/myFile2
./myDir/myFile3
```

But if `.gitignore` file contains the lines:

```
# skip all files like myFile[0-9] but only if in the root folder
/myFile[0-9]
```

then only  `./myFile1` will be skipped.



# How to check which files will be ignored?

Git provides a handy utility to debug `.gitignore`: `git-check-ignore`.

To check whether a path or file is going to be ignored by `git` use:

    git check-ignore [<options>] <pathname>…​
    git check-ignore [<options>] --stdin

## Example

My `.gitignore` file contains one single line, the kind of pattern I want to ignore are all files like `myFile1`, `myFile2`, ... but only if they are in the root folder.


```
% cat .gitignore
# skip all files like myFile[0-9] but only if in the root folder
/myFile[0-9]
```

With the above `.gitignore` the file `myFile1` in the top directory is skipped:

```
% git check-ignore -v myFile1  
.gitignore:2:/myFile[0-9]	myFile1
```

while `myFile1` in `myDir` is not (no output)

```
% git check-ignore -v myDir/myFile1 
```

To get an output also for non-matching files use the option `-n` in combination with `-v`:

```
git check-ignore -v -n myDir/myFile1
::	myDir/myFile1
```

In this case, the file `myDir/myFile1` is shown in the output of `git check-ignore` but the file won't be ignored by `git` as it does not match any pattern in `.gitignore`.

## A useful command to check all ignored files

Check all files in the Git working directory:

    % find . -not -path './.git/*' | git check-ignore --stdin -v
    
This command will scan all files and folders in your current directory and check whether they're going to be ignored according to the directives in `.gitignore`. For files that are going to get ignored the `.gitignore` rule is also indicated (line number in `.gitignore`) when using the option `-v`.

Credit for the `find` command: [this answer on Stackoverflow](https://stackoverflow.com/a/467053) (_Git command to show which specific files are ignored by .gitignore_).

 
# What happens to files that were being tracked before adding `.gitignore`?

Since `.gitignore` does not have a retroactive effect, you need to untrack the files that were tracked before the introduction of `.gitignore`.




# How to stop tracking previously tracked files?

To tell Git to stop tracking a file (aka ignoring it) you can use `git rm --cached`. 

See also: [How do I make Git forget about a file that was tracked, but is now in .gitignore?](https://stackoverflow.com/questions/1274057/how-do-i-make-git-forget-about-a-file-that-was-tracked-but-is-now-in-gitignore).

## Example

**Note:** `gitignore_tests % ` represents my shell prompt.

Remove all files in the directory `gitignore_tests`

    gitignore_tests % rm -rf .git 
    gitignore_tests % rm -rf .gitignore 
    
Initialize an empty `git` repository

    gitignore_tests % git init
 Initialized empty Git repository in /Users/myUsername/Documents/gitignore_tests/.git/
 
 Create two files in the working directory `gitignore_tests` and commit them to the repository
 
    gitignore_tests % touch myFile1 myFile2
    gitignore_tests % git add .
    gitignore_tests % git status
    On branch master

    No commits yet

    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)
	    new file:   myFile1
	    new file:   myFile2

    gitignore_tests % git commit -m "add myFile1 myFile2"   
    [master (root-commit) a5bc16b] add myFile1 myFile2
     2 files changed, 6 insertions(+)
     create mode 100644 myFile1
     create mode 100644 myFile2

Now create a `.gitignore` file 

    gitignore_tests % cat << EOF > .gitignore                
    # skip all myFile0, myFile1
    myFile[0-1]
    EOF

**Note:** I used [this solution](https://stackoverflow.com/a/43423683) to add text to a file on the command line with [_here documents_](https://tldp.org/LDP/abs/html/here-docs.html).

Add the `.gitignore` file to the repository

    gitignore_tests % git add .
    gitignore_tests % git commit -m "add .gitignore" 
    [master 13ce4cf] add .gitignore
     1 file changed, 2 insertions(+)
     create mode 100644 .gitignore
     
You can see that `myFile1` is still being tracked by running `ls-files`:

    gitignore_tests % git ls-files
    .gitignore
    myFile1
    myFile2
    
If you change and commit `myFile1`, the changes will be tracked by `git`.

    gitignore_tests % cat << EOF >> myFile1                 
    some text
    EOF
    gitignore_tests % git add .
    gitignore_tests % git commit -m "modify myFile1"
    [master 9e8c612] modify myFile1
     1 file changed, 1 insertion(+)
     
To remove `myFile1` from the `git` index use `git rm --cached`

    gitignore_tests % git rm --cached myFile1
    rm 'myFile1'

Now `myFile1` does not appear in `ls-files`

    gitignore_tests % git ls-files                          
    .gitignore
    myFile2

and it is not tracked by `git` anymore

    gitignore_tests % git commit -m "untrack myFile1"
    [master 7f978a2] untrack myFile1
     1 file changed, 7 deletions(-)
     delete mode 100644 myFile1
    gitignore_tests % cat << EOF >> myFile1               
    some other text
    EOF
    gitignore_tests % git add .                      
    gitignore_tests % git commit -m "modify myFile1 again"
    On branch master
    nothing to commit, working tree clean
    gitignore_tests % 





# Why would I want to ignore `.gitignore`?

Oftentimes one finds the line 

    .gitignore

in `.gitignore` files. 

This is not good practice but be aware that it is allowed.


# Where should I put `.gitignore`?

`.gitignore` is usually located in Git project's working directory (this is the directory containing the `.git` folder, the actual Git repository). 

However a `.gitignore` file can also be located in any subfolder of the top-level of the working tree. Files to be ignored can also be specified as options from the command line. Patterns from different `.gitignore` files are taken into account by `git` following some rules of precedence.


`.gitignore` files that are not in the root directory of your Git project have an “ignore” effect on the folders in which they're located and on their subfolders.

## Order of precedence of `.gitignore` patterns 

From the [`.gitignore` documentation](https://git-scm.com/docs/gitignore):

> Each line in a gitignore file specifies a pattern. When deciding whether to ignore a path, Git normally checks gitignore patterns from multiple sources, with the following order of precedence, from highest to lowest (within one level of precedence, the last matching pattern decides the outcome):
> 
> - Patterns read from the command line for those commands that support them.
> 
> - Patterns read from a `.gitignore` file in the same directory as the path, or in any parent directory (up to the top-level of the working tree), with patterns in the higher level files being overridden by those in lower level files down to the directory containing the file. These patterns match relative to the location of the `.gitignore` file. A project normally includes such `.gitignore` files in its repository, containing patterns for files generated as part of the project build.
> 
> - Patterns read from `$GIT_DIR/info/exclude`.
> 
> - Patterns read from the file specified by the configuration variable `core.excludesFile`.

# And what is `.gitkeep`?

`.gitkeep` is a dummy file used in empty directories to trick Git into tracking them. Git would otherwise not add empty directories.

While `.gitkeep` is a common choice, any other file name can be used to keep empty directories tracked. Some prefer to use the file `.keep` or even an empty `.gitignore` (or containing the line `!.gitignore` to prevent it from being ignored in case already untracked).

See also: [What are the differences between .gitignore and .gitkeep?](https://stackoverflow.com/questions/7229885/what-are-the-differences-between-gitignore-and-gitkeep).



# Sources and further reading

See also:

- [gitignore](https://git-scm.com/docs/gitignore) by the Git community
- Pro Git book, by Scott Chacon and Ben Straub available [online](https://git-scm.com/book/en/v2)
- [Ignore files that have already been committed to a Git repository](https://stackoverflow.com/questions/1139762/ignore-files-that-have-already-been-committed-to-a-git-repository/1139797#1139797)
- [How do I make Git forget about a file that was tracked, but is now in .gitignore?](https://stackoverflow.com/questions/1274057/how-do-i-make-git-forget-about-a-file-that-was-tracked-but-is-now-in-gitignore)
- [.gitignore](https://www.atlassian.com/git/tutorials/saving-changes/gitignore)
- [Multiple .gitignore in subfolders](https://stackoverflow.com/questions/53208235/multiple-gitignore-in-subfolders)
- [Don't ignore .gitignore](https://opensource.com/article/20/8/dont-ignore-gitignore)
- [Learning All about GitIgnore : Ignoring Files and Folders](https://medium.com/@it.hhkn/learning-all-about-gitignore-ignoring-files-and-folders-d731998a7790)
- [Git command to show which specific files are ignored by .gitignore](https://stackoverflow.com/questions/466764/git-command-to-show-which-specific-files-are-ignored-by-gitignore)
- [git-check-ignore](https://git-scm.com/docs/git-check-ignore)
