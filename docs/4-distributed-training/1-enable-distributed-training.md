# ⚙️ Enable Distributed Training Components

## Install DataScienceCluster components

Distributed training on OpenShift AI requires specific components in the `DataScienceCluster` (DSC). At minimum, for Kubeflow Trainer workloads from a workbench or the CLI:

| Component | Pipelines only | Workbenches only | Pipelines and workbenches |
| --- | --- | --- | --- |
| dashboard | Managed | Managed | Managed |
| aipipelines | Managed | Removed | Managed |
| kueue | Unmanaged | Unmanaged | Unmanaged |
| ray | Managed | Managed | Managed |
| trainingoperator | Managed | Managed | Managed |
| workbenches | Removed | Managed | Managed |

?> It is important to avoid defining kueue as *Managed* because we will install Red Hat Build of Kueue (RHBoK) 

1. As `cluster-admin`, open the OpenShift console and navigate to **Ecosystem** → **Operators** → **Installed Operators** → **Red Hat OpenShift AI** → **Data Science Cluster** → your DSC instance (for example, `default-dsc`).

2. Select the **YAML** tab and confirm the component states:

```yaml
spec:
  components:
    dashboard:
      managementState: Managed
    aipipelines:
      managementState: Managed
    kueue:
      managementState: Unmanaged
    ray:
      managementState: Managed
    trainingoperator:
      managementState: Managed
    workbenches:
      managementState: Managed
```

3. Save the DSC if you changed any values and wait for reconciliation to complete.

4. Open the OpenShift console and navigate to **Ecosystem** → **Operators** → **Software Catalog** → Filter by `Red Hat build of Kueue` →  Install the Operator

5. Wait for `Red Hat build of Kueue` operator installation to complete.

## Verify DataScienceCluster components

After changing DataScienceCluster and installing the `Red Hat build of Kueue` operator, the platform is ready to support any 

### Verify Kueue

Kueue queues and admits distributed workloads according to cluster quotas. Confirm the OpenShift Kueue operator and controller are running:

```bash
oc get pods -n openshift-kueue-operator
```

* **Sample output** for the Kueue controller in `openshift-kueue-operator`:

```text
NAME                                        READY   STATUS    RESTARTS   AGE
kueue-controller-manager-79bc65cbf5-298d5   1/1     Running   0          3m57s
kueue-controller-manager-79bc65cbf5-qc68q   1/1     Running   0          3m57s
openshift-kueue-operator-774fd4fc95-n9s5n   1/1     Running   0          4m37s
openshift-kueue-operator-774fd4fc95-qs58t   1/1     Running   0          4m37s
```

Additionally, confirm that a Kueue instance has been created:

```bash
oc get kueue
```

* **Sample output** for the Kueue:

```text
NAME            READY   REASON
default-kueue   True    
```

### Verify Ray

In OpenShift AI, you can run a Ray-based distributed workload from a Jupyter notebook or from a pipeline. Confirm the Ray controller is running:

```bash
oc get pods -n redhat-ods-applications -l app.kubernetes.io/name=kuberay
```

* **Sample output** for the Kueue controller in `redhat-ods-applications`:

```text
NAME                               READY   STATUS    RESTARTS      AGE
kuberay-operator-8dddf9c89-gc2zp   1/1     Running   0             17h
```

## Verify Training Operator (Kubeflow Training Operator)

To reduce the time needed to train a Large Language Model (LLM), you can run the training job in parallel. In Red Hat OpenShift AI, the Kubeflow Training Operator and Kubeflow Training Operator Python Software Development Kit (Training Operator SDK) simplify the job configuration.

```bash
oc get pods -n redhat-ods-applications -l app.kubernetes.io/name=training-operator
```

* **Sample output** for the Training Operator in `redhat-ods-applications`:

```text
NAME                                          READY   STATUS    RESTARTS   AGE
kubeflow-training-operator-774b75cb67-s9qfw   1/1     Running   0          9m36s
```
