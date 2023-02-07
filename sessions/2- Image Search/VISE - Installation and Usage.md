# Installation VISE

## Prerequisites

- Internet connection
- This information is mainly aimed at installations on Windows and Mac systems

- On linux systems install from source or as docker image (might take a while)
- In case of docker run with:
  ```
  docker run -d -p <local port>:9669 -v <local data folder>:/opt/vise/data/ ubuntu/vise:latest
  ```

## Steps and commands
- Go to website and download for your system: https://www.robots.ox.ac.uk/~vgg/software/vise/ 
- Install on your system
- Start application by opening http://localhost:9669 in your browser

![vise setup correctly](./assets/VISE-ready.png)

# Usage

## Create search engine with sample data

- Download sample data: !!link!!
- Unzip sample data
- Create project (name of your choice)

  ![vise configure name](./assets/Vise-projectname.png)
- Add Images: point to your unzipped sample data

  ![vise add images](./assets/Vise-addimages.png)
- Project Settings: Preset 1 (keep unchanged for this exercise)

  ![vise preset 1](./assets/Vise-preset.png)
- Create Visual Search Engine (will take a few minutes)

## Basic use

- Select your created image search
- Select an image to query on
- Optional: Draw region of interest
- Search results
- Results list can be extended by clicking on show-button (twice)
- To learn more about the matches between two images, click on image in result list
