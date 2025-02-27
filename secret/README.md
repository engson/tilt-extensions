# Secret

Author: [Nick Santos](https://github.com/nicks)

Helper functions for creating Kubernetes secrets.

## Functions

### secret_yaml_generic

```
secret_yaml_generic(name: str, namespace: str = "", from_file: Union[str, List] = None, secret_type: str = None): Blob
```

Returns YAML for a generic secret.

* `from_file` ( str ) – equivalent to `kubectl create secret --from-file`
* `secret_type` ( str ) - equivalent to `kubectl create secret --type`

### secret_create_generic

```
secret_create_generic(name: str, namespace: str = "", from_file: Union[str, List] = None, secret_type: str = None)
```

Deploys a secret to the cluster. Equivalent to

```
load('ext://secret', 'secret_yaml_generic')
k8s_yaml(secret_yaml_generic('name', from_file=[...]))
```

### secret_from_dict

```
secret_from_dict(name: str, namespace: str = "", inputs = None): blob
```

Returns YAML for a secret from a dictionary.

* `inputs` ( dict) - A dict of keys and values to use. Nesting is not supported


### secret_yaml_tls

```
secret_yaml_tls(name: str, cert: str, key: str, namespace: str = ""): Blob
```

Returns YAML for a TLS secret. Equivalent to `kubectl create secret tls --cert=... --key=...`.

### secret_create_tls

```
secret_create_tls(name: str, cert: str, key: str, namespace: str = "")
```

Deploys a secret to the cluster. Equivalent to

```
load('ext://secret', 'secret_yaml_tls')
k8s_yaml(secret_yaml_tls('name', cert=..., key=...))
```

## Example Usage

### For a Postgres password:

```
load('ext://secret', 'secret_create_generic')
secret_create_generic('pgpass', from_file='.pgpass=./.pgpass')
```

### For Google Cloud Platform Key:

```
load('ext://secret', 'secret_create_generic')
secret_create_generic('gcp-key', from_file='key.json=./gcp-creds.json')
```

### From a dict:

```
load('ext://secret', 'secret_from_dict')
k8s_yaml(secret_from_dict("secrets", inputs = {
    'SOME_TOKEN' : os.getenv('SOME_TOKEN')
}))
```

### For a TLS cert:

Check out [`mkcert`](https://github.com/FiloSottile/mkcert) for generating HTTPS certs for localhost.

```
load('ext://secret', 'secret_create_tls')
cert_file='./.secrets/cert.pem'
key_file='./.secrets/key.pem'
secret_create_tls('subdomain-localhost', cert=cert_file, key=key_file)
```

## Caveats

- This extension doesn't do any validation to confirm that names or namespaces are valid.
