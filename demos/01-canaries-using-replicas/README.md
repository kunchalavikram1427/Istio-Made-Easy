# Canaries with replicas
Response model of `StudentInfo` application
```json
{
    "data": [
        {
            "name": "John",
            "age": 30,
            "address": "501, Street A, Mumbai",
            "details_server": {
                "host": "display-address-8f6d85db9-6w5vr",
                "ip": "172.17.0.10",
                "time": "19:15:41"
            },
            "hobbies": [
                "Soccer",
                "Badminton",
                "Cricket"
            ],
            "hobbies_server": {
                "host": "hobby-58bb9d45cc-zrnhx",
                "ip": "172.17.0.12",
                "time": "19:15:41"
            },
            "photo": "John.jpeg",
            "photo_server": {
                "host": "photo-v2-696b465797-pbjkz",
                "ip": "172.17.0.14",
                "time": "19:15:41"
            }
        },
        {
            "name": "Clark",
            "age": 28,
            "address": "77/1, Street B, Delhi",
            "details_server": {
                "host": "display-address-8f6d85db9-6w5vr",
                "ip": "172.17.0.10",
                "time": "19:15:41"
            },
            "hobbies": [
                "Basketball",
                "Tennis",
                "Chess"
            ],
            "hobbies_server": {
                "host": "hobby-58bb9d45cc-zrnhx",
                "ip": "172.17.0.12",
                "time": "19:15:41"
            },
            "photo": "Clark.jpeg",
            "photo_server": {
                "host": "photo-v2-696b465797-pbjkz",
                "ip": "172.17.0.14",
                "time": "19:15:41"
            }
        },
        {
            "name": "David",
            "age": 32,
            "address": "22-1-1/2, Street C, Hyderabad",
            "details_server": {
                "host": "display-address-8f6d85db9-6w5vr",
                "ip": "172.17.0.10",
                "time": "19:15:41"
            },
            "hobbies": [
                "Basketball",
                "Badminton",
                "Volleyball"
            ],
            "hobbies_server": {
                "host": "hobby-58bb9d45cc-zrnhx",
                "ip": "172.17.0.12",
                "time": "19:15:41"
            },
            "photo": "David.jpeg",
            "photo_server": {
                "host": "photo-v2-696b465797-pbjkz",
                "ip": "172.17.0.14",
                "time": "19:15:41"
            }
        }
    ]
}

```
Deploy the application
```sh
kubectl apply -f deployments-no-istio-canaries.yaml
```
Adjust the replicas
```sh
kubectl scale deploy/photo-v1 --replicas=8
kubectl scale deploy/photo-v2 --replicas=2
```
Send traffic
```sh
while true; do curl -s 'http://localhost:30100/api/student/John' ; echo ; sleep 1 ; done
```