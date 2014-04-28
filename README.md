logstash-recipes
================

Recipes and configs for Logstash on Debian and family.

Feel free to add to my collection and send me pull requests.

Version assumptions:

* Logstash: 1.4
* Elasticsearch: 1.0.1
* Redis: 2.8.9
* Apache: 2.4*


>\* The Apache configs can be modified to work with 2.2. 2.4 introduced a few different pragmas that don't work 2.2. Likewise, configs for 2.2 don't work with 2.4 unless modified. Kinda not cool Apache Group. See [Upgrading from 2.2 to 2.4](http://httpd.apache.org/docs/2.4/upgrading.html) for details.


# Files

The `server/` directory holds the configs for the various services `logstash`, `redis`, and `ElasticSearch`.

The `client/` directory includes some configs for reading logs for other services.

## Redis

Just grab from [http://redis.io/]() and `make && make install`.


### server/redis/redis-server

This `init.d/` config for redis drops privs after loading. Unfortunately the one that ships with redis doesn't. You'll want to `update-rc.d` the file once you drop it in.

You'll need to do the following:

~~~
adduser --no-create-home --disabled-login redis
mkdir -p /var/log/redis/
mkdir -p /var/run/redis/
chown -R redis:redis /var/log/redis/
chown -R redis:redis /var/run/redis/
chown -R redis:redis /var/lib/redis/
~~~

### server/redis/redis.conf

Nothing special here, just added for completeness.

## Logstash

I used the .deb from ES. If you do, you won't need the logstash-web it provides if you use my Apache configs for pointing at a self-installed Kibana. And you won't need my logstash-server as the .deb provides one.


### server/logstash/server.conf

A simple config for shipping to ES on the same box that logstash runs on. I put it in `/etc/logstash/conf.d/server.conf`

### server/logstash/logstash-server

An `init.d/` config for logstash.


### server/apache/logstash-apache.conf

Feel free to drop this in your `sites-available` and name it what you want. This is intended to indirectly expose ES through a proxy so you can control access appropriately.

I set mine to allow access only from 127.0.0.1 as I SSH tunnel my traffic. This gets you authentication and encryption for free, but feel free to change for your environment.

### client/logstash/shipper.conf

I've included a couple of examples. For virtual hosting on Apache, it's useful to know which website the logs are for, so I `add_field` a `host_header` for each website. This makes it easy to search in Kibana.

### server/elasticsearch/elasticsearch.yml

I installed ES using their .deb file and made some changes to `/etc/elasticsearch/elasticsearch.yml` to disable discovery and allow only localhost communication. Also included is `logging.yml`.
