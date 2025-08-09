# Term Frequency - Inverse Document Frequency

[Term Frequency-Inverse Document Frequency](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)
for a [Hugo](https://gohugo.io/)
blog, implemented in shell scripts.

## Running

I wrote it to work in three phases,
because some of the calculations end up very time consuming.
I didn't want to repeat them every time I tried something new.

These scripts assume your current working directory contains
the scripts.
The scripts create directories and files in place with themselves.

### Phase 1: generate plain text from Hugo markdown documents.

```
$ ./create_plaintexts $BLOGDIR
```

This leaves you with a directory `plaintext/` and a file `published`.
`plaintext/` has nearly non-markdown UTF-8 encoded files full of text.
`published` is a list of **published** post filenames, from `$BLOGDIR`.
The files in `plaintext/` have the basenames of the blog post markdown format
files, so that you can see where `pandoc` had a problem.

If you delete file `published`,
the script `create_plaintexts` recreates it,
and starts over from scratch.
If you modify the contents of file `published`,
`create_plaintexts` will start from the first blog post markdown file
in `published`.
You can recover from some errors this way.

Transforming Hugo markdown to plaintext took about 2 minutes
for 350 blog posts on my Dell E7470 laptop.

I did discover some markdown issues (missing URLs in links mostly) during this process.

### Phase 2: generate term counts from plain text files

```
$ ./count_terms
```

Shell script `count_terms` uses the plaintext files from Phase 1
to make files in directory `counts/`, with filenames that are the basenames
of the files in `plaintext/`.
The files in `counts/` have alphabetized term and count of times that term
appears in the plain text file.

Script `count_terms` does use a file `/usr/share/groff/current/eign`
for [stop words], very common words like "a", "the", "of",
which you probably don't want to include in further calculations.
It's boring to note that "the" is the single most common word in any
English text.

The file `/usr/share/groff/current/eign` comes with [GNU Groff](https://www.gnu.org/software/groff/).

### Phase 3: calculate term frequency, inverse document frequency, tf-idf

```
$ ./term_calcs
```

Resulting files: `document.counts`, `term.idf`,
files in directories `frequency/` and `tf-idf/`.

File `document.counts` contains the total count of terms in all documents.
File `term.idf` contains inverse document frequency for each term.

Directory `frequency` has the term frequencies for the original
markdown post in each file.

Directory `tf-idf` has the TF-IDF statistic for each term
in the original document.
Files in `tf-idf/` are sorted numerically
so the highest TF-IDF scores are at the beginning of the file.

#### Executables used

I decided I could do all the TF-IDF work in shell scripts.
I did all my work with a reasonably up-to-date Arch Linux install,
but just to be sure, here's the executables the scripts invoke:

- `awk`
- `basename`
- `cat`
- `cut`
- `find`
- `grep` (with -F, so `fgrep`)
- `join`
- `mkdir`
- `pandoc`
- `rm`
- `sed`
- `sort`
- `tr`
- `uniq`
- `wc`
- `xargs`

It's August 2025 as I write this, we are all transitioning
from `fgrep` to `grep -F`.
[pandoc](https://pandoc.org/) is the only unusual executable installed.
The script does use `/usr/share/groff/current/eign`
which might require the installation of `groff`.
