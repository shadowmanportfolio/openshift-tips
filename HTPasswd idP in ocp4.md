# How to configure an HTPasswd identity provider in OpenShift Container Platform 4.x?

## Environment

- Red Hat OpenShift Container Platform
  - 4.x

## Issue

- How to configure an `HTPasswd identity provider` in OpenShift Container Platform 4.x?

## Resolution

- Create an `HTPasswd` file by installing the `htpasswd` utility by installing the `httpd-tools` package:

```
# yum install httpd-tools
```

- Create or update your with a user name and hashed password:

```
$ htpasswd -c -B -b </path/to/users.htpasswd> <user_name> <password>
```

- Create the `HTPasswd Secret`:

```
$ oc create secret generic htpass-secret --from-file=htpasswd=</path/to/users.htpasswd> -n openshift-config
```

- Create a custom resource for an `HTPasswd identity provider.`

```
$ cat auth.cr
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: my_htpasswd_provider 
    challenge: true 
    login: true 
    mappingMethod: claim 
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpass-secret 
```

- Apply the defined CR:

```
$ oc apply -f </path/to/CR>
```

- Now login using newly created user:

```
$ oc login -u <username>
```

- Confirm that the user logged in successfully, and display the user name.

```
$ oc whoami
```
