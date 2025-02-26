```bash
mkdir -p /tools/teleport/rbac
cd /tools/teleport/rbac
```
# Tạo user admin
- full quyền

vi admin.yaml

```yml
kind: role
version: v7
metadata:
  name: admin
spec:
  allow:
    logins: ["root"] # user login
    node_labels: # truy cập 
      "*": "*"
    kubernetes_groups: ["system:masters"] # Được gán vào nhóm system:masters, nhóm này có quyền quản trị cao nhất trong Kubernetes.
    rules:
      - resources: ["*"] # quyền truy cập resouce trên teleport
        verbs: ["*"] # có những chức năng hành động cụ thể tạo mới, read, delete and list, watch, connect
  deny: {}
```

```bash
tctl create -f admin.yaml
tctl users add admin --roles=admin
# password: admin1234567
```

# user member

```bash
vi member.yaml
```

```yaml
kind: role
version: v7
metadata:
  name: member
spec:
  allow:
    logins: ["member"]  # Chỉ cho phép SSH bằng 'member'
    node_labels:
      "*": "*"
    rules:
      - resources: ["node"]
        verbs: ["*"]  # Toàn quyền với node (read, create, update, delete)
  deny:
    logins: ["root"]  # Không cho phép SSH với user root

```

```bash
tctl create -f member.yaml
tctl users add member --roles=member
# password: member1234567
```

# user reader


```bash

```

```yaml
kind: role
version: v7
metadata:
  name: reader
spec:
  allow:
    logins: ["reader"]  
    node_labels:
      "*": "*"
    rules:
      - resources: ["node"]
        verbs: ["list", "read"]  
  deny: {}
```

```bash
tctl create -f reader.yaml
tctl users add reader --roles=reader

# reader1234567
```

####
- Ví dụ ta cấp quyền root cho bạn devops rồi, bạn ý vào hoàn toàn có thể tạo ra những role tương tác như vậy
===> Làm sao không cho phép bạn ý truy cập vào server này nữa ==> trong teleport có lables 

```yaml
ssh_service:
  enabled: true
  labels:
    environment: dev
```

- Ta sẽ gán nhãn vào một server agent ví dụ server master, để chỉ định rằng đây là server master và ta sẽ sử dụng pp loại trừ, ta sẽ denny server nào có lable là master đi sẽ không được phép truy cập

```bash
 vi /etc/teleport.yaml
```

- sẽ thấy trường là ssh_service

```yaml
ssh_service:
  enabled: "yes"
  labels:
    teleport.internal/resource-id: e0e9a75e-3619-414d-a120-50d17a1c6864
    namesv: "teleport-master"
```

```bash
systemctl restart teleport
```

```yaml
kind: role
version: v7
metadata:
  name: reader
spec:
  allow:
    # logins: ["reader"]  
    node_labels:
      "*": "*"
    rules:
      - resources: ["node"]
        verbs: ["list", "read"]  
  deny:
    node_labels:
      "namesv": "load-balancer"

```


