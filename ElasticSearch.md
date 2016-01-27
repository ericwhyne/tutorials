# Elasticsearch
Elasticsearch is a scalable, full-text search engine with real-time analytics and search features. It provides a RESTful API for indexing and querying. It's document-oriented/based and can store documents as JSON. This makes it powerful, simple and flexible.

It is built on top of Apache Lucene; by default, it uses port `9200` `+1` per node.

# Quickstart

## Download
https://www.elastic.co/downloads/elasticsearch

Install prerequisites:

    yum install -y epel-release && yum install -y java-1.8.0-openjdk-headless net-tools jq

Set the proper location of `JAVA_HOME` and test it:

    echo export JAVA_HOME=\"$(readlink -f $(which java) | grep -oP '.*(?=/bin)')\" >> /root/.bash_profile
    source /root/.bash_profile
    $JAVA_HOME/bin/java -version

Download the Elasticsearch tarball (from https://www.elastic.co/downloads/elasticsearch):

    curl -OL https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.7.0.tar.gz

Note: Newer versions of elasticsearch are out, if using in production download one of those. The features in this quickstart shouldn't have changed much.

To extract the tarball:

    tar xzf elasticsearch-1.7.0.tar.gz

Run the elasticsearch-file inside the ./bin/ folder:

    cd elasticsearch-1.7.0
    nohup ./bin/elasticsearch &

Note: When running in production, the rpm version of ES includes an init script that can be used to manage the processs. This is preferred.

This will start a master node on port `9200`. You can check that the service is listening with the command `netstat -tnlp`. If you need to investigate a problem, check the output written to the file `nohup.out`. Browse to your public address to see the search engine in action. `curl` works as well:

    curl -X GET http://localhost:9200

You should see output like this:

    {
      "status" : 200,
      "name" : "Polaris",
      "cluster_name" : "elasticsearch",
      "version" : {
        "number" : "1.7.0",
        "build_hash" : "929b9739cae115e73c346cb5f9a6f24ba735a743",
        "build_timestamp" : "2015-07-16T14:31:07Z",
        "build_snapshot" : false,
        "lucene_version" : "4.10.4"
      },
      "tagline" : "You Know, for Search"
    }

Then to do a simple search, you can use the "_search" request built into ElasticSearch, like this:

    curl -X GET http://localhost:9200/_search?q=test

For prettier output, run the `curl` command and pipe the output to `jq`:

    curl -X GET http://localhost:9200/_search?q=test | jq -r '.'

Note the total number of documents matching the query (hits -> total) in the response. For more information on the format of the response, see https://www.elastic.co/guide/en/elasticsearch/reference/current/_the_search_api.html.

To index new documents you can use the HTTP POST method. For more information on indexing records with the API, consult https://www.elastic.co/guide/en/elasticsearch/reference/current/.

Try inserting a new document this way:

    curl -X POST 'http://localhost:9200/artist/bieber' -d '{
            "talent" : 5,
            "best_song.release_year": 2010,
            "best_song.title": "Eenie Meenie"
    }'

... and then a few more:

    curl -X POST 'http://localhost:9200/artist/plant' -d '{
            "talent": 90,
            "best_song.release_year": 1970,
            "best_song.title": "Bron-Y-Aur Stomp"
    }'

...

    curl -X POST 'http://localhost:9200/artist/bluhm' -d '{
            "talent": 91,
            "best_song.release_year": 2013,
            "best_song.title": "Little too Late"
    }'

Elasticsearch will respond with a success message in JSON if the document was indexed. Documents can be retrieved withsearch requests:

    curl -X GET http://localhost:9200/artist/_search

You can also specify a query and output options:

    curl -X GET 'http://localhost:9200/artist/_search?q=talent:\[90+TO+100\]&pretty'

Note how queries issued without specified field names are handled:

    curl -X GET 'http://localhost:9200/artist/_search?q=Stomp&pretty'

## Further reading

There are many graphical frontends built on top of the ES API:
- https://www.elastic.co/products/kibana
- https://github.com/jettro/elasticsearch-gui.

Cluster mode is a slightly different game, but ES does parallilze gracefully and easily.
