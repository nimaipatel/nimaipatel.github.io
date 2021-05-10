# Nimai's personal website

This is my static blog generated using a modified version of the amazing 
[makesite ](http://github.com/sunainapai/makesite) python script. The website currently 
uses zero Javascript and that is how I plan on keeping it.

## Build

The website can be built by running `make site`. The original Makefile had a `serve`
target to host the static site in `_site/` using a python server. 

I have additionally added a `live` target which watches for changes on files
in the `content/`, `static/` and `layout/` folders. it will automatically run `make site` and
live reload in the browser too.

The live target depends on [entr](http://eradman.com/entrproject/) and 
[livereload](https://pypi.org/project/livereload/).

The script `make live` runs:

```
find content/ layout/ static/ | entr sh -c 'make site'&	livereload _site
```