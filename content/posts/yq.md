---
title: "YAML processing with yq"
date: 2021-07-27T15:01:22+07:00
draft: false
---

Nowadays many configurations are done via YAML format. GitLab CI, GitHub Actions, Hugo, Docker compose, and of course Kubernetes. A YAML processor like `yq` will be useful when working with tools like `kubectl` that can output YAML.

## Basic features
For examples in basic features section, we use a file `deployment.yaml` with the following content:
```yaml
apiVersion: v1
kind: Deployment
metadata:
  name: hello-world
spec:
  template:
    spec:
      containers:
        - name: hello
          image: hello:v0.0.1
        - name: world
          image: world:v0.0.2
```

### Get specific field
To get only the `.metadata.name` field, use:
```bash
yq e '.metadata.name' deployment.yaml
```

### Loop over array
Let's get all images:
```bash
yq e '.spec.template.spec.containers[].image' deployment.yaml
```

### Delete field
To delete the first container, use:
```bash
yq e 'delete(.spec.template.spec.containers[0])' deployment.yaml
```

### Update field
Update the second container name to `new-world`:
```bash
yq e '.spec.containers[1].name = "new-world"' deployment.yaml
```

## Specific kubectl example
Here's some example of using kubectl with yq.

### Get Service names where the type is NodePort
```bash
kubectl get svc -o yaml | yq e '.items[] | select(.spec.type == "NodePort")' -
```

### Get Deployments with some fields removed
```bash
kubectl get deployment -o yaml | yq e '
	.items[] |
	del(.metadata.annotations) |
	del(.metadata.creationTimestamp) |
	del(.metadata.generation) |
	del(.metadata.resourceVersion) |
	del(.metadata.selfLink) |
	del(.metadata.uid) |
	del(.status) |
	splitDoc' -
```

## Reference
To learn more about yq, just check the official documentation at <https://mikefarah.gitbook.io/yq/>
