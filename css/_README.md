Currently LESS compilation isn't part of the jekyll build process. If you
make style changes, run a local less compiler to generate a single style.css
file in this directory from the files in the \_less directory. 

Example:

    /usr/local/bin/lessc _less/style.less css/style.css