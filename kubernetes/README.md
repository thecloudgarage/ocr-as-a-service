# OpenOCR runs on Kubernetes!

* [Instructions to run on Google Container Engine](https://github.com/tleyden/open-ocr/wiki/Installation-on-Google-Container-Engine)

* [Instructions to run on Azure Kubernetes Service](https://github.com/tleyden/open-ocr/wiki/Installation-on-Azure-Kubernetes-Service)

If you want to run it on a different Kubernetes provider, particularly on ones that don't offer the `Type: LoadBalancer` support for Kubernetes Service definitions, you will need to change the [open-ocr-httpd service](https://github.com/tleyden/open-ocr/blob/master/kubernetes/services/open_ocr_httpd.yml) accordingly.

# Quick Start - Automated

You can use the `bootstrap.sh` script found on this directory to automate all of the steps described below.

```
curl -O bootstrap.sh https://raw.githubusercontent.com/dcasati/open-ocr/dcasati-azure-demo/kubernetes/bootstrap.sh
chmod +x bootstrap.sh
```

Using the provided script:

```
usage: bootstrap.sh [-cit]

Options:
    -c     clean up the demo.
    -i     install the demo.
    -t     run a cURL test against the API.
```

To install the demo:
```
bootstrap.sh -i
```

To test the REST API

```
bootstrap.sh -t
```

Finally, to clean up all of the resources from the Kubernetes cluster

```
bootstrap.sh -c
```

# Quick Start - Manual

## Create RabbitMQ password

You will want to replace the `YOUR_RABBITMQ_PASS` below with something more secure.

```
printf "YOUR_RABBITMQ_PASS" > ./password
kubectl create secret generic rabbit-mq-password --from-file=./password

```

## Clone OpenOCR repo

```
git clone https://github.com/tleyden/open-ocr.git
```

## Launch RabbitMQ 

```
kubectl create -f kubernetes/pods/rabbitmq.yaml
kubectl create -f kubernetes/services/rabbitmq.yml
```

Wait until it launches by checking:

```
kubectl describe pod rabbitmq
```

and make sure the state is `RUNNING`

# Launch REST API Server

```
kubectl create -f kubernetes/pods/open_ocr_httpd.yml
kubectl create -f kubernetes/services/open_ocr_httpd.yml
```

# Launch OCR Worker

```
kubectl create -f kubernetes/replication-controllers/open-ocr-worker.yaml
```

# Verify

**Find the external IP**

```
kubectl describe service open-ocr-httpd-service
```

and look for the `LoadBalancer Ingress` value.  That is the publicly available IP address.

**Run curl against the REST API**

Replace the IP below with *your* `LoadBalancer Ingress` returned above in this command:

```
curl -X POST -H "Content-Type: application/json" -d '{"img_url":"http://bit.ly/ocrimage","engine":"tesseract"}' http://104.197.33.5/ocr
```

and you should get the output:

```
You can create local variables for the pipelines within the template by
preﬁxing the variable name with a “$" sign. Variable names have to be
composed of alphanumeric characters and the underscore. In the example
below I have used a few variations that work for variable names.
```
