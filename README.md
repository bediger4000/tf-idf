# Term Frequency - Inverse Document Frequency

[Term Frequency-Inverse Document Frequency](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)
for a [Hugo](https://gohugo.io/)
blog, implemented in shell scripts.

## Running

Three phases, because some of the calculations end up very time consuming.

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
$ ./count_terms
```

Resulting files: `term.counts`, `term.freqs`, `document.counts`, `term.idf`, `tf-idf`.

Files `term.counts`, `document.counts` have the total count of terms in all documents,
and the count of documents each term appears in respectively.

Files `term.freqs` is each term's count divided  by the number of terms total.
`term.idf` has the individual terms inverse document frequence, ln(1/n<sub>t</sub>).

File `tf-idf` has the TF-IDF statistic for each term in the blog.
This is wrong, the term frequency is supposed to count per document, not overall.
