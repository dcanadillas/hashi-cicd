# CI/CD Pipelines Vault Integration

Example of Vault integration with CI/CD pipelines

## Requirements

* Running K8s cluster
* `kubectl` CLI installed
* Helm CLI installed
* A Terraform Cloud user and organization (some of the example pipelines do a TFC run)


## Installation

Install Vault and Jenkins:
```bahs
cd install
make jenkins
```

The script is going to ask if you are using the righ K8s context. Press any key to continue or Crtl'C to cancel.

> NOTE:
> *If you want to install every included CI/CD engine (Tekton Pipelines by now):*
> ```bash
> make install all
> ```

Change the values of the static secrets to be used in Vault in the file `install/config/secrets.json`:
```json
{
  "<tfc_var1>": "<value_to_be_set>",
  "<tfc_var2>": "<value_to_be_set>",
  "tfe_org": "<your_tfc_org>",
  "tfe_token": "<static_example_tfe_token>",
  "gh_user": "<gh_token>",
  "gh_token": "<gh_token> "
}
```


Configure Vault with the required secrets and Kubernetes auth:

```bash
make configure TFEORG=<your_TFC_organization> TFEUSER=<your_TFC_user>
```

## Try Jenkins pipelines integration

Jenkins is installed in the `jenkins` namespace:

```bash
kubectl get all -n jenkins
```

The password for the `admin` account is in `jenkins-admin` secret:

```bash
kubectl get secret -n jenkins jenkins-admin -o go-template='{{ index .data "jenkins-admin-password" }}' | base64 -d
```

If you can't expose a `LoadBalancer` service, do a `port-forward` of your Jenkins service in a different terminal:
```bash
kubectl port-forward svc/jenkins -n jenkins 9090:8080 --address 0.0.0.0
```

Then you should be able to access Jenkins at [http://localhost:9090](http://localhost:9090)

You should have a pipeline already configure in [http://localhost:9090/job/HashiCorp/job/vault-tfe-pipeline/](http://localhost:9090/job/HashiCorp/job/vault-tfe-pipeline/)



