This repository contains the documentation and code for the Bonsai project backend data storage. It is a work in progress, with deliverables aimed for March 29th 2019 as part of the Bonsai Hackathon.

The goal is to establish a maintainable and expandable core triplestore, filled with preliminary data showing application examples of the core Bonsai data.


# About Bonsai's use of Apache Jena
Apache Jena [is a framework](https://jena.apache.org/getting_started/index.html). It uses the TDB storage engine by default, which is [available in version 1 or 2](https://jena.apache.org/documentation/tdb/index.html). As performance is not a hackathon-deliverable, the standard/stable version 1 is selected for the initial prototype.

Our [Fuzeki SPARQL server](https://jena.apache.org/documentation/fuseki2/) is available now at https://db.bonsai.uno/ (credentials needed - ask Tom for now).

There is also the Aalborg University-hosted instance at https://odas.aau.dk/fuseki/. After the hackathon, we will use this, to reduce management and financial requirements on the Bonsai contributors (Thanks Aalborg IT team!)

# How users can get started
See [the repo Issues](https://github.com/BONSAMURAIS/triple-store/issues)

# Where users can get help with your project
[The triple-store channel](https://bonsai-open.slack.com/messages/CH9MHJL2K) within the Bonsai Slack Workspace 

# Who maintains and contributes to the project
Tom Millross is co-ordinating work on this for the hackathon

# Setting up your own Jena db instance on a server
Bonsai (with guidance from @tngTUDOR) chose the following architecture during the hackathon: 

setup a nginx as a proxy in the front in a virtual private server from digitalocean
run Jena db inside a docker container, as an upstream for the proxy.
We used the docker image from: https://hub.docker.com/r/stain/jena-fuseki/ with the default configuration

> docker run -p 3030:3030 --name jena-fuseki stain/jena-fuseki 

Access this through localhost:3030 if on your server. We configured nginx to forward requests on db.bonsai.uno to our docker container.


****
# Background information
To apply e.g. contribution analysis on the calculated footprints, not only raw data need to be stored, but also the linked data (the Direct Requirements Matrices) needs to be stored. In addition to the RDF stores, other database formats can be used when speed of search and calculation makes this desirable.

In the longer run, it may be considered to place the triple-store on IPFS. IPFS was developed out of http://dat-data.com - where the second focuses specifically on scientific data (see IPLD and https://github.com/ipfs/faq/issues/119 and the discussion near the end of https://github.com/ipfs/ipfs/issues/36 ). A parallel that focus more on social media data is SOLID.


## Triple-store choice
A decision was taken (_can someone link to this?_) before the hackathon to use **Apache Jena**. This is the [third-most-popular](https://db-engines.com/en/ranking/rdf+store) RDF store, and second-most popular open-source option, after Virtuoso. 

Several database management systems exist for RDF. An alternative to Apache Jena that has been considered is the open data management service CKAN (Comprehensive Knowledge Archive Network). CKAN is built with Python on the backend and Javascript on the frontend, and uses The Pylons web framework and SQLAlchemy as its ORM. Its database engine is PostgreSQL and its search is powered by SOLR. See the EUDAT funded assessment of CKAN vs. Apache Jena.

## Language standards
_Text copied from [here](https://github.com/BONSAMURAIS/bonsai/wiki/Data-Storage) and edited to match outcome of Hackathon Monday mornig discussion_
Since RDF is an abstract syntax, the triples need to be serialised in a concrete RDF syntax. For human-readbaility, we use Turtle. For data interchange, we use JSON-LD.

## Conversion & other
There are also many converters for external data to RDF, some of the most relevant are CSV2RDF for tabular data, D2RQ for relational database data, and XSLT for XML formatted data. The latter allows the full use of the tools of the SDMX community, which is sponsored and used by UN, the World Bank, EUROSTAT, OECD, and others, and is specially developed for exchange and sharing of open data and metadata among these organisations. The tools, which all have as basis the ISO 17369 SDMX standard, are provided by the developers of this standard. Probably the most relevant in this context is the Fusion Registry, which contains all that is needed to set up a management system for distributed data. Support contracts are available from MetaData Technology Ltd. While the SDMX community currently works by default in XML (SDMX-ML), the RDF Data Cube Vocabulary is also based on SDMX, enabling smooth translation from SDMX-ML to RDF, see also the work of Sarven Capadisli.
