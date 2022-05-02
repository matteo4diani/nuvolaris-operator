<!--
  ~ Licensed to the Apache Software Foundation (ASF) under one
  ~ or more contributor license agreements.  See the NOTICE file
  ~ distributed with this work for additional information
  ~ regarding copyright ownership.  The ASF licenses this file
  ~ to you under the Apache License, Version 2.0 (the
  ~ "License"); you may not use this file except in compliance
  ~ with the License.  You may obtain a copy of the License at
  ~
  ~   http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing,
  ~ software distributed under the License is distributed on an
  ~ "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  ~ KIND, either express or implied.  See the License for the
  ~ specific language governing permissions and limitations
  ~ under the License.
  ~
-->
# nuvolaris-operator

This is the Kubernetes Operator of the [bit.ly/nuvolaris](nuvolaris project). Follow the link to learn more.

You can discuss it in the #[nuvolaris-operator](https://discord.gg/RzJ4FHR2aR) discord channel and in the forum under the category [operator](https://github.com/nuvolaris/nuvolaris/discussions/categories/operator).

If you are interested in developing it, please read the [design document](DESIGN.doc). Please also read the [development environment document](https://github.com/nuvolaris/nuvolaris/blob/main/docs/DEVEL.md) to learn how to setup it.

## Developer notes

The operator is built in Python with [kopf](https://kopf.readthedocs.io/en/stable/). You can find some [examples here](https://github.com/nolar/kopf/tree/main/examples).

The operator uses under the hood [kustomize](https://kustomize.io/) and [kubectl](https://kubernetes.io/docs/reference/kubectl/) to interact with Kubernetes.
## Selecting the Kubernetes Clusters

If you use the development environment, using Docker and VSCode, you have available a Kind cluster ready for test. You can use other clusters for testing and development.  You should place all the configurations for the other clusters in the folder clusters, with extension `.kubeconfig`. See [below](#creating-a-new-cluster) for informations about this.

In order to test the operator against different clusters, you can list all the cluster configuations available with `task` and then select one with `task N` where N is the number of the configuration in the list shown.

## Developing the operator

You can develop the operator without having to deploy it. Start it with `task run`.  Then open another terminal and Use `task deploy` to apply a configuration depending on the current clusters, file and `task d:destroy` to remove it. 

You can also interact with a python interpreter with the same libraries and some useful imports and configuration ready (most notably the autoreload) with `task cli`. Check `TaskfileDev.yml` for other useful targets for cleanup and debug.

Finally, you can run unit tests with `task utest`, integration tests with `task itest` and deployiment tests with `task dtest`. Also there are the targets `task uitest` to run the first two,  and `uidtest` to run all of them.

The target `task all-kubes -- <target>` runs a group of tests against all the available clusters, so you can run all the tests with `task all-kubes -- utest`

## Releasing the operator

Once the operator is ready, you can build and test it a against a kubernetes cluster.

First, generate a new image tag with `task image-tag`.

You can test locally using the kind cluster (provided by default by the development environment) with  `build-and-load`. 

To test it against other clusters, you need to build a public image on github. You can do it again generating an image tag and then pushing it to a public repository. 

If you have push access to Nuvolaris repository just the tag to trigger the GitHub Action to buld and publish it. 

If you have your own repository, you can run

`task IMAGE=<user>/nuvolaris-operator buildx-and-push`
 ## Testing the operator

First, [switch to the cluster](#kubernetes-cluster) you want to test with and [release the operator](#releasing-the-operator) accordingly.

Once you have the right cluster and the image properly published, you can test the operator with the following targets:

- `t:build-and-load`: build and load the operator in the cluster
- `t:operator`: deploys the operator in the current cluster.
- `t:instance`: deploy an instance of the configuration to build an actual cluster.
- `t:config`: once it is deployed, extracts the current configuration to use `wsk`
- `t:hello`: runs a simple hello world test
- `t:destroy`: destroy the current deployment

# Creating a new cluster

Currently the following clusters are supported: 

- `kind` 
- Amazon `eks` 
- Ubuntu `microk8s`

See below for creating clusters and configurations. All the configuration for the available clusters are expected to be in `clusters/*.kubeconfig`

Available commands are:

- `xxx:list`: list existing clusters
- `xxx:create`: create a test cluster
- `xxx:destroy`: destroy a test cluster
- `xxx:config`: set the kubeconfig to the current cluster

Note that `kind` is available in the development environment by defaut. You may want to use `task kind:config` to extract the configuration to be able to switch back to the local clusters.

Also note there is not a script to build a microk8s cluster, you are supposed to install it manually using instructions provided in the documentation.

