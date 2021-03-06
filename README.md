# What is MediaWiki?

MediaWiki is a free and open-source wiki app, used to power wiki websites such
as Wikipedia, Wiktionary and Commons, developed by the Wikimedia Foundation and
others.

> [wikipedia.org/wiki/MediaWiki](https://en.wikipedia.org/wiki/MediaWiki)

## Versions

- `1.28-alpine` - Built on Alpine linux running PHP 5.6
- `1.28-php` - Built on the official PHP 7.0 apache image

## Repo for Docker Files

https://github.com/lfkeitel/mediawiki-docker

## How to use this image

    docker run --name some-mediawiki --link some-mysql:mysql -d lfkeitel/mediawiki

The following environment variables are also honored for configuring your MediaWiki instance:

- `-e MEDIAWIKI_DB_HOST=ADDR:PORT` (defaults to the address and port of the linked mysql container)
- `-e MEDIAWIKI_DB_USER=...` (defaults to "root")
- `-e MEDIAWIKI_DB_PASSWORD=...` (defaults to the value of the `MYSQL_ROOT_PASSWORD` environment variable from the linked mysql container)
- `-e MEDIAWIKI_DB_NAME=...` (defaults to "mediawiki")

If the `MEDIAWIKI_DB_NAME` specified does not already exist in the given MySQL container, it will be created automatically upon container startup, provided that the `MEDIAWIKI_DB_USER` specified has the necessary permissions to create it.

To use with an external database server, use `MEDIAWIKI_DB_HOST` (along with `MEDIAWIKI_DB_USER` and `MEDIAWIKI_DB_PASSWORD` if necessary):

    docker run --name some-mediawiki -e MEDIAWIKI_DB_HOST=10.0.0.1:3306 \
        -e MEDIAWIKI_DB_USER=app -e MEDIAWIKI_DB_PASSWORD=secure lfkeitel/mediawiki

If you'd like to be able to access the instance from the host without the container's IP, standard port mappings can be used:

    docker run --name some-mediawiki --link some-mysql:mysql -p 8080:80 -d lfkeitel/mediawiki

Then, access it via `http://localhost:8080` or `http://host-ip:8080` in a browser.