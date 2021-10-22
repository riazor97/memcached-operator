# memcached-opearator

operator-sdk

* Project scaffolding and code generation to bootstrap a new project fast

* High level APIs and abstractions so developer can write the operational logic more intuitively

* Extensions to cover more common operator use cases

Bundle

"bundle" (directory too) is meant to represent a specific version of an operator with a structured directory of files with one ClusterServiceVersion

Operator Lifecycle Manager

OLM defines a precise operational model to cover bundling, distribution, discovery, and provisioning so that we have a better way to collaborate, manage and operate Kubernetes-native applications, aka operators.

## First step

Rename `laimison` to your account in the code

## Prerequisites

Run only first time

```
operator-sdk olm install
operator-sdk olm status
kubectl get CatalogSource,ClusterServiceVersion,Subscription,OperatorGroup,OperatorConditions -n olm

mkdir memcached-operator
operator-sdk init --domain example.com --repo github.com/example/memcached-operator
operator-sdk create api --group cache --version v1alpha1 --kind Memcached --resource --controller
```

## Build

```
docker login

make docker-build docker-push IMG="docker.io/laimison/memcached-operator:v0.0.2" VERSION="0.0.2"
make bundle VERSION="0.0.2"

make bundle-build bundle-push VERSION=0.0.2 # alternative direct approach
```

## Install

```
alias k='kubectl -n memcached'

k create ns memcached

operator-sdk run bundle docker.io/laimison/memcached-operator-bundle:v0.0.2 -n memcached

make deploy # alternative direct approach

make install # install CRDs

k apply -f config/samples/cache_v1alpha1_memcached.yaml
```

## Other commands

```
make run # regenrated code and run against cluster / run it as normal go project
```

## Status

```
k get pods

k get ns --sort-by=.metadata.creationTimestamp

kubectl logs -n memcached docker-io-laimison-memcached-operator-bundle-v0-0-1

k logs memcached-operator-controller-manager-7cf5bbfb8c-xrpb4 manager

kubectl get deploy -n memcached memcached-operator-controller-manager -o yaml

k get catalogsource -A
```

## Uninstall

```
operator-sdk cleanup memcached-operator -n memcached
kubectl delete Memcached/memcached-sample
k delete pod -n memcached -l control-plane=controller-manager --force

make undeploy # alternative direct approach
```

## Files that were updated

config/default/kustomization.yaml - namespace change

Makefile - Docker push related

controllers/memcached_controller.go - added some log
