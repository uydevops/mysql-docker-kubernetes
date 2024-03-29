#uDevops 



MYSQL_CONTAINER_NAME="mysql_db"

K8S_DEPLOYMENT_NAME="mysql-deployment"

K8S_NAMESPACE="default"

K8S_DEPLOYMENT_FILE="mysql-deployment.yaml"

MYSQL_VOLUME_NAME="mysql_data"

MYSQL_ROOT_PASSWORD=$(cat /path/to/mysql_password_file)

echo "Docker'daki MySQL konteynerini durduruluyor..."
docker stop $MYSQL_CONTAINER_NAME

echo "Docker volume'u Kubernetes'e kopyalanıyor..."
docker run --rm -v $MYSQL_VOLUME_NAME:/mysql_data -v $(pwd):/kubernetes alpine sh -c "cp -r /mysql_data /kubernetes"

cat <<EOF > $K8S_DEPLOYMENT_FILE
apiVersion: apps/v1
kind: Deployment
metadata:
  name: $K8S_DEPLOYMENT_NAME
  namespace: $K8S_NAMESPACE
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:latest
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "$MYSQL_ROOT_PASSWORD"
        volumeMounts:
        - name: mysql-data
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-data
        persistentVolumeClaim:
          claimName: mysql-pvc
EOF

echo "Kubernetes'e MySQL deployment'ı yükleniyor..."
kubectl apply -f $K8S_DEPLOYMENT_FILE -n $K8S_NAMESPACE

echo "MySQL deployment'ı başarıyla yüklendi!"
