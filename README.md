# NAME

Catmandu::Plack::unAPI - unAPI webservice based on Catmandu

# STATUS

[![Build Status](https://travis-ci.org/gbv/Catmandu-Plack-unAPI.png)](https://travis-ci.org/gbv/Catmandu-Plack-unAPI)
[![Coverage Status](https://coveralls.io/repos/gbv/Catmandu-Plack-unAPI/badge.png?branch=devel)](https://coveralls.io/r/gbv/Catmandu-Plack-unAPI?branch=devel)
[![Kwalitee Score](http://cpants.cpanauthors.org/dist/Catmandu-Plack-unAPI.png)](http://cpants.cpanauthors.org/dist/Catmandu-Plack-unAPI)

# DESCRIPTION

Catmandu::Plack::unAPI implements an unAPI web service as PSGI application.

# SYNOPSIS

Set up an `app.psgi` for instance to get data via [http://arxiv.org](http://arxiv.org)
identifier using [Catmandu::Importer::ArXiv](https://metacpan.org/pod/Catmandu::Importer::ArXiv):

    use Catmandu::Plack::unAPI;
    use Catmandu::Importer::ArXiv;

    Catmandu::Plack::unAPI->new(
        query => sub {
            my ($id) = @_;
            return if $id !~ qr{^(arXiv:)?[0-9abc/.]+}i;
            Catmandu::Importer::ArXiv->new( id => $id )->first;
        }
    )->to_app;

Retrieving items from a [Catmandu::Store](https://metacpan.org/pod/Catmandu::Store) is even simpler:

    Catmandu::Plack::unAPI->new( store => $store )->to_app;

Start the application, e.g. with `plackup app.psgi` and query via unAPI:

    curl 'localhost:5000'
    curl 'localhost:5000?id=1204.0492&format=json'

# CONFIGURATION

- query

    Code reference with a query method to get an item (as reference) by a given
    identifier (HTTP request parameter `id`). If the method returns undef, the
    application returns HTTP 404. If the methods returns a scalar, it is used as
    error message for HTTP response 400 (Bad Request).

- store

    Instance of [Catmandu::Store](https://metacpan.org/pod/Catmandu::Store) or store name and options as array reference to
    query items from.

- formats

    Hash reference with format names mapped to MIME type, [Catmandu::Exporter](https://metacpan.org/pod/Catmandu::Exporter)
    configuration and (optional) documentation for each format. By default only
    JSON and YAML are configured as following:

        json => {
            type     => 'application/json',
            exporter => [ 'JSON', pretty => 1 ],
            docs     => 'http://json.org/'
        },
        yaml => {
            type     => 'text/yaml',
            exporter => [ 'YAML' ],
            docs     => 'http://en.wikipedia.org/wiki/YAML'
        }

# LIMITATIONS

An exporter is instanciated for each request, so performance may be low
depending on configuration.

The error response is always `text/plain`, this may be configurable in a
future release.

Timeouts are not implemented yet.

# AUTHOR

Jakob Voß <jakob.voss@gbv.de>

# COPYRIGHT AND LICENSE

Copyright 2014- Jakob Voß

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.

# SEE ALSO

- [http://unapi.info/](http://unapi.info/)
- [Catmandu::Plack::REST](https://metacpan.org/pod/Catmandu::Plack::REST)
