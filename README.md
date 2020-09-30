# OSKR Owner's Manual

The OSKR Owner's Manual is generated via the [Sphinx Documentation
Generator](https://www.sphinx-doc.org/en/master/index.html).

Most people will want to view the documents directly online at:

* HTML
* PDF

This repository is only of use if you wish to modify the document,
create additional content, etc.

Docs can be viewed online by clicking on the [developer
directory](./developer), viewed on your local machine, or
built out in to pretty html or pdf documents.

To install:

    virtualenv -p python3 ./oom
    source ./oom/bin/activate
    pip install -r requirements.txt

To build locally:

    source ./bin/activate # if virtualenv hasn't been activated yet
    make html

To genreate PDFs on OSX:

    brew cask install mactex-no-gui # one time install
    make latexpdf

To read locally in your browser:

    open _build/html/index.html

To read the pdf locally:

    open _build/latex/oom.pdf
