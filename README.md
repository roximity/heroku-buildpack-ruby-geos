# Heroku Buildpack Ruby Geos

This buildpack was designed to support the [shareabouts](https://github.com/openplans/shareabouts)
application. Specifically, it vendors [geos](http://trac.osgeo.org/geos/) libs
and bins.


## Usage

```bash
$ heroku create --stack cedar --buildpack http://github.com/ryandotsmith/heroku-buildpack-ruby-geos.git
```
