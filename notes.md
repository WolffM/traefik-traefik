## Steps to reproduce
1. From the repository root, run `go test ./pkg/provider/kubernetes/ingress -run 'TestLoadConfigurationFromIngresses/Ingress with TLS section should create HTTP and HTTPS routers' -count=1`.
2. This test uses an Ingress containing a `spec.tls` section for `example.com` but no `traefik.ingress.kubernetes.io/router.tls` annotation.
3. Observe the generated dynamic configuration from `loadConfigurationFromIngresses`.

## Observed
Before the fix, the provider produced only one HTTP router (`testing-myingress-example-com-foo`) and no HTTPS router. The test failed with a diff showing the expected TLS router (`testing-myingress-example-com-foo-tls`) was missing. This reproduces the bug: a single Ingress with TLS material could not expose both protocols at once without extra duplicated objects.

## Expected
A single Kubernetes Ingress with `spec.tls` should produce both an HTTP router and an HTTPS router for matching hosts, so clients can reach the same backend through either protocol. The generated HTTPS router should include `TLS: &dynamic.RouterTLSConfig{}` while preserving the existing HTTP router behavior.
