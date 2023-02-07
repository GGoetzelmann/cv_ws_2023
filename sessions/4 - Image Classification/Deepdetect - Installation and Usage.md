# Installation Deepdetect

For detailed information see [Quick start with DeepDetect Server](https://www.deepdetect.com/quickstart-server)

## Docker

### prerequisites

- Docker installation
- docker virtualization with GPU support in case of gpu

WARNING: Even then, training will take quite some time, at least a few hours.

### Steps and commands

1. select docker image from [website](https://www.deepdetect.com/quickstart-server)
1. Use docker run to pull and start according image
    ```
    docker run -d --gpus all -p <local port>:8080 -v <local model folder>:/opt/models/ jolibrain/deepdetect_<selected suffix>
    ```
1. Check with `localhost:<local port>/info`

# Using a pre-trained model for prediction or training

## Register service
```
curl --location --request PUT 'http://localhost:9998/services/squeeze4' \
--header 'Content-Type: application/json' \
--data-raw '{
   "mllib":"caffe",
   "description":"image classification service",
   "type":"supervised",
   "parameters":{
     "input":{
       "connector":"image",
       "width":224,
       "height":224,
       "db": true
     },
     "mllib":{
       "template":"squeezenet",
       "nclasses":4,
       "finetuning":true
     }
   },
   "model":{
     "templates":"../templates/caffe/",
     "repository":"/opt/models/multiple/",
 "weight": "squeezenet_v1.1.caffemodel"
   }
 }'
```
## Train Model
only do this with GPU support

```
curl --location --request POST 'http://localhost:9998/train' \
--header 'Content-Type: application/json' \
--data-raw '{
       "service":"squeeze4",
       "async":true,
       "resume":true,
       "parameters":{
         "input":{
           "connector":"image",
           "test_split":0.1,
           "shuffle":true,
           "width":224,
           "height":224,
           "db":true
         },
         "mllib":{
           "gpu":false,
           "mirror":true,
           "net":{
             "batch_size":32
           },
           "solver":{
             "test_interval":500,
             "iterations":5000,
             "base_lr":0.001
           },
       "noise":{"all_effects":true, "prob":0.001},
       "distort":{"all_effects":true, "prob":0.01}
         },
         "output":{
           "measure":["acc","mcll","f1"]
         }
       },
       "data":["/opt/models/multiple/trainingdata/"]
     }'
```

# Reloading a trained model

## Register service

- find best model by checking best_model.txt

```
curl --location --request PUT 'http://localhost:9998/services/trainedsqueeze2' \
--header 'Content-Type: application/json' \
--data-raw '{
       "mllib":"caffe",
       "description":"image classification service",
       "type":"supervised",
       "parameters":{
         "input":{
           "connector":"image",
           "width":224,
           "height":224,
           "db": true
         },
         "mllib":{
           "nclasses":4,
           "finetuning": true
         }
       },
       "model":{
         "repository":"/opt/models/multiple/", 
         "weights": "/opt/models/multiple/model_iter_1500.caffemodel"
       }
     }'
```

# Predict

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
