# Run reactome graph database
Tutorial can be found: https://reactome.org/dev/graph-database

- Step 1: Download Docker APP (for Mac user)

- Step 2: Pull a docker image that contains Neo4j and a Reactome graph database
  
  Find the lastest image tag: https://gallery.ecr.aws/reactome/graphdb
  
  ```bash
  docker pull public.ecr.aws/reactome/graphdb:latest
  ```
  docker pull downloads the Reactome Neo4j image to your machine
- Step 3: run the container with docker run

  ```bash
  docker run -d --name reactome-graphdb \
    -p 7474:7474 -p 7687:7687 \
    -e NEO4J_dbms_memory_heap_maxSize=8g \
    public.ecr.aws/reactome/graphdb:latest
  ```

  If you get a warning like: "WARNING: The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested 34e649bb2b18e8eb1641fee3b02738a42073444fd189505dcb185a5f999e435a"

  That warning is normal on Apple Silicon / M1 / M2 Macs (ARM64) because the Reactome Docker image is built for amd64.

  You can check if the docker is currectly running:

  ```bash
  docker ps
  ```

  You can stop and remove the currect docker:
  
  ```bash
  # Stop the current container
  docker stop reactome-graphdb
  
  # Remove it
  docker rm reactome-graphdb
  ```

  And you can rerun that command without the warning:

  ```bash
  docker run -d --name reactome-graphdb \
  --platform linux/amd64 \
  -p 7474:7474 -p 7687:7687 \
  -e NEO4J_dbms_memory_heap_maxSize=8g \
  public.ecr.aws/reactome/graphdb:latest
  ```
- Step 3: Access to http://localhost:7474

  Recommend using chrome.

  You can check the connnection to this website using:

  ```bash
  curl http://localhost:7474
  ```

  It should return:
  {
  "bolt_routing" : "neo4j://localhost:7687",
  "transaction" : "http://localhost:7474/db/{databaseName}/tx",
  "bolt_direct" : "bolt://localhost:7687",
  "neo4j_version" : "4.3.6",
  "neo4j_edition" : "enterprise"
  }

