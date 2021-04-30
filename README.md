# Modern containerize template for Symfony

![Build Status](https://github.com/vuongxuongminh/docker-helm-symfony/actions/workflows/ci.yaml/badge.svg)

## About it

This is a clean template base on `symfony/skeleton` project template and set of Docker services, include basic Helm chart help you save time and easy to deploy it in your Kubernetes cluster.

## Prerequisites

+ Helm version 3.0+
+ Docker compose 1.17+
+ Docker engine 17.09+
+ Kubernetes cluster 1.14+

## Getting started

Start by downloading [distribution .tar.gz file](https://github.com/vuongxuongminh/docker-helm-symfony/releases), or generate a GitHub repo from this repo. 

After download and extract, open a terminal, and navigate to the directory containing your project. Run the following command to start all services using Docker Compose:

```shell script
$ docker-compose pull # Download the latest versions of the pre-built images.
$ docker-compose run --rm setup # Setup project (first time only).
$ docker-compose up -d # Running in detached mode
```

This starts the following services:

| Name          |           Description                                               | Ports | Environments |
|---------------|---------------------------------------------------------------------|------ |--------------|
| fpm           | FastCGI process manager with PHP-FPM, Composer.              | n/a   | all          |
| nginx         | Reverse proxy handle request with NGINX                        | 80    | all          |
| setup         | Setup service help run migration & install Composer package         | n/a   | dev          |

This results in the following running containers:

```shell script
$ docker-compose ps

           Name                          Command                  State                   Ports             
------------------------------------------------------------------------------------------------------------
docker-helm-symfony_fpm_1     docker-entrypoint fpm            Up (healthy)                                 
docker-helm-symfony_nginx_1   /opt/bitnami/scripts/nginx ...   Up (healthy)   0.0.0.0:80->8080/tcp, 8443/tcp
docker-helm-symfony_setup_1   docker-entrypoint setup          Exit 0 
```

If you want to change a PHP version, open `docker-compose.yaml` and add build args:

+ `PHP_VERSION` for change php version (ex: `PHP_VERSION: '7.4'`).

Now you can visiting your Symfony app: http://localhost

## Deploy project into Kubernetes cluster

### Install

Firstly you need to update helm dependencies by running:

```shell script
$ helm dep update ./helm-chart/
```

And install chart:

```shell script
$ helm install app ./helm-chart/
```

The command deploys your project on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

### Uninstalling the Chart

To uninstall/delete the app deployment:

```shell script
$ helm uninstall app
```

### Parameters

The following table lists the configurable parameters of this chart and their default values.

| Parameter                                    | Description                                                                                                                            | Default                                                                                   |
|----------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| imagePullSecrets                             | Docker registry secret names as an array.                                                                                              | `[]`                                                                                      |
| global.fpm.image.repository                  | Image repo.                                                                                                                        | `vuongxuongminh/docker-helm-symfony-php`                                                  |
| global.fpm.image.tag                         | Image tag.                                                                                                                         | `prod`                                                                                    |
| global.fpm.image.pullPolicy                  | Image pull policy.                                                                                                                         | `prod`                                                                                    |
| global.fpm.env                               | Use to set `APP_ENV` env in FPM container.                                                                               | `prod`                                                                                    |
| global.fpm.debug                             | Use to set `APP_DEBUG` env in FPM container.                                                                             | `0`                                                                                       |

Specify each parameter using the --set key=value[,key=value] argument to helm install. For example,

```shell script
$ helm install app ./helm-chart \
  --set global.fpm.env=prod,global.fpm.debug=1
```

The above command sets the environment variables APP_ENV to `prod`, APP_DEBUG to `1`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```shell script
$ helm install app ./helm-chart -f values.yaml
```

> Tip: You can use the default [values.yaml](/helm-chart/values.yaml) and read more Nginx parameters at [here](https://github.com/bitnami/charts/tree/master/bitnami/nginx).
