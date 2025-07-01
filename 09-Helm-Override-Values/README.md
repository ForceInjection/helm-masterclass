# Helm 覆盖 values.yaml 中的默认值

## 步骤-01：介绍

- 我们将在本节中学习以下内容
  - helm install --set
  - helm upgrade -f myvalues.yaml
  - **--dry-run:**
  - **--debug:**
  - helm get values
  - helm get values --revision
  - helm get manifest
  - helm get manifest --revision
  - helm get all
- 讨论值层次结构

## 步骤-02：使用 --set 覆盖默认 NodePort 31231

### 步骤-02-01：查看我们的 mychart1 Helm Chart values.yaml

- [mychart1 values.yaml](https://github.com/stacksimplify/helm-charts/blob/main/mychart1/values.yaml)

### 步骤-02-02：了解 helm install 命令的 --dry-run 和 --debug 标志

- 通过将 NodePort 31231 覆盖为 31240 来安装 Helm Chart

```bash
# Helm Install with --dry-run command
helm install myapp901 stacksimplify/mychart1 --set service.nodePort=31240 --dry-run 

# Helm Install with --dry-run and --debug command
helm install myapp901 stacksimplify/mychart1 --set service.nodePort=31240 --dry-run --debug

## THE BELOW IS THE SAMPLE OUTPUT WITH DEBUG ADDED
NAME: myapp901
NAMESPACE: default
STATUS: pending-install
REVISION: 1
 USER-SUPPLIED VALUES:
service:
  nodePort: 31240
COMPUTED VALUES:
fullnameOverride: ""
image:
  pullPolicy: IfNotPresent
  repository: ghcr.io/stacksimplify/kubenginx
  tag: ""
nameOverride: ""
podAnnotations: {}
replicaCount: 1
service:
  nodePort: 31240
  port: 80
  type: NodePort
```

### 步骤-02-03：使用 --set 进行 helm install 并测试

```bash
# Helm Install 
helm install myapp901 stacksimplify/mychart1 --set service.nodePort=31240 

# helm status --show-resources
helm status myapp901 --show-resources
Observation:
We can see that our NodePort service is running on port 31240

# Access Application
http://localhost:31240
```

## 步骤-03：查看 myvalues.yaml

- **myvalues.yaml 文件位置：** 09-Helm-Override-Values/myvalues.yaml

```yaml
# Change-1: change replicas from 1 to 2
replicaCount: 2

# Change-2: Add tag as "2.0.0" which will override the default appversion "1.0.0" from our mychart1
image:
  repository: ghcr.io/stacksimplify/kubenginx
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: "2.0.0"

# Change-3: Change nodePort from 31240 to 31250
service:
  type: NodePort
  port: 80
  nodePort: 31250
```

## 步骤-04：使用 -f myvalues.yaml 覆盖 NodePort 31240

- 我们可以使用 `-f myvalues.yaml` 或 `--values myvalues.yaml`。两者都是有效输入

```bash
# Verify if myvalues.yaml
cd 09-Helm-Override-Values
cat myvalues.yaml

# helm upgrade with --dry-run and --debug commands
helm upgrade myapp901 stacksimplify/mychart1 -f myvalues.yaml --dry-run --debug

# helm upgrade
helm upgrade myapp901 stacksimplify/mychart1 -f myvalues.yaml

# helm status
helm status myapp901 --show-resources
Observation: 
1. Two pods will be running as we changed replicacount to 2
2. Service Node Port will be 31250 

# Access Application
http://localhost:31250
Observation: 
1. We should see V2 application because we have used the "image tag as 2.0.0"
```

## Step-05: helm get values command

- **helm get values:** This command downloads a values file for a given release

```bash
# helm get values
helm get values RELEASE_NAME
helm get values myapp901
Observation:
1. Provides the values from current/latest release version 2 from Release myapp901

## Sample Output
Kalyans-MacBook-Pro:09-Helm-Override-Values kalyan$ helm get values myapp901
USER-SUPPLIED VALUES:
image:
  pullPolicy: IfNotPresent
  repository: ghcr.io/stacksimplify/kubenginx
  tag: 2.0.0
replicaCount: 2
service:
  nodePort: 31250
  port: 80
  type: NodePort


# helm history (History prints historical revisions for a given release.)
helm history myapp901


# helm get values with --revision
helm get values RELEASE-NAME --revision int
helm get values myapp901 --revision 1

## Sample Output
Kalyans-MacBook-Pro:09-Helm-Override-Values kalyan$ helm get values myapp901 --revision 1
USER-SUPPLIED VALUES:
service:
  nodePort: 31240
```

## Step-06: helm get manifest command

- **helm get manifest:** This command fetches the generated manifest for a given release.

```bash
# helm get manifest
helm get manifest RELEASE-NAME
helm get manifest myapp901

# helm get manifest --revision
helm get manifest RELEASE-NAME --revision int
helm get manifest myapp901 --revision 1
```

## Step-07: helm get all command

- **helm get all:** This command prints a human readable collection of information about the notes, hooks, supplied values, and generated manifest file of the given release.
- This is a good way to see what templates are installed on the kubernetes cluster server.
- **helm get notes and helm get hooks:** These two commmands we will explore when we are discussing about helm chart development.

```bash
# helm get all
helm get all RELEASE-NAME
helm get all myapp901
```

## Step-08: Uninstall Helm Release

```bash
# Uninstall Helm Release
helm uninstall myapp901

# List Helm Releases
helm list
```

## Step-09: Values Hierarchy

1. Sub chart `values.yaml` can be overriden by parents chart `values.yaml`
2. Parent charts `values.yaml` can be overriden by user-supplied value file `(-f myvalues.yaml)`
3. User-supplied value file `(-f myvalues.yaml)` can be overriden by `--set` parameters

## Step-10: Deleting a default Key by passing null

- If you need to delete a key from the default values, you may override the value of the key to be null, in which case Helm will remove the key from the overridden values merge.

```bash
# Release: myapp901
helm install myapp901 stacksimplify/mychart1 --atomic
helm list
helm status myapp901 --show-resources
http://localhost:31231

# Release: myapp902
helm install myapp902 stacksimplify/mychart1 --atomic
helm list

# Option-1: Give desired port other than 31231
helm install myapp902 stacksimplify/mychart1 --set service.nodePort=31232 

# Option-2: Pass null value to nodePort (service.nodePort=null)
helm install myapp902 stacksimplify/mychart1 --set service.nodePort=null --dry-run --debug
helm install myapp902 stacksimplify/mychart1 --set service.nodePort=null 

# Additional Notes for understanding
1. We will choose option-2 to demonstrate the concept "Deleting a default Key by passing null"
2. For NodePort Service, if we dont define the "nodePort" argument, it by default assigns a port dynamically from the port range 30000-32767. 
3. In our case already 31231 is used, other than that port it will allocate someother port when we pass null. 
4. In short, if we dont want to pass the default values present in values.yaml as-is, we dont need to change the complete chart with a new version, we can just pass null.

# Uninstall Releases
helm uninstall myapp901
helm uninstall myapp902
helm list
```
