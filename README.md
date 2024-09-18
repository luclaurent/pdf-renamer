# pdf-renamer
pdf-renamer is a Python command-line tool to automatically rename the pdf files of a scientific paper, or in general any publication which can be associated to a [DOI](http://dx.doi.org) or
other identifiers (e.g. [arXiv](https://arxiv.org)). It can be used to rename single files or to scan entire folders and sub-folders.
The format of the filename can be specified by the user by choosing among several tags. Besides command-line operation, it can also be used as a library
from your Python project. 

[![Downloads](https://pepy.tech/badge/pdf-renamer)](https://pepy.tech/project/pdf-renamer)[![Downloads](https://pepy.tech/badge/pdf-renamer/month)](https://pepy.tech/project/pdf-renamer)
[![Pip Package](https://img.shields.io/pypi/v/pdf-renamer?logo=PyPI)](https://pypi.org/project/pdf-renamer?versions=1.0?versions=1.1)

## Warning
```pdf-renamer``` uses ```pdf2doi``` to find the DOI of a paper. Versions of ```pdf2doi``` prior to the **1.6** are affected by a very annoying bug. By default, after finding the DOI of a pdf paper, ```pdf2doi``` will store the DOI into the metadata of the pdf file. Due to a bug, the size of the pdf file doubles everytime that a metadata was added. This bug has been fixed in all versions of ```pdf2doi``` >= 1.6. 

If you have pdf files that have been affected by this bug, you can use ```pdf2doi``` to fix it. After updating ```pdf2doi``` to a version >= 1.6, run ```pdf2doi path/to/folder/containing/pdf/files -id ''```. This will restore the pdf files to their original size.


## Latest stable version
The latest stable version of ```pdf-renamer``` is the **1.1**. See [here](https://github.com/MicheleCotrufo/pdf-renamer/releases) for the full change log.

## Table of Contents
- [pdf-renamer](#pdf-renamer)
  - [Warning](#warning)
  - [Latest stable version](#latest-stable-version)
  - [Table of Contents](#table-of-contents)
  - [Installation](#installation)
  - [Description](#description)
  - [Usage](#usage)
  - [Contributing](#contributing)
  - [License](#license)

## Installation
Use the package manager pip to install ```pdf-renamer```.

```bash
pip install pdf-renamer==1.1
```
This will install ```pdf-renamer``` as  Python package, but also as a stand-alone executable script. 
The executable will be installed in a directory whose path depends on your Python installation and operating system. 
Make sure that this directory is added to the ```PATH``` variable of your operating system (for standard Python installations under Windows this should be already the case). 
You can check how to add the folder to the ```PATH``` variable for [Windows](https://www.google.com/search?q=python+add+script+folder+to+path+windows), 
[Mac](https://www.google.com/search?q=python+add+script+folder+to+path+mac) and [Linux](https://www.google.com/search?q=python+add+script+folder+to+path+linux).

Under Windows, it is also possible to add [shortcuts to the right-click context menu](#installing-the-shortcuts-in-the-right-click-context-menu-of-windows).

## Description
```pdf-renamer``` uses the libraries [pdf2doi](https://github.com/MicheleCotrufo/pdf2doi) and [pdf2bib](https://github.com/MicheleCotrufo/pdf2bib) to extract 
bibliographic data of a paper starting from a .pdf file. The retrieved data can then be used to automatically rename pdf files with a custom format (e.g. 'Year - Journal - Authors - Title').

## Usage

```pdf-renamer``` can be invoked directly from the command line, without having to open a python console.
The simplest command-line invokation is

```
$ pdfrenamer 'path/to/target'
```
where ```target``` is either a valid pdf file or a directory containing pdf files. ```pdf-renamer``` will automatically rename the file(s) in ```path/to/target``` 
(assuming that they are valid publications for which a DOI/arXiv ID can be found), by using the standard settings.

A list of the standard settings, and additional commands, can be obtained by typing ```pdfrenamer --h```

```
$ pdfrenamer --h
usage: pdfrenamer [-h] [-s] [-ro] [-f FORMAT] [-sf] [-max_length_authors MAX_LENGTH_AUTHORS]
                  [-max_length_filename MAX_LENGTH_FILENAME] [-max_words_title MAX_WORDS_TITLE] [-case CASE]
                  [-add_abbreviation_file PATH_ABBREVIATION_FILE] [-fr] [-sd] [-install--right--click]
                  [-uninstall--right--click]
                  [path ...]

Automatically renames pdf files of scientific publications by retrieving their identifiers (e.g. DOI or arxiv ID) and looking up their bibtex infos.

positional arguments:
  path                  Relative path of the pdf file or of a folder.

options:
  -h, --help            show this help message and exit
  -s, --decrease_verbose
                        Decrease verbosity. By default (i.e. when not using -s), all steps performed by pdf-renamer, pdf2dbib and pdf2doi are documented.
  -ro, --readonly       By default, pdf-renamer and pdf2doi store some information into the metadata of the pdf file in order to speed up subsequent processing. By using this additional option, no metadata is ever added.
  -f FORMAT             Format of the new filename. Default = "{YYYY} - {Jabbr} - {A3etal} - {T}".
                        Valid tags:
                        {YYYY}          =        Year of publication
                        {MM}            =        Month of publication (in digits)
                        {DD}            =        Day of publication (in digits)
                        {J}             =        Full name of Journal
                        {Jabbr}         =        Abbreviated name of Journal, if any available (otherwise full name is used)
                        {Aall}          =        Last name of all authors (separated by comma)
                        {Aetal}         =        Last name of the first author, add 'et al.' if more authors are present
                        {A3etal}        =        Last name of the first three authors (separated by comma), add 'et al.' if more authors are present
                        {A}             =        Last name of the first author
                        {A3}            =        Last name of the first three authors (separated by comma)
                        {aAall}         =        First initial and last name of all authors (separated by comma)
                        {aAetal}        =        First initial and last name of the first author, add 'et al.' if more authors are present
                        {aA3etal}       =        First initial and last name of the first three authors (separated by comma), add 'et al.' if more authors are present
                        {aA}            =        First initial and last name of the first author
                        {aA3}           =        First initial and last name of the first three authors (separated by comma)
                        {T}             =        Title
  -sf, --sub_folders    Rename also pdf files contained in subfolders of target folder. Default = "False".
  -max_length_authors MAX_LENGTH_AUTHORS
                        Sets the maximum length of any string related to authors (default=80).
  -max_length_filename MAX_LENGTH_FILENAME
                        Sets the maximum length of any generated filename. Any filename longer than this will be truncated (default=250).
  -max_words_title MAX_WORDS_TITLE
                        Sets the maximum number of words from the paper title to use for the filename (default=20).
  -case CASE            Possible values are 'camel', 'snake', 'kebab', 'none' (default=none).
                        If different from 'none', converts each tag string into either 'camel' (e.g., LoremIpsumDolorSitAmet), 'snake' (e.g., Lorem_ipsum_dolor_sit_amet), or 'kebab' case (e.g., Lorem-ipsum-dolor-sit-amet).
                        Note: this will not affect any punctuation symbol or space contained in the filename format by the user.
  -add_abbreviation_file PATH_ABBREVIATION_FILE
                        The content of the text file specified by PATH_ABBREVIATION_FILE will be added to the user list of journal abbreviations.
                        Each row of the text file must have the format 'FULL NAME = ABBREVIATION'.
  -fr, --force_rename   By default, whenever pdf-renamer renames a pdf file by using a certain filename format, it also stores the format string into a metadata of the pdf file.
                        In this way, if pdf-renamer comes across that same file later, and the current filename format is the same as the one stored in the pdf file metadata,
                        the file is ignored. By adding the command '-fr', this behavior is overruled: pdf-renamer will always rename each file it comes across.
  -sd, --set_default    By adding this command, any value specified (in this same command) for the filename format (-f),
                        max length of author string (-max_length_authors), max length of filename string (-max_length_filename),
                        max number of title words (-max_words_title), and case (-case) will be also stored as default value(s) for the future.
  -install--right--click
                        Add a shortcut to pdf-renamer in the right-click context menu of Windows. You can rename a single pdf file (or all pdf files in a folder) by just right clicking on it!
                        NOTE: this feature is only available on Windows, and it needs to be run from a terminal opened with administrator rights.
  -uninstall--right--click
                        Uninstall the right-click context menu functionalities. NOTE: this feature is only available on Windows, and it needs to be run from a terminal opened with administrator rights.
```
Several tags (as listed above) can be used to change the standard filename format, for example,
```
$ pdfrenamer 'path/to/target' -f "{YYYY} - {Aetal} - {J} - {T}"
```
will produce filenames which start with the year of publication, followed by first initial and full last name of first author + et al. (if more authors are present), followed by the full
journal name and the paper title.  Note that **the tags are case sensitive**.
Other useful settings are `-max_length_authors`, `-max_length_filename` and `-max_words_title`, which set, respectively, the maximum number of characters allowed for the author string and for the overall filename, and the maximum nunber of words that will be used from the title.
The optional command  `-case CASE` can be used to convert all substrings (such as title, journal, etc.) to either the camel, snake or kebab case. The values of all these settings can be specified simultaneously, e.g.
```
$ pdfrenamer 'path/to/target' -f "{YYYY} - {Aetal} - {J} - {T}" -max_length_authors 40 -max_length_filename 200 -max_words_title 20 -case snake
```
The values set for most parameters, however, are not permanently changed, unless the optional command ```-sd``` (set default) is added,
```
$ pdfrenamer 'path/to/target' -f "{YYYY} - {Aetal} - {J} - {T}" -max_length_authors 40 -max_length_filename 200 -max_words_title 20  -case snake -sd
```
In this case the new values are saved in a settings.ini file inside the ```pdf-renamer``` folder (as can be checked by typing ```pdfrenamer --h``` again).



## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.


## License
[MIT](https://choosealicense.com/licenses/mit/)
