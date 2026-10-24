# Repository Security Audit — Lab 06

**Review date:** 24 October 2026  
**Reviewed source commit:** [`539a5f072b159c06b3aaad91050b24835c10b852`](https://github.com/SkynetAI-Labs/lab-06-kubernetes-security/tree/539a5f072b159c06b3aaad91050b24835c10b852)  
**Scope:** Public repository configuration, documentation, retained evidence and available workflow results. This is a repository audit, not a fresh live-environment assessment or penetration test.

## Outcome
Application YAML parsing passed in [run 40132741038](https://github.com/SkynetAI-Labs/lab-06-kubernetes-security/actions/runs/40132741038), for `539a5f072b159c06b3aaad91050b24835c10b852`. Checkov 3.3.30 reported **279 passed, 4 failed, 0 skipped**. The workflow failed on those findings.

## Evidence checked
- Application manifests, kind cluster configuration, CI and retained test evidence.
- Workload controls include non-root execution, read-only root filesystems, no privilege escalation, dropped capabilities, runtime-default seccomp, resource limits and disabled automatic service-account token mounting.
- The web deployment has health probes. Disposable client and secret-check pods have no readiness/liveness probes, causing CKV_K8S_8 and CKV_K8S_9 on each pod.
- RBAC evidence uses administrator impersonation; it does not demonstrate token-based authentication as the auditor.
- Network policies select web/client labels, not every pod in the namespace. Egress is unrestricted by these policies.
- Client-ingress deny configuration was not live-tested. Vendor Calico manifests were outside the application-manifest CI scan scope.

## Recommendations
1. **Priority 1 for isolation claims — Test the complete allow/deny matrix**, including client ingress, unrelated pods, cross-namespace traffic and egress.
2. **Priority 2 — Decide whether namespace-wide ingress/egress isolation is required.** Add narrowly scoped DNS and application allowances and repeat connectivity tests if implemented.
3. **Priority 2 — Record a rationale for the four disposable-pod probe findings**, or redesign the test workloads with meaningful health checks. Do not add cosmetic probes solely to turn the scan green.
4. **Priority 2 — Test authenticated RBAC with a short-lived token**, avoiding publication of credentials.
5. **Priority 2 — Add Kubernetes schema/server-side validation and separately review CNI/version compatibility.** YAML parsing alone is not API validation.
6. **Priority 2 — Pin the checkout action to a verified SHA** and maintain image digests through reviewed updates.

The matching Lab 06 banner was added earlier in this audit cycle. No cluster was recreated or new live result asserted.

## Banner and evidence integrity

The README links an existing `skynet-lab-06.png` banner and the asset is present. No replacement was needed in this portfolio-wide pass. Historical evidence was preserved. Recommendations above remain open unless explicitly described as completed.

## References

- [GitHub Actions security guidance](https://docs.github.com/en/actions/reference/security/secure-use)
- [AWS S3 transport-encryption guidance](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingEncryptionInTransit.html)
- [Kubernetes NetworkPolicy semantics](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

