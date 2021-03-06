---
title: Creating a Repository and Tracking Changes
teaching: 20
exercises: 0
questions:
- "Where does Git store information?"
- "How do I record changes in Git?"
- "How do I check the status of my version control repository?"
- "How do I record notes about what changes I made and why?"
objectives:
- "Create a local Git repository."
- "Go through the modify-add-commit cycle for one or more files."
- "Explain where information is stored at each stage of Git commit workflow."
- "Distinguish between descriptive and non-descriptive commit messages."
keypoints:
- "`git init` initializes a repository."
- "`git status` shows the status of a repository."
- "Files can be stored in a project's working directory (which users see), the staging area (where the next commit is being built up) and the local repository (where commits are permanently recorded)."
- "`git add` puts files in the staging area."
- "`git commit` saves the staged content as a new commit in the local repository."
- "Always write a log message when committing changes."
---

Change your working directory to the `cats-as-data` directory you created
in the [setup section]({{ page.root }}/setup), and initialize a git repository
in it:

~~~
$ cd ~/Desktop/cats-as-data # or wherever you unzipped it
$ git init
~~~
{: .bash}

If we use `ls` to show the directory's contents, it appears that nothing has
changed.  But if we add the `-a` flag to show everything, we can see that Git
has created a hidden directory called `.git`:

~~~
$ ls -a
~~~
{: .bash}

~~~
.  ..  cats-human-situations.csv  cats-human-situations.md  .git  .gitignore  images  README.md
~~~
{: .output}

Git stores information about the project in this special sub-directory.  If we
ever delete it, we will lose the project's history.

We can check that everything is set up correctly by asking Git to tell us the
status of our project:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        .gitignore
        README.md
        cats-human-situations.csv
        cats-human-situations.md
        images/

nothing added to commit but untracked files present (use "git add" to track)
~~~
{: .output}

The "untracked files" message means that there are files in the directory that
Git isn't keeping track of.  We can tell Git to track a file using `git add`:

~~~
$ git add README.md
~~~
{: .bash}

and then check that the right thing happened:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        .gitignore
        cats-human-situations.csv
        cats-human-situations.md
        images/
~~~
{: .output}

Git now knows that it's supposed to keep track of `README.md`, but we want it to
track the associated metadata as well:

~~~
$ git add cats-human-situations.csv
~~~
{: .bash}

(You can add multiple files at the same time, e.g. `git add README.md
cats-human-situations.csv`.  You can even do `git add -A` to add everything in
the directory!)

Now we’ve let Git know to start tracking those two files, but we haven’t
recorded these changes as a commit yet.  To get it to do that, we need to run
one more command:

~~~
$ git commit -m "add notes on metadata, and the metadata"
~~~
{: .bash}

~~~
[master (root-commit) e46e6eb] add notes on metadata, and the metadata
 2 files changed, 9 insertions(+)
 create mode 100644 README.md
 create mode 100644 cats-human-situations.csv
~~~
{: .output}

When we run `git commit`, Git takes everything we have told it to save
by using `git add` and stores a copy permanently inside the special
`.git` directory.  This permanent copy is called a [commit]({{
page.root }}/reference/#commit) (or [revision]({{ page.root
}}/reference/#revision)) and its short identifier is `c118dee`.  Your
commit may have another identifier; as to how this identifier is
chosen, we’ll get to that in a minute.

We use the `-m` flag (for "message") to record a short, descriptive,
and specific comment that will help us remember later on what we did
and why.  If we just run `git commit` without the `-m` option, Git
will launch `nano` (or whatever other editor we configured as
`core.editor`) so that we can write a longer message.

[Good commit messages][commit-messages] start with a brief (<50
characters) summary of changes made in the commit.  If you want to go
into more detail, add a blank line between the summary line and your
additional notes.

If we want to know what we've done recently, we can ask Git to show us the
project's history using `git log`:

~~~
$ git log
~~~
{: .bash}

~~~
commit e46e6eb89b9b4c7d95e9ea21337d9b29f78930b8 (HEAD -> master)
Author: Catsy Cline <ccline@pawson.edu>
Date:   Sun Nov 5 14:40:08 2017 -0600

    add notes on metadata, and the metadata
~~~
{: .output}

`git log` lists all commits made to a repository in reverse
chronological order.  The listing for each commit includes the
commit's full identifier (which starts with the same characters as the
short identifier printed by the `git commit` command earlier), the
commit's author, when it was created, and the log message Git was
given when the commit was created.

> ## Where Are My Changes?
>
> If we run `ls` or `dir` at this point, we will still see just the
> same files we started with.  Where are the versions that Git is
> keeping track of?
>
> Git saves information about files' history in the special `.git`
> directory mentioned earlier so that our filesystem doesn't become
> cluttered (and so that we can't accidentally edit or delete an old
> version).
> {: .callout}

Now suppose we add more information to the file.  Type the text below
at the end of the `README.md` file:

~~~
The images are in the `images/` subdirectory of this repository.
~~~
{: .output}

Your `README.md` file should now look like:

~~~
# about your new image collection

The `cats-human-situations.csv` file contains metadata for three image objects.
The original metadata from the source institutions has been abbreviated and made
messier so you have something to clean up!

The images are in the `images/` subdirectory of this repository.
~~~
{: .output}

When we run `git status` now, it tells us that a file it already knows
about has been modified:

~~~
$ git status
~~~
{: .bash}

~~~
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        .gitignore
        cats-human-situations.md
        images/

no changes added to commit (use "git add" and/or "git commit -a")
~~~
{: .output}

The last line is the key phrase: "no changes added to commit".  We
have changed this file, but we haven't told Git we will want to save
those changes (which we do with `git add`) nor have we saved them
(which we do with `git commit`).  So let's do that now. It is good
practice to always review our changes before saving them. We do this
using `git diff`.  This shows us the differences between the current
state of the file and the most recently saved version:

~~~
$ git diff
~~~
{: .bash}

~~~
diff --git a/README.md b/README.md
index f2a73f2..06aa594 100644
--- a/README.md
+++ b/README.md
@@ -3,3 +3,5 @@
 The `cats-human-situations.csv` file contains metadata for three image objects.
 The original metadata from the source institutions has been abbreviated and made
 messier so you have something to clean up!
+
+The images are in the `images/` subdirectory of this repository.
~~~
{: .output}

The output is cryptic because it is actually a series of
machine-readable commands for modifying one version of a file
(`a/README.md`) and producing another (`b/README.md`).  If we break it
down into pieces:

1.  The first line tells us that Git is producing output similar to
    the Unix `diff` command comparing the old and new versions of the
    file.
2.  The second line tells exactly which versions of the file Git is
    comparing; `f2a73f2` and `06aa594` are unique identifiers for
    those versions.
3.  The third and fourth lines once again show the name of the file
    being changed.
4.  The remaining lines are the most interesting, they show us the
    actual differences and the lines on which they occur.  In
    particular, the `+` marker in the first column shows where we
    added a line.  If we had instead removed lines, there would be `-`
    markers instead.

Because the output of `git diff` is a representation of the difference
between two files, it’s called (unsurprisingly) a diff.

After reviewing our change, it's time to commit it:

~~~
$ git add README.md
$ git commit -m "readme: where are the files?"
~~~
{: .bash}

> ## Terminology: diff vs. patch vs. commit
>
> What the version control system is concerned with is changes between states (of documents, images, etc.)
>
> - **diff** – a diff, broadly speaking, is a representation of the
>   difference between two states.
>
> - **patch** – a patch is just a diff with some metadata attached, such
>   as the author of the change and the time of authorship.  You can
>   generate something like the below with `git format-patch HEAD~1..HEAD`:
>
>     ~~~
>     From 2199d61adc2123fe163f4103d1dec4df46ee76bb Mon Sep 17 00:00:00 2001
>     From: Catsy Cline <ccline@pawson.edu>
>     Date: Sun, 5 Nov 2017 14:44:03 -0600
>     Subject: [PATCH] readme: where are the files?
>
>     ---
>      README.md | 2 ++
>      1 file changed, 2 insertions(+)
>
>     diff --git a/README.md b/README.md
>     index f2a73f2..06aa594 100644
>     --- a/README.md
>     +++ b/README.md
>     @@ -3,3 +3,5 @@
>      The `cats-human-situations.csv` file contains metadata for three image objects.
>      The original metadata from the source institutions has been abbreviated and made
>      messier so you have something to clean up!
>     +
>     +The images are in the `images/` subdirectory of this repository.
>     --
>     2.15.0
>     ~~~
>
> - **commit** – a commit is a patch that is managed by a version
>   control system.
>
> Viewed in this light, Git is just a tool for managing patches.  And
> the mysterious identifiers we’ve been seeing are actually [SHA1
> checksums of the patch headers!](https://gist.github.com/masak/2415865)
{: .callout}

You might be wondering why Git insists that we add files to the set we
want to commit before actually committing anything. This it because it
allows us to commit our changes in stages and capture changes in
logical portions rather than only large batches.  For example, suppose
we're adding a few citations to our supervisor's work to our thesis.
We might want to commit those additions, and the corresponding
addition to the bibliography, but *not* commit the work we're doing on
the conclusion (which we haven't finished yet).

To allow for this, Git has a special *staging area* where it keeps
track of things that have been added to the current [changeset]({{
page.root }}/reference/#changeset) but not yet committed.

> ## Staging Area
>
> If you think of Git as taking snapshots of changes over the life of
> a project, `git add` specifies *what* will go in a snapshot (putting
> things in the staging area), and `git commit` then *actually takes*
> the snapshot, and makes a permanent record of it (as a commit).  If
> you don't have anything staged when you type `git commit`, Git will
> prompt you to use `git commit -a` or `git commit --all`, which is
> kind of like gathering *everyone* for the picture!  However, it's
> almost always better to explicitly add things to the staging area,
> because you might commit changes you forgot you made. (Going back to
> snapshots, you might get the extra with incomplete makeup walking on
> the stage for the snapshot because you used `-a`!)  Try to stage
> things manually, or you might find yourself searching for "git undo
> commit" more than you would like!
{: .callout}

![The Git Staging Area](../fig/git-staging-area.svg)

Let's watch as our changes to a file move from our editor to the staging area
and into long-term storage.  First, we'll add more files to our repository:

~~~
$ git add images
$ git diff
~~~
{: .bash}

There is no output: as far as Git can tell, there's no difference
between what it's been asked to save permanently and what's currently
in the directory.  However, if we run `git diff --staged`, we’ll see
something interesting:

~~~
$ git diff --staged
diff --git a/images/00064183.jpg b/images/00064183.jpg
new file mode 100644
index 0000000..111d4ec
Binary files /dev/null and b/images/00064183.jpg differ
diff --git a/images/12_04_000583.jpg b/images/12_04_000583.jpg
new file mode 100644
index 0000000..6d6a306
Binary files /dev/null and b/images/12_04_000583.jpg differ
diff --git a/images/822811.jpg b/images/822811.jpg
new file mode 100644
index 0000000..0b8dd21
Binary files /dev/null and b/images/822811.jpg differ
~~~
{: .bash}

Since git is a command-line application, it doesn’t have native support for
visualizing the difference between binary files like images.  There are
[applications](https://kaleidoscopeapp.com/) that can show diffs of images, and
[GitHub can diff
images](https://help.github.com/articles/rendering-and-diffing-images/), but for
now we’ll be satisfied with seeing that we are adding binary data to our
repository.

~~~
$ git commit -m "add pictures of cats"
~~~
{: .bash}

Now we can look at the history of what we've done so far:

~~~
$ git log
~~~
{: .bash}

~~~
commit bce6b419c7e1bc785447fe19b89d4153c2d96366 (HEAD -> master)
Author: Catsy Cline <ccline@pawson.edu>
Date:   Sun Nov 5 14:45:37 2017 -0600

    add pictures of cats

commit 2199d61adc2123fe163f4103d1dec4df46ee76bb
Author: Catsy Cline <ccline@pawson.edu>
Date:   Sun Nov 5 14:44:03 2017 -0600

    readme: where are the files?

commit e46e6eb89b9b4c7d95e9ea21337d9b29f78930b8
Author: Catsy Cline <ccline@pawson.edu>
Date:   Sun Nov 5 14:40:08 2017 -0600

    add notes on metadata, and the metadata
~~~
{: .output}

> ## Directories
>
> Two important facts you should know about directories in Git.
>
> 1. Git does not track directories on their own, only files within them.
> Try it for yourself:
>
> ~~~
> $ mkdir directory
> $ git status
> $ git add directory
> $ git status
> ~~~
> {: .bash}
>
> Note, our newly created empty directory `directory` does not appear in
> the list of untracked files even if we explicitly add it (_via_ `git add`) to our
> repository. This is the reason why you will sometimes see `.keep` files
> in otherwise empty directories. Unlike `.gitignore`, these files are not special
> and their sole purpose is to populate a directory so that Git adds it to
> the repository. In fact, you can name such files anything you like.
>
> {:start="2"}
> 2. If you create a directory in your Git repository and populate it with files,
> you can add all files in the directory at once by:
>
> ~~~
> git add <directory-with-files>
> ~~~
> {: .bash}
>
{: .callout}

To recap, when we want to add changes to our repository,
we first need to add the changed files to the staging area
(`git add`) and then commit the staged changes to the
repository (`git commit`):

![The Git Commit Workflow](../fig/git-committing.svg)

[commit-messages]: http://chris.beams.io/posts/git-commit/
