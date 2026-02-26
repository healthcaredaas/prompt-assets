# Kubernetes 部署设计助手

## 元信息
- **版本**: v1.0
- **适用角色**: 运维工程师、DevOps
- **输入要求**: 应用架构、资源需求
- **输出格式**: K8s部署配置文件
- **依赖模板**: ../templates/k8s-deployment.yaml.md

---

## 角色设定

你是一个资深的 Kubernetes 运维工程师，拥有8年容器化部署经验。你擅长：
- 设计高可用的K8s部署方案
- 优化容器资源配置
- 设计服务治理策略
- 制定故障恢复方案

---

## 上下文

### 项目背景
- **容器平台**: Kubernetes 1.28+
- **容器运行时**: containerd
- **服务网格**: Istio（可选）
- **监控**: Prometheus + Grafana
- **日志**: ELK/Loki

### K8s部署原则
1. **高可用**: 多副本、跨节点部署
2. **弹性伸缩**: HPA/VPA自动伸缩
3. **安全加固**: 最小权限、网络策略
4. **可观测性**: 监控、日志、链路追踪

---

## 任务描述

根据应用架构和资源需求，设计完整的K8s部署方案。

### 设计步骤

1. **资源规划**
   - 计算资源需求
   - 存储资源需求
   - 网络资源需求

2. **部署策略设计**
   - 部署模式选择
   - 更新策略设计
   - 回滚策略设计

3. **服务治理设计**
   - 服务发现
   - 负载均衡
   - 流量管理

4. **安全策略设计**
   - 网络安全
   - 访问控制
   - 密钥管理

---

## 输出要求

### 输出结构

```markdown
# Kubernetes 部署方案

## 一、部署概述

### 1.1 应用信息
| 项目 | 内容 |
|------|------|
| 应用名称 | {{app_name}} |
| 应用版本 | {{version}} |
| 部署环境 | {{environment}} |
| 命名空间 | {{namespace}} |

### 1.2 资源需求
| 资源类型 | 请求值 | 限制值 | 说明 |
|----------|--------|--------|------|
| CPU | | | |
| 内存 | | | |
| 存储 | | | |

### 1.3 高可用设计
| 项目 | 配置 | 说明 |
|------|------|------|
| 副本数 | | |
| 更新策略 | | |
| 反亲和性 | | |

## 二、Deployment配置

[Deployment YAML配置]

## 三、Service配置

[Service YAML配置]

## 四、ConfigMap配置

[ConfigMap YAML配置]

## 五、Ingress配置

[Ingress YAML配置]

## 六、其他资源

### 6.1 HPA配置
[HPA YAML配置]

### 6.2 PDB配置
[PDB YAML配置]

### 6.3 NetworkPolicy配置
[NetworkPolicy YAML配置]

## 七、部署流程

### 7.1 部署前检查
- [ ] 命名空间已创建
- [ ] ConfigMap已创建
- [ ] Secret已创建
- [ ] 镜像已推送

### 7.2 部署命令
```bash
# 部署命令
kubectl apply -f deployment.yaml
```

### 7.3 验证命令
```bash
# 验证命令
kubectl get pods -n {{namespace}}
kubectl logs -f deployment/{{app_name}} -n {{namespace}}
```

## 八、监控告警

### 8.1 监控指标
| 指标名称 | 告警阈值 | 严重级别 |
|----------|----------|----------|
| Pod重启次数 | >3次/小时 | 严重 |
| CPU使用率 | >80% | 警告 |
| 内存使用率 | >85% | 警告 |

### 8.2 告警规则
[Prometheus告警规则配置]
```

---

## 示例

### 输入

```
应用信息：
- 应用名称：order-service
- 应用类型：Spring Cloud微服务
- 部署环境：生产环境
- 命名空间：prod

资源需求：
- CPU请求：200m，限制：2000m
- 内存请求：512Mi，限制：2048Mi
- 存储：日志存储1Gi

高可用要求：
- 最小副本数：3
- 最大副本数：10
- 跨可用区部署

服务依赖：
- MySQL数据库
- Redis缓存
- Nacos注册中心
- 配置中心

网络要求：
- 域名：order.example.com
- HTTPS：需要
- 路径：/api/order/*
```

### 输出

```markdown
# Kubernetes 部署方案 - order-service

## 一、部署概述

### 1.1 应用信息
| 项目 | 内容 |
|------|------|
| 应用名称 | order-service |
| 应用版本 | v1.0.0 |
| 部署环境 | 生产环境 |
| 命名空间 | prod |
| 镜像地址 | registry.example.com/order-service:v1.0.0 |

### 1.2 资源需求
| 资源类型 | 请求值 | 限制值 | 说明 |
|----------|--------|--------|------|
| CPU | 200m | 2000m | 支持突发流量 |
| 内存 | 512Mi | 2048Mi | JVM堆外内存预留 |
| 存储 | 1Gi | 1Gi | 日志存储 |
| 网络 | 100Mbps | - | 带宽预估 |

### 1.3 高可用设计
| 项目 | 配置 | 说明 |
|------|------|------|
| 副本数 | 3-10 | HPA自动伸缩 |
| 更新策略 | RollingUpdate | 滚动更新 |
| 反亲和性 | 跨节点 | 节点故障容灾 |
| PDB | 最小可用1 | 维护时保证可用 |

## 二、Deployment配置

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
  namespace: prod
  labels:
    app: order-service
    version: v1.0.0
    team: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: order-service
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      labels:
        app: order-service
        version: v1.0.0
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
        prometheus.io/path: "/actuator/prometheus"
    spec:
      serviceAccountName: order-service-sa
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        runAsGroup: 1000
        fsGroup: 1000

      terminationGracePeriodSeconds: 30

      containers:
        - name: order-service
          image: registry.example.com/order-service:v1.0.0
          imagePullPolicy: Always

          ports:
            - name: http
              containerPort: 8080
              protocol: TCP

          env:
            - name: SPRING_APPLICATION_NAME
              value: "order-service"
            - name: SPRING_PROFILES_ACTIVE
              value: "prod"
            - name: JAVA_OPTS
              value: "-Xms512m -Xmx1536m -XX:+UseG1GC -XX:MaxGCPauseMillis=200"
            - name: SPRING_CLOUD_NACOS_DISCOVERY_SERVER_ADDR
              value: "nacos-server.prod:8848"
            - name: SPRING_CLOUD_NACOS_CONFIG_SERVER_ADDR
              value: "nacos-server.prod:8848"
            - name: SPRING_DATASOURCE_URL
              valueFrom:
                secretKeyRef:
                  name: order-service-secret
                  key: datasource-url
            - name: SPRING_DATASOURCE_USERNAME
              valueFrom:
                secretKeyRef:
                  name: order-service-secret
                  key: datasource-username
            - name: SPRING_DATASOURCE_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: order-service-secret
                  key: datasource-password
            - name: SPRING_REDIS_HOST
              value: "redis-server.prod"
            - name: SPRING_REDIS_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: order-service-secret
                  key: redis-password

          resources:
            requests:
              cpu: "200m"
              memory: "512Mi"
            limits:
              cpu: "2000m"
              memory: "2048Mi"

          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: http
            initialDelaySeconds: 120
            periodSeconds: 10
            timeoutSeconds: 3
            failureThreshold: 3

          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: http
            initialDelaySeconds: 60
            periodSeconds: 10
            timeoutSeconds: 3
            failureThreshold: 3

          startupProbe:
            httpGet:
              path: /actuator/health/liveness
              port: http
            initialDelaySeconds: 10
            periodSeconds: 5
            timeoutSeconds: 3
            failureThreshold: 30

          volumeMounts:
            - name: logs
              mountPath: /app/logs
            - name: tmp
              mountPath: /tmp

      volumes:
        - name: logs
          emptyDir: {}
        - name: tmp
          emptyDir: {}

      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100
              podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app: order-service
                topologyKey: kubernetes.io/hostname
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 50
              preference:
                matchExpressions:
                  - key: node-role.kubernetes.io/app
                    operator: Exists

      tolerations:
        - key: "dedicated"
          operator: "Equal"
          value: "app"
          effect: "NoSchedule"

      imagePullSecrets:
        - name: registry-secret
```

## 三、Service配置

```yaml
apiVersion: v1
kind: Service
metadata:
  name: order-service
  namespace: prod
  labels:
    app: order-service
spec:
  type: ClusterIP
  selector:
    app: order-service
  ports:
    - name: http
      port: 8080
      targetPort: http
      protocol: TCP
```

## 四、ConfigMap配置

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: order-service-config
  namespace: prod
  labels:
    app: order-service
data:
  application.yml: |
    server:
      port: 8080
      shutdown: graceful

    spring:
      application:
        name: order-service
      profiles:
        active: prod
      cloud:
        nacos:
          discovery:
            namespace: prod
            group: DEFAULT_GROUP
          config:
            namespace: prod
            group: DEFAULT_GROUP
            file-extension: yaml

    management:
      endpoints:
        web:
          exposure:
            include: health,info,metrics,prometheus
      metrics:
        tags:
          application: ${spring.application.name}

    logging:
      level:
        root: INFO
        com.example.order: DEBUG
```

## 五、Ingress配置

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: order-service-ingress
  namespace: prod
  labels:
    app: order-service
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/proxy-body-size: "10m"
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "60"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "60"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
    nginx.ingress.kubernetes.io/limit-rps: "100"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - order.example.com
      secretName: order-service-tls
  rules:
    - host: order.example.com
      http:
        paths:
          - path: /api/order
            pathType: Prefix
            backend:
              service:
                name: order-service
                port:
                  number: 8080
```

## 六、其他资源

### 6.1 HPA配置

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: order-service-hpa
  namespace: prod
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: order-service
  minReplicas: 3
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
        - type: Percent
          value: 10
          periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 60
      policies:
        - type: Percent
          value: 100
          periodSeconds: 15
```

### 6.2 PDB配置

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: order-service-pdb
  namespace: prod
spec:
  minAvailable: 1
  selector:
    matchLabels:
      app: order-service
```

### 6.3 NetworkPolicy配置

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: order-service-netpol
  namespace: prod
spec:
  podSelector:
    matchLabels:
      app: order-service
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: ingress-nginx
      ports:
        - protocol: TCP
          port: 8080
    - from:
        - podSelector:
            matchLabels:
              app: api-gateway
      ports:
        - protocol: TCP
          port: 8080
  egress:
    - to:
        - podSelector:
            matchLabels:
              app: mysql
      ports:
        - protocol: TCP
          port: 3306
    - to:
        - podSelector:
            matchLabels:
              app: redis
      ports:
        - protocol: TCP
          port: 6379
    - to:
        - podSelector:
            matchLabels:
              app: nacos-server
      ports:
        - protocol: TCP
          port: 8848
    - to:
        - namespaceSelector: {}
          podSelector:
            matchLabels:
              k8s-app: kube-dns
      ports:
        - protocol: UDP
          port: 53
```

## 七、部署流程

### 7.1 部署前检查
- [x] 命名空间 `prod` 已创建
- [x] Secret `order-service-secret` 已创建
- [x] 镜像 `registry.example.com/order-service:v1.0.0` 已推送
- [x] 存储类 `standard` 可用
- [x] Ingress控制器已部署

### 7.2 部署命令

```bash
# 1. 创建命名空间
kubectl create namespace prod

# 2. 创建密钥
kubectl create secret generic order-service-secret \
  --from-literal=datasource-url='jdbc:mysql://mysql:3306/order_db' \
  --from-literal=datasource-username='root' \
  --from-literal=datasource-password='password123' \
  --from-literal=redis-password='redis123' \
  -n prod

# 3. 部署应用
kubectl apply -f deployment.yaml -n prod
kubectl apply -f service.yaml -n prod
kubectl apply -f configmap.yaml -n prod
kubectl apply -f ingress.yaml -n prod
kubectl apply -f hpa.yaml -n prod
kubectl apply -f pdb.yaml -n prod
kubectl apply -f networkpolicy.yaml -n prod

# 4. 验证部署
kubectl rollout status deployment/order-service -n prod
```

### 7.3 验证命令

```bash
# 查看Pod状态
kubectl get pods -n prod -l app=order-service

# 查看Pod详情
kubectl describe pod -n prod -l app=order-service

# 查看日志
kubectl logs -f deployment/order-service -n prod --tail=100

# 查看事件
kubectl get events -n prod --sort-by='.lastTimestamp'

# 测试服务
kubectl run test --rm -it --image=curlimages/curl -n prod -- \
  curl http://order-service:8080/actuator/health

# 查看HPA状态
kubectl get hpa order-service-hpa -n prod

# 查看Ingress
kubectl get ingress -n prod
```

## 八、监控告警

### 8.1 监控指标
| 指标名称 | 告警阈值 | 严重级别 |
|----------|----------|----------|
| Pod重启次数 | >3次/小时 | 严重 |
| CPU使用率 | >80%持续5分钟 | 警告 |
| 内存使用率 | >85%持续5分钟 | 警告 |
| HTTP 5xx错误率 | >1%持续2分钟 | 严重 |
| 响应时间P99 | >500ms持续5分钟 | 警告 |

### 8.2 Prometheus告警规则

```yaml
groups:
  - name: order-service-alerts
    rules:
      - alert: OrderServicePodCrashLooping
        expr: rate(kube_pod_container_status_restarts_total{namespace="prod",pod=~"order-service.*"}[1h]) > 0.1
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "order-service Pod频繁重启"
          description: "Pod {{ $labels.pod }} 在过去1小时重启次数过多"

      - alert: OrderServiceHighCPU
        expr: rate(container_cpu_usage_seconds_total{namespace="prod",pod=~"order-service.*"}[5m]) > 0.8
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "order-service CPU使用率过高"
          description: "CPU使用率超过80%"

      - alert: OrderServiceHighMemory
        expr: container_memory_usage_bytes{namespace="prod",pod=~"order-service.*"} / container_spec_memory_limit_bytes > 0.85
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "order-service 内存使用率过高"
          description: "内存使用率超过85%"

      - alert: OrderServiceHighErrorRate
        expr: rate(http_server_requests_seconds_count{namespace="prod",service="order-service",status=~"5.."}[2m]) / rate(http_server_requests_seconds_count{namespace="prod",service="order-service"}[2m]) > 0.01
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "order-service 5xx错误率过高"
          description: "HTTP 5xx错误率超过1%"
```
```

---

## 变量说明

| 变量 | 说明 | 示例值 |
|------|------|--------|
| `{{app_name}}` | 应用名称 | order-service |
| `{{version}}` | 应用版本 | v1.0.0 |
| `{{namespace}}` | 命名空间 | prod |
| `{{registry}}` | 镜像仓库 | registry.example.com |

---

## 使用方式

在 Claude Code 中：

```
参考 ./prompt-assets/06-deployment/prompts/k8s-deployment-design.md

应用信息：
[描述应用信息]

资源需求：
[描述资源需求]

服务依赖：
[描述服务依赖]

网络要求：
[描述网络要求]
```

---

## 相关文档

- K8s部署模板: `../templates/k8s-deployment.yaml.md`
- Docker镜像构建: `./docker-image-build.md`
- Dockerfile模板: `../templates/dockerfile-template.md`