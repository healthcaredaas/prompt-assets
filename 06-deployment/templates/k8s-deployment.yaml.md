# Kubernetes 部署配置模板

> 版本: v1.0 | 最后更新: 2026-02-26

本文档提供 Kubernetes 部署的标准配置模板，包含 Deployment、Service、ConfigMap、Secret、Ingress 等核心资源。

---

## 一、Deployment 配置

### 1.1 标准Deployment

```yaml
# ==========================================
# Deployment - 标准配置
# 适用: 无状态应用
# ==========================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{app-name}}
  namespace: {{namespace}}
  labels:
    app: {{app-name}}
    version: v1.0.0
spec:
  replicas: 3
  selector:
    matchLabels:
      app: {{app-name}}
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1          # 滚动更新时最多多出的Pod数量
      maxUnavailable: 0    # 滚动更新时最多不可用的Pod数量
  template:
    metadata:
      labels:
        app: {{app-name}}
        version: v1.0.0
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
        prometheus.io/path: "/actuator/prometheus"
    spec:
      # 服务账户
      serviceAccountName: {{app-name}}-sa

      # 安全上下文
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        runAsGroup: 1000
        fsGroup: 1000

      # 优雅关闭时间
      terminationGracePeriodSeconds: 30

      # 容器配置
      containers:
        - name: {{app-name}}
          image: {{registry}}/{{app-name}}:{{version}}
          imagePullPolicy: Always

          # 端口配置
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP

          # 环境变量
          env:
            - name: JAVA_OPTS
              value: "-Xms512m -Xmx1024m"
            - name: SPRING_PROFILES_ACTIVE
              value: "k8s"
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace

          # 从ConfigMap读取配置
          envFrom:
            - configMapRef:
                name: {{app-name}}-config

          # 资源限制
          resources:
            requests:
              cpu: "100m"
              memory: "512Mi"
            limits:
              cpu: "1000m"
              memory: "1024Mi"

          # 存活探针
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: http
            initialDelaySeconds: 60
            periodSeconds: 10
            timeoutSeconds: 3
            failureThreshold: 3

          # 就绪探针
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: http
            initialDelaySeconds: 30
            periodSeconds: 10
            timeoutSeconds: 3
            failureThreshold: 3

          # 启动探针
          startupProbe:
            httpGet:
              path: /actuator/health/liveness
              port: http
            initialDelaySeconds: 10
            periodSeconds: 5
            timeoutSeconds: 3
            failureThreshold: 30

          # 卷挂载
          volumeMounts:
            - name: config
              mountPath: /app/config
              readOnly: true
            - name: logs
              mountPath: /app/logs
            - name: tmp
              mountPath: /tmp

      # 卷定义
      volumes:
        - name: config
          configMap:
            name: {{app-name}}-config
        - name: logs
          emptyDir: {}
        - name: tmp
          emptyDir: {}

      # 节点亲和性
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100
              podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app: {{app-name}}
                topologyKey: kubernetes.io/hostname

      # 容忍配置
      tolerations:
        - key: "dedicated"
          operator: "Equal"
          value: "app"
          effect: "NoSchedule"
```

### 1.2 微服务Deployment

```yaml
# ==========================================
# Deployment - 微服务配置
# 特点: 配置中心、服务注册、链路追踪
# ==========================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{service-name}}
  namespace: {{namespace}}
  labels:
    app: {{service-name}}
    version: v1.0.0
    team: {{team-name}}
spec:
  replicas: 3
  selector:
    matchLabels:
      app: {{service-name}}
  template:
    metadata:
      labels:
        app: {{service-name}}
        version: v1.0.0
      annotations:
        # Prometheus监控
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
        prometheus.io/path: "/actuator/prometheus"
        # 链路追踪
        sidecar.istio.io/inject: "true"
    spec:
      serviceAccountName: {{service-name}}-sa
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000

      containers:
        - name: {{service-name}}
          image: {{registry}}/{{service-name}}:{{version}}
          ports:
            - name: http
              containerPort: 8080

          env:
            # 服务名
            - name: SPRING_APPLICATION_NAME
              value: "{{service-name}}"
            # 配置中心
            - name: SPRING_CONFIG_IMPORT
              value: "configserver:http://config-server:8888"
            - name: SPRING_CLOUD_CONFIG_LABEL
              value: "main"
            # Nacos服务发现
            - name: SPRING_CLOUD_NACOS_DISCOVERY_SERVER_ADDR
              value: "nacos-server:8848"
            - name: SPRING_CLOUD_NACOS_DISCOVERY_NAMESPACE
              value: "{{namespace}}"
            # 链路追踪
            - name: SPRING_ZIPKIN_BASE_URL
              value: "http://zipkin-server:9411"
            # JVM参数
            - name: JAVA_OPTS
              value: "-Xms512m -Xmx1024m -XX:+UseG1GC"

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

          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: http
            initialDelaySeconds: 60
```

---

## 二、Service 配置

### 2.1 ClusterIP Service

```yaml
# ==========================================
# Service - ClusterIP
# 适用: 内部服务访问
# ==========================================
apiVersion: v1
kind: Service
metadata:
  name: {{app-name}}
  namespace: {{namespace}}
  labels:
    app: {{app-name}}
spec:
  type: ClusterIP
  selector:
    app: {{app-name}}
  ports:
    - name: http
      port: 8080
      targetPort: http
      protocol: TCP
```

### 2.2 NodePort Service

```yaml
# ==========================================
# Service - NodePort
# 适用: 外部直接访问（测试环境）
# ==========================================
apiVersion: v1
kind: Service
metadata:
  name: {{app-name}}-nodeport
  namespace: {{namespace}}
  labels:
    app: {{app-name}}
spec:
  type: NodePort
  selector:
    app: {{app-name}}
  ports:
    - name: http
      port: 8080
      targetPort: http
      nodePort: 30080  # 范围: 30000-32767
      protocol: TCP
```

### 2.3 LoadBalancer Service

```yaml
# ==========================================
# Service - LoadBalancer
# 适用: 云环境外部访问
# ==========================================
apiVersion: v1
kind: Service
metadata:
  name: {{app-name}}-lb
  namespace: {{namespace}}
  labels:
    app: {{app-name}}
  annotations:
    # 云厂商特定注解
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
    service.beta.kubernetes.io/aws-load-balancer-cross-zone-load-balancing-enabled: "true"
spec:
  type: LoadBalancer
  selector:
    app: {{app-name}}
  ports:
    - name: http
      port: 80
      targetPort: http
      protocol: TCP
```

### 2.4 Headless Service

```yaml
# ==========================================
# Service - Headless
# 适用: StatefulSet、直接Pod访问
# ==========================================
apiVersion: v1
kind: Service
metadata:
  name: {{app-name}}-headless
  namespace: {{namespace}}
  labels:
    app: {{app-name}}
spec:
  type: ClusterIP
  clusterIP: None  # Headless
  selector:
    app: {{app-name}}
  ports:
    - name: http
      port: 8080
      targetPort: http
```

---

## 三、ConfigMap 配置

### 3.1 应用配置

```yaml
# ==========================================
# ConfigMap - 应用配置
# ==========================================
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{app-name}}-config
  namespace: {{namespace}}
  labels:
    app: {{app-name}}
data:
  # 应用配置
  application.yml: |
    server:
      port: 8080
      shutdown: graceful

    spring:
      application:
        name: {{app-name}}
      profiles:
        active: k8s
      datasource:
        url: jdbc:mysql://mysql:3306/{{database}}
        driver-class-name: com.mysql.cj.jdbc.Driver
        hikari:
          maximum-pool-size: 20
          minimum-idle: 5
          idle-timeout: 600000
          connection-timeout: 30000
      redis:
        host: redis
        port: 6379
        database: 0
        timeout: 3000

    # 日志配置
    logging:
      level:
        root: INFO
        com.example: DEBUG
      pattern:
        console: "%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n"

    # 监控配置
    management:
      endpoints:
        web:
          exposure:
            include: health,info,metrics,prometheus
      metrics:
        tags:
          application: ${spring.application.name}

  # 环境变量配置
  SPRING_PROFILES_ACTIVE: "k8s"
  LOG_LEVEL: "INFO"
  TZ: "Asia/Shanghai"
```

### 3.2 环境特定配置

```yaml
# ==========================================
# ConfigMap - 开发环境配置
# ==========================================
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{app-name}}-config-dev
  namespace: dev
data:
  application.yml: |
    spring:
      datasource:
        url: jdbc:mysql://mysql-dev:3306/{{database}}
      redis:
        host: redis-dev

---
# ==========================================
# ConfigMap - 生产环境配置
# ==========================================
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{app-name}}-config-prod
  namespace: prod
data:
  application.yml: |
    spring:
      datasource:
        url: jdbc:mysql://mysql-prod:3306/{{database}}
        hikari:
          maximum-pool-size: 50
      redis:
        host: redis-prod
        cluster:
          nodes: redis-cluster:6379
```

---

## 四、Secret 配置

### 4.1 敏感信息配置

```yaml
# ==========================================
# Secret - 敏感配置
# 注意: 生产环境建议使用外部密钥管理
# ==========================================
apiVersion: v1
kind: Secret
metadata:
  name: {{app-name}}-secret
  namespace: {{namespace}}
  labels:
    app: {{app-name}}
type: Opaque
data:
  # Base64编码的值
  # echo -n "root" | base64
  DB_USERNAME: cm9vdA==
  DB_PASSWORD: cm9vdDEyMzQ1Ng==
  REDIS_PASSWORD: cmVkaXNfcGFzc3dvcmQ=
  JWT_SECRET: and0X3NlY3JldF9rZXlfZm9yX3Rva2VuX2dlbmVyYXRpb24=
```

### 4.2 TLS证书配置

```yaml
# ==========================================
# Secret - TLS证书
# ==========================================
apiVersion: v1
kind: Secret
metadata:
  name: {{app-name}}-tls
  namespace: {{namespace}}
type: kubernetes.io/tls
data:
  # Base64编码的证书
  tls.crt: LS0tLS1CRUdJTi...
  tls.key: LS0tLS1CRUdJTi...
```

---

## 五、Ingress 配置

### 5.1 标准Ingress

```yaml
# ==========================================
# Ingress - HTTP路由
# ==========================================
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{app-name}}-ingress
  namespace: {{namespace}}
  labels:
    app: {{app-name}}
  annotations:
    # Nginx Ingress注解
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/proxy-body-size: "50m"
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "60"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "60"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
    # 限流配置
    nginx.ingress.kubernetes.io/limit-rps: "100"
    nginx.ingress.kubernetes.io/limit-connections: "50"
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - {{app-name}}.example.com
      secretName: {{app-name}}-tls
  rules:
    - host: {{app-name}}.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: {{app-name}}
                port:
                  number: 8080
```

### 5.2 多服务Ingress

```yaml
# ==========================================
# Ingress - 多服务路由
# ==========================================
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{app-name}}-ingress
  namespace: {{namespace}}
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  ingressClassName: nginx
  rules:
    - host: api.example.com
      http:
        paths:
          # 用户服务
          - path: /user(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: user-service
                port:
                  number: 8080
          # 订单服务
          - path: /order(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: order-service
                port:
                  number: 8080
          # 商品服务
          - path: /product(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: product-service
                port:
                  number: 8080
```

---

## 六、其他资源

### 6.1 HorizontalPodAutoscaler

```yaml
# ==========================================
# HPA - 自动伸缩
# ==========================================
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: {{app-name}}-hpa
  namespace: {{namespace}}
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: {{app-name}}
  minReplicas: 2
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

### 6.2 PodDisruptionBudget

```yaml
# ==========================================
# PDB - Pod中断预算
# ==========================================
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: {{app-name}}-pdb
  namespace: {{namespace}}
spec:
  minAvailable: 1  # 或使用 maxUnavailable: 1
  selector:
    matchLabels:
      app: {{app-name}}
```

### 6.3 NetworkPolicy

```yaml
# ==========================================
# NetworkPolicy - 网络策略
# ==========================================
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: {{app-name}}-netpol
  namespace: {{namespace}}
spec:
  podSelector:
    matchLabels:
      app: {{app-name}}
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: ingress-nginx
        - podSelector:
            matchLabels:
              app: another-app
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
        - namespaceSelector: {}
          podSelector:
            matchLabels:
              k8s-app: kube-dns
      ports:
        - protocol: UDP
          port: 53
```

---

## 使用说明

1. **替换变量**: 将 `{{variable}}` 替换为实际值
2. **调整资源**: 根据应用需求调整 CPU/内存 配置
3. **配置探针**: 根据应用启动时间调整探针参数
4. **安全加固**: 使用非root用户，配置网络策略

## 相关文档

- K8s部署设计: `../prompts/k8s-deployment-design.md`
- Docker镜像构建: `../prompts/docker-image-build.md`
- Dockerfile模板: `./dockerfile-template.md`