# How to define and reference different helm `values` depending on the `environment` being deployed

## Approach 1

* Using `environment` slug to determine environment variable values from helm `values` structure for multi-environment configuration

### Directory structure

```shell
$ tree
.
├── Chart.yaml
├── templates
│   ├── _helpers.tpl
│   ├── configmap.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── values.yaml

1 directory, 6 files
```

### `Chart.yaml` content

```shell
$ cat Chart.yaml
apiVersion: v2
name: myapp
description: A Helm chart for my Kubernetes nginx App
type: application
version: 0.1.0
appVersion: "1.19.0"
```

### `values.yaml` content

```shell
$ cat values.yaml
replicaCount: 1
image:
  repository: nginx
  tag: "" # Overrides the image tag whose default is the chart appVersion.

service:
  type: ClusterIP
  port: 80

app:
  ENV:
    prod: "prod"
    dev: "dev"
    _default: "dev"
  NGINX_PORT:
    prod: "80"
    dev: "8080"
    _default: "8080"
```

### Manifest's content in `templates` directory

* ConfigMap for *Nginx* default configuration template

```shell
$ cat templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: default-conf-template
data:
  default.conf.template: |
    server {
        listen       ${NGINX_PORT};
        listen  [::]:${NGINX_PORT};
        server_name  localhost;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
```

* *Nginx* deployment with custom default configuration template on custom port

```shell
$ cat templates/deployment.yaml
{{- $D := dict "Values" .Values }}
{{- include "myapp.envs" $D }}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "myapp.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "myapp.selectorLabels" . | nindent 8 }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          env:
            - name: KUBERNETES_DEPLOYED
              value: {{ now }}
            - name: ENV
              value: {{ get $D "ENV" }}
            - name: NGINX_PORT
              value: {{ get $D "NGINX_PORT" | quote }}
          ports:
            - name: http
              containerPort: {{ get $D "NGINX_PORT" }}
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          volumeMounts:
            - mountPath: /etc/nginx/templates
              readOnly: true
              name: default-conf-template
      volumes:
        - name: default-conf-template
          configMap:
            name: default-conf-template
            items:
              - key: default.conf.template
                path: default.conf.template
```

* Service for *Nginx* deployment pods

```shell
$ cat templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "myapp.fullname" . }}
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    {{- include "myapp.selectorLabels" . | nindent 4 }}
```

### Helm helper functions

```shell
$ cat templates/_helpers.tpl
{{- define "myapp.fullname" -}}
{{- if contains .Chart.Name .Release.Name }}
{{- .Release.Name | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- printf "%s-%s" .Release.Name .Chart.Name | trunc 63 | trimSuffix "-" }}
{{- end }}
{{- end }}

{{- define "myapp.selectorLabels" -}}
app.kubernetes.io/name: {{ .Chart.Name | trunc 63 | trimSuffix "-" }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end }}

{{- define "myapp.labels" -}}
helm.sh/chart: {{ printf "%s-%s" .Chart.Name .Chart.Version | replace "+" "_" | trunc 63 | trimSuffix "-" }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{ include "myapp.selectorLabels" . }}
{{- if .Chart.AppVersion }}
app.kubernetes.io/version: {{ .Chart.AppVersion | quote }}
{{- end }}
{{- end }}

{{- define "myapp.envs" }}
{{- $env := "" }}
{{- if and (.Values.global) (.Values.global.env) }}
{{- $env = (split "-" .Values.global.env)._0 }}
{{- end }}
{{- $app := .Values.app }}
{{- $dict := . }}
{{- range $name, $map := $app }}
  {{- $default := $map._default }}
  {{- $value := pluck $env $map | first | default $default }}
  {{- $_ := set $dict $name $value }}
{{- end }}
{{- end }}
```

### Helm `myapp.envs` helper function explanation

* Define `$env` variable as a string 

```
{{- $env := "" }}
```

* If `--set-string global.env=xxxx` is used for `helm` install/ugrade command, use the part of `global.env` variable before the first `-` as a value for `$env` variable

```
{{- if and (.Values.global) (.Values.global.env) }}
{{- $env = (split "-" .Values.global.env)._0 }}
{{- end }}
```

* Read `app` dictionary from `values.yaml` to `$app` variable

```
{{- $app := .Values.app }}
```

* When we use `set $dict` below it will set top level (root) structure of golang `template` engine

```
{{- $dict := . }}
```

* Go through all key-value pair for `$app` dictionary and do the following
  * Set `$name` variable to the dictionary key, like
    * `ENV`
    * `NGINX_PORT`
    * ...
  * Set `$map` variable to the dictionary value, which is nested map, like
    * `{prod: "prod", dev: "dev", _default: "dev"}`
    * `{prod: "80", dev: "8080", _default: "8080"}`
    * ...
  * Set the `$default` variable to the nested `$map` `_default` key
  * Set the `$value` variable to the value of the first key-value pair of the nested `$map` where key is equal to `$env`, or to the defined above `$default` variable if there is no such  key-value pair
  * In top level (root) structure of golang `template` engine (`.`) set key-value pair as `$name: $value`, e.g., if `--set-string global.env=prod` was used, then
    * For key of `$name (ENV)` and value of `$value (prod)` set key-value pair as `ENV: prod`
    * For key of `$name (NGINX_PORT)` and value of `$value (80)` set key-value pair as `NGINX_PORT: 80`

```
{{- range $name, $map := $app }}
  {{- $default := $map._default }}
  {{- $value := pluck $env $map | first | default $default }}
  {{- $_ := set $dict $name $value }}
{{- end }}
```

## Approach 2

* Extending the approach 1 for to the whole helm `values` configuraton structure for multi-environment configuration

### Directory structure

```shell
$ tree
.
├── Chart.yaml
├── templates
│   ├── _helpers.tpl
│   ├── configmap.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── values.yaml

1 directory, 6 files
```

### `Chart.yaml` content

```shell
$ cat Chart.yaml
apiVersion: v2
name: myapp
description: A Helm chart for my Kubernetes nginx App
type: application
version: 0.1.0
appVersion: "1.19.0"
```

### `values.yaml` content

```shell
$ cat values.yaml
app:
  SPEC:
    prod:
      replicaCount: 8
      image:
        repository: nginx
        tag: ""
      service:
        type: LoadBalancer
        port: 11111
      envs:
        ENV: "prod"
        NGINX_PORT: "80"
    dev:
      replicaCount: 4
      image:
        repository: nginx
        tag: ""
      service:
        type: NodePort
        port: 22222
      envs:
        ENV: "dev"
        NGINX_PORT: "8080"
    _default:
      replicaCount: 2
      image:
        repository: nginx
        tag: ""
      service:
        type: ClusterIP
        port: 33333
      envs:
        ENV: "dev"
        NGINX_PORT: "8080"
```

### Manifest's content in `templates` directory

* ConfigMap for *Nginx* default configuration template

```shell
$ cat templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: default-conf-template
data:
  default.conf.template: |
    server {
        listen       ${NGINX_PORT};
        listen  [::]:${NGINX_PORT};
        server_name  localhost;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
```

* *Nginx* deployment with custom default configuration template on custom port

```shell
$ cat templates/deployment.yaml
{{- $D := dict "Values" .Values }}
{{- include "myapp.envs" $D }}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
spec:
  replicas: {{ (get $D "SPEC").replicaCount }}
  selector:
    matchLabels:
      {{- include "myapp.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "myapp.selectorLabels" . | nindent 8 }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: {{ printf "%s:%s" (get $D "SPEC").image.repository ((get $D "SPEC").image.tag | default .Chart.AppVersion) | quote }}
          env:
            - name: KUBERNETES_DEPLOYED
              value: {{ now }}
            - name: ENV
              value: {{ (get $D "SPEC").envs.ENV }}
            - name: NGINX_PORT
              value: {{ (get $D "SPEC").envs.NGINX_PORT | quote }}
          ports:
            - name: http
              containerPort: {{ (get $D "SPEC").envs.NGINX_PORT }}
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          volumeMounts:
            - mountPath: /etc/nginx/templates
              readOnly: true
              name: default-conf-template
      volumes:
        - name: default-conf-template
          configMap:
            name: default-conf-template
            items:
              - key: default.conf.template
                path: default.conf.template
```

* Service for *Nginx* deployment pods

```shell
$ cat templates/service.yaml
{{- $D := dict "Values" .Values }}
{{- include "myapp.envs" $D }}
---
apiVersion: v1
kind: Service
metadata:
  name: {{ include "myapp.fullname" . }}
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
spec:
  type: {{ (get $D "SPEC").service.type }}
  ports:
    - port: {{ (get $D "SPEC").service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    {{- include "myapp.selectorLabels" . | nindent 4 }}
```

### Helm helper functions

* The Helm `myapp.envs` helper function is the same as in the perious approach — see the details on how it works above

```shell
$ cat templates/_helpers.tpl
{{- define "myapp.fullname" -}}
{{- if contains .Chart.Name .Release.Name }}
{{- .Release.Name | trunc 63 | trimSuffix "-" }}
{{- else }}
{{- printf "%s-%s" .Release.Name .Chart.Name | trunc 63 | trimSuffix "-" }}
{{- end }}
{{- end }}

{{- define "myapp.selectorLabels" -}}
app.kubernetes.io/name: {{ .Chart.Name | trunc 63 | trimSuffix "-" }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end }}

{{- define "myapp.labels" -}}
helm.sh/chart: {{ printf "%s-%s" .Chart.Name .Chart.Version | replace "+" "_" | trunc 63 | trimSuffix "-" }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{ include "myapp.selectorLabels" . }}
{{- if .Chart.AppVersion }}
app.kubernetes.io/version: {{ .Chart.AppVersion | quote }}
{{- end }}
{{- end }}

{{- define "myapp.envs" }}
{{- $env := "" }}
{{- if and (.Values.global) (.Values.global.env) }}
{{- $env = (split "-" .Values.global.env )._0 }}
{{- end }}
{{- $app := .Values.app }}
{{- $dict := . }}
{{- range $name, $map := $app }}
  {{- $default := $map._default }}
  {{- $value := pluck $env $map | first | default $default }}
  {{- $_ := set $dict $name $value }}
{{- end }}
{{- end }}
```
