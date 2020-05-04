# PASTEC Sample installation


- **data** : Pastec Data Directory. This is where image-Index will be saved to or loaded from. Could be probably a Docker shared Volume?
- **pics** : Training & Query Images
  - **train**: Images to train Image Index
  - **search**:  Query Images (photos shot from Smartphone)
    - resized: Query Images resized and b/w  - **quicker processed**
  
---
## Start

- Run `docker-compose up`
- This starts the container and reads in the `/data/visualWordsORB.dat` (Internal requirement - could also be put inside the container)

``` bash
pastec $ docker-compose up
Creating network "pastec_default" with the default driver
Creating pastec ... done
Attaching to pastec
pastec    | Pastec Index v0.0.1
pastec    | Could not open the backward index file.
pastec    | Reading the visual words file.
pastec    | Building the word index.
pastec    | Ready to accept queries.
```

When ready to accept queries, Pastec can be accessed, see API: http://pastec.io/doc/#api

### Add an Image to the Index
- via PUT Request to `/index/images/:id`, and image as Binary Payload:
- ID should be unique Integer

```bash
$   cd ./pics/train # go to train directory
$   curl -X PUT --data-binary @$(pwd)/094_0102.jpg http://localhost:4212/index/images/123

{"image_id":123,"nb_features_extracted":1760,"type":"IMAGE_ADDED"}
```



To add whole `train` directory to the index, run:
```bash
$   cd ./pics/train # go to train directory
$   i=0; for f in *.jpg; do curl -X PUT --data-binary @$(pwd)/$f http://localhost:4212/index/images/$i; i=$((i+1)); done
```

### Query for Image
- via POST to `/index/searcher`, and image to query for as Binary Payload:
- gives back the corresponding image-id
```bash
$   cd ./pics/search/resized # go to search/resized directory
$   curl -X POST --data-binary @$(pwd)/094_0102.jpg http://localhost:4212/index/searcher

{"bounding_rects":[{"height":186,"width":153,"x":38,"y":65}],"image_ids":[123],"scores":[27.0],"tags":[""],"type":"SEARCH_RESULTS"}

```
 
 ---
 # TBD
 - Pastec kann auch String-Tags zu Image-IDs hinzufügen (nicht in Dokumentation). Vlt. als Improvement für Matching?
 - Index speichern - vermutlich reicht auch ein Shared Volume zwischen den Instancen, statt eine MongoDB?