% kube-x-lib(1) Version Latest | kube-x-lib
# kube-x-lib

A library of kubernetes functions

## DESCRIPTION

A library of kubernetes functions

## Index

* [kube::get_qualified_app_name](#kubeget_qualified_app_name)
* [kube::get_app_label](#kubeget_app_label)
* [kube::get_resources_by_app_name](#kubeget_resources_by_app_name)
* [kube::get_resource_by_app_name](#kubeget_resource_by_app_name)
* [kube::get_json_path](#kubeget_json_path)
* [kube::get_app_directory](#kubeget_app_directory)
* [kube::get_app_crds_directory](#kubeget_app_crds_directory)
* [kube::is_helm_chart_installed](#kubeis_helm_chart_installed)
* [kube::test_connection](#kubetest_connection)

### kube::get_qualified_app_name

Return the app name and namespace from a string
A qualified app name is made of one optional namespace and a name separated by a slash

#### Example

```bash
read APP_NAMESPACE APP_NAME <<< "$(kube::get_qualified_app_name "$APP_NAME")"
```

#### Arguments

* **$1** (string): The app name

#### Output on stdout

* The app label ie `app.kubernetes.io/name=<app name>`

### kube::get_app_label

Return the app label used to locate resources
It will return the label `app.kubernetes.io/name=<app name>`
This is the common app label as seen on the [common label page](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/)

#### Example

```bash
APP_LABEL="$(kube::get_app_label "$APP_NAME")"
```

#### Arguments

* **$1** (string): The app name

#### Output on stdout

* The app label ie `app.kubernetes.io/name=<app name>`

### kube::get_resources_by_app_name

Function to search for resources across all namespaces by app name
and returns data about them

#### Example

```bash
PODS="$(kube::get_resources_by_app_name --type pod "$APP_NAME")"
```

#### Arguments

* **$1** (string): `x`                  - the app name (mandatory) used in the label "app.kubernetes.io/name=$APP_NAME"
* **$2** (string): `--type x`           - the resource type: pod, ... (mandatory)
* **$3** (string): `--custom-columns x` - the custom columns (Default to `NAME:.metadata.name,NAMESPACE:.metadata.namespace`)
* **$4** (string): `--headers`          - the headers (Default to `no headers`)

#### Output on stdout

* The resources data (one resource by line) or an empty string

### kube::get_resource_by_app_name

Function to search for 1 resource across all namespaces by app name
and returns data

#### Example

```bash
read -r POD_NAME POD_NAMESPACE <<< "$(kube::get_resource_by_app_name --type pod "$APP_NAME" )"
if [ -z "$POD_NAME" ]; then
    echo "Error: Pod not found with label $(kube::get_app_label $APP_NAME)"
    exit 1
fi
```

#### Arguments

* **$1** (string): `x`           - The app name
* **$2** (string): `--type type` - The resource type (pod, ...)
* **$3** (string): `--custom-columns x` - the custom columns (Default to `NAME:.metadata.name,NAMESPACE:.metadata.namespace`)
* **$4** (string): `--headers`          - the headers (Default to `no headers`)

#### Exit codes

* **1**: - if too many resource was found

#### Output on stdout

* The resource name and namespace separated by a space or an empty string

### kube::get_json_path

Return a json path to be used in a `-o jsonpath=x` kubectl option

#### Arguments

* **$1** (string): The Json expressions (Default to: `.metadata.name .metadata.namespace`)

### kube::get_app_directory

Return the directory of an app

#### Arguments

* **$1** (string): The app namespace

### kube::get_app_crds_directory

Return the crds directory of an app

#### Arguments

* **$1** (string): The app namespace

#### Exit codes

* **1**: if there is no crds directory
* **0**: if there is a crds directory

#### Output on stdout

* the crds directory

### kube::is_helm_chart_installed

Return if the chart is already installed

#### Exit codes

* **1**: - if the chart is not installed or with another app version

### kube::test_connection

test the connection to the cluster

#### Exit codes

* **1**: - if the connection did not succeed
