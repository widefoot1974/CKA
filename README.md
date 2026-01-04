# CKA
CKA 시험 대비 문제 풀이
(Z2hwX2U4NTFvakh2amt6TGs3WEdSdVZlTXNUc0djN0pFbjRPcVR3NQo=)

1) CKA Simulator - A
2) CKA Simulator - B

# 가입자 생성 작업
### 1) Create private key
```bash
# 2048 비트의 개인키 생성
openssl genrsa -out myuser.key 2048
# 인증 요청서(CSR) 생성 (CN은 사용자 이름이 됩니다)
openssl req -new -key myuser.key -out myuser.csr -subj "/CN=myuser"
```

### 2) Create CertificateSiging Request
#### 1. yaml 파일 생성
```yaml
cat <<EOF > myuser-csr.yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: my-svc.my-namespace
spec:
  # 생성한 csr 파일을 base64로 인코딩하여 삽입
  request: $(cat myuser.csr | base64 | tr -d '\n')
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400 # 24시간 (선택 사항)
  usages:
  - client auth
EOF
```
#### 2. 생성된 파일 적용
```bash
kubectl apply -f myuser-csr.yaml
kubectl get csr  # CONDITION Pending
```

### 3) Approve certificate signing reqest
```bash
kubectl certificate approve myuser
kubectl get csr  # CONDITION - Approved,Issued
```

### 4) Get the certificate
```bash
kubectl get csr/myser -o yaml
kubectl get csr/myuser -o jsonpath='{.status.certificate}' | base64 -d > myuser.crt
```

### 5) Add to kubeconfig
```bash
# Add user 
kubectl config set-credentials myuser --client-certificate=myuser.crt --client-key=myuser.key --embed-certs=true
# Add context
kubectl config set-context myuser --cluster=kubernetes --user=myuser
```
