# private-docker-registry in kind cluster following all standards i.e with tls certs and authentication.

## certificate command
openssl req -x509 -newkey rsa:4096 -days 365 -nodes -sha256 -keyout certs/tls.key -out certs/tls.crt -subj "/CN=my-registry" -addext "subjectAltName= DNS:my-registry"

## nginx-secret to pull images from private registry
kubectl create secret docker-registry nginx-secret --docker-server=my-registry:5000 --docker-username=myuser --docker-password=mypasswd

## auth-secret sealed secret
docker run \\n--entrypoint htpasswd \\nhttpd:2 -Bbn myuser mypasswd > auth/htpasswd

kubectl create secret generic auth-secret --from-file=/Users/shashankshahi/kubernetes/registry/auth/htpasswd

## certs-secret

kubectl create secret tls certs-secret --cert=/Users/shashankshahi/kubernetes/registry/certs/tls.crt --key=/Users/shashankshahi/kubernetes/registry/certs/tls.key
