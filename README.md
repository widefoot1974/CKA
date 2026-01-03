# CKA
CKA 시험 대비 문제 풀이
(Z2hwX2U4NTFvakh2amt6TGs3WEdSdVZlTXNUc0djN0pFbjRPcVR3NQo=)

1) CKA Simulator - A
2) CKA Simulator - B

# 가입자 생성 작업

1) Create private key
openssl genrsa -out myuser.key 2048
openssl req -new - key myuser.key -out myuser.csr -subj "/CN=myuser"

3) Create CertificateSiging Request
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: my-svc.my-namespace
spec:
  request: $(cat myuser.csr | base64 | tr -d '\n')
  signerName: example.com/serving
  usages:
  - client auth
EOF
