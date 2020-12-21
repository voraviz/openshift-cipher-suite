# Configure Cipher Suite For OpenShift
## API Server
1. Edit API server
    ```
    oc edit apiservers
    ```
2. Update spec: with custom profile
   ```
   spec:
    tlsSecurityProfile:
     custom:
       ciphers:
       - ECDHE-ECDSA-CHACHA20-POLY1305
       - ECDHE-RSA-CHACHA20-POLY1305
       - ECDHE-RSA-AES128-GCM-SHA256
       - ECDHE-ECDSA-AES128-GCM-SHA256
       minTLSVersion: VersionTLS12
     type: Custom
   ```
   Sample output
   ```
   apiserver.config.openshift.io/cluster edited
   ```

3. Check that API Server is updated. You need to wait around 5 minutes. You will see minTLSVersion:VersionTLS12 when API Server is updated.
    ```
    watch oc get openshiftapiservers.operator.openshift.io cluster -o jsonpath='{.spec.observedConfig.servingInfo}'
    ```
    Sample outout
   ```
   map[cipherSuites:[TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305 TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305 TLS_ECDH
   E_RSA_WITH_AES_128_GCM_SHA256 TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256] minTLSVersion:VersionTLS12]
   ```
4. Check that CipherSuites and minTLSVersion is updated
   ```
    oc get cm -n openshift-authentication v4-0-config-system-cliconfig -o  jsonpath='{.data.v4\-0\-config\-system\-cliconfig}' | awk -F'servingInfo' '{print $2}'

    oc get openshiftapiservers.operator.openshift.io cluster -o jsonpath='{ .spec.observedConfig.servingInfo.cipherSuites}'

    oc get openshiftapiservers.operator.openshift.io cluster -o jsonpath='{ .spec.observedConfig.servingInfo.minTLSVersion}'
   ```
## Router
1. Configure ingresscontroller by run command
```bash
oc edit ingresscontroller default -n openshift-ingress-operator
```
2. Configure TLS Security profile and minimum TLS version
```yaml
spec:
  tlsSecurityProfile:
    type: Custom
    custom:
    ciphers:
    - ECDHE-ECDSA-AES128-GCM-SHA256
    - ECDHE-RSA-AES128-GCM-SHA256
    minTLSVersion: VersionTLS12
```

