# Installation Larex

## Docker

### Prerequesites

- Docker installation
- Internet connection

### Steps and commands

- Create folder with subfolders of books
- Create config file ([basic example](https://github.com/maxnth/LAREX_Docker/blob/latest/larex.config))
- Pull remote docker image and start application
  ```
  docker run -p <local port>:8080 -v <parent folder of books>:/home/books/ -v <config file path>:/larex.config -it maxnth/larex:latest
  ```
- if installed correctly, the application should be reachable at `http//:localhost:<local port>/Larex`
- if installed correctly and book data is available, the link above should show data folders of books
- if not, check the `docker logs <container name>` for further information


# Usage Larex

- for general usage information see [Quick Guide](https://www.uni-wuerzburg.de/fileadmin/10030600/Mitarbeiter/Reul_Christian/Projects/Layout_Analysis/LAREX_Quick_Guide.pdf)

## Basics





