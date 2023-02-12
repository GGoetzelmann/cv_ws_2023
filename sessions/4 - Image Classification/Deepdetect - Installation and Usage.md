# Installation Deepdetect

For detailed information see [Quick start with DeepDetect Server](https://www.deepdetect.com/quickstart-server)

NOTE: The information in this notebook is not so much meant as an exercise, because training takes up too much time. However, it documents the steps you have to take after you have successfully finished the installation instructions in session 3 in case you have labels for transfer learning

## Docker

### prerequisites

- Docker installation
- docker virtualization with GPU support in case of gpu
- you need to provide a folder with folders of different labels (at least two)

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

- adjust the "nclasses" to the number of classes you want to train on
- adjust the model if needed. The squeezenet model ist very fast but results might not be ideal.

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

- only do this with GPU support
- you can use the registered service after training finished successfully

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

- After restarting the server or moving to a different one, you have to register your trained model again. You do so by applying your newly trained weights.

## Register service

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
           "nclasses":4
         }
       },
       "model":{
         "repository":"/opt/models/multiple/"
       }
     }'
```
- make sure the correct file is reloaded as weights (check against `best_model.txt`)

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
