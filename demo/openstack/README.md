Assumes existence of flavor, image and network.

1. `kubectl apply -f .`
2. Observe creation of resources
3. Connect to the instance `ssh <user>@<host> -i crossplane-playground-openstack-keypair-er.pem`
4. Check available resources `lscpu && free -h`
5. Change flavor
6. Observe adjustment of the instance
7. Repeat steps 3, 4; the reported values should differ
