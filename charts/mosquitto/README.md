# Mosquitto Kubernetes Helm Chart

A Kubernetes Helm Chart for Mosquitto.

## Installation

Install the helm chart repository:

```bash
helm repo add mosquitto https://sintef.github.io/mosquitto-helm-chart
```

Install the chart:

```bash
helm install mosquitto mosquitto/mosquitto -f your-values.yaml
```

## Configuration

| Property | Description | Default |
| -------- | ----------- | ------- |
| `mqttOverWebsocket` | Wether MQTT Obver Websocket is enabled | `true` |
| `auth.enabled` | Enabled authentication, otherwise anonymous users are allowed | `true` |
| `auth.users[index].username` | Username | `admin`|
| `auth.users[index].password` | Password in Mosquitto's sha512-pbkdf2 format | `admin` (in Mosquitto's sha512-pbkdf2 format) |
| `auth.users[index].acl[index].topic` | MQTT Topic of the access control rule | `#` |
| `auth.users[index].acl[index].access` | Access level of the access control rule | `readwrite` |
| `general.maxInflightMessages` | Maximum number of messages that can be in-flight at once, set to 0 for unlimited. | `20` |
| `general.maxQueuedMessages` | Maximum number of messages that can be queued for a client, set to 0 for unlimited (not recommended) | `1000` |
| `image.repository` | The image | `eclipse-mosquitto` |
| `image.tag` | The image tag | `2.0.15` |
| `ingress` | Ingress settings | *see values.yaml* |
| `resources` | Resources settings | *see values.yaml* |
| `nodeSelector` | Note selector settings | *see values.yaml* |
| `tolerations` | Tolerations settings | *see values.yaml* |
| `affinity` | Affinity settings | *see values.yaml* |

## Passwords

Use the software `mosquitto_passwd` to generate the password in the correct format:

```bash
FILE=$(mktemp)
mosquitto_passwd -H sha512-pbkdf2 -c $FILE USERNAME
cut -d ":" -f 2 $FILE
rm $FILE
```

## TLS

This Helm chart relies on the Kubernetes Ingress to manage the TLS termination and the certificates. Mosquitto is not configured with TLS, and the internal MQTT and MQTT over wobsocket services are in clear text. Consider using a Kubernetes network plugin that supports encryption if it's a problem, or you can improve this Helm chart to add TLS support inside Mosquitto.

Kubernetes' Ingress do not handle raw TCP but HTTP, therefore TLS is only supported when MQTT over Websocket is enabled.