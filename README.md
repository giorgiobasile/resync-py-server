OpenMinTeD ResourceSync server demo
===================================

A Python implementation of a ResourceSync generator and server provided by the OpenMinTeD project, using 
[Elasticsearch](https://www.elastic.co/products/elasticsearch) as storage system. Given a file to be tracked, a document
 containing its metadata is stored into Elasticsearch, allowing fast retrieval and tracking. 

The [resyncserver.elastic](resyncserver/elastic) package provides an extension of the [rspub-core](https://github.com/EHRI/rspub-core) 
library for ResourceSync sitemaps manipulation. For demo purposes, it is possible to run three different python scripts:

* [populator](resyncserver/elastic/elastic_populator.py): stores metadata of files to be tracked into Elasticsearch
* [generator](resyncserver/elastic/elastic_generator.py): creates a Resource List from the metadata stored 
into Elasticsearch
* [server](resync-server.py): exposes ResourceSync sitemaps and resources on the web

These modules are executable providing well-formed configuration files. Examples of configuration files can be
found in the [config/test](config/test) folder.

The server implementation is based on the [resync-simulator](https://github.com/resync/resync-simulator) project.


Populator
---------
The [elastic_populator.py](resyncserver/elastic/elastic_populator.py) module allows to record files' metadata into 
Elasticsearch, given a configuration file specifying an Elasticsearch node address and the folders to be tracked. 
To run the populator, simply write:

```
cd resync-omtd-demo
python resyncserver/elastic/elastic_populator --config=/path/to/config
```

Generator
---------
The [elastic_generator.py](resyncserver/elastic/elastic_generator.py) module allows to generate new ResourceSync 
documents. It takes advantage of the rspub-core library [rspub-core](https://github.com/EHRI/rspub-core) 
library, extending some of its classes in order to support document generation from Elasticsearch instead of looking 
into the file system. The configuration file must contain all the
parameters required by the [rspub.core.rs_paras.RsParameters](https://github.com/EHRI/rspub-core/blob/master/rspub/core/rs_paras.py)
class, apart from the ones regarding the selector, which will be replaced by Elasticsearch configuration parameters. 
Indeed, the role of the selector is played by Elasticsearch, which will already contain only the files that have to be 
tracked. 

The generator will create a new capability list for each configuration file. A source description will be created or
updated if already existing.

To run the generator, simply write:

```
cd resync-omtd-demo
python resyncserver/elastic/elastic_generator --config=/path/to/config
```

Server
------
The [resync-server.py](resync-server.py) module runs a simple Tornado server to expose ResourceSync sitemaps previously 
generated by the generator module and possibly the actual files linked into it. For simplicity, each file into the root 
folder provided through the configuration file is exposed through the same Tornado controller.
To run the server, simply write:

```
cd resync-omtd-demo
python resync-server.py --config=/path/to/config
```


