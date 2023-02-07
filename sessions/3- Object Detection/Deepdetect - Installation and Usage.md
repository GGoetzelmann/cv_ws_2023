# Installation Deepdetect

For detailed information see [Quick start with DeepDetect Server](https://www.deepdetect.com/quickstart-server)

## Docker

### prerequisites

- Docker installation
- docker virtualization with GPU support in case of gpu

### Steps and commands

1. select docker image from [website](https://www.deepdetect.com/quickstart-server)
1. Use docker run to pull and start according image
    ```
    docker run -d --gpus all -p <local port>:8080 -v <local model folder>:/opt/models/ jolibrain/deepdetect_<selected suffix>
    ```
1. Check with `localhost:<local port>/info`

# Using a pre-trained model for prediction

## Register service
```
curl --location --request PUT 'http://localhost:9998/services/faces' \
--header 'Content-Type: application/json' \
--data-raw '{
 "description": "face detection service",
 "model": {
  "repository": "/opt/models/faces",
  "create_repository": true,
  "init":"https://deepdetect.com/models/init/desktop/images/detection/faces_512.tar.gz"
 },
 "mllib": "caffe",
 "type": "supervised",
 "parameters": {
  "input": {
   "connector": "image"
  }
 }
}'
```


## Predict

Data for prediction can be an image url or a base 64 string

```
curl --location --request POST 'http://localhost:9998/predict' \
--header 'Content-Type: application/json' \
--data-raw '{
       "service":"trainedsqueeze2",
       "parameters":{
         "input":{
           "width":224,
           "height":224
         },
         "output":{
           "best":3
         }
       },
       "data":[<urls or base64 strings>]
     }'
```
